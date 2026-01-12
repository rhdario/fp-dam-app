# 📋 RESUMEN COMPLETO TÉCNICO - FP DAM GESTOR PWA v1.0

## 🎯 PROPÓSITO

Este documento contiene **TODA la información técnica** para crear la aplicación desde cero sin errores.

**AUDIENCIA:** Desarrolladores.

---

## 📦 PROYECTO

### **Usuario:**
- **Ubicación:** Ciutadella, Balearic Islands, ES
- **Estudiante:** FP DAM
- **Situación:** Variable (trabajo 8h o sin trabajo)
- **Dispositivos:** Samsung A36 + PC
- **Deploy:** GitHub Pages

### **Versión:**
- **v1.0** - Redistribución Optimizada + Firebase
- **Fecha:** 12 Enero 2026
- **Archivos:** `index.html` (~1620 líneas), `manifest.json`, `service-worker.js`

---

## 🛠️ STACK TÉCNICO

```
Frontend: JavaScript Vanilla ES6+, HTML5, CSS3 inline
Persistencia: Firebase Firestore + localStorage (backup)
PWA: Service Worker, manifest.json, notificaciones push
```

---

## 📊 BASE_ACTIVITIES (17 actividades)

**NOTA:** `descanso1` eliminado (usuario usa Pomodoro).

