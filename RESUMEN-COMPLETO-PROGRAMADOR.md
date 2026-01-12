# 📋 RESUMEN COMPLETO TÉCNICO - FP DAM GESTOR PWA v4.0

## 🎯 PROPÓSITO DE ESTE DOCUMENTO

Este documento contiene **TODA la información técnica necesaria** para duplicar la aplicación exactamente igual en cualquier chat nuevo con Claude u otro LLM, sin errores ni omisiones.

**AUDIENCIA:** Programadores que necesitan replicar o modificar esta app.

---

## 📦 INFORMACIÓN DEL PROYECTO

### **Datos del Usuario:**
- **Ubicación:** Ciutadella, Balearic Islands, ES
- **Estudiante:** FP DAM (Desarrollo de Aplicaciones Multiplataforma)
- **Situación laboral:** Variable (puede tener trabajo 8h o no tenerlo)
- **Dispositivos:** Samsung A36 (Android) + PC
- **Navegador:** Chrome Canary (recomendado) / Chrome
- **Deploy:** GitHub Pages

### **Versión Actual:**
- **Versión:** 4.0 - Firebase Integration
- **Fecha:** 12 Enero 2026
- **Archivos:** `index.html` (~1550 líneas), `manifest.json`, `service-worker.js`

---

## 🛠️ STACK TÉCNICO COMPLETO

### **Frontend:**
```
- JavaScript Vanilla ES6+ (NO frameworks, NO React, NO Vue)
- HTML5 con CSS3 inline en un solo archivo
- Responsive design con Flexbox/Grid
- Safe-area insets para PWA móvil
```

### **Persistencia:**
```
- Firebase Firestore (sincronización multiplataforma)
- localStorage (backup local y fallback)
- Persistencia permanente (NO sessionStorage)
```

### **PWA:**
```
- Service Worker para cache offline
- manifest.json con iconos SVG inline
- Notificaciones push (Web Notifications API)
```

### **Arquitectura:**
```
Archivo único: index.html
├── HTML Structure
├── CSS inline (<style> en <head>)
└── JavaScript (<script> al final de <body>)
    ├── Firebase Config
    ├── BASE_ACTIVITIES (datos constantes)
    ├── STATE (estado reactivo)
    ├── Funciones de datos (save/load/sync)
    ├── Redistribución inteligente
    ├── Sistema de alarmas
    ├── Renderizado de vistas
    └── Inicialización
```

---

## 📊 ESTRUCTURA DE DATOS

### **BASE_ACTIVITIES** (Constante - 18 actividades)

