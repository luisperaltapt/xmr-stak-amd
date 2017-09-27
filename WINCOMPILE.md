# Compile **xmr-stak** for Windows

## Install Dependencies

### Preparation

- open a command line `cmd`
- run `mkdir C:\xmr-stak-dep`

### Visual Studio 2017 Community

- download VS2017 Community and install from [https://www.visualstudio.com/downloads/](https://www.visualstudio.com/downloads/)
- during the install chose the components
  - `Desktop development with C++` (left side)
  - `Toolset for Visual Studio C++ 2015.3 v140...` (right side)

### CMake for Win64

- download and install the latest version from [https://cmake.org/download/](https://cmake.org/download/)
- tested version: [cmake 3.9](https://cmake.org/files/v3.9/cmake-3.9.0-rc3-win64-x64.msi)
- during the install choose the option `Add CMake to the system PATH for all users`

### AMD APP SDK 3.0

- download and install the latest version from [http://developer.amd.com/amd-accelerated-parallel-processing-app-sdk/](http://developer.amd.com/amd-accelerated-parallel-processing-app-sdk/)
- tested version: AMD APP SDK 3.0

### Dependencies OpenSSL/Hwloc and Microhttpd

- download the precompiled binary from [https://github.com/fireice-uk/xmr-stak-dep/releases/download/v1/xmr-stak-dep.zip](https://github.com/fireice-uk/xmr-stak-dep/releases/download/v1/xmr-stak-dep.zip)
- unzip all to `C:\xmr-stak-dep`

### Validate the Dependency Folder

- open a command line `cmd`
- run
   ```
   cd c:\xmr-stak-dep
   tree .
   ```
- the result should have the same structure
  ```
    C:\xmr-stak-dep>tree .
    Folder PATH listing for volume Windows
    Volume serial number is XX02-XXXX
    C:\XMR-STAK-DEP
    ├───hwloc
    │   ├───include
    │   │   ├───hwloc
    │   │   │   └───autogen
    │   │   └───private
    │   │       └───autogen
    │   └───lib
    ├───libmicrohttpd
    │   ├───include
    │   └───lib
    └───openssl
        ├───bin
        ├───include
        │   └───openssl
        └───lib
  ```

## Compile

- download and unzip `xmr-stak-amd`
- open the command line terminal `cmd`
- `cd` to your unzipped source code directory
- execute the following commands (NOTE: path to VS2017 can be different)
  ```
  set "VSCMD_START_DIR=%CD%"
  "C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\Tools\VsMSBuildCmd.bat"
  set CMAKE_PREFIX_PATH=C:\xmr-stak-dep\hwloc;C:\xmr-stak-dep\libmicrohttpd;C:\xmr-stak-dep\openssl
  mkdir build
  cd build
  cmake -G "Visual Studio 15 2017 Win64" -T v141,host=x64 ..
  ```
- keep the command line open
- explore into `C:\xmr-stak-amd-master\build` and launch `xmr-stak-amd.vcxproj` on Visual Studio
- in the Solution Explorer open `xmd-stak-amd > Source files > socket.cpp` (or any other .cpp file)
- on top, before the includes, add the following line
  ```
  #pragma comment(lib, "Ws2_32.lib")
  ```
- save the file
- go back to command line you left open and continue with the following commands:
  ```
  msbuild xmr-stak-amd.sln /p:Configuration=Release
  cd bin\Release
  copy ..\..\..\config.txt .
  copy c:\xmr-stak-dep\openssl\bin\*.* .
  mkdir opencl
  copy ..\..\..\opencl\*.* opencl\
  ```
- customize your `config.txt` file by adding the pool, username and password
