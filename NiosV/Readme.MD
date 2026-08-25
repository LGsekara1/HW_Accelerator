# Nios V Hello World

This project contains a Nios V/m processor system and a small C application that prints:

```text
Hellow Nios V World
```

## Requirements

Install:

- Quartus Prime Lite 22.1
- Platform Designer
- RiscFree IDE/toolchain
- CMake
- GNU Make included with RiscFree

The project uses the RiscFree tools located at:

```text
C:\intelFPGA_lite\22.1std\riscfree\toolchain\riscv32-unknown-elf\bin
C:\intelFPGA_lite\22.1std\riscfree\build_tools\bin
C:\intelFPGA_lite\22.1std\niosv\bin
```

Add these directories to the Windows `Path` environment variable.

Verify the installation in a new PowerShell terminal:

```powershell
riscv32-unknown-elf-gcc --version
riscv32-unknown-elf-g++ --version
riscv32-unknown-elf-objdump --version
niosv-stack-report --help
cmake --version
make --version
```

## Hardware Generation

1. Open `NiosV.qpf` in Quartus Prime.
2. Open the Platform Designer system `NiosV.qsys`.
3. Confirm that the Nios V system has sufficient on-chip memory.
4. Generate the Platform Designer HDL.
5. Compile the Quartus project.
6. Confirm that `NiosV.sopcinfo` has been regenerated.

The current system uses 128 KiB of SRAM.

After changing the hardware system, regenerate the BSP before compiling the application.

## Application

The application source is:

```text
NiosV/software/app/hello.c
```

The application uses the BSP in:

```text
NiosV/software/bsp/
```

The linker script is:

```text
NiosV/software/bsp/linker.x
```

## Configure the Build

Open PowerShell in the repository root:

```powershell
cd NiosV
```

Configure a clean RiscFree build:

```powershell
& "C:\Program Files\CMake\bin\cmake.exe" `
  -S "software/app" `
  -B "build-riscfree" `
  -G "MinGW Makefiles" `
  -DCMAKE_TOOLCHAIN_FILE="$PWD\software\bsp\toolchain.cmake" `
  -DCMAKE_MAKE_PROGRAM="C:\intelFPGA_lite\22.1std\riscfree\build_tools\bin\make.exe" `
  -DCMAKE_BUILD_TYPE=Release
```

## Compile

Build the application:

```powershell
& "C:\Program Files\CMake\bin\cmake.exe" `
  --build "build-riscfree"
```

The generated files are:

```text
NiosV/build-riscfree/app.elf
NiosV/build-riscfree/app.elf.objdump
```

To build only the ELF file:

```powershell
& "C:\Program Files\CMake\bin\cmake.exe" `
  --build "build-riscfree" `
  --target app.elf
```

## Check Memory Usage

The build automatically runs the Nios V stack and heap report:

```text
niosv-stack-report -p riscv32-unknown-elf- app.elf
```

A successful build should report the program size and free stack/heap memory.

## Program and Debug

`ashling-cli` is not required to compile or link the application.

It is required only for programming or debugging the FPGA target through the Ashling/RiscFree tools. Use the RiscFree IDE or Ashling tools to:

- Download `app.elf` to the Nios V processor
- Start a debug session
- Set breakpoints
- Inspect registers and memory

## Important Files

```text
NiosV.qpf                         Quartus project
NiosV.qsf                         Quartus settings
NiosV.qsys                        Platform Designer system
NiosV.sopcinfo                    Generated hardware description
NiosV/software/app/hello.c        Application source
NiosV/software/app/CMakeLists.txt Application build configuration
NiosV/software/bsp/               Nios V BSP
NiosV/software/bsp/linker.x       Linker memory layout
NiosV/software/bsp/toolchain.cmake RiscFree compiler configuration
```

## Clean Rebuild

If CMake uses an old compiler or linker, configure a new build directory:

```powershell
Remove-Item "build-riscfree" -Recurse -Force

& "C:\Program Files\CMake\bin\cmake.exe" `
  -S "software/app" `
  -B "build-riscfree" `
  -G "MinGW Makefiles" `
  -DCMAKE_TOOLCHAIN_FILE="$PWD\software\bsp\toolchain.cmake" `
  -DCMAKE_MAKE_PROGRAM="C:\intelFPGA_lite\22.1std\riscfree\build_tools\bin\make.exe" `
  -DCMAKE_BUILD_TYPE=Release

& "C:\Program Files\CMake\bin\cmake.exe" `
  --build "build-riscfree"
```