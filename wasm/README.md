# AA+ WebAssembly Build

This directory contains the WebAssembly build system for the AA+ astronomical algorithms library.

## Prerequisites

### 1. Emscripten SDK

Install the Emscripten SDK to compile C++ to WebAssembly:

```bash
# Clone the emsdk repository
git clone https://github.com/emscripten-core/emsdk.git ~/.emsdk
cd ~/.emsdk

# Install and activate the latest version
./emsdk install latest
./emsdk activate latest

# Add to your shell profile (~/.bashrc, ~/.zshrc, etc.)
source ~/.emsdk/emsdk_env.sh
```

**Note:** You'll need to run `source ~/.emsdk/emsdk_env.sh` in each new terminal session, or add it to your shell profile.

### 2. Python with libclang

The binding generator uses Python with clang bindings to parse C++ headers:

```bash
# Install Python package for clang
pip install libclang

# On macOS with Homebrew, install LLVM for libclang
brew install llvm
```

The build script automatically looks for libclang at `/opt/homebrew/opt/llvm/lib/libclang.dylib` (macOS Homebrew default). Set `LIBCLANG_PATH` environment variable if your installation is elsewhere.

### 3. CMake

```bash
# macOS
brew install cmake

# Ubuntu/Debian
sudo apt-get install cmake

# Or download from https://cmake.org/download/
```

### 4. Node.js (for testing)

To run the example scripts:

```bash
# macOS
brew install node

# Ubuntu/Debian
sudo apt-get install nodejs npm

# Or download from https://nodejs.org/
```

### 5. TypeScript (optional, for type checking)

```bash
cd wasm/test
npm install --save-dev typescript
```

## Building

### Quick Start

```bash
# From the repository root
./wasm/build.sh
```

### Build Outputs

The build produces three files in `wasm/dist/`:

- **aaplus.js** - JavaScript module (44KB)
- **aaplus.wasm** - WebAssembly binary (8.9MB)
- **aaplus.d.ts** - TypeScript definitions with proper parameter names (45KB)

### Build Options

```bash
# Clean build (removes dist and build directories first)
./wasm/build.sh --clean

# Set custom libclang path
LIBCLANG_PATH=/path/to/libclang.dylib ./wasm/build.sh
```

### Build Process

The build script (`build.sh`) performs these steps:

1. **Generate bindings** - Scans all `AA*.h` headers using clang and generates:
   - `bindings/aa_bindings.cpp` - Emscripten embind bindings
   - `function_signatures.json` - Parameter metadata for demos
   - `dist/aaplus.d.ts` - TypeScript definitions with named parameters
   - `CLASS_SUMMARY.md` - Documentation of all bound classes

2. **Compile to WASM** - Uses Emscripten to compile all C++ sources to WebAssembly

3. **Clean up** - Removes the temporary build directory

## Testing

### Type Checking

Verify TypeScript definitions are correct:

```bash
cd wasm/test
npx tsc --noEmit typecheck.ts
```

### Run Example

Execute calculations with the actual WASM module:

```bash
node wasm/test/run_example.mjs
```

This will output:
- Sun position (ecliptic & equatorial coordinates)
- Moon position and radius vector
- Coordinate transformations
- Sidereal time calculations
- Nutation values
- Planet positions (Mercury through Saturn)

### HTML Demos

#### Sun Position Demo
Open `wasm/test/sun_position.html` in a web browser to calculate the Sun's position for a given date.

#### Function Explorer
Open `wasm/test/all_functions.html` to browse and test all available functions with their signatures and parameters.

## Usage

### In Node.js

```javascript
import { readFileSync } from 'fs';
import createModule from './wasm/dist/aaplus.js';

const wasmBinary = readFileSync('./wasm/dist/aaplus.wasm');
const Module = await createModule({ wasmBinary });

// Calculate Julian Date
const jd = Module.dateToJD(2026, 2, 3, 12, 0, 0);

// Get Sun's position
const sunLong = Module.CAASun.GeometricEclipticLongitude(jd, true);
const sunLat = Module.CAASun.GeometricEclipticLatitude(jd, true);
```

### In Browser

```html
<script type="module">
  import createModule from './wasm/dist/aaplus.js';
  
  const Module = await createModule();
  
  const jd = Module.dateToJD(2026, 2, 3, 12, 0, 0);
  const sunLong = Module.CAASun.GeometricEclipticLongitude(jd, true);
  
  console.log('Sun longitude:', sunLong, 'degrees');
</script>
```

### With TypeScript

```typescript
import type { MainModule } from './wasm/dist/aaplus';
import createModule from './wasm/dist/aaplus.js';

const Module: MainModule = await createModule();

// TypeScript will provide autocompletion and type checking
const jd: number = Module.dateToJD(2026, 2, 3, 12, 0, 0);
const sunPos = Module.CAASun.GeometricEclipticLongitude(jd, true);
```

## API Coverage

The bindings include **169 classes** from the AA+ library:

- **51 value types/structs** - Coordinate types, detail classes
- **76 core computational classes** - Sun, Moon, planets, transformations, etc.
- **42 VSOP87 variants** - High-precision planetary position calculations

### Filtering Rules

Not all C++ methods are exposed to avoid binding issues:

- ❌ Methods with raw pointers
- ❌ Methods returning `std::vector` or `std::array`
- ❌ Methods with nested enum parameters (e.g., `Planet::Type`)
- ❌ Methods with non-const reference parameters (output parameters)
- ❌ Overloaded methods (require manual disambiguation)

## Architecture

### Generated Files

- **`generate_bindings.py`** - Python script that parses headers and generates bindings
  - Uses clang.cindex for accurate C++ parsing
  - Extracts parameter names for TypeScript definitions
  - Filters incompatible methods automatically

- **`bindings/aa_bindings.cpp`** - Generated embind bindings (C++)
  - Exposes all value types with `.field()`
  - Exposes static methods with `.class_function()`
  - Includes `dateToJD` helper for convenience

- **`dist/aaplus.d.ts`** - Generated TypeScript definitions
  - Preserves original C++ method names (e.g., `GeometricEclipticLongitude`)
  - Includes proper parameter names from clang (not `_0`, `_1`, etc.)
  - Single-character field names preserved to avoid collisions (e.g., `X`, `Y`, `Z`, `B`, `b`)

### CMake Configuration

`CMakeLists.txt` configures the Emscripten build with:
- ES6 module output
- Memory growth enabled
- Optimized for size and speed (`-O3`)
- No deprecated TypeScript emit (we generate our own)

## Troubleshooting

### Emscripten not found

```bash
# Make sure emsdk is sourced
source ~/.emsdk/emsdk_env.sh
```

### libclang not found

```bash
# macOS
brew install llvm
export LIBCLANG_PATH=/opt/homebrew/opt/llvm/lib/libclang.dylib

# Linux - install libclang-dev
sudo apt-get install libclang-dev
```

### Build fails with "Missing binding for type"

This usually means a method is using an unbound type. The generator should automatically filter these, but if you see this error, the method needs to be excluded in `is_bindable_method()` in `generate_bindings.py`.

### TypeScript errors

Regenerate the bindings to ensure TypeScript definitions match the compiled WASM:

```bash
./wasm/build.sh --clean
```

## Contributing

When adding support for new AA+ features:

1. The binding generator automatically detects new classes in `AA*.h` headers
2. Run `./wasm/build.sh` to regenerate bindings
3. Test with `node wasm/test/run_example.mjs`
4. Verify types with `npx tsc --noEmit wasm/test/typecheck.ts`

## License

The AA+ library and these bindings follow the AA+ license terms. See the main repository README for details.

## Generated by Copilot
