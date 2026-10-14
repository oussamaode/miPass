# VaultKey 🔐

**Gestor de contraseñas 100% offline** que funciona como aplicación web instalable (PWA). Guarda tus contraseñas cifradas en tu propio dispositivo, sin cuentas, sin servidores y sin conexión a internet.

> Demo: https://oussamaode.github.io/miPass/

---

## ✨ Características

- **Cifrado fuerte**: AES-256-GCM con clave derivada de tu contraseña maestra mediante PBKDF2 (SHA-256, 100.000 iteraciones).
- **100% offline**: funciona sin internet una vez instalada. Ningún dato sale de tu dispositivo.
- **Sin registro**: no hay cuentas ni nube. Solo tú y tu contraseña maestra.
- **Dashboard con logos**: vista en rejilla con los logos de más de **130 servicios populares** (Google, GitHub, Netflix, Spotify, Amazon…). El icono se detecta automáticamente al escribir el nombre del servicio.
- **Catálogo de servicios**: añade cuentas frecuentes con un toque desde el catálogo.
- **Generador de contraseñas**: longitud y tipos de caracteres configurables, con indicador de fuerza.
- **Buscador** por servicio, usuario o notas.
- **Importar / Exportar** el cofre completo como archivo `.vault` cifrado.
- **Guardado local** automático (y opción de continuar con el último cofre).
- **Auto-bloqueo**: el cofre se bloquea por inactividad (5 min) y al ocultar la pestaña.
- **Instalable (PWA)**: añádela a la pantalla de inicio en móvil o escritorio.

---

## 🔒 Seguridad

VaultKey está diseñado bajo el principio de **confianza cero**: todo el cifrado y descifrado ocurre en tu navegador mediante la **Web Crypto API** nativa. No hay backend.

### Criptografía

| Elemento | Detalle |
|---|---|
| Cifrado | AES-256-GCM (cifrado autenticado: confidencialidad + integridad) |
| Derivación de clave | PBKDF2 con SHA-256 |
| Iteraciones | 100.000 |
| Salt | 16 bytes aleatorios (único por guardado) |
| IV / nonce | 12 bytes aleatorios |
| Aleatoriedad | `crypto.getRandomValues` / `crypto.randomUUID` |

### Formato del cofre cifrado

Cada vez que se guarda, se genera un `salt` y un `iv` nuevos y el formato binario es:

```
[ salt (16 bytes) ][ iv (12 bytes) ][ ciphertext + auth tag (GCM) ]
```

El **auth tag** de GCM garantiza que si alguien modifica el archivo, el descifrado falla. La contraseña maestra **nunca** se guarda: solo se usa en memoria mientras el cofre está abierto.

### Qué protege y qué no

**Protege:**
- Tus contraseñas **en reposo**: tanto en `localStorage` como en el archivo `.vault` exportado, todo va cifrado.
- La **integridad** del cofre (detección de manipulación).

**No protege contra:**
- Una **contraseña maestra débil**. Como el cifrado es offline, un atacante con el archivo puede intentar fuerza bruta. Usa una contraseña maestra larga y única.
- **Malware, keyloggers** o un dispositivo comprometido.
- **Acceso físico** con la app abierta y desbloqueada (aunque se bloquea automáticamente por inactividad a los 5 min y al ocultar la pestaña).
- Que el navegador **borre** el `localStorage` (por eso existe la exportación `.vault` a archivo).
- Servicios de terceros que cargues (VaultKey no hace peticiones externas).

### Recomendaciones

1. Usa una **contraseña maestra larga** (frase de paso) que no uses en ningún otro sitio.
2. **Exporta** tu `.vault` periódicamente como copia de seguridad.
3. Usa HTTPS o `localhost` (contexto seguro) para que la Web Crypto y la PWA funcionen correctamente.

---

## ⚙️ Cómo funciona

1. **Crear cofre**: defines una contraseña maestra. Se deriva una clave con PBKDF2 + salt aleatorio.
2. **Añadir entradas**: servicio, usuario, contraseña (o generada) y notas. Se asigna automáticamente el logo si se reconoce la marca.
3. **Guardar (auto)**: todo el objeto del cofre se serializa a JSON, se cifra con AES-GCM y se guarda en `localStorage` (como hexadecimal). Salt e IV se regeneran en cada guardado.
4. **Abrir**: al introducir la contraseña maestra se deriva la clave con el mismo salt y se descifra. Si la contraseña es incorrecta, GCM lanza error y se avisa.
5. **Exportar / Importar**: se genera o lee el archivo `.vault` con el mismo formato binario cifrado.
6. **Offline (PWA)**: un *service worker* precachea la app (HTML, JS, iconos) para funcionar sin conexión.

### Arquitectura

Aplicación **sin dependencias** y sin build: HTML + CSS + JavaScript vanilla.

```
miPass/
├── index.html          # Interfaz + lógica de la app (cripto, UI, estado)
├── brands.js           # Datos de 131 logos de marcas (SVG inline)
├── service-worker.js   # Caché offline (PWA)
├── manifest.json       # Manifiesto de la PWA
├── icon-192.png        # Icono PWA
└── icon-512.png        # Icono PWA
```

Todo el procesamiento criptográfico vive en `index.html` (objeto `Crypto` y `Brands`).

---

## 🚀 Uso

1. Abre la app (GitHub Pages o en local).
2. Pulsa **Crear nuevo cofre** y define tu contraseña maestra.
3. Añade contraseñas con el botón **+**, o elige un servicio del **catálogo**.
4. Cambia entre **lista** y **dashboard** con el botón de vista.
5. Exporta tu cofre para tener copia de seguridad.

### Ejecutar en local

Sirve la carpeta con cualquier servidor estático (recomendado para que funcione la PWA y la Web Crypto):

```bash
# Opción 1: Python
python -m http.server 8080

# Opción 2: Node
npx serve .
```

Luego abre http://localhost:8080.

---

## 🎨 Créditos

Los logotipos de marcas provienen de:

- **[Simple Icons](https://simpleicons.org/)** — licencia CC0-1.0.
- **[Bootstrap Icons](https://icons.getbootstrap.com/)** — licencia MIT (para marcas no disponibles en Simple Icons).

Todas las marcas y logotipos son propiedad de sus respectivos dueños y se usan únicamente con fines identificativos.

---

## 📄 Licencia

Este proyecto es **código abierto** bajo la licencia **MIT**. Puedes usarlo, estudiarlo, modificarlo y distribuirlo libremente. Consulta el archivo [LICENSE](LICENSE).

---

## ⚠️ Aviso

VaultKey es un proyecto para uso personal y educativo. Aunque usa criptografía estándar sólida, **revísalo y audítalo** antes de confiar en él para datos críticos. Úsalo bajo tu propia responsabilidad.
