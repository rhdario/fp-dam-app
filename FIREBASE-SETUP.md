# 🔥 Configuración Firebase - FP DAM Gestor

## 📋 Guía Paso a Paso

### **1️⃣ Crear Proyecto Firebase**

1. Ve a [Firebase Console](https://console.firebase.google.com/)
2. Click en **"Agregar proyecto"** o **"Add project"**
3. Nombre del proyecto: `fp-dam-gestor` (o el que prefieras)
4. Acepta términos y click **"Continuar"**
5. Desactiva Google Analytics (no es necesario)
6. Click **"Crear proyecto"**
7. Espera que se cree (~30 segundos)
8. Click **"Continuar"**

---

### **2️⃣ Activar Firestore Database**

1. En el menú lateral, busca **"Firestore Database"**
2. Click en **"Crear base de datos"** o **"Create database"**
3. Selecciona **"Modo de producción"** (Production mode)
4. Click **"Siguiente"**
5. Ubicación: Selecciona la más cercana (ej: `europe-west3` para España)
6. Click **"Habilitar"**
7. Espera que se cree la base de datos (~1 minuto)

---

### **3️⃣ Configurar Reglas de Seguridad**

1. En Firestore, ve a la pestaña **"Reglas"** (Rules)
2. Reemplaza el contenido con:

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

3. Click **"Publicar"** (Publish)

> ⚠️ **IMPORTANTE:** Estas reglas permiten acceso público a cualquier documento en la colección `users`. 
> Para producción real, implementa autenticación con Firebase Auth.

---

### **4️⃣ Obtener Configuración de Firebase**

1. En el menú lateral, click en el **ícono de engranaje ⚙️** junto a "Descripción general del proyecto"
2. Selecciona **"Configuración del proyecto"** (Project settings)
3. Scroll down hasta **"Tus apps"** (Your apps)
4. Click en el ícono **</> Web**
5. Nombre de la app: `FP DAM Gestor Web`
6. **NO** marcar "También configura Firebase Hosting"
7. Click **"Registrar app"**
8. Verás un código JavaScript similar a:

```javascript
const firebaseConfig = {
  apiKey: "AIzaSyC1234567890abcdefghijklmnopqrstuvw",
  authDomain: "fp-dam-gestor-12345.firebaseapp.com",
  projectId: "fp-dam-gestor-12345",
  storageBucket: "fp-dam-gestor-12345.appspot.com",
  messagingSenderId: "123456789012",
  appId: "1:123456789012:web:a1b2c3d4e5f6g7h8i9j0"
};
```

9. **COPIA** estos valores (los necesitarás en el siguiente paso)
10. Click **"Continuar a la consola"**

---

### **5️⃣ Actualizar index.html**

1. Abre tu archivo `index.html`
2. Busca la línea **~170** (sección Firebase Configuration)
3. Reemplaza los valores de ejemplo con tu configuración:

**ANTES:**
```javascript
const firebaseConfig = {
    apiKey: "AIzaSyDemoKey123456789",
    authDomain: "fp-dam-gestor.firebaseapp.com",
    projectId: "fp-dam-gestor",
    storageBucket: "fp-dam-gestor.appspot.com",
    messagingSenderId: "123456789",
    appId: "1:123456789:web:abcdef123456"
};
```

**DESPUÉS:**
```javascript
const firebaseConfig = {
    apiKey: "TU_API_KEY_REAL",
    authDomain: "tu-proyecto-real.firebaseapp.com",
    projectId: "tu-proyecto-id-real",
    storageBucket: "tu-proyecto-real.appspot.com",
    messagingSenderId: "TU_SENDER_ID_REAL",
    appId: "TU_APP_ID_REAL"
};
```

4. Guarda el archivo
5. Sube a GitHub Pages (commit + push)

---

### **6️⃣ Verificar Funcionamiento**

1. Abre la app en tu navegador
2. Abre la consola de Chrome (F12 → Console)
3. Busca el mensaje:
   - ✅ `✅ Firebase inicializado`
   - ✅ `☁️ Sincronizado con Firebase`

4. Verifica en Firebase Console:
   - Ve a **Firestore Database**
   - Deberías ver una colección `users`
   - Dentro, un documento con tu `userId`
   - Dentro del documento, tus datos: `hasWork`, `allActivities`, etc.

---

### **7️⃣ Prueba de Sincronización**

1. **En tu PC:**
   - Abre la app
   - Cambia algún dato (ej: marca tarea completada)
   - Verifica en consola: `☁️ Sincronizado con Firebase`

2. **En tu móvil:**
   - Abre la misma URL
   - Espera ~2 segundos
   - Deberías ver los mismos datos del PC
   - Los cambios se sincronizan automáticamente

---

## 🔒 Seguridad Mejorada (Opcional)

### **Para Producción Real:**

Si quieres que cada usuario tenga sus propios datos privados, necesitas implementar autenticación:

#### **1. Activar Authentication:**

1. En Firebase Console, ve a **Authentication**
2. Click **"Comenzar"**
3. Activa **"Correo electrónico/contraseña"** o **"Google"**
4. Configura según prefieras

#### **2. Actualizar Reglas de Firestore:**

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      // Solo el usuario autenticado puede leer/escribir sus propios datos
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

#### **3. Modificar index.html:**

Busca la función `getOrCreateUserId()` y reemplázala con:

```javascript
async function getOrCreateUserId() {
    try {
        // Autenticación anónima
        const userCredential = await firebase.auth().signInAnonymously();
        const userId = userCredential.user.uid;
        console.log('🔐 Usuario autenticado:', userId);
        return userId;
    } catch (error) {
        console.error('❌ Error autenticación:', error);
        // Fallback a ID local
        let userId = localStorage.getItem('fpDamUserId');
        if (!userId) {
            userId = 'user_' + Date.now() + '_' + Math.random().toString(36).substr(2, 9);
            localStorage.setItem('fpDamUserId', userId);
        }
        return userId;
    }
}
```

---

## ❓ Preguntas Frecuentes

### **¿Es necesario Firebase?**
No. La app funciona perfectamente solo con localStorage. Firebase es opcional para sincronización multiplataforma.

### **¿Cuánto cuesta Firebase?**
El plan gratuito (Spark) incluye:
- 1 GB de almacenamiento
- 50,000 lecturas/día
- 20,000 escrituras/día

Para uso personal de una app como esta, **es más que suficiente**.

### **¿Qué pasa si me quedo sin internet?**
La app sigue funcionando con localStorage. Cuando vuelvas a tener internet, se sincronizará automáticamente.

### **¿Puedo compartir mis datos con otros?**
No con la configuración actual. Cada usuario tiene sus propios datos separados por `userId`.

### **¿Cómo elimino mis datos de Firebase?**
1. Ve a Firestore Database
2. Busca tu documento en `users/{tuUserId}`
3. Click derecho → Eliminar documento

---

## 🛟 Troubleshooting

### **Error: "Firebase no inicializado"**

**Causa:** Firebase SDK no cargó correctamente

**Solución:**
1. Verifica conexión a Internet
2. Verifica que las URLs de Firebase SDK sean correctas en `<head>`
3. Espera 3-5 segundos después de cargar la página

### **Error: "Permission denied"**

**Causa:** Reglas de Firestore muy restrictivas

**Solución:**
1. Ve a Firestore → Reglas
2. Verifica que tengas `allow read, write: if true;`
3. Publica las reglas

### **Los datos no se sincronizan**

**Causa:** Usuario diferente en cada dispositivo

**Solución:**
1. Verifica que ambos dispositivos usen la misma URL
2. Verifica en consola que ambos tienen el mismo `userId`
3. Si son diferentes, necesitas implementar autenticación (ver arriba)

---

## 📞 Soporte

Si tienes problemas, revisa:
1. Console de Chrome (F12) para ver errores
2. Firebase Console → Firestore → Ver datos guardados
3. Verifica que la configuración de Firebase sea correcta

---

**¡Listo! Tu app ahora se sincroniza automáticamente en todos tus dispositivos.** 🎉
