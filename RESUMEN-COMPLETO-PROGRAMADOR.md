# 📋 RESUMEN COMPLETO PARA PROGRAMADOR - FP DAM GESTOR v4.0

## 🎯 PROPÓSITO
Este documento contiene TODO lo necesario para duplicar exactamente esta aplicación en un chat nuevo sin necesidad de explicaciones adicionales.

---

## 📱 QUÉ ES

PWA para gestionar horarios de estudio FP DAM con:
- Redistribución automática de horarios cuando hay entregas urgentes
- Sincronización multiplataforma vía Firebase
- Notificaciones push automáticas
- Dos modos: con/sin trabajo

**Usuario:** Estudiante FP DAM, 50 años, Ciutadella (ES), Samsung A36 + Chrome Canary

---

## 🛠️ STACK TÉCNICO

```
- JavaScript Vanilla ES6+ (SIN frameworks)
- Firebase Realtime Database 10.7.1
- HTML5 + CSS3 inline (un solo archivo)
- PWA con Service Worker
- localStorage (fallback)
- Web Notifications API
```

**Archivos:**
```
/
├── index.html           (~1550 líneas - ÚNICO A MODIFICAR)
├── manifest.json        (PWA config - NO TOCAR)
├── service-worker.js    (Cache - NO TOCAR)
├── README.md            (GitHub público)
├── FIREBASE-SETUP.md    (Guía configuración)
└── RESUMEN-COMPLETO-PROGRAMADOR.md (este archivo)
```

---

## 📊 ESTRUCTURA DE DATOS

### Estado Global
```javascript
let STATE = {
    currentView: 'setup|home|daily|weekly|deliveries|settings',
    setupStep: 1|2,
    hasWork: true|false|null,
    allActivities: {...BASE_ACTIVITIES},
    activeActivities: ['prog', 'bd', ...],
    completedTasks: { '2026-1-14': { 'prog': true } },
    deliveries: [{ id, subject, date, description, created }],
    currentDate: new Date(),
    notifiedActivities: new Set()
};
```

### Actividad Base
```javascript
{
    id: 'prog',
    name: 'Programación',
    type: 'fp-critical|fp-important|fp-light|learning|personal|break',
    color: 'badge-red|badge-yellow|badge-green|badge-purple|badge-blue|badge-orange',
    scheduleNoWork: { start: '08:00', end: '10:00', hours: '2h' },
    scheduleWithWork: { start: '07:30', end: '08:30', hours: '1h' },
    priority: 1|2|3|5,
    weekDays: [1,2,4],  // Solo learning
    onlyNoWork: false,
    fixed: false  // true para cena/familia/rutina
}
```

### 18 Actividades Totales

**7 Materias FP:**
- prog, bd (critical, red, priority 1)
- si, ed (important, yellow, priority 2)
- lm, digi, ipo (light, green, priority 3)

**3 Capacitaciones:**
- ingles (weekDays: [1,2,4])
- ias (weekDays: [1,3,5])
- seo (weekDays: [2,4,5])

**8 Bloques Personales:**
- trabajo, tiempolibre, revision (onlyNoWork: true)
- descanso1 (onlyNoWork: true)
- almuerzo
- cena, familia, nocturna (fixed: true)

---

## 🔥 REDISTRIBUCIÓN INTELIGENTE

### Activación
```javascript
daysUntil = getDaysUntilDelivery(delivery.date);
if (daysUntil >= 0 && daysUntil <= 3) // Urgente
```

### Multiplicadores
```javascript
const multiplier = daysUntil === 0 ? 3 : daysUntil === 1 ? 2.5 : 2;
```

### Algoritmo (5 PASOS)

#### PASO 0: Restaurar Base
```javascript
Object.keys(BASE_ACTIVITIES).forEach(key => {
    STATE.allActivities[key][schedule] = {...BASE_ACTIVITIES[key][schedule]};
    delete STATE.allActivities[key].boosted;
    delete STATE.allActivities[key].originalHours;
    delete STATE.allActivities[key].boostedHours;
    delete STATE.allActivities[key].reduced;
    delete STATE.allActivities[key].originalHoursReduced;
    delete STATE.allActivities[key].reducedHours;
});
```