```javascript
const BASE_ACTIVITIES = {
    // fp-critical (Prioridad 1, Mínimo 1h)
    prog: { 
        id: 'prog', name: 'Programación', type: 'fp-critical', color: 'badge-red',
        scheduleNoWork: { start: '08:00', end: '10:00', hours: '2h' },
        scheduleWithWork: { start: '07:30', end: '08:30', hours: '1h' },
        priority: 1 
    },
    bd: { 
        id: 'bd', name: 'Base de Datos', type: 'fp-critical', color: 'badge-red',
        scheduleNoWork: { start: '10:15', end: '11:45', hours: '1.5h' },
        scheduleWithWork: { start: '17:30', end: '18:30', hours: '1h' },
        priority: 1 
    },
    
    // fp-important (Prioridad 2, Mínimo 45min)
    si: {
        id: 'si', name: 'Sistemas Informáticos', type: 'fp-important', color: 'badge-yellow',
        scheduleNoWork: { start: '15:00', end: '16:00', hours: '1h' },
        scheduleWithWork: { start: '18:30', end: '19:00', hours: '0.5h' },
        priority: 2
    },
    ed: {
        id: 'ed', name: 'Entornos Desarrollo', type: 'fp-important', color: 'badge-yellow',
        scheduleNoWork: { start: '16:00', end: '16:45', hours: '0.75h' },
        scheduleWithWork: { start: '20:30', end: '21:00', hours: '0.5h' },
        priority: 2
    },
    
    // fp-light (Prioridad 3, Mínimo 45min)
    lm: {
        id: 'lm', name: 'Lenguajes Marcas', type: 'fp-light', color: 'badge-green',
        scheduleNoWork: { start: '16:45', end: '17:30', hours: '0.75h' },
        scheduleWithWork: { start: 'Var', end: 'Var', hours: 'Var' },
        priority: 3
    },
    digi: {
        id: 'digi', name: 'Digitalización', type: 'fp-light', color: 'badge-green',
        scheduleNoWork: { start: '17:30', end: '18:00', hours: '0.5h' },
        scheduleWithWork: { start: 'Var', end: 'Var', hours: 'Var' },
        priority: 3
    },
    ipo: {
        id: 'ipo', name: 'Itinerario IPO', type: 'fp-light', color: 'badge-green',
        scheduleNoWork: { start: '18:00', end: '18:30', hours: '0.5h' },
        scheduleWithWork: { start: 'Var', end: 'Var', hours: 'Var' },
        priority: 3
    },
    
    // learning (Prioridad 2, Mínimo 15min, rotan por día)
    ingles: {
        id: 'ingles', name: 'Inglés', type: 'learning', color: 'badge-purple',
        scheduleNoWork: { start: '11:45', end: '12:15', hours: '0.5h' },
        scheduleWithWork: { start: '20:30', end: '20:50', hours: '0.33h' },
        priority: 2, weekDays: [1,2,4]
    },
    ias: {
        id: 'ias', name: 'Estudiar IAs', type: 'learning', color: 'badge-purple',
        scheduleNoWork: { start: '12:15', end: '12:45', hours: '0.5h' },
        scheduleWithWork: { start: '19:30', end: '20:00', hours: '0.5h' },
        priority: 2, weekDays: [1,3,5]
    },
    seo: {
        id: 'seo', name: 'Estudiar SEO', type: 'learning', color: 'badge-purple',
        scheduleNoWork: { start: '12:45', end: '13:15', hours: '0.5h' },
        scheduleWithWork: { start: '20:00', end: '20:30', hours: '0.5h' },
        priority: 2, weekDays: [2,4,5]
    },
    
    // personal
    trabajo: {
        id: 'trabajo', name: 'Búsqueda Trabajo', type: 'personal', color: 'badge-blue',
        scheduleNoWork: { start: '18:30', end: '20:00', hours: '1.5h' },
        scheduleWithWork: { start: '', end: '', hours: '' },
        priority: 2, onlyNoWork: true
    },
    tiempolibre: {
        id: 'tiempolibre', name: 'Tiempo Libre', type: 'personal', color: 'badge-blue',
        scheduleNoWork: { start: '', end: '', hours: '' },
        scheduleWithWork: { start: '', end: '', hours: '' },
        priority: 3, onlyNoWork: true
    },
    revision: {
        id: 'revision', name: 'Revisión del Día', type: 'personal', color: 'badge-blue',
        scheduleNoWork: { start: '20:00', end: '21:00', hours: '1h' },
        scheduleWithWork: { start: '', end: '', hours: '' },
        priority: 2, onlyNoWork: true
    },
    
    // break
    almuerzo: {
        id: 'almuerzo', name: 'Almuerzo + Descanso', type: 'break', color: 'badge-orange',
        scheduleNoWork: { start: '13:15', end: '15:00', hours: '1.75h' },
        scheduleWithWork: { start: '13:00', end: '14:00', hours: '1h' },
        priority: 5, fixed: true
    },
    
    // fixed (INTOCABLES)
    cena: {
        id: 'cena', name: 'Cena', type: 'personal', color: 'badge-orange',
        scheduleNoWork: { start: '21:00', end: '22:00', hours: '1h' },
        scheduleWithWork: { start: '22:00', end: '23:00', hours: '1h' },
        priority: 5, fixed: true
    },
    familia: {
        id: 'familia', name: 'Pareja/Familia', type: 'personal', color: 'badge-purple',
        scheduleNoWork: { start: '22:00', end: '23:00', hours: '1h' },
        scheduleWithWork: { start: '21:00', end: '22:00', hours: '1h' },
        priority: 5, fixed: true
    },
    nocturna: {
        id: 'nocturna', name: 'Rutina Nocturna', type: 'personal', color: 'badge-blue',
        scheduleNoWork: { start: '23:00', end: '00:00', hours: '1h' },
        scheduleWithWork: { start: '23:00', end: '00:00', hours: '1h' },
        priority: 5, fixed: true
    }
};
```

---

## 🎯 REGLAS POR TIPO

| Tipo | Mínimo | Reducible | Eliminable | Notas |
|------|--------|-----------|------------|-------|
| fp-critical | 1h | ✅ (nunca <1h) | ❌ | Prog, BD |
| fp-important | 45min | ✅ (nunca <45min) | ❌ | Sistemas, Entornos |
| fp-light | 45min | ✅ (nunca <45min) | ✅ rotar | Lenguajes, Digi, IPO |
| learning | 15min | ✅ (nunca <15min) | ✅ rotar | Inglés, IAs, SEO |
| personal | - | ✅ | ✅ | Revisión siempre eliminable |
| break | - | ❌ | ❌ | Almuerzo movible 14:00-15:15 ideal |
| fixed | - | ❌ NUNCA | ❌ NUNCA | Cena, Familia, Rutina |

---

## 🔥 REDISTRIBUCIÓN INTELIGENTE

### **Activación:**
Entregas urgentes (≤3 días).

### **Multiplicadores:**
```
HOY (0 días): x3
MAÑANA (1 día): x2.5
2-3 días: x2
```

### **ALGORITMO:**

#### **PASO 0: Restaurar**
```javascript
Object.keys(BASE_ACTIVITIES).forEach(key => {
    stateAct[schedule] = { ...BASE_ACTIVITIES[key][schedule] };
    stateAct.boosted = false;
    stateAct.boostedMultiplier = null;
    stateAct.boostedBaseHours = null;
});
```

#### **PASO 1: Reducir fp-critical (1h) y fp-important (45min)**
```javascript
let minMinutes = 0;
if (activity.type === 'fp-critical') minMinutes = 60;
else if (activity.type === 'fp-important') minMinutes = 45;

if (minMinutes > 0 && currentMinutes > minMinutes) {
    timeLiberated += currentMinutes - minMinutes;
    activity[schedule].hours = `${(minMinutes/60).toFixed(2)}h`;
}
```

#### **PASO 2: Eliminar Revisión**
```javascript
if (actId === 'revision') {
    timeLiberated += hours * 60;
    activity[schedule].hours = '0h';
    activity[schedule].start = '';
    activity[schedule].end = '';
}
```

#### **PASO 3: Reducir fp-light (45min) y learning (15min)**
```javascript
// Mínimos
const minMinutes = item.type === 'fp-light' ? 45 : 15;

// Primero reducir al mínimo
if (currentMinutes > minMinutes) {
    tempLiberated += currentMinutes - minMinutes;
    item.schedule.hours = `${(minMinutes/60).toFixed(2)}h`;
}
// Si ya está en mínimo, eliminar (rotando)
else {
    tempLiberated += currentMinutes;
    item.schedule.hours = '0h';
}

// Rotación
const dayOfWeek = STATE.currentDate.getDay();
const startIndex = dayOfWeek % reducibles.length;
```

#### **PASO 4: Aplicar multiplicadores**
```javascript
const multiplier = daysUntil === 0 ? 3 : daysUntil === 1 ? 2.5 : 2;
const newHours = baseHours * multiplier;

activity[schedule].hours = `${newHours.toFixed(2)}h`;
activity.boosted = true;
activity.boostedMultiplier = multiplier;
activity.boostedBaseHours = baseHours;
```

#### **PASO 5: Recalcular horarios SIN HUECOS**
```javascript
// Dividir materias >3h en 2 bloques
if (item.boosted && item.hours > 3) {
    const halfHours = item.hours / 2;
    scheduledItems.push({ ...item, hours: halfHours, name: `${item.name} (Parte 1)` });
    scheduledItems.push({ ...item, hours: halfHours, name: `${item.name} (Parte 2)`, priority: item.priority + 0.5 });
}

// Asignar horarios secuencialmente
let currentMinutes = 8 * 60;
const endDay = 21 * 60;

scheduledItems.forEach(item => {
    const durationMinutes = Math.round(item.hours * 60);
    
    // Saltar almuerzo si choca
    if (currentMinutes < almuerzoEnd && currentMinutes + durationMinutes > almuerzoStart) {
        if (currentMinutes < almuerzoStart) currentMinutes = almuerzoEnd;
    }
    
    // Verificar límite
    if (currentMinutes + durationMinutes > endDay) {
        item.schedule.hours = '0h';
        return;
    }
    
    // Calcular start/end
    item.schedule.start = calcTime(currentMinutes);
    item.schedule.end = calcTime(currentMinutes + durationMinutes);
    
    // Avanzar SIN huecos
    currentMinutes = currentMinutes + durationMinutes;
});
```

### **Bloques fixed:**
```javascript
if (activity.fixed || actId === 'cena' || actId === 'familia' || actId === 'nocturna') {
    activity[schedule] = { ...BASE_ACTIVITIES[actId][schedule] };
    return;
}
```

---

## ☁️ FIREBASE

### **SDK en `<head>`:**
```html
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore-compat.js"></script>
```

### **Config:**
```javascript
const firebaseConfig = {
    apiKey: "TU_API_KEY",
    authDomain: "tu-proyecto.firebaseapp.com",
    projectId: "tu-proyecto-id",
    storageBucket: "tu-proyecto.appspot.com",
    messagingSenderId: "123456789",
    appId: "1:123456789:web:abcdef"
};

let db = null;
let firebaseInitialized = false;

try {
    if (typeof firebase !== 'undefined') {
        firebase.initializeApp(firebaseConfig);
        db = firebase.firestore();
        firebaseInitialized = true;
    }
} catch (error) {
    firebaseInitialized = false;
}
```

