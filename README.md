# Visual Studio Build Tools ARM

Compilation and cross-compilation.

## Install Visual Studio Build Tools

If installed onto an ARM device, this will include ARM64 build tools as well as x86 and x64. If you're installing onto an x64 device, you'll need to explicitly specify ARM64 build tools ("Microsoft.VisualStudio.Component.VC.Tools.ARM64" [note 1]) to enable cross-compilation targeting ARM64.

```PowerShell
Invoke-WebRequest -Uri 'https://aka.ms/vs/17/release/vs_BuildTools.exe' -OutFile "$env:TEMP/vs_BuildTools.exe"

& "$env:TEMP/vs_BuildTools.exe" --passive --wait --add Microsoft.VisualStudio.Workload.VCTools --includeRecommended [--add Microsoft.VisualStudio.Component.VC.Tools.ARM64]
```

**Note 1:** See the [Component Directory](https://learn.microsoft.com/en-us/visualstudio/install/workload-component-id-vs-build-tools?view=vs-2022)

## Add CMake to PATH

```PowerShell
$env:PATH += ";${Env:ProgramFiles(x86)}\Microsoft Visual Studio\2022\BuildTools\Common7\IDE\CommonExtensions\Microsoft\CMake\CMake\bin"
```

## Build for x64

**Note:** VCPKG manifest mode doesn't work if build path includes a backslash.  So don't use something like "build_x64"!

```PowerShell
cmake.exe -B build_x64 -S . -D CMAKE_TOOLCHAIN_FILE='/vcpkg/scripts/buildsystems/vcpkg.cmake'

cmake.exe --build build_x64 --parallel --config Debug

build_x64\Debug\helloworld.exe
```

## Cross compile for ARM

Specify platform name using `-A ARM64`. Could also use `CMAKE_GENERATOR_PLATFORM` or `CMAKE_VS_PLATFORM_NAME`

```PowerShell
cmake.exe -B build_ARM64 -S . -D CMAKE_TOOLCHAIN_FILE='/vcpkg/scripts/buildsystems/vcpkg.cmake' -A ARM64

cmake.exe --build build_ARM64 --parallel --config Debug
```

## Check EXE with dumpbin

```PowerShell
$dumpbin = "${Env:ProgramFiles(x86)}\Microsoft Visual Studio\2022\BuildTools\VC\Tools\MSVC\14.35.32215\bin\Hostx64\x64\dumpbin.exe"

& $dumpbin /HEADERS build_ARM64\Debug\helloworld.exe | Select-String 'machine' -SimpleMatch
```

## References

[cmake - Cross compiling for x64_arm with MSVC tools on Windows - Stack Overflow](https://stackoverflow.com/questions/66063056/cross-compiling-for-x64-arm-with-msvc-tools-on-windows)

[Cross Compiling With CMake — Mastering CMake](https://cmake.org/cmake/help/book/mastering-cmake/chapter/Cross%20Compiling%20With%20CMake.html)

[Cross-compiling under Visual Studio (#18225) · Issues · CMake - CMake · GitLab](https://gitlab.kitware.com/cmake/cmake/-/issues/18225)