#### PASO 1: Reducir fp-critical/important
- Solo materias **SIN entrega urgente**
- Reducir a **1h mínimo** (60 minutos)
- Marcar como reducidas:
```javascript
activity.reduced = true;
activity.originalHoursReduced = `${currentHours}h`;
activity.reducedHours = `${newHours}h`;
```

#### PASO 2: Eliminar Descanso y Revisión
```javascript
if (actId === 'descanso1' || actId === 'revision') {
    sched.hours = '0h';
    sched.start = '';
    sched.end = '';
}
```

#### PASO 3: Reducir fp-light y learning (si falta tiempo)
- Reducir primero a 15min (0.25h)
- Si no alcanza → eliminar (0h)
- **Rotar eliminación** usando día de semana

#### PASO 4: Aplicar Multiplicadores
```javascript
activity.boosted = true;
activity.originalHours = `${baseHours}h`;
activity.boostedHours = `${newHours.toFixed(2)}h`;
sched.hours = `${newHours.toFixed(2)}h`;
```

#### PASO 5: Recalcular start/end
```javascript
let currentMinutes = 8 * 60; // 08:00
const endDay = 21 * 60; // 21:00

activitiesToSchedule.forEach(item => {
    const durationMinutes = Math.round(item.hours * 60);
    
    // Saltar almuerzo si choca
    if (currentMinutes < almuerzoStart && currentMinutes + durationMinutes > almuerzoStart) {
        currentMinutes = almuerzoEnd;
    }
    
    // Calcular start/end
    item.schedule.start = `${pad(Math.floor(currentMinutes/60))}:${pad(currentMinutes%60)}`;
    item.schedule.end = `${pad(Math.floor((currentMinutes+durationMinutes)/60))}:${pad((currentMinutes+durationMinutes)%60)}`;
    
    // Avanzar SIN descanso adicional (corrige huecos)
    currentMinutes = currentMinutes + durationMinutes;
});
```

**CRÍTICO:** NO agregar 15min entre actividades (causa huecos)

### Bloques Fixed
```javascript
if (activity.fixed || actId === 'cena' || actId === 'familia' || actId === 'nocturna') {
    sched.start = BASE_ACTIVITIES[actId][schedule].start;
    sched.end = BASE_ACTIVITIES[actId][schedule].end;
    sched.hours = BASE_ACTIVITIES[actId][schedule].hours;
    return; // No reorganizar
}
```

---

## 🔔 NOTIFICACIONES

### Sistema
```javascript
setInterval(() => {
    const now = new Date();
    const timeStr = `${pad(now.getHours())}:${pad(now.getMinutes())}`;
    const dateKey = getDateKey(now);
    
    STATE.activeActivities.forEach(actId => {
        const activity = STATE.allActivities[actId];
        const sched = activity[schedule];
        const key = `${dateKey}-${actId}-${sched.start}`;
        
        if (sched.start === timeStr && !STATE.notifiedActivities.has(key)) {
            new Notification('⏰ FP DAM - Es la hora!', {
                body: `Ahora: ${activity.name} (${sched.hours})`,
                icon: '📚'
            });
            STATE.notifiedActivities.add(key);
        }
    });
}, 1000);
```

### Permisos
```javascript
if ('Notification' in window && Notification.permission === 'default') {
    Notification.requestPermission();
}
```

---

## ☁️ FIREBASE

### Configuración (línea ~166)
```javascript
const firebaseConfig = {
    apiKey: "...",
    authDomain: "...",
    databaseURL: "https://...-default-rtdb.firebaseio.com",
    projectId: "...",
    storageBucket: "...",
    messagingSenderId: "...",
    appId: "..."
};

let db = null;
let firebaseEnabled = false;
const FIREBASE_PATH = 'fp-dam-user-data';

if (firebaseConfig.apiKey !== "TU_API_KEY_AQUI") {
    firebase.initializeApp(firebaseConfig);
    db = firebase.database();
    firebaseEnabled = true;
}
```