### **saveData():**
```javascript
function saveData() {
    const dataToSave = { hasWork, allActivities, activeActivities, completedTasks, deliveries, lastSaved, version: '1.0-final' };
    
    localStorage.setItem('fpDamApp', JSON.stringify(dataToSave));
    
    if (firebaseInitialized && db) {
        const userId = getOrCreateUserId();
        db.collection('users').doc(userId).set(dataToSave, { merge: true });
    }
    
    showSaveIndicator();
    return true;
}
```

### **loadData():**
```javascript
async function loadData() {
    if (firebaseInitialized && db) {
        const userId = getOrCreateUserId();
        const doc = await db.collection('users').doc(userId).get();
        if (doc.exists) {
            const data = doc.data();
            // Cargar STATE desde data
            localStorage.setItem('fpDamApp', JSON.stringify(data));
            return true;
        }
    }
    
    const saved = localStorage.getItem('fpDamApp');
    if (saved) {
        const data = JSON.parse(saved);
        // Cargar STATE desde data
        return true;
    }
    return false;
}
```

### **getOrCreateUserId():**
```javascript
function getOrCreateUserId() {
    let userId = localStorage.getItem('fpDamUserId');
    if (!userId) {
        userId = 'user_' + Date.now() + '_' + Math.random().toString(36).substr(2, 9);
        localStorage.setItem('fpDamUserId', userId);
    }
    return userId;
}
```

---

## 🎨 BADGE VISUAL

```javascript
${activity.boosted ? `<span class="badge badge-orange">${activity.boostedBaseHours}h → ${parseFloat(sched.hours).toFixed(1)}h 🔥</span>` : ''}
```

**Resultado:** `"2h → 4h 🔥"`

---

## 📱 PWA

### **manifest.json:**
```json
{
  "name": "FP DAM Gestor de Estudio",
  "short_name": "FP DAM",
  "start_url": "./",
  "display": "standalone",
  "theme_color": "#2563eb"
}
```

### **service-worker.js:**
```javascript
const CACHE_NAME = 'fp-dam-v2';
const urlsToCache = ['./', './index.html', './manifest.json'];

self.addEventListener('install', event => {
  event.waitUntil(caches.open(CACHE_NAME).then(cache => cache.addAll(urlsToCache)));
  self.skipWaiting();
});

self.addEventListener('fetch', event => {
  event.respondWith(
    fetch(event.request)
      .then(response => {
        caches.open(CACHE_NAME).then(cache => cache.put(event.request, response.clone()));
        return response;
      })
      .catch(() => caches.match(event.request))
  );
});
```

---

## ⚠️ REGLAS CRÍTICAS

### **NO:**
1. ❌ Agregar huecos de 15min
2. ❌ Reducir por debajo de mínimos (1h, 45min, 45min, 15min)
3. ❌ Mover bloques fixed
4. ❌ Reducir almuerzo
5. ❌ Extender después de 21:00

### **SÍ:**
1. ✅ Horarios SIN HUECOS (`currentMinutes = endMinutes`)
2. ✅ Respetar mínimos
3. ✅ Rotar eliminación
4. ✅ Dividir materias boosted >3h
5. ✅ Establecer boosted, boostedMultiplier, boostedBaseHours
6. ✅ Ventana estricta 08:00-21:00

---

## ✅ CHECKLIST

- [ ] BASE_ACTIVITIES completo (17, sin descanso1)
- [ ] Firebase SDK configurado
- [ ] applyUrgentBoost() completo
- [ ] Mínimos correctos (1h, 45min, 45min, 15min)
- [ ] Horarios SIN HUECOS
- [ ] Bloques fixed no se mueven
- [ ] División materias >3h
- [ ] Rotación eliminación
- [ ] Badge visual correcto
- [ ] saveData() híbrido
- [ ] loadData() async
- [ ] manifest.json + service-worker.js
- [ ] Safe-area insets
- [ ] Notificaciones
- [ ] Probar en Samsung A36

---

**FIN - v1.0 Final - 12 Enero 2026**