```javascript
const BASE_ACTIVITIES = {
    // 7 MATERIAS FP
    prog: { 
        id: 'prog', 
        name: 'Programación', 
        type: 'fp-critical', 
        color: 'badge-red',
        scheduleNoWork: { start: '08:00', end: '10:00', hours: '2h' },
        scheduleWithWork: { start: '07:30', end: '08:30', hours: '1h' },
        priority: 1 
    },
    bd: { 
        id: 'bd', 
        name: 'Base de Datos', 
        type: 'fp-critical', 
        color: 'badge-red',
        scheduleNoWork: { start: '10:15', end: '11:45', hours: '1.5h' },
        scheduleWithWork: { start: '17:30', end: '18:30', hours: '1h' },
        priority: 1 
    },
    si: {
        id: 'si',
        name: 'Sistemas Informáticos',
        type: 'fp-important',
        color: 'badge-yellow',
        scheduleNoWork: { start: '15:00', end: '16:00', hours: '1h' },
        scheduleWithWork: { start: '18:30', end: '19:00', hours: '0.5h' },
        priority: 2
    },
    ed: {
        id: 'ed',
        name: 'Entornos Desarrollo',
        type: 'fp-important',
        color: 'badge-yellow',
        scheduleNoWork: { start: '16:00', end: '16:45', hours: '0.75h' },
        scheduleWithWork: { start: '20:30', end: '21:00', hours: '0.5h' },
        priority: 2
    },
    lm: {
        id: 'lm',
        name: 'Lenguajes Marcas',
        type: 'fp-light',
        color: 'badge-green',
        scheduleNoWork: { start: '16:45', end: '17:30', hours: '0.75h' },
        scheduleWithWork: { start: 'Var', end: 'Var', hours: 'Var' },
        priority: 3
    },
    digi: {
        id: 'digi',
        name: 'Digitalización',
        type: 'fp-light',
        color: 'badge-green',
        scheduleNoWork: { start: '17:30', end: '18:00', hours: '0.5h' },
        scheduleWithWork: { start: 'Var', end: 'Var', hours: 'Var' },
        priority: 3
    },
    ipo: {
        id: 'ipo',
        name: 'Itinerario IPO',
        type: 'fp-light',
        color: 'badge-green',
        scheduleNoWork: { start: '18:00', end: '18:30', hours: '0.5h' },
        scheduleWithWork: { start: 'Var', end: 'Var', hours: 'Var' },
        priority: 3
    },
    
    // 3 CAPACITACIONES (rotan por día de semana)
    ingles: {
        id: 'ingles',
        name: 'Inglés',
        type: 'learning',
        color: 'badge-purple',
        scheduleNoWork: { start: '11:45', end: '12:15', hours: '0.5h' },
        scheduleWithWork: { start: '20:30', end: '20:50', hours: '0.33h' },
        priority: 2,
        weekDays: [1,2,4] // Lunes, Martes, Jueves
    },
    ias: {
        id: 'ias',
        name: 'Estudiar IAs',
        type: 'learning',
        color: 'badge-purple',
        scheduleNoWork: { start: '12:15', end: '12:45', hours: '0.5h' },
        scheduleWithWork: { start: '19:30', end: '20:00', hours: '0.5h' },
        priority: 2,
        weekDays: [1,3,5] // Lunes, Miércoles, Viernes
    },
    seo: {
        id: 'seo',
        name: 'Estudiar SEO',
        type: 'learning',
        color: 'badge-purple',
        scheduleNoWork: { start: '12:45', end: '13:15', hours: '0.5h' },
        scheduleWithWork: { start: '20:00', end: '20:30', hours: '0.5h' },
        priority: 2,
        weekDays: [2,4,5] // Martes, Jueves, Viernes
    },
    
    // TRABAJO Y TIEMPO LIBRE
    trabajo: {
        id: 'trabajo',
        name: 'Búsqueda Trabajo',
        type: 'personal',
        color: 'badge-blue',
        scheduleNoWork: { start: '18:30', end: '20:00', hours: '1.5h' },
        scheduleWithWork: { start: '', end: '', hours: '' },
        priority: 2,
        onlyNoWork: true
    },
    tiempolibre: {
        id: 'tiempolibre',
        name: 'Tiempo Libre',
        type: 'personal',
        color: 'badge-blue',
        scheduleNoWork: { start: '', end: '', hours: '' },
        scheduleWithWork: { start: '', end: '', hours: '' },
        priority: 3,
        onlyNoWork: true
    },
    
    // REVISIÓN
    revision: {
        id: 'revision',
        name: 'Revisión del Día',
        type: 'personal',
        color: 'badge-blue',
        scheduleNoWork: { start: '20:00', end: '21:00', hours: '1h' },
        scheduleWithWork: { start: '', end: '', hours: '' },
        priority: 2,
        onlyNoWork: true
    },
    
    // COMIDAS Y DESCANSOS
    descanso1: {
        id: 'descanso1',
        name: 'Descanso',
        type: 'break',
        color: 'badge-yellow',
        scheduleNoWork: { start: '10:00', end: '10:15', hours: '0.25h' },
        scheduleWithWork: { start: '', end: '', hours: '' },
        priority: 3,
        onlyNoWork: true
    },
    almuerzo: {
        id: 'almuerzo',
        name: 'Almuerzo + Descanso',
        type: 'break',
        color: 'badge-orange',
        scheduleNoWork: { start: '13:15', end: '15:00', hours: '1.75h' },
        scheduleWithWork: { start: '13:00', end: '14:00', hours: '1h' },
        priority: 5,
        fixed: true
    },
    
    // VIDA PERSONAL INTOCABLE
    cena: {
        id: 'cena',
        name: 'Cena',
        type: 'personal',
        color: 'badge-orange',
        scheduleNoWork: { start: '21:00', end: '22:00', hours: '1h' },
        scheduleWithWork: { start: '22:00', end: '23:00', hours: '1h' },
        priority: 5,
        fixed: true
    },
    familia: {
        id: 'familia',
        name: 'Pareja/Familia',
        type: 'personal',
        color: 'badge-purple',
        scheduleNoWork: { start: '22:00', end: '23:00', hours: '1h' },
        scheduleWithWork: { start: '21:00', end: '22:00', hours: '1h' },
        priority: 5,
        fixed: true
    },
    nocturna: {
        id: 'nocturna',
        name: 'Rutina Nocturna',
        type: 'personal',
        color: 'badge-blue',
        scheduleNoWork: { start: '23:00', end: '00:00', hours: '1h' },
        scheduleWithWork: { start: '23:00', end: '00:00', hours: '1h' },
        priority: 5,
        fixed: true
    }
};
```

### **STATE** (Estado reactivo de la app)

```javascript
let STATE = {
    currentView: 'setup',           // 'setup' | 'home' | 'daily' | 'weekly' | 'deliveries' | 'settings'
    setupStep: 1,                   // 1 (trabajo) | 2 (actividades)
    hasWork: null,                  // true | false | null
    allActivities: {...BASE_ACTIVITIES},
    activeActivities: [],           // Array de IDs: ['prog', 'bd', ...]
    completedTasks: {},             // { 'YYYY-MM-DD': { 'prog': true, ... } }
    deliveries: [],                 // [{ id, subject, date, description, created }]
    currentDate: new Date(),
    notifiedActivities: new Set()   // Set de 'dateKey-activityId-startTime'
};
```

### **Tipos de Actividades:**

