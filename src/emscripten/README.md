# Simutrans WebAssembly Browser Port

This directory contains the web shell (`index.html`) for running Simutrans directly in modern browsers using Emscripten, WebAssembly, SDL2, and Asyncify.

## Features
- Compiles the C++ SDL2 Simutrans engine to WebAssembly (`simutrans.js` + `simutrans.wasm`) using Emscripten's official `sdl2`, `zlib`, `bzip2`, `libpng`, and `freetype` ports.
- Preloads base Simutrans data and a pakset (such as `pak64`) into `/simutrans` inside the virtual filesystem (`simutrans.data`).
- Mounts `IDBFS` at `/userdir` so settings (`settings.xml`) and savegames (`/userdir/save/*.sve`) persist in browser IndexedDB.
- Supports importing and exporting `.sve` save files from the browser UI.

## Building Locally

1. Activate Emscripten SDK (`emsdk`):
   ```bash
   source /path/to/emsdk/emsdk_env.sh
   ```
2. Prepare a preload directory containing `simutrans/` base assets and at least one pakset (e.g., `pak/`):
   ```bash
   cp -a simutrans preload-simutrans
   cp -a /path/to/pak64/simutrans/pak preload-simutrans/pak
   ```
3. Configure and build with `emcmake`:
   ```bash
   emcmake cmake -G Ninja -B build-wasm \
     -DCMAKE_BUILD_TYPE=Release \
     -DSIMUTRANS_BACKEND=sdl2 \
     -DSIMUTRANS_EMSCRIPTEN_PRELOAD_DIR="$PWD/preload-simutrans" \
     .
   cmake --build build-wasm -j
   ```
4. Serve `build-wasm/simutrans/` over HTTP:
   ```bash
   python3 -m http.server 8765 --directory build-wasm/simutrans
   ```
   Open `http://localhost:8765/` in your browser.
