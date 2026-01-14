# 🔥 FIREBASE SETUP - Sincronización Multiplataforma

## 📱 ¿Qué hace Firebase?

Firebase permite que tu app FP DAM Gestor sincronice **automáticamente** entre:
- 📱 Tu móvil (Samsung A36)
- 💻 Tu PC/Laptop
- 📟 Cualquier otro dispositivo

**Sin Firebase:** Solo funciona en cada dispositivo por separado (necesitas backup manual)
**Con Firebase:** Cambios en un dispositivo aparecen instantáneamente en todos

---

## 🚀 CONFIGURACIÓN (5 minutos)

### PASO 1: Crear Proyecto Firebase

1. Ve a [Firebase Console](https://console.firebase.google.com/)
2. Clic en **"Agregar proyecto"** o **"Add project"**
3. Nombre del proyecto: `fp-dam-gestor` (o el que prefieras)
4. **Google Analytics**: Desactívalo (no lo necesitas)
5. Clic **"Crear proyecto"**
6. Espera 30 segundos → Clic **"Continuar"**

### PASO 2: Habilitar Realtime Database

1. En el menú izquierdo → **"Realtime Database"**
2. Clic **"Crear base de datos"** o **"Create database"**
3. **Ubicación**: Elige la más cercana (ej: `europe-west1`)
4. **Reglas de seguridad**: Selecciona **"Modo de prueba"** / **"Test mode"**
   - ⚠️ IMPORTANTE: Esto permite lectura/escritura sin autenticación
   - Es suficiente para uso personal
5. Clic **"Habilitar"**

### PASO 3: Obtener Configuración

1. En el menú superior → Ícono **⚙️ (engranaje)** → **"Configuración del proyecto"**
2. Scroll hasta **"Tus apps"**
3. Clic en el ícono **`</>`** (Web)
4. **Alias de la app**: `fp-dam-web`
5. ❌ **NO marcar** "También configurar Firebase Hosting"
6. Clic **"Registrar app"**
7. Aparecerá tu configuración:

```javascript
const firebaseConfig = {
  apiKey: "AIzaSyXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  authDomain: "fp-dam-gestor.firebaseapp.com",
  databaseURL: "https://fp-dam-gestor-default-rtdb.europe-west1.firebasedatabase.app",
  projectId: "fp-dam-gestor",
  storageBucket: "fp-dam-gestor.firebasestorage.app",
  messagingSenderId: "123456789012",
  appId: "1:123456789012:web:abcdef0123456789abcdef"
};
```

8. **COPIA TODA ESTA CONFIGURACIÓN** (la necesitarás)

### PASO 4: Configurar en la App

1. Abre `index.html` en un editor de texto (Notepad++, VSCode, Sublime, etc.)
2. **Busca** (Ctrl+F): `TU_API_KEY_AQUI`
3. Encontrarás esto (línea ~166):

```javascript
const firebaseConfig = {
    apiKey: "TU_API_KEY_AQUI",
    authDomain: "TU_PROJECT_ID.firebaseapp.com",
    databaseURL: "https://TU_PROJECT_ID-default-rtdb.firebaseio.com",
    projectId: "TU_PROJECT_ID",
    storageBucket: "TU_PROJECT_ID.appspot.com",
    messagingSenderId: "TU_MESSAGING_ID",
    appId: "TU_APP_ID"
};
```

4. **REEMPLAZA** con tu configuración de Firebase (la que copiaste en PASO 3)
5. **GUARDA** el archivo

### PASO 5: Subir a GitHub Pages

1. Ve a tu repositorio de GitHub
2. Sube el `index.html` modificado (reemplaza el anterior)
3. Espera 1-2 minutos que GitHub Pages se actualice
4. Abre la app en tu navegador

---

## ✅ VERIFICAR QUE FUNCIONA

### Prueba 1: Consola del Navegador

1. Abre la app
2. Presiona **F12** (o botón derecho → Inspeccionar)
3. Ve a la pestaña **Console**
4. Deberías ver:

```
✅ Firebase inicializado - sincronización activa
💾 Guardado: XX:XX:XX
```

❌ Si ves:
```
⚠️ Firebase no configurado - usando solo localStorage
```
→ Revisa que reemplazaste correctamente el `firebaseConfig`

### Prueba 2: Sincronización entre Dispositivos

1. **Dispositivo A (PC):**
   - Abre la app
   - Agrega una entrega nueva
   - Marca una tarea como completada

2. **Dispositivo B (Móvil):**
   - Abre la app (misma URL)
   - Refresca la página (pull-to-refresh)
   - **Deberías ver** los cambios del Dispositivo A

3. **Si NO sincroniza:**
   - Verifica que ambos dispositivos usen la MISMA URL
   - Revisa la consola (F12) en ambos para ver errores
   - Confirma que las reglas de Firebase están en "Test mode"

---

## 🔒 SEGURIDAD (Opcional pero Recomendado)

Las reglas en "Modo de prueba" permiten que **cualquiera** lea/escriba tu base de datos.

### Mejorar Seguridad:

1. Ve a Realtime Database → **Reglas**
2. Reemplaza con:

```json
{
  "rules": {
    "fp-dam-user-data": {
      ".read": "auth == null",
      ".write": "auth == null"
    }
  }
}
```

O para mayor seguridad (requiere autenticación):

```json
{
  "rules": {
    "fp-dam-user-data": {
      ".read": "auth != null",
      ".write": "auth != null"
    }
  }
}
```

**Nota:** Si usas autenticación, necesitarás agregar Firebase Authentication (fuera del alcance de esta guía).

---

## 🐛 SOLUCIÓN DE PROBLEMAS

### Error: "Permission denied"

**Causa:** Reglas de Firebase muy restrictivas
**Solución:** Ve a Realtime Database → Reglas → Cámbiala a "Test mode"

```json
{
  "rules": {
    ".read": true,
    ".write": true
  }
}
```

### Error: "Firebase: Firebase App named '[DEFAULT]' already exists"

**Causa:** Firebase se inicializó dos veces
**Solución:** Refresca la página (Ctrl+F5)

### No sincroniza entre dispositivos

1. **Verifica misma URL** en ambos dispositivos
2. **Refresca** (no solo abrir, sino refrescar)
3. **Revisa consola** (F12) para ver si hay errores
4. **Espera 5 segundos** (a veces Firebase tarda un poco)

### Sincroniza pero con retraso

**Normal:** Firebase puede tardar 1-3 segundos en sincronizar
**Solución:** Paciencia, no es instantáneo al 100%

---

## 📊 CÓMO FUNCIONA (Técnico)

```
Usuario hace cambio
    ↓
saveData() guarda en localStorage (inmediato)
    ↓
saveData() guarda en Firebase (asíncrono, 1-2 seg)
    ↓
Firebase notifica a otros dispositivos
    ↓
loadData() en otros dispositivos detecta cambio
    ↓
Se actualiza la interfaz automáticamente
```

**Ventajas:**
- ✅ No bloquea la app (localStorage es backup)
- ✅ Funciona offline (usa localStorage)
- ✅ Sincroniza cuando hay conexión

---

## 💡 CONSEJOS

1. **Backup manual sigue disponible**
   - Settings → Descargar Backup
   - Guarda el .json por si acaso

2. **Usa la misma cuenta de Google**
   - Para que Firebase funcione en todos tus dispositivos

3. **No compartas tu configuración**
   - El `firebaseConfig` es tuyo, no lo compartas en GitHub público
   - Si lo haces, otras personas podrían escribir en tu base de datos

4. **Límites gratuitos de Firebase**
   - 1GB de almacenamiento
   - 10GB de transferencia/mes
   - **Suficiente** para uso personal de esta app

---

## ✨ ¡LISTO!

Tu app ahora sincroniza automáticamente entre todos tus dispositivos.

**¿Problemas?** Abre un issue en el repositorio o revisa la consola del navegador (F12) para ver errores específicos.

---

**Última actualización:** Enero 2026
**Versión de la app:** 4.0
