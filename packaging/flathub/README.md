# PicoSoft - Word 2026 · Flatpak

Instrucciones de instalación del paquete **Flatpak** de PicoSoft - Word 2026, y guía para publicarlo en **Flathub**.

---

## 📦 Instalación del Flatpak (método directo)

El archivo `.flatpak` es un bundle autocontenido. Solo necesitas tener **Flatpak** instalado.

### 1. Instalar Flatpak (si no lo tienes)

**Debian / Ubuntu / Linux Mint:**
```bash
sudo apt install flatpak
```

**Fedora:**
```bash
sudo dnf install flatpak
```

**Arch Linux / Manjaro:**
```bash
sudo pacman -S flatpak
```

> Después de instalar, cierra sesión y vuelve a entrar para que el menú de aplicaciones se actualice.

### 2. Descargar e instalar el bundle

Descarga [`io.github.agdala10.PicoSoftWord-1.0.0-x86_64.flatpak`](../release/io.github.agdala10.PicoSoftWord-1.0.0-x86_64.flatpak) y ejecuta:

```bash
# Instalar el bundle (usuario actual, sin permisos root)
flatpak install --user io.github.agdala10.PicoSoftWord-1.0.0-x86_64.flatpak
```

### 3. Ejecutar la aplicación

```bash
flatpak run io.github.agdala10.PicoSoftWord
```

O desde el menú de aplicaciones: **PicoSoft - Word 2026**.

> **Nota:** la primera vez se descargará el runtime base `org.freedesktop.Platform` (~1 GB) si no lo tienes. Flatpak lo hace automáticamente.

### Desinstalar

```bash
flatpak uninstall --user io.github.agdala10.PicoSoftWord
```

---

## 🛠️ Compilar el Flatpak localmente desde el bundle

Si prefieres construirlo tú mismo a partir de los fuentes del repo:

```bash
# Instalar flatpak-builder (Debian/Ubuntu)
sudo apt install flatpak-builder

# Añadir el runtime y SDK de freedesktop 24.08
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
flatpak install flathub org.freedesktop.Platform//24.08 org.freedesktop.Sdk//24.08

# Compilar desde el manifiesto
flatpak-builder --force-clean --user --install build-dir \
  packaging/flathub/io.github.agdala10.PicoSoftWord.yml

# Ejecutar
flatpak run io.github.agdala10.PicoSoftWord
```

---

## 🌐 Publicar en Flathub

**Flathub** es el repositorio oficial de Flatpak. No se puede "subir" directamente: se envía una **solicitud (Pull Request)** al repositorio [`flathub/flathub`](https://github.com/flathub/flathub) y el equipo de Flathub compila y publica el paquete tras una revisión.

### Requisitos previos

1. **App ID en DNS inverso** bajo un dominio que controles. El manifiesto usa `io.github.agdala10.PicoSoftWord`, basado en tu usuario de GitHub. Debes demostrar el control del dominio (en este caso, que eres el dueño de `github.com/agdala10`).
2. **El código fuente debe ser público** en GitHub, con licencia abierta.
3. **Compilación desde fuente**: Flathub prohíbe binarios precompilados; el manifiesto `packaging/flathub/io.github.agdala10.PicoSoftWord.yml` construye el proyecto con `cargo` desde el repo.

### Pasos para enviarlo

1. **Forkea** el repositorio [`flathub/flathub`](https://github.com/flathub/flathub).

2. **Crea una rama nueva** con el nombre de tu App ID:
   ```bash
   git clone https://github.com/TU_USUARIO/flathub.git
   cd flathub
   git checkout -b io.github.agdala10.PicoSoftWord
   ```

3. **Copia el manifiesto** a la raíz del fork con el nombre `io.github.agdala10.PicoSoftWord.yml`:
   ```bash
   cp packaging/flathub/io.github.agdala10.PicoSoftWord.yml /ruta/a/flathub/io.github.agdala10.PicoSoftWord.yml
   ```

4. **Ajusta el manifiesto** si es necesario. Puntos importantes:
   - `sources` debe apuntar a tu repo público de GitHub.
   - Verifica que `runtime-version` (`24.08`) y los `finish-args` cumplen las [reglas de Flathub](https://docs.flathub.org/docs/for-app-authors/requirements).
   - El `metainfo.xml` debe pasar la validación: `appstream-util validate io.github.agdala10.PicoSoftWord.metainfo.xml`.

5. **Valida el manifiesto** en tu máquina con las herramientas de Flathub:
   ```bash
   # Lint del manifiesto
   flatpak run --command=flatpak-builder-lint org.flatpak.Builder manifest io.github.agdala10.PicoSoftWord.yml
   # Lint del appstream
   flatpak run --command=flatpak-builder-lint org.flatpak.Builder appstream io.github.agdala10.PicoSoftWord.metainfo.xml
   ```

6. **Abre un Pull Request** contra `flathub/flathub` desde tu rama.

7. **Espera la revisión**. El bot de Flathub (`flathubbot`) compila automáticamente el paquete y publica los resultados. Los mantenedores revisan permisos, metadatos y cumplimiento de normas. Pueden pedir cambios en los `finish-args` o en el `metainfo`.

8. Una vez aprobado y fusionado, Flathub compila y publica la app. Aparecerá en [flathub.org](https://flathub.org/) y será instalable con:
   ```bash
   flatpak install flathub io.github.agdala10.PicoSoftWord
   ```

### Recomendaciones para que la revisión sea rápida

- **Metadatos completos**: el `metainfo.xml` ya incluye descripción, capturas con `<caption>`, URLs, `content_rating` y `releases`.
- **Capturas reales**: usa las imágenes de `docs/images/` con resolución adecuada (mínimo 640×480, recomendado 1600×900).
- **Permisos mínimos**: el manifiesto solo pide lo necesario (X11/Wayland, GPU, audio, home). Evita `--filesystem=host` o `--talk-name` innecesarios, que la revisión suele rechazar.
- **App ID limpio**: `io.github.agdala10.PicoSoftWord` sigue el formato esperado por Flathub.

### Referencias oficiales

- [Documentación de Flathub para autores](https://docs.flathub.org/docs/for-app-authors/)
- [Requisitos de aplicación](https://docs.flathub.org/docs/for-app-authors/requirements)
- [Cómo enviar una app](https://docs.flathub.org/docs/for-app-authors/submission)