| Tipo | Descripción | Prioridad | Ejemplos |
|------|-------------|-----------|----------|
| `fp-critical` | Materias críticas FP | 1 | Programación, BD |
| `fp-important` | Materias importantes FP | 2 | Sistemas, Entornos |
| `fp-light` | Materias leves FP | 3 | Lenguajes, Digi, IPO |
| `learning` | Capacitaciones rotativas | 2 | Inglés, IAs, SEO |
| `personal` | Vida personal | 2-3 | Trabajo, Revisión |
| `break` | Descansos y comidas | 3-5 | Descanso, Almuerzo |
| `fixed: true` | **INTOCABLES** | 5 | Cena, Familia, Rutina |

---

## 🔥 SISTEMA DE REDISTRIBUCIÓN INTELIGENTE

### **¿Cuándo se activa?**
Cuando hay entregas urgentes (≤3 días hasta la fecha de entrega).

### **Multiplicadores de Tiempo:**

```javascript
const daysUntil = getDaysUntilDelivery(delivery.date);
const multiplier = daysUntil === 0 ? 3 : daysUntil === 1 ? 2.5 : 2;

// HOY (0 días):      x3   (+200% tiempo)
// MAÑANA (1 día):    x2.5 (+150% tiempo)
// 2-3 días:          x2   (+100% tiempo)
```

### **Algoritmo Completo (función `applyUrgentBoost()`):**

#### **PASO 0: Restaurar horarios base**
```javascript
// Limpiar boosted de entregas anteriores
Object.keys(BASE_ACTIVITIES).forEach(key => {
    const stateAct = STATE.allActivities[key];
    stateAct[schedule] = { ...BASE_ACTIVITIES[key][schedule] };
    stateAct.boosted = false;
    stateAct.boostedMultiplier = null;
});
```

#### **PASO 1: Calcular tiempo extra necesario**
```javascript
let totalExtraNeeded = 0;
urgentDeliveries.forEach(delivery => {
    const baseHours = parseFloat(BASE_ACTIVITIES[delivery.subject][schedule].hours);
    const multiplier = daysUntil === 0 ? 3 : daysUntil === 1 ? 2.5 : 2;
    const neededHours = baseHours * multiplier;
    totalExtraNeeded += (neededHours - baseHours);
});
```

#### **PASO 2: Reducir fp-critical y fp-important**
```javascript
// Mínimo obligatorio: 1 hora (60 minutos)
const minMinutes = 60;

STATE.activeActivities.forEach(actId => {
    const activity = STATE.allActivities[actId];
    
    // Solo si NO tiene entrega urgente
    if (urgentDeliveries.some(d => d.subject === actId)) return;
    
    // Solo fp-critical y fp-important
    if (activity.type !== 'fp-critical' && activity.type !== 'fp-important') return;
    
    const currentMinutes = parseFloat(activity[schedule].hours) * 60;
    
    if (currentMinutes > minMinutes) {
        const liberated = currentMinutes - minMinutes;
        timeLiberated += liberated;
        activity[schedule].hours = '1h';
    }
});
```

#### **PASO 3: Eliminar Descanso y Revisión**
```javascript
['descanso1', 'revision'].forEach(actId => {
    const activity = STATE.allActivities[actId];
    if (activity && activity[schedule]) {
        const hours = parseFloat(activity[schedule].hours);
        timeLiberated += hours * 60;
        activity[schedule].hours = '0h';
        activity[schedule].start = '';
        activity[schedule].end = '';
    }
});
```

#### **PASO 4: Reducir/Eliminar fp-light y learning (si aún falta tiempo)**
```javascript
const stillNeeded = (totalExtraNeeded * 60) - timeLiberated;

if (stillNeeded > 0) {
    // Rotar eliminación (usar fecha como semilla)
    const dayOfWeek = STATE.currentDate.getDay();
    const startIndex = dayOfWeek % reducibles.length;
    
    for (let i = 0; i < reducibles.length && needMore; i++) {
        const idx = (startIndex + i) % reducibles.length;
        const item = reducibles[idx];
        
        // Intentar reducir a 15min (0.25h) primero
        if (item.hours > 0.25) {
            item.schedule.hours = '0.25h';
        } else {
            // Eliminar completamente
            item.schedule.hours = '0h';
            item.schedule.start = '';
            item.schedule.end = '';
        }
    }
}
```

#### **PASO 5: Aplicar multiplicadores a materias con entrega**
```javascript
urgentDeliveries.forEach(delivery => {
    const activity = STATE.allActivities[delivery.subject];
    const baseHours = parseFloat(BASE_ACTIVITIES[delivery.subject][schedule].hours);
    const multiplier = daysUntil === 0 ? 3 : daysUntil === 1 ? 2.5 : 2;
    const newHours = baseHours * multiplier;
    
    activity[schedule].hours = `${newHours.toFixed(2)}h`;
    
    // ✅ FIX BUG VISUAL: Agregar propiedades para badge
    activity.boosted = true;
    activity.boostedMultiplier = multiplier;
    activity.boostedBaseHours = baseHours;
});
```

