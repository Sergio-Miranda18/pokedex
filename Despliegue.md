
# 🚀 Despliegue.md — Proceso de Despliegue de PokéDex en Azure Web Apps

**Fecha de despliegue:** 22 de abril de 2026  
**Plataforma:** Azure Web Apps  
**URL final:** https://app-pokedex-web-prod-sm-gqe5egg6abepejhx.centralus-01.azurewebsites.net

----------

## 1. Preparación del Repositorio en GitHub

### 1.1 Creación del repositorio

Se creó un repositorio público en GitHub con el nombre `pokedex`:

-   Plataforma: [https://github.com](https://github.com/)
-   Usuario: Sergio-Miranda18
-   Nombre del repositorio: `pokedex`
-   Visibilidad: Público

### 1.2 Inicialización de Git local

Dentro de la carpeta del proyecto, se ejecutaron los siguientes comandos:

```bash
git init
git add .
git commit -m "primer commit - app pokedex"
git branch -M main
git remote add origin https://Sergio-Miranda18:TOKEN@github.com/Sergio-Miranda18/pokedex.git
git push -u origin main

```

### ⚠️ Error 1: Autenticación fallida

**Mensaje de error:**

```
remote: Invalid username or token. Password authentication is not supported.
fatal: Authentication failed

```

**Causa:** GitHub eliminó el soporte de autenticación por contraseña para operaciones Git.

**Solución:** Se creó un Personal Access Token (PAT) en GitHub:

1.  GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)
2.  Se generó un token con scopes `repo` y `workflow`
3.  Se actualizó la URL remota con el token incluido

----------

## 2. Creación del Recurso en Azure Web Apps

### 2.1 Pasos en el Portal de Azure

1.  Ingresar al [Portal de Azure](https://portal.azure.com/)
2.  Buscar **"Web App"** en la barra de búsqueda
3.  Clic en **"+ Crear"**
4.  Completar la configuración:

Campo

Valor

Suscripción

Azure Subscription 1

Grupo de recursos

sm-pokedex-prod (nuevo)

Nombre

app-pokedex-web-prod-sm

Publicar

Código

Pila en tiempo de ejecución

.NET 9 (STS)

Sistema operativo

Windows

Región

Central US

Plan de App Service

ASP-smpokedexprod-a485

Plan de precios

Free F1 (Shared Infrastructure)

5.  Clic en **"Revisar y Crear"** → **"Crear"**
6.  Esperar 2-3 minutos a que el recurso esté disponible

### 2.2 URL asignada

```
https://app-pokedex-web-prod-sm-gqe5egg6abepejhx.centralus-01.azurewebsites.net

```

----------

## 3. Despliegue del Código Fuente con Kudu

### 3.1 Acceso a Kudu

1.  En el Portal de Azure, ir al recurso `app-pokedex-web-prod-sm`
2.  Menú lateral → **Herramientas de desarrollo** → **Herramientas avanzadas**
3.  Clic en **"Ir →"** para abrir Kudu en una nueva pestaña

### 3.2 Subida de archivos

1.  En Kudu, clic en **"Debug Console"** → **"CMD"**
2.  Navegar a la carpeta de destino:

```bash
cd site/wwwroot

```

3.  Arrastrar y soltar todos los archivos del proyecto directamente en el explorador de archivos de Kudu dentro de `/site/wwwroot/`
4.  Esperar a que la carga finalice y verificar que todos los archivos fueron subidos correctamente

### 3.3 Verificación del despliegue

-   Abrir la URL pública en el navegador
-   Verificar que la aplicación carga correctamente
-   Confirmar que HTTPS está activo

----------

## 4. Configuración de Headers de Seguridad

### 4.1 Creación del archivo web.config

Se creó el archivo `web.config` en la raíz del proyecto para configurar los headers HTTP de seguridad mediante IIS:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="Strict-Transport-Security" value="max-age=31536000; includeSubDomains; preload" />
        <add name="X-Frame-Options" value="DENY" />
        <add name="X-Content-Type-Options" value="nosniff" />
        <add name="Referrer-Policy" value="strict-origin-when-cross-origin" />
        <add name="Permissions-Policy" value="camera=(), microphone=(), geolocation=()" />
        <add name="Content-Security-Policy" value="default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com; font-src 'self' https://fonts.gstatic.com; img-src 'self' data: https:; connect-src 'self' https://beta.pokeapi.co;" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>

```

### 4.2 Subida del web.config a Kudu

El archivo `web.config` fue subido a través de Kudu arrastrándolo a la carpeta `/site/wwwroot/`.

### 4.3 Resultado en securityheaders.com

Se escaneó la URL pública en [https://securityheaders.com](https://securityheaders.com/):

-   **Calificación obtenida: A** 🟢
-   Todos los encabezados fueron detectados y validados correctamente

Header

Estado

Strict-Transport-Security

✅ Presente

X-Frame-Options

✅ Presente

X-Content-Type-Options

✅ Presente

Referrer-Policy

✅ Presente

Permissions-Policy

✅ Presente

Content-Security-Policy

✅ Presente

----------

## 5. Verificaciones Finales

Verificación

Estado

Aplicación carga correctamente desde URL pública

✅

HTTPS activo

✅

Calificación en securityheaders.com distinta de F

✅ (Calificación A)

Archivos subidos correctamente en Kudu

✅

----------

## 6. Comandos Git Resumen

```bash
# Inicializar repositorio
git init
git add .
git commit -m "primer commit - app pokedex"
git branch -M main
git remote add origin https://TOKEN@github.com/Sergio-Miranda18/pokedex.git
git push -u origin main

# Agregar documentación
git add README.md Despliegue.md
git commit -m "agregar documentacion del proyecto"
git push origin main

# Agregar web.config con headers de seguridad
git add web.config
git commit -m "agregar web.config con headers de seguridad"
git push origin main

```

----------

_Documentación generada como parte del proceso de despliegue — Pueblo Paleta Inc. / PumasLab — 2026_
