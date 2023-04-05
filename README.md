# Visual Studio / VCPKG Build - ARM

Compilation and cross-compilation.

## Download Visual Studio Build Tools

```PowerShell
Invoke-WebRequest -Uri 'https://aka.ms/vs/17/release/vs_BuildTools.exe' -OutFile "$env:TEMP/vs_BuildTools.exe"
```

## Install Visual Studio Build Tools

**Native (on ARM device):**

When installing onto an ARM device, this will include ARM64 build tools as well as x86 and x64 [^1].

```PowerShell
& "$env:TEMP/vs_BuildTools.exe" --passive --wait --add Microsoft.VisualStudio.Workload.VCTools --includeRecommended
```

**Cross-Compile (on x64 device):**

When installing onto an x64 device, you'll need to explicitly specify ARM64 build tools ("Microsoft.VisualStudio.Component.VC.Tools.ARM64" [^2]) to enable cross-compilation targeting ARM64.

```PowerShell
& "$env:TEMP/vs_BuildTools.exe" --passive --wait --add Microsoft.VisualStudio.Workload.VCTools --includeRecommended --add Microsoft.VisualStudio.Component.VC.Tools.ARM64
```

[^1]: See [Visual Studio on Arm-powered devices
](https://learn.microsoft.com/en-us/visualstudio/install/visual-studio-on-arm-devices?view=vs-2022)

[^2]: See the [Component Directory](https://learn.microsoft.com/en-us/visualstudio/install/workload-component-id-vs-build-tools?view=vs-2022)

## Add CMake to PATH

Visual Studio Build Tools includes cmake, but it's not put into the PATH.

```PowerShell
$env:PATH += ";${Env:ProgramFiles(x86)}\Microsoft Visual Studio\2022\BuildTools\Common7\IDE\CommonExtensions\Microsoft\CMake\CMake\bin"
```

## Install VCPKG

Install to `\vcpkg` for convenience.

```PowerShell
git clone https://github.com/Microsoft/vcpkg.git /vcpkg

\vcpkg\bootstrap-vcpkg.bat
```

## Build natively (x64 on x64, ARM on arm)

```PowerShell
cmake.exe -B build -S . -D CMAKE_TOOLCHAIN_FILE='/vcpkg/scripts/buildsystems/vcpkg.cmake'

cmake.exe --build build --parallel --config Debug

build\Debug\helloworld.exe
```

## Cross compile for ARM (on x64 device)

**Important:** VCPKG manifest mode doesn't work if build path includes a backslash.  So don't use something like `-B build\ARM64`!

Specify platform name using `-A ARM64`. Could also use `CMAKE_GENERATOR_PLATFORM` or `CMAKE_VS_PLATFORM_NAME`

```PowerShell
cmake.exe -B build_ARM64 -S . -D CMAKE_TOOLCHAIN_FILE='/vcpkg/scripts/buildsystems/vcpkg.cmake' -A ARM64

cmake.exe --build build_ARM64 --parallel --config Debug
```

## Check EXE with dumpbin

### Add dumpbin to PATH

**On ARM device:**

```PowerShell
$dumpbin = "${Env:ProgramFiles(x86)}\Microsoft Visual Studio\2022\BuildTools\VC\Tools\MSVC\14.35.32215\bin\Hostarm64\x64\dumpbin.exe"
```

**On x64 device:**

```PowerShell
$dumpbin = "${Env:ProgramFiles(x86)}\Microsoft Visual Studio\2022\BuildTools\VC\Tools\MSVC\14.35.32215\bin\Hostx64\x64\dumpbin.exe"
```

### Check "machine" type

```PowerShell
& $dumpbin /HEADERS build_ARM64\Debug\helloworld.exe | Select-String 'machine' -SimpleMatch
```

## References

[cmake - Cross compiling for x64_arm with MSVC tools on Windows - Stack Overflow](https://stackoverflow.com/questions/66063056/cross-compiling-for-x64-arm-with-msvc-tools-on-windows)

[Cross Compiling With CMake — Mastering CMake](https://cmake.org/cmake/help/book/mastering-cmake/chapter/Cross%20Compiling%20With%20CMake.html)

[Cross-compiling under Visual Studio (#18225) · Issues · CMake - CMake · GitLab](https://gitlab.kitware.com/cmake/cmake/-/issues/18225)