#### **PASO 6: Recalcular horarios start/end REALES**
```javascript
// Crear lista de actividades a programar (excepto fixed)
const activitiesToSchedule = STATE.activeActivities
    .filter(actId => {
        const activity = STATE.allActivities[actId];
        if (!activity) return false;
        if (activity.fixed || actId === 'cena' || actId === 'familia' || actId === 'nocturna') {
            // Bloques intocables mantienen horario de BASE_ACTIVITIES
            activity[schedule] = { ...BASE_ACTIVITIES[actId][schedule] };
            return false;
        }
        return activity[schedule].hours !== '0h' && activity[schedule].hours !== '';
    })
    .sort((a, b) => a.priority - b.priority);

// Calcular horarios secuencialmente
let currentMinutes = 8 * 60; // 08:00
const endDay = 21 * 60; // 21:00 límite

activitiesToSchedule.forEach(item => {
    const durationMinutes = Math.round(item.hours * 60);
    
    // Saltar almuerzo
    if (currentMinutes < almuerzoStart && currentMinutes + durationMinutes > almuerzoStart) {
        currentMinutes = almuerzoEnd;
    }
    
    // Si no cabe, no agregar
    if (currentMinutes + durationMinutes > endDay) {
        console.log(`⚠️ ${item.name} no cabe en el horario`);
        return;
    }
    
    // Calcular start/end
    const startH = Math.floor(currentMinutes / 60);
    const startM = currentMinutes % 60;
    const endMinutes = currentMinutes + durationMinutes;
    const endH = Math.floor(endMinutes / 60);
    const endM = endMinutes % 60;
    
    item.schedule.start = `${String(startH).padStart(2, '0')}:${String(startM).padStart(2, '0')}`;
    item.schedule.end = `${String(endH).padStart(2, '0')}:${String(endM).padStart(2, '0')}`;
    
    // Avanzar (con 15min de descanso implícito)
    currentMinutes = endMinutes + 15;
});
```

### **Mínimos Estrictos:**
```
fp-critical/important: 1h (60min)
fp-light: 30min
learning: 15min
Si no llega al mínimo → eliminar del día
```

### **Bloques INTOCABLES (NUNCA se tocan):**
```
Cena: 21:00-22:00 (o según scheduleWithWork)
Familia: 22:00-23:00
Rutina Nocturna: 23:00-00:00
```

---

## ☁️ INTEGRACIÓN FIREBASE

### **Configuración (línea ~170):**

```javascript
const firebaseConfig = {
    apiKey: "AIzaSyDemoKey123456789",
    authDomain: "fp-dam-gestor.firebaseapp.com",
    projectId: "fp-dam-gestor",
    storageBucket: "fp-dam-gestor.appspot.com",
    messagingSenderId: "123456789",
    appId: "1:123456789:web:abcdef123456"
};

let db = null;
let firebaseInitialized = false;

try {
    if (typeof firebase !== 'undefined') {
        firebase.initializeApp(firebaseConfig);
        db = firebase.firestore();
        firebaseInitialized = true;
        console.log('✅ Firebase inicializado');
    }
} catch (error) {
    console.warn('⚠️ Firebase no disponible, usando solo localStorage:', error);
    firebaseInitialized = false;
}
```

### **Función saveData() - Híbrida:**

```javascript
function saveData() {
    const dataToSave = {
        hasWork: STATE.hasWork,
        allActivities: STATE.allActivities,
        activeActivities: STATE.activeActivities,
        completedTasks: STATE.completedTasks,
        deliveries: STATE.deliveries,
        lastSaved: new Date().toISOString(),
        version: '4.0-firebase'
    };
    
    // 1. Guardar en localStorage (backup local)
    localStorage.setItem('fpDamApp', JSON.stringify(dataToSave));
    
    // 2. Guardar en Firebase (sincronización)
    if (firebaseInitialized && db) {
        const userId = getOrCreateUserId();
        db.collection('users').doc(userId).set(dataToSave, { merge: true })
            .then(() => console.log('☁️ Sincronizado con Firebase'))
            .catch((error) => console.warn('⚠️ Error Firebase:', error));
    }
    
    showSaveIndicator();
    return true;
}
```

### **Función loadData() - Prioridad Firebase:**

```javascript
async function loadData() {
    // 1. Intentar cargar desde Firebase primero
    if (firebaseInitialized && db) {
        const userId = getOrCreateUserId();
        try {
            const doc = await db.collection('users').doc(userId).get();
            if (doc.exists) {
                const data = doc.data();
                // Cargar STATE desde Firebase
                STATE.hasWork = data.hasWork;
                STATE.allActivities = data.allActivities || {...BASE_ACTIVITIES};
                STATE.activeActivities = data.activeActivities || [];
                STATE.completedTasks = data.completedTasks || {};
                STATE.deliveries = (data.deliveries || []).map(d => ({
                    ...d,
                    date: new Date(d.date),
                    created: new Date(d.created || Date.now())
                }));
                
                // Guardar en localStorage como backup
                localStorage.setItem('fpDamApp', JSON.stringify(data));
                
                console.log('✅ Datos cargados desde Firebase');
                return true;
            }
        } catch (error) {
            console.warn('⚠️ Error Firebase, usando localStorage');
        }
    }
    
    // 2. Fallback a localStorage
    const saved = localStorage.getItem('fpDamApp');
    if (saved) {
        const data = JSON.parse(saved);
        // Cargar STATE...
        console.log('✅ Datos cargados desde localStorage');
        return true;
    }
    
    return false;
}
```

