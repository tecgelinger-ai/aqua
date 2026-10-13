# AquaRadar Mobile para Android

Aplicación Android nativa (Kotlin + Jetpack Compose) para consultar el ESP32 AquaRadar por WiFi directa, sin Internet.

## Funciones
- Panel en vivo: TDS, pH, caudal L/min, caudal L/h y volumen.
- Consulta del endpoint `GET /datos` cada 2 segundos.
- Gráficos de las últimas 120 lecturas.
- Historial SQLite local (se conserva al cerrar la app; se limita a 2000 lecturas).
- Reinicio del volumen del ESP32 mediante `GET /reset`.
- Configuración de la URL base.
- Banner gráfico de AquaRadar + I.S.F.T. N.º 206 integrado en la app.

## Requisitos
- Android Studio reciente.
- JDK 17.
- SDK Android 35 instalado (Android Studio puede descargarlo).

## Abrir y compilar
1. Extraer el ZIP.
2. En Android Studio elegir **Open** y seleccionar la carpeta `AquaRadarMobile`.
3. Esperar a que Gradle sincronice y descargar las dependencias si lo solicita.
4. Conectar un teléfono Android por USB con depuración USB habilitada o crear un emulador.
5. Ejecutar `app`.
6. En el teléfono real, conectarse a la WiFi `AquaRadar-ESP32` (clave `12345678`). Si Android avisa que la red no tiene Internet, elegir permanecer conectado. Puede ser necesario desactivar temporalmente los datos móviles o la función de cambio automático a datos.
7. Abrir AquaRadar. La URL predeterminada es `http://192.168.4.1`.

## Contrato de API requerido en el ESP32
`GET /datos` debe devolver JSON con los campos:
```json
{
  "tds": 61.7,
  "ph": 7.12,
  "caudal_min": 0.5,
  "caudal_h": 30.0,
  "volumen": 12.54,
  "wifi": "CONECTADO",
  "rssi": -59
}
```
La app también muestra `ip_ap` e `ip_wifi` si están presentes, aunque no son obligatorios. `GET /reset` debe reiniciar el volumen y responder HTTP 200.

## Importante
- El teléfono debe estar conectado a la red WiFi creada por el ESP32 para usar `192.168.4.1`.
- Android puede mostrar “Esta red no tiene Internet”; se debe mantener la conexión a esa red.
- La app registra en el historial los valores recibidos, no datos calculados localmente.
- El código permite tráfico HTTP local sin cifrado porque el ESP32 usa un servidor HTTP. No expongas ese servidor directamente a Internet.
- Esta entrega es el proyecto fuente Android; para generar APK hay que compilarlo con Android Studio.

## Generar el APK automáticamente (sin instalar Android Studio)

Este proyecto incluye un workflow de GitHub Actions en `.github/workflows/android-apk.yml`.

1. Crear una cuenta en GitHub si todavía no tenés una.
2. Crear un repositorio nuevo (por ejemplo `AquaRadarMobile`). Se puede mantener privado.
3. Subir el contenido de esta carpeta al repositorio, incluyendo la carpeta oculta `.github/workflows`.
4. En GitHub, abrir la pestaña **Actions** y seleccionar **Build AquaRadar APK**.
5. Pulsar **Run workflow**. También se ejecuta automáticamente al subir cambios a la rama `main` o `master`.
6. Cuando termine en verde, entrar a esa ejecución, bajar el artefacto **AquaRadar-debug-apk** y extraerlo. Dentro estará `app-debug.apk`.
7. Pasar `app-debug.apk` al Android e instalarlo. Si Android lo pide, permitir temporalmente la instalación desde la aplicación usada para abrir el archivo.

El APK generado es de depuración y no está firmado como versión de publicación. El workflow debe completarse correctamente antes de considerar que el APK está listo. No subir contraseñas privadas ni claves de firma al repositorio.