### Guardar (Dual: localStorage + Firebase)
```javascript
function saveData() {
    const data = { hasWork, allActivities, activeActivities, completedTasks, deliveries, lastSaved, version };
    
    // localStorage (siempre, fallback)
    localStorage.setItem('fpDamApp', JSON.stringify(data));
    
    // Firebase (asíncrono, no bloquea)
    if (firebaseEnabled && db) {
        db.ref(FIREBASE_PATH).set(data).catch(err => console.error(err));
    }
}
```

### Cargar (localStorage → luego Firebase en background)
```javascript
function loadData() {
    // 1. Cargar localStorage (rápido)
    const saved = localStorage.getItem('fpDamApp');
    if (saved) {
        applyDataToState(JSON.parse(saved));
    }
    
    // 2. Firebase en background (no bloquea)
    if (firebaseEnabled && db) {
        db.ref(FIREBASE_PATH).once('value').then(snapshot => {
            const firebaseData = snapshot.val();
            if (firebaseData) {
                const localTS = saved ? JSON.parse(saved).lastSaved : null;
                const firebaseTS = firebaseData.lastSaved;
                
                // Si Firebase es más reciente
                if (!localTS || firebaseTS > localTS) {
                    applyDataToState(firebaseData);
                    localStorage.setItem('fpDamApp', JSON.stringify(firebaseData));
                    render(); // Re-renderizar
                }
            }
        });
    }
}
```

**Estrategia:** No bloquea el inicio, carga localStorage primero y sincroniza Firebase después.

---

## 🎨 VISTAS

### DailyView - Renderizado de Actividades

```javascript
activitiesHTML += `
    <div class="activity-item ${completed ? 'completed' : ''}" onclick="toggleTask('${activity.id}')">
        <div class="checkbox ${completed ? 'checked' : ''}"></div>
        <div class="activity-content">
            <div class="activity-name">${activity.name}</div>
            <div class="activity-time">⏰ ${sched.start} - ${sched.end}</div>
        </div>
        <span class="badge ${activity.color}">
            ${activity.boosted ? activity.boostedHours : (activity.reduced ? activity.reducedHours : sched.hours)}
        </span>
        ${activity.boosted ? `<span class="badge badge-orange">🔥 ${activity.originalHours} → ${activity.boostedHours}</span>` : ''}
        ${activity.reduced ? `<span class="badge badge-blue">⬇️ ${activity.originalHoursReduced} → ${activity.reducedHours}</span>` : ''}
    </div>
`;
```

**Badges:**
- 🔥 **Naranja** (badge-orange): Materia boosteada
- ⬇️ **Azul** (badge-blue): Materia reducida
- Color de la materia: Horas actuales

---

## 🔧 FUNCIONES CRÍTICAS

```javascript
function formatDate(date) {
    const days = ['Domingo','Lunes','Martes','Miércoles','Jueves','Viernes','Sábado'];
    const months = ['Enero','Febrero','Marzo','Abril','Mayo','Junio','Julio','Agosto','Septiembre','Octubre','Noviembre','Diciembre'];
    return `${days[date.getDay()]} ${date.getDate()} de ${months[date.getMonth()]}`;
}

function getDateKey(date) {
    return `${date.getFullYear()}-${date.getMonth()}-${date.getDate()}`;
}

function getDaysUntilDelivery(deliveryDate) {
    const today = new Date();
    today.setHours(0,0,0,0);
    const delivery = new Date(deliveryDate);
    delivery.setHours(0,0,0,0);
    return Math.floor((delivery - today) / (1000*60*60*24));
}

function getProgress(date = STATE.currentDate) {
    const dateKey = getDateKey(date);
    const tasks = STATE.completedTasks[dateKey] || {};
    const activitiesToday = STATE.activeActivities.filter(id => {
        const act = STATE.allActivities[id];
        if (STATE.hasWork && act.onlyNoWork) return false;
        if (act.weekDays && !act.weekDays.includes(date.getDay())) return false;
        const sched = act[STATE.hasWork ? 'scheduleWithWork' : 'scheduleNoWork'];
        return sched && sched.start !== '' && sched.start !== 'Var' && sched.hours !== '0h';
    });
    const completed = activitiesToday.filter(id => tasks[id]).length;
    return activitiesToday.length === 0 ? 0 : Math.round((completed / activitiesToday.length) * 100);
}

function toggleTask(activityId) {
    const dateKey = getDateKey(STATE.currentDate);
    if (!STATE.completedTasks[dateKey]) STATE.completedTasks[dateKey] = {};
    STATE.completedTasks[dateKey][activityId] = !STATE.completedTasks[dateKey][activityId];
    saveData();
    render();
}
```