### **Función getOrCreateUserId():**

```javascript
function getOrCreateUserId() {
    let userId = localStorage.getItem('fpDamUserId');
    if (!userId) {
        userId = 'user_' + Date.now() + '_' + Math.random().toString(36).substr(2, 9);
        localStorage.setItem('fpDamUserId', userId);
        console.log('🆔 Nuevo ID de usuario creado:', userId);
    }
    return userId;
}
```

### **Reglas de Firestore (Firebase Console):**

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read, write: if true;
    }
  }
}
```

> **⚠️ IMPORTANTE:** Para producción, implementar autenticación real (Firebase Auth).

---

## 🐛 BUG VISUAL DEL BOOSTER - SOLUCIÓN

### **Problema:**
Cuando hay entrega urgente, el badge muestra "16h" o "23h" en vez de "2h → 4h 🔥"

### **Causa:**
La función `applyUrgentBoost()` aplicaba el multiplicador pero **NO establecía las propiedades `boosted`, `boostedMultiplier`, `boostedBaseHours`** en las actividades.

### **Solución:**

**1. Agregar propiedades al aplicar multiplicador:**
```javascript
// En applyUrgentBoost(), línea ~695
urgentDeliveries.forEach(delivery => {
    const activity = STATE.allActivities[delivery.subject];
    const baseHours = parseFloat(BASE_ACTIVITIES[delivery.subject][schedule].hours);
    const multiplier = daysUntil === 0 ? 3 : daysUntil === 1 ? 2.5 : 2;
    const newHours = baseHours * multiplier;
    
    activity[schedule].hours = `${newHours.toFixed(2)}h`;
    
    // ✅ AGREGAR ESTAS LÍNEAS:
    activity.boosted = true;
    activity.boostedMultiplier = multiplier;
    activity.boostedBaseHours = baseHours;
});
```

**2. Limpiar propiedades al restaurar horarios:**
```javascript
// En applyUrgentBoost(), línea ~540
Object.keys(BASE_ACTIVITIES).forEach(key => {
    const stateAct = STATE.allActivities[key];
    stateAct[schedule] = { ...BASE_ACTIVITIES[key][schedule] };
    
    // ✅ AGREGAR ESTAS LÍNEAS:
    stateAct.boosted = false;
    stateAct.boostedMultiplier = null;
});
```

**3. Cambiar renderizado del badge:**
```javascript
// En renderDaily(), línea ~1092
// ANTES:
${activity.boosted ? `<span class="badge badge-orange">🔥 ${activity.boostedMultiplier}x</span>` : ''}

// DESPUÉS:
${activity.boosted ? `<span class="badge badge-orange">${activity.boostedBaseHours}h → ${parseFloat(sched.hours).toFixed(1)}h 🔥</span>` : ''}
```

### **Resultado:**
✅ Badge ahora muestra: `"2h → 4h 🔥"` en vez de solo `"🔥 2x"`

---

## 🔔 SISTEMA DE NOTIFICACIONES

### **Implementación:**

```javascript
function initAlarms() {
    // Pedir permisos
    if ('Notification' in window && Notification.permission === 'default') {
        setTimeout(() => {
            Notification.requestPermission().then(permission => {
                if (permission === 'granted') {
                    console.log('✅ Permisos de notificación concedidos');
                }
            });
        }, 2000);
    }
    
    // Verificar cada minuto
    setInterval(checkAlarms, 60000);
}

function checkAlarms() {
    const now = new Date();
    const currentTimeStr = `${String(now.getHours()).padStart(2, '0')}:${String(now.getMinutes()).padStart(2, '0')}`;
    const schedule = STATE.hasWork ? 'scheduleWithWork' : 'scheduleNoWork';
    
    STATE.activeActivities.forEach(activityId => {
        const activity = STATE.allActivities[activityId];
        const sched = activity[schedule];
        
        if (sched.start === currentTimeStr) {
            const notificationKey = `${getDateKey(now)}-${activityId}-${sched.start}`;
            
            if (!STATE.notifiedActivities.has(notificationKey)) {
                if ('Notification' in window && Notification.permission === 'granted') {
                    new Notification('⏰ FP DAM - Es la hora!', {
                        body: `Ahora: ${activity.name} (${sched.hours})`,
                        icon: '📚',
                        tag: notificationKey
                    });
                }
                
                STATE.notifiedActivities.add(notificationKey);
            }
        }
    });
}
```

---

## 📱 PWA - CONFIGURACIÓN

### **manifest.json:**

```json
{
  "name": "FP DAM Gestor de Estudio",
  "short_name": "FP DAM",
  "description": "Gestor de tiempo y estudio para FP DAM con sincronización automática",
  "start_url": "./",
  "scope": "./",
  "display": "standalone",
  "background_color": "#2563eb",
  "theme_color": "#2563eb",
  "orientation": "portrait",
  "icons": [
    {
      "src": "data:image/svg+xml,...",
      "sizes": "192x192",
      "type": "image/svg+xml",
      "purpose": "any maskable"
    },
    {
      "src": "data:image/svg+xml,...",
      "sizes": "512x512",
      "type": "image/svg+xml",
      "purpose": "any maskable"
    }
  ],
  "categories": ["education", "productivity"]
}
```

### **service-worker.js:**

```javascript
const CACHE_NAME = 'fp-dam-v2';
const urlsToCache = ['./', './index.html', './manifest.json'];

