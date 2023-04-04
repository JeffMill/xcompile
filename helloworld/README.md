# helloworld

## Install BuildTools

See the [Component Directory](https://learn.microsoft.com/en-us/visualstudio/install/workload-component-id-vs-build-tools?view=vs-2022)

Includes cmake.

Also need to specify ARM64 build tools (Microsoft.VisualStudio.Component.VC.Tools.ARM64)

Invoke-WebRequest -Uri 'https://aka.ms/vs/17/release/vs_BuildTools.exe' -OutFile "$env:TEMP/vs_BuildTools.exe"

& "$env:TEMP/vs_BuildTools.exe" --passive --wait --add Microsoft.VisualStudio.Workload.VCTools --includeRecommended --add Microsoft.VisualStudio.Component.VC.Tools.ARM64

## Add CMake to PATH

```PowerShell
$env:PATH += ";${Env:ProgramFiles(x86)}\Microsoft Visual Studio\2022\BuildTools\Common7\IDE\CommonExtensions\Microsoft\CMake\CMake\bin"
```

## Build for x64

```PowerShell
cmake.exe -B build\x64 -S . -D CMAKE_TOOLCHAIN_FILE='/vcpkg/scripts/buildsystems/vcpkg.cmake'

cmake.exe --build build\x64 --parallel --config Debug

build\x64\Debug\helloworld.exe
```

## Cross compile for ARM

Specify platform name using "-A ARM64"

```PowerShell
cmake.exe -B build\ARM64 -S . -D CMAKE_TOOLCHAIN_FILE='/vcpkg/scripts/buildsystems/vcpkg.cmake' -A ARM64

cmake.exe --build build\ARM64 --parallel --config Debug

build\ARM64\Debug\helloworld.exe
```