---

## 🎨 ESTILOS

### Badges
```css
.badge-red { background: #ef4444; }
.badge-yellow { background: #eab308; }
.badge-green { background: #22c55e; }
.badge-purple { background: #a855f7; }
.badge-blue { background: #3b82f6; }
.badge-orange { background: #f97316; }
```

### Mobile-First
- `max-width: 600px` container
- `padding: env(safe-area-inset-top/bottom)` para PWA
- Botones: `min-height: 50px`, `padding: 15px`
- `font-size: 16px` mínimo

---

## ⚠️ REGLAS CRÍTICAS

### SIEMPRE:
✅ Solo modificar `index.html`
✅ Bloques fixed mantienen horario BASE_ACTIVITIES
✅ Mínimos: 1h críticas, 30min leves, 15min learning
✅ Rotar eliminación de capacitaciones
✅ Ventana: 08:00-21:00
✅ Domingo: 0 actividades
✅ NO agregar 15min entre actividades (causa huecos)
✅ Limpiar TODAS las propiedades boost/reducción al restaurar

### NUNCA:
❌ Mover cena (21:00), familia (22:00), rutina (23:00)
❌ Reducir críticas < 1h
❌ Estudiar después de 21:00
❌ Eliminar de BASE_ACTIVITIES (solo marcar hours: '0h')
❌ Usar frameworks externos
❌ Modificar manifest.json o service-worker.js
❌ Agregar descansos entre actividades en recálculo

---

## 📦 DEPLOYMENT

1. Usuario crea repo GitHub
2. Sube 3 archivos: index.html, manifest.json, service-worker.js
3. Settings → Pages → main branch
4. Configura Firebase (opcional, ver FIREBASE-SETUP.md)
5. URL: `username.github.io/repo-name`

---

## 🐛 BUGS SOLUCIONADOS EN v4.0

### Bug 1: Booster se acumulaba
**Causa:** No se limpiaban propiedades boosted al restaurar
**Solución:** Delete de TODAS las propiedades en PASO 0

### Bug 2: Huecos en horarios
**Causa:** Se agregaban 15min entre TODAS las actividades
**Solución:** Eliminado `currentMinutes + 15` en recálculo (línea ~749)

### Bug 3: No mostraba reducciones
**Causa:** Solo se marcaban boosts, no reducciones
**Solución:** Agregar propiedades `reduced`, `originalHoursReduced`, `reducedHours` + badges azules

### Bug 4: Firebase bloqueaba inicio
**Causa:** loadData() esperaba Firebase (asíncrono)
**Solución:** Cargar localStorage primero, Firebase en background sin bloquear

---

## 📊 ESTADO FINAL

**Versión:** 4.0-firebase
**Fecha:** 14 Enero 2026
**Funcionalidades:**
- ✅ Redistribución sin huecos
- ✅ Visualización boost + reducción
- ✅ Firebase sincronización
- ✅ Notificaciones automáticas
- ✅ Backup manual
- ✅ PWA instalable
- ✅ Domingo OFF

---

## 🚀 USO EN CHAT NUEVO

**Prompt:**
```
Tengo esta app FP DAM Gestor (PWA gestión horarios estudio FP).

[Subir: RESUMEN-COMPLETO-PROGRAMADOR.md + ZIP]

La app funciona. Solo necesito [cambio específico].

Importante:
- NO romper lo que funciona
- Solo modificar index.html
- Respetar reglas críticas del resumen

¿Puedes hacerlo?
```

---

**FIN - Todo para duplicar la app está aquí** 🚀