// Instalación
self.addEventListener('install', event => {
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then(cache => cache.addAll(urlsToCache))
  );
  self.skipWaiting();
});

// Activación
self.addEventListener('activate', event => {
  event.waitUntil(
    caches.keys().then(cacheNames => {
      return Promise.all(
        cacheNames.map(cacheName => {
          if (cacheName !== CACHE_NAME) {
            return caches.delete(cacheName);
          }
        })
      );
    })
  );
  self.clients.claim();
});

// Fetch - Network First, falling back to Cache
self.addEventListener('fetch', event => {
  if (event.request.method !== 'GET') return;
  
  event.respondWith(
    fetch(event.request)
      .then(response => {
        if (response && response.status === 200) {
          const responseToCache = response.clone();
          caches.open(CACHE_NAME).then(cache => {
            cache.put(event.request, responseToCache);
          });
        }
        return response;
      })
      .catch(() => caches.match(event.request))
  );
});
```

### **index.html - Meta tags:**

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<meta name="mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="theme-color" content="#2563eb">
<link rel="manifest" href="./manifest.json">
```

### **CSS - Safe Area Insets:**

```css
body {
    padding-top: env(safe-area-inset-top);
    padding-bottom: env(safe-area-inset-bottom);
}
```

---

## ⚠️ ERRORES CRÍTICOS A EVITAR

### **NO HACER:**

1. ❌ **Eliminar actividades de BASE_ACTIVITIES** (solo marcar `hours: '0h'` en redistribución)
2. ❌ **Cambiar estructura de STATE sin migración**
3. ❌ **Mover bloques fixed** (cena, familia, rutina) durante redistribución
4. ❌ **Reducir materias críticas por debajo de 1h**
5. ❌ **Día de estudio terminar después de 21:00**
6. ❌ **Eliminar siempre las mismas capacitaciones** (deben rotar)
7. ❌ **Usar frameworks** (React, Vue, Angular)
8. ❌ **Modificar manifest.json o service-worker.js** sin razón
9. ❌ **Olvidar async/await en loadData()** con Firebase
10. ❌ **No establecer propiedades boosted** al aplicar multiplicador

### **SIEMPRE HACER:**

1. ✅ **Solo modificar index.html** para cambios funcionales
2. ✅ **Probar en Samsung A36 real** antes de considerar terminado
3. ✅ **Verificar que bloques fixed NO se mueven** en redistribución
4. ✅ **Respetar mínimos** (1h críticas, 30min leves, 15min capacitaciones)
5. ✅ **Rotar eliminación de capacitaciones** (usar día de semana como semilla)
6. ✅ **Mantener ventana de estudio 08:00-21:00**
7. ✅ **Usar console.log** para debugging extensivo
8. ✅ **Establecer boosted=true, boostedMultiplier, boostedBaseHours** al aplicar multiplicador
9. ✅ **Limpiar propiedades boosted** al restaurar horarios base
10. ✅ **Hacer saveData/loadData async** si usa Firebase

---

## 📊 FUNCIONES PRINCIPALES

### **Lista de Funciones Críticas:**

