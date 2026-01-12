# 📚 FP DAM Gestor - PWA de Gestión de Estudio

Progressive Web App (PWA) para gestionar el horario de estudio de **Formación Profesional DAM** (Desarrollo de Aplicaciones Multiplataforma) con **sistema inteligente de redistribución automática** cuando hay entregas urgentes.

![Estado](https://img.shields.io/badge/estado-activo-success)
![Versión](https://img.shields.io/badge/versión-4.1-blue)
![PWA](https://img.shields.io/badge/PWA-ready-purple)

---

## ✨ Características Principales

### 🎯 **Gestión Inteligente de Horarios**
- **7 materias FP** + **3 capacitaciones** + vida personal
- Dos modos: **Con trabajo** (8h) o **Sin trabajo**
- Redistribución automática con entregas urgentes (≤3 días)
- Multiplicadores de tiempo: **x3 (HOY)**, **x2.5 (MAÑANA)**, **x2 (2-3 días)**
- Sin huecos innecesarios entre materias

### ☁️ **Sincronización Multiplataforma**
- Sincronización automática con **Firebase**
- Backup local con **localStorage** (fallback)
- Accede desde móvil, tablet o PC sin cargar backups
- Tus datos siempre disponibles en todos tus dispositivos

### 📱 **PWA Instalable**
- Funciona **offline** (Service Worker)
- Instálala como app nativa en móvil/PC
- Notificaciones push al inicio de cada actividad
- Optimizada para **Samsung A36** y **Chrome Canary**

### 📊 **Seguimiento y Estadísticas**
- Vista diaria con progreso de tareas
- Vista semanal con resumen
- Gestión de entregas con alertas automáticas
- Backup/Restore local + Google Drive

---

## 🚀 Instalación y Uso

### **1️⃣ Deployment en GitHub Pages**

1. **Fork o clona** este repositorio
2. Sube los archivos: `index.html`, `manifest.json`, `service-worker.js`
3. Ve a **Settings → Pages**
4. Selecciona **Source: main branch**
5. Espera 2-3 minutos
6. Accede a: `https://tu-usuario.github.io/nombre-repo`

### **2️⃣ Instalación en Móvil**

1. Abre la URL en **Chrome** o **Chrome Canary**
2. Toca el menú (⋮) → **"Agregar a pantalla de inicio"**
3. ¡Listo! La app funcionará como nativa

### **3️⃣ Configuración Inicial**

1. Selecciona si **tienes trabajo** o **no**
2. Marca las **actividades** que quieres incluir
3. La app calculará automáticamente tu horario óptimo

---

## 🔧 Configuración de Firebase (Opcional)

Para habilitar la sincronización multiplataforma:

1. Ve a [Firebase Console](https://console.firebase.google.com/)
2. Crea un nuevo proyecto
3. Activa **Firestore Database**
4. Copia tu configuración de Firebase
5. Reemplaza en `index.html` (línea ~170):

```javascript
const firebaseConfig = {
    apiKey: "TU_API_KEY",
    authDomain: "tu-proyecto.firebaseapp.com",
    projectId: "tu-proyecto-id",
    storageBucket: "tu-proyecto.appspot.com",
    messagingSenderId: "123456789",
    appId: "1:123456789:web:abcdef"
};
```

### **Reglas de Firestore:**

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

> **⚠️ IMPORTANTE:** Para producción, implementa Firebase Authentication.

Ver guía completa en `FIREBASE-SETUP.md`.

---

## 📋 Funcionalidades

| Funcionalidad | Descripción |
|--------------|-------------|
| **Redistribución Inteligente** | Ajusta horarios automáticamente con entregas urgentes |
| **Sin Huecos** | Horarios secuenciales sin descansos innecesarios |
| **Alarmas Automáticas** | Notificaciones push al inicio de cada actividad |
| **Progreso Diario** | % de tareas completadas del día |
| **Domingo OFF** | Sin actividades programadas |
| **Backup Local** | Descarga JSON de tus datos |
| **Backup a Drive** | Integración con Google Drive |
| **Modo Con/Sin Trabajo** | Cambia horarios según disponibilidad |
| **Sincronización Cloud** | Firebase para múltiples dispositivos |

---

## 🎓 Estructura de Actividades

### **Materias FP (7)**
- **Críticas (Mínimo 1h):** Programación, Base de Datos
- **Importantes (Mínimo 45min):** Sistemas Informáticos, Entornos Desarrollo
- **Leves (Mínimo 45min):** Lenguajes Marcas, Digitalización, Itinerario IPO

### **Capacitaciones (3) - Mínimo 15min**
- Inglés (L, M, J)
- Estudiar IAs (L, X, V)
- Estudiar SEO (M, J, V)

### **Vida Personal**
- Búsqueda Trabajo, Revisión del Día
- Almuerzo (movible 14:00-15:15 ideal)
- **Intocables:** Cena (21:00), Familia (22:00), Rutina Nocturna (23:00)

---

## 🐛 Sistema de Redistribución

### **Multiplicadores según urgencia:**
```
HOY (0 días):      x3   (+200% tiempo)
MAÑANA (1 día):    x2.5 (+150% tiempo)
2-3 días:          x2   (+100% tiempo)
```

### **Orden de reducción:**
1. **Reducir materias críticas/importantes** a mínimos (1h / 45min)
2. **Eliminar Revisión del Día**
3. **Reducir/Eliminar materias leves y capacitaciones** a mínimos (45min / 15min), rotando eliminación
4. **Dividir materias muy largas** (>3h) en 2 bloques (mañana + tarde)
5. **Recalcular horarios 08:00-21:00** sin huecos

### **Bloques intocables:**
- Cena: 21:00-22:00
- Familia: 22:00-23:00
- Rutina Nocturna: 23:00-00:00

**NUNCA se mueven ni reducen.**

---

## 🛠️ Stack Técnico

- **JavaScript Vanilla** (ES6+) - SIN frameworks
- **HTML5 + CSS3** inline
- **Firebase Firestore** para sincronización
- **localStorage** para backup local
- **PWA** con Service Worker
- **Archivo único:** `index.html` (~1620 líneas)

---

## 📱 Compatibilidad

| Dispositivo | Estado |
|------------|--------|
| **Samsung A36** | ✅ Optimizado |
| Android | ✅ Compatible |
| iPhone/iPad | ✅ Compatible |
| PC/Mac | ✅ Compatible |
| Chrome Canary | ✅ Recomendado |
| Chrome/Firefox | ✅ Soportado |

---

## 🔒 Privacidad y Datos

- **Datos guardados localmente** en tu navegador (localStorage)
- **Sincronización opcional** con Firebase (requiere configuración)
- **Sin tracking**, sin analytics, sin cookies de terceros
- **Open source** - puedes revisar todo el código

---

## 📄 Licencia

MIT License - Libre para uso personal y educativo.

---

## 🤝 Contribuciones

Este es un proyecto personal educativo, pero siéntete libre de:
- 🐛 Reportar bugs
- 💡 Sugerir mejoras
- 🔧 Enviar pull requests

---

## 📞 Soporte

¿Problemas con la instalación? Revisa:
1. Que los 3 archivos estén en el mismo directorio
2. Que GitHub Pages esté activado correctamente
3. Que tu navegador soporte PWAs
4. Consola del navegador (F12) para logs detallados

---

## 🎯 Changelog v4.1

### **Mejoras:**
- ✅ Redistribución optimizada con mínimos correctos (1h, 45min, 45min, 15min)
- ✅ Horarios secuenciales sin huecos innecesarios
- ✅ División automática de materias muy largas (>3h) en 2 bloques
- ✅ Rotación inteligente de eliminación de materias leves/capacitaciones
- ✅ Badge visual mejorado del booster (muestra "2h → 4h 🔥")
- ✅ Integración Firebase para sincronización multiplataforma
- ✅ Almuerzo movible con horario ideal 14:00-15:15
- ✅ Eliminación de descansos innecesarios (usuario usa Pomodoro)

---

**Hecho con ❤️ para estudiantes de FP DAM**

*Versión 4.1 - Enero 2026*
