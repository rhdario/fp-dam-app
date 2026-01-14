# 📚 FP DAM Gestor v4.0 - Gestión Inteligente de Horarios

> Progressive Web App para gestionar tu horario de estudio de Formación Profesional DAM con redistribución automática de horarios y sincronización multiplataforma.

![Version](https://img.shields.io/badge/version-4.0-blue.svg)
![License](https://img.shields.io/badge/license-MIT-green.svg)
![Tech](https://img.shields.io/badge/tech-Vanilla_JS-yellow.svg)
![Firebase](https://img.shields.io/badge/sync-Firebase-orange.svg)

## ✨ Características Principales

### 🎯 Redistribución Inteligente
- **Detecta entregas urgentes** (≤3 días) automáticamente
- **Multiplica tiempo** según urgencia: x3 (hoy), x2.5 (mañana), x2 (2-3 días)
- **Reorganiza horarios** liberando tiempo de materias menos prioritarias
- **Respeta bloques intocables** (cena 21:00, familia 22:00, rutina 23:00)

### 📱 Sincronización Multiplataforma con Firebase
- ☁️ **Sincronización automática** entre móvil, tablet y PC
- 🔄 **Actualización en tiempo real** - cambios aparecen en todos los dispositivos
- 💾 **Backup automático** en la nube + backup manual local
- 📡 **Funciona offline** - sincroniza cuando hay conexión

### ⏰ Sistema de Alarmas
- 🔔 **Notificaciones push** al inicio de cada actividad
- 📊 **Progreso diario** con barra visual
- ✅ **Checklist interactivo** para marcar tareas completadas

### 📅 Vistas Completas
- **Vista Diaria**: Agenda ordenada cronológicamente con indicadores de boost/reducción
- **Vista Semanal**: Progreso de lunes a sábado (domingo OFF)
- **Gestión de Entregas**: Control de fechas límite con alertas automáticas
- **Estadísticas**: Análisis de rendimiento y hábitos de estudio

### 🌙 Dos Modos de Horario
- **Sin trabajo**: Ventana de estudio 08:00-21:00
- **Con trabajo**: Horarios adaptados para trabajadores (jornada 8h)

---

## 🚀 Instalación Rápida

### Opción 1: GitHub Pages (Recomendada)

1. **Fork/Descarga** este repositorio
2. **Sube los archivos** a tu repositorio GitHub
3. **Activa GitHub Pages**:
   - Settings → Pages
   - Source: main branch
   - Save
4. **Espera 2-3 minutos**
5. **Accede** desde cualquier dispositivo: `https://tu-usuario.github.io/tu-repo`

### Opción 2: Uso Local

1. Descarga el ZIP
2. Descomprime
3. Abre `index.html` en tu navegador

---

## ⚙️ Configurar Firebase (Sincronización)

Para habilitar sincronización entre dispositivos, sigue la guía completa en:

📖 **[FIREBASE-SETUP.md](FIREBASE-SETUP.md)** (5 minutos)

**Resumen rápido:**
1. Crea proyecto en [Firebase Console](https://console.firebase.google.com/)
2. Habilita Realtime Database (modo Test)
3. Copia la configuración
4. Reemplaza en `index.html` línea ~166
5. ¡Listo! Sincroniza automáticamente

**Sin Firebase:** La app funciona perfectamente, pero solo en cada dispositivo por separado.

---

## 📖 Uso Diario

### Primera Vez

1. **¿Tienes trabajo?** → Elige tu situación laboral
2. **Selecciona actividades** → Marca las materias que cursas
3. **¡Empezar!** → La app genera tu horario

### Agregar Entregas

1. Ve a **Entregas**
2. Selecciona materia + fecha + descripción
3. Clic **Agregar**
4. **Automático**: Si faltan ≤3 días, los horarios se redistribuyen

### Redistribución Automática

Cuando agregas una entrega urgente (≤3 días):

✅ La materia con entrega **multiplica sus horas** (x2, x2.5 o x3)
✅ Otras materias se **reducen** para liberar tiempo
✅ Se muestran badges:
- 🔥 **Naranja**: Materia boosteada (ej: `2h → 5h`)
- ⬇️ **Azul**: Materia reducida (ej: `1.5h → 1h`)

✅ Los horarios se **recalculan automáticamente** sin huecos

### Domingo OFF

Los domingos no hay actividades programadas - es tu día de descanso.

---

## 🛠️ Tecnologías

- **JavaScript Vanilla** (ES6+) - Sin frameworks
- **Firebase Realtime Database** - Sincronización en tiempo real
- **PWA** (Progressive Web App) - Instalable en móvil
- **Service Worker** - Funciona offline
- **CSS3** - Diseño responsive mobile-first
- **Web Notifications API** - Alarmas automáticas

---

## 📊 Estructura de Actividades

### 7 Materias FP
- 🔴 **Críticas** (prioridad 1): Programación, Base de Datos
- 🟡 **Importantes** (prioridad 2): Sistemas Informáticos, Entornos Desarrollo
- 🟢 **Ligeras** (prioridad 3): Lenguajes Marcas, Digitalización, Itinerario IPO

### 3 Capacitaciones
- 🟣 Inglés, IAs, SEO (rotan por día de la semana)

### Bloques Personales
- Búsqueda Trabajo, Tiempo Libre, Revisión del Día
- Descansos, Almuerzo
- **Intocables** (NO se mueven): Cena, Familia, Rutina Nocturna

---

## 🔧 Solución de Problemas

### Las notificaciones no aparecen
- Otorga permisos al navegador
- Chrome: Settings → Site Settings → Notifications → Permitir

### No sincroniza entre dispositivos
- Verifica que configuraste Firebase correctamente
- Lee [FIREBASE-SETUP.md](FIREBASE-SETUP.md)
- Revisa la consola del navegador (F12) para errores

### Los horarios no se redistribuyen
- La entrega debe tener ≤3 días hasta la fecha límite
- Revisa la consola (F12) para ver logs de redistribución

### Huecos en el horario
- **Solucionado en v4.0** - Ahora los horarios se calculan sin espacios innecesarios

---

## 💾 Backup y Restauración

Aunque Firebase sincroniza automáticamente, puedes hacer backups manuales:

1. **Backup Local**: Settings → Descargar Backup (archivo .json)
2. **Backup a Drive**: Settings → Backup a Google Drive
3. **Restaurar**: Settings → Restaurar desde Backup (selecciona .json)

---

## 📜 Licencia

MIT License - Uso libre para fines personales y educativos

---

## 🤝 Contribuciones

¿Encontraste un bug? ¿Tienes una mejora?
1. Abre un Issue
2. Fork el repositorio
3. Crea una Pull Request

---

## 📧 Soporte

Creado para estudiantes FP DAM en Ciutadella, Balearic Islands, ES

**¿Problemas con la app?**
- Revisa la consola del navegador (F12)
- Lee [FIREBASE-SETUP.md](FIREBASE-SETUP.md)
- Lee [RESUMEN-COMPLETO-PROGRAMADOR.md](RESUMEN-COMPLETO-PROGRAMADOR.md) para detalles técnicos

---

**v4.0** - Firebase Sync + Redistribución sin huecos + Visualización de reducciones

¡Éxito en tus estudios! 🎓📚