```javascript
// DATOS
function saveData()                    // Guarda en localStorage + Firebase
async function loadData()              // Carga desde Firebase o localStorage
function getOrCreateUserId()           // Genera/recupera ID único de usuario
function showSaveIndicator()           // Muestra indicador "Guardado"

// REDISTRIBUCIÓN
function applyUrgentBoost()            // Redistribución inteligente completa
function getDaysUntilDelivery(date)    // Calcula días hasta entrega

// ESTADO
function selectWorkStatus(hasWork)     // Configura modo con/sin trabajo
function finishSetup()                 // Finaliza configuración inicial
function selectAllActivities()         // Selecciona todas las actividades
function toggleActivitySelection(id)   // Toggle actividad en setup
function toggleTask(activityId)        // Toggle tarea completada en daily

// ENTREGAS
function addDelivery(subject, date, desc)  // Agrega entrega urgente
function deleteDelivery(id)                // Elimina entrega

// NAVEGACIÓN
function changeView(view)              // Cambia vista actual
function changeDate(days)              // Navega fechas en daily

// RENDERIZADO
function render()                      // Renderizado principal reactivo
function renderSetup()                 // Vista setup
function renderHome()                  // Vista home
function renderDaily()                 // Vista daily
function renderWeekly()                // Vista weekly
function renderDeliveries()            // Vista deliveries
function renderSettings()              // Vista settings

// ALARMAS
function initAlarms()                  // Inicializa sistema de notificaciones
function checkAlarms()                 // Verifica alarmas cada minuto

// UTILIDADES
function getDateKey(date)              // Convierte Date a 'YYYY-MM-DD'
function formatDate(date)              // Formatea fecha legible
function isTaskCompleted(activityId)   // Verifica si tarea está completada
function getDayProgress()              // Calcula % progreso del día
function updateTime()                  // Actualiza reloj en header

// BACKUP/RESTORE
function downloadBackup()              // Descarga JSON local
function uploadBackup(event)           // Restaura desde JSON
function downloadBackupToDrive()       // Abre Google Drive para guardar
function restoreFromDrive()            // Instrucciones restore desde Drive
```

---

## 🎨 ESTILOS Y DISEÑO

### **Paleta de Colores:**

```css
/* Primarios */
--blue: #2563eb;
--purple: #9333ea;
--gradient: linear-gradient(135deg, #2563eb, #9333ea);

/* Estado */
--red: #ef4444;      /* fp-critical */
--yellow: #eab308;   /* fp-important */
--green: #22c55e;    /* fp-light, success */
--orange: #f97316;   /* break, boosted */

/* Grises */
--gray-50: #f9fafb;
--gray-100: #f3f4f6;
--gray-200: #e5e7eb;
--gray-600: #6b7280;
--gray-900: #1f2937;
```

### **Badges:**

```css
.badge-red { background: #ef4444; }     /* Críticas */
.badge-yellow { background: #eab308; }  /* Importantes */
.badge-green { background: #22c55e; }   /* Leves */
.badge-blue { background: #3b82f6; }    /* Personal */
.badge-purple { background: #a855f7; }  /* Learning */
.badge-orange { background: #f97316; }  /* Boosted */
```

### **Responsive:**

```css
/* Mobile-first */
.container { max-width: 800px; margin: 0 auto; padding: 15px; }

/* Safe area insets para PWA móvil */
body {
    padding-top: env(safe-area-inset-top);
    padding-bottom: env(safe-area-inset-bottom);
}
```

---

## 🚀 DEPLOYMENT

### **GitHub Pages:**

1. Crear repo público en GitHub
2. Subir 3 archivos: `index.html`, `manifest.json`, `service-worker.js`
3. Settings → Pages → Source: main branch
4. URL: `https://usuario.github.io/repo-name`
5. Esperar 2-3 minutos
6. Abrir en Chrome móvil
7. Menú (⋮) → "Agregar a pantalla de inicio"

### **Firebase Setup (Opcional):**

