# Packages

Install required build tools:

```sh
apt update
apt install cmake clangd g++-10 gdb git
```

Configure GCC 10 as the default compiler:

```sh
update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-10 100
update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-10 100
# The last number, 100, is the "priority" for this compiler alternative.
# When multiple alternatives exist, the one with the highest priority is selected as default (unless set manually).
```

Verify the compiler version:

```sh
g++ --version
# Should show: g++ (Ubuntu 10.5.0-4ubuntu2) 10.5.0
```

# Extensions

Install the following VSCode extensions for C++ development:

- [CMake Tools](https://open-vsx.org/extension/ms-vscode/cmake-tools)
- C/C++
- clangd

# Build

## Build Types

CMake supports different build types via `CMAKE_BUILD_TYPE`. If not specified, the build type defaults to an empty configuration (no explicit optimizations or debug symbols).

The `CMAKE_BUILD_TYPE` affects compilation flags as follows:

- **Debug**: Adds debug symbols (`-g`) and disables optimization (`-O0`). Suitable for development and debugging.
- **Release**: Enables optimizations (`-O3`) and defines `NDEBUG`. Best for performance-critical code and benchmarks.
- **Unspecified (default)**: No explicit flags set. Not recommended for production use.

## Default Build

By default, CMake does not set a build type. To build with debug symbols and zero optimization, explicitly specify Debug mode:

```sh
cd <cpp-high-performance-repo>
mkdir build
cd build
cmake .. -DCMAKE_BUILD_TYPE=Debug
make -j$(nproc)
```

This step will download and build all third party dependencies (Boost, GTest, and benchmark) using Hunter. This may take a while but will only be needed the first time you run CMake.

The Debug build type adds `-g` for debug symbols and `-O0` to disable optimizations, making it ideal for debugging with gdb.

## Release Build (for Benchmarks)

For benchmarks and performance testing, use Release mode which enables optimizations:

```sh
cd <cpp-high-performance-repo>
mkdir release-build
cd release-build
cmake .. -DCMAKE_BUILD_TYPE=Release
make -j$(nproc)
```

Release mode adds `-O3` optimizations and `-DNDEBUG`, which is recommended for accurate benchmark results.

## clangd Configuration

The CMake configuration automatically generates `compile_commands.json` in the build directory. This is enabled by adding `set(CMAKE_EXPORT_COMPILE_COMMANDS ON)` to `CMakeLists.txt`. This file contains the compilation commands used to build the project and is required by clangd for accurate code navigation (go to definition, etc.).

A `.clangd` configuration file is provided in the repository root that points clangd to the `compile_commands.json` file in the build directory. After running CMake, clangd should automatically use this file to understand the project structure.

## Simplified Naming Convention

The project uses a simplified naming convention:
- Main chapter executables are named `chapter01`, `chapter02`, `chapter03`, etc. (previously named `Chapter01-A_Brief_Introduction_to_C++`, `Chapter02-Essential_C++_Techniques`, etc.)
- Benchmark executables are named `chapter02_benchmark`, `chapter03_benchmark`, etc. (previously named `Chapter02-Essential_C++_Techniques_Benchmarks`, etc.)

The simplified naming convention was introduced to automate the debug process. The VS Code launch configuration (`.vscode/launch.json`) uses variables like `${input:chapterNumber}` to dynamically construct executable paths, allowing debuggers to launch any chapter without manual path configuration.

## Run

You can run individual chapter executables directly:

```sh
./chapter01
./chapter02
# etc.
```

You can also run individual benchmarks directly:

```sh
./chapter02_benchmark
./chapter03_benchmark
# etc.
```

Run tests:

```sh
ctest
```

The tests are configured to run all chapter executables using the simplified naming convention.