1. Crear proyecto en [Firebase Console](https://console.firebase.google.com/)
2. Activar Firestore Database
3. Copiar config de Firebase
4. Reemplazar en `index.html` línea ~170
5. Configurar reglas de Firestore (ver sección Firebase)
6. Deploy a GitHub Pages

---

## 📝 LOGS Y DEBUGGING

### **Console Logs Importantes:**

```javascript
// Inicio
🚀 FP DAM Gestor - Versión JavaScript Vanilla Completa
💾 Almacenamiento: Firebase + localStorage permanente
✅ Firebase inicializado

// Carga
✅ Datos cargados desde Firebase
✅ Datos cargados desde localStorage
ℹ️ No hay datos guardados

// Redistribución
🔄 Redistribuyendo horarios...
🔥 1 entrega(s) urgente(s) detectada(s)
📚 Base de Datos: 1.5h → 3.75h (x2.5) - necesita +2.25h extra
⏰ Total tiempo extra necesario: 2.25h
⬇️ Programación: 2h → 1h (libera 1h)
❌ Eliminado: Descanso (libera 0.25h)
✅ Total tiempo liberado: 2.25h
✓ Programación: 08:00-09:00 (1h)
✓ Base de Datos: 09:15-13:00 (3.75h)
✅ Redistribución completada

// Guardado
💾 Datos guardados: 12:34:56
☁️ Sincronizado con Firebase

// Alarmas
✅ Permisos de notificación concedidos
⏰ Alarma: Programación (08:00)
```

### **Chrome DevTools:**

```javascript
// Ver estado completo
console.log(STATE);

// Ver actividades con horarios
STATE.activeActivities.forEach(id => {
    const act = STATE.allActivities[id];
    const sched = STATE.hasWork ? act.scheduleWithWork : act.scheduleNoWork;
    console.log(`${act.name}: ${sched.start}-${sched.end} (${sched.hours})`);
});

// Ver entregas urgentes
STATE.deliveries.filter(d => getDaysUntilDelivery(d.date) <= 3);

// Simular entrega urgente
addDelivery('bd', '2026-01-13', 'Práctica SQL');
applyUrgentBoost();
render();
```

---

## 🔐 SEGURIDAD Y PRIVACIDAD

### **localStorage:**
- Persistente, NO se borra al cerrar navegador
- Limitado a 5-10MB por dominio
- Visible en DevTools (NO datos sensibles)

### **Firebase:**
- Reglas actuales permiten lectura/escritura pública
- **Para producción:** Implementar Firebase Authentication
- Datos cifrados en tránsito (HTTPS)
- Copias de seguridad automáticas

### **Recomendaciones Producción:**

```javascript
// Implementar autenticación
firebase.auth().signInAnonymously()
  .then((userCredential) => {
    const userId = userCredential.user.uid;
    // Usar este userId en vez de generado localmente
  });

// Actualizar reglas Firestore
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

---

## 📞 TROUBLESHOOTING

### **Problema: Firebase no sincroniza**
**Solución:**
1. Verificar console logs: `⚠️ Firebase no disponible`
2. Verificar config en línea ~170
3. Verificar reglas de Firestore
4. Verificar conexión a Internet
5. Fallback automático a localStorage

### **Problema: Badge muestra "16h" en vez de "2h → 4h 🔥"**
**Solución:**
1. Verificar que `activity.boosted = true` se establece en `applyUrgentBoost()` línea ~700
2. Verificar que `activity.boostedBaseHours` existe
3. Verificar renderizado en `renderDaily()` línea ~1092

### **Problema: Bloques intocables se mueven**
**Solución:**
1. Verificar `activity.fixed === true` en BASE_ACTIVITIES
2. Verificar que se excluyen en línea ~673 de `applyUrgentBoost()`
3. Verificar que se restauran desde BASE_ACTIVITIES

### **Problema: PWA no se instala**
**Solución:**
1. Verificar HTTPS (GitHub Pages lo tiene)
2. Verificar manifest.json presente
3. Verificar service-worker.js registrado
4. Chrome DevTools → Application → Manifest

---

## 🎯 CHECKLIST COMPLETO PARA DUPLICAR APP

- [ ] Copiar `index.html` completo (~1550 líneas)
- [ ] Copiar `manifest.json` sin modificar
- [ ] Copiar `service-worker.js` sin modificar
- [ ] Verificar Firebase SDK en `<head>`
- [ ] Configurar Firebase config (línea ~170)
- [ ] Verificar función `applyUrgentBoost()` completa
- [ ] Verificar propiedades `boosted`, `boostedMultiplier`, `boostedBaseHours`
- [ ] Verificar badge renderizado correctamente (línea ~1092)
- [ ] Verificar funciones async: `loadData()`, `window.addEventListener('load')`
- [ ] Verificar `getOrCreateUserId()` existe
- [ ] Verificar bloques `fixed` no se mueven
- [ ] Verificar mínimos: 1h críticas, 30min leves, 15min capacitaciones
- [ ] Verificar ventana de estudio 08:00-21:00
- [ ] Verificar Cena/Familia/Rutina intocables
- [ ] Probar redistribución con entrega urgente
- [ ] Probar sincronización Firebase (si configurado)
- [ ] Probar backup local
- [ ] Probar notificaciones
- [ ] Probar en Samsung A36
- [ ] Verificar PWA instalable

---

## 📚 RECURSOS ADICIONALES

### **Documentación:**
- [Firebase Firestore](https://firebase.google.com/docs/firestore)
- [PWA Documentation](https://web.dev/progressive-web-apps/)
- [Web Notifications API](https://developer.mozilla.org/en-US/docs/Web/API/Notifications_API)
- [Service Workers](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)

### **Testing:**
- Chrome DevTools → Application → Storage
- Chrome DevTools → Application → Service Workers
- Chrome DevTools → Console (F12)
- Mobile: chrome://inspect

---

## ✅ CHANGELOG

### **v4.0 - Firebase Integration (12 Enero 2026)**
- ✅ Integración Firebase Firestore para sincronización multiplataforma
- ✅ Mantener localStorage como backup local
- ✅ Función `loadData()` async con prioridad Firebase
- ✅ Función `saveData()` híbrida (localStorage + Firebase)
- ✅ **FIX:** Bug visual del booster (agregar propiedades `boosted`, `boostedMultiplier`, `boostedBaseHours`)
- ✅ **FIX:** Badge ahora muestra "2h → 4h 🔥" en vez de solo multiplicador
- ✅ Actualizar storage-badge en header: "☁️ Sincronizado en todos tus dispositivos"

### **v3.0 - Redistribución Corregida (11 Enero 2026)**
- ✅ Sistema de redistribución inteligente completo
- ✅ Bloques intocables respetados
- ✅ Mínimos correctos (1h críticas)
- ✅ Recálculo de horarios start/end REALES
- ✅ Rotación de eliminación de capacitaciones

---

**FIN DEL DOCUMENTO TÉCNICO**

Este documento contiene TODO lo necesario para replicar la app exactamente igual en cualquier entorno.

*Última actualización: 12 Enero 2026 - v4.0*
