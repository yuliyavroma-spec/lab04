## Laboratory work IV

## Report

## Homework

Вы продолжаете проходить стажировку в "Formatter Inc." (см [подробности](https://github.com/tp-labs/lab03#Homework)).

В прошлый раз ваше задание заключалось в настройке автоматизированной системы **CMake**.

Сейчас вам требуется настроить систему непрерывной интеграции для библиотек и приложений, с которыми вы работали в [прошлый раз](https://github.com/tp-labs/lab03#Homework). Настройте сборочные процедуры на различных платформах:
* используйте  для сборки на операционной системе **Linux** с использованием компиляторов **gcc** и **clang**;
**Файл `.github/workflows/ci.yml `**
```bash
$ cat > .github/workflows/ci.yml <<'EOF'

name: CI

on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]

jobs:
  build-linux:
    name: Linux - ${{ matrix.compiler }}
    runs-on: ubuntu-latest
    
    strategy:
      matrix:
        compiler: [gcc, clang]
        cmake_version: ["3.10", "3.22"]
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
    
    - name: Setup CMake ${{ matrix.cmake_version }}
      uses: jwlawson/actions-setup-cmake@v2
      with:
        cmake-version: ${{ matrix.cmake_version }}
    
    - name: Configure with CMake
      run: |
        mkdir -p build
        cd build
        cmake .. -DCMAKE_INSTALL_PREFIX=../install
    
    - name: Build
      run: |
        cd build
        cmake --build .
    
    - name: Install
      run: |
        cd build
        cmake --build . --target install
    
    - name: Verify installation
      run: |
        ls -la install/
        test -d install/bin || test -d install/lib
```
* используйте [AppVeyor](https://www.appveyor.com/) для сборки на операционной системе **Windows**.
**Файл `.appveyor.yml`**
```bash
$ cat > .appveyor.yml <<'EOF'

version: 1.0.{build}
image: Visual Studio 2019

build:
  verbosity: minimal

before_build:
  - mkdir build
  - cd build
  - cmake .. -G "Visual Studio 16 2019" -DCMAKE_INSTALL_PREFIX=../install

build_script:
  - cmake --build . --config Release

after_build:
  - cmake --build . --target install --config Release

test: off
EOF
```
**Результат сборки:**
```
Build started
git clone -q --branch=master https://github.com/yuliyavroma-spec/lab04.git C:\projects\lab04
git checkout -qf 2af0a5ead7adb53812f6e2fba399d4ae59c346b3
mkdir build
cd build
cmake .. -G "Visual Studio 16 2019" -DCMAKE_INSTALL_PREFIX=../install
-- Selecting Windows SDK version 10.0.26100.0 to target Windows 10.0.17763.
-- The C compiler identification is MSVC 19.29.30159.0
-- The CXX compiler identification is MSVC 19.29.30159.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: C:/Program Files (x86)/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.29.30133/bin/Hostx64/x64/cl.exe - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: C:/Program Files (x86)/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.29.30133/bin/Hostx64/x64/cl.exe - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done (24.7s)
-- Generating done (0.0s)
-- Build files have been written to: C:/projects/lab04/build
cmake --build . --config Release
Microsoft (R) Build Engine version 16.11.6+a918ceb31 for .NET Framework
Copyright (C) Microsoft Corporation. All rights reserved.
  1>Checking Build System
  Building Custom Rule C:/projects/lab04/CMakeLists.txt
  print.cpp
  print.vcxproj -> C:\projects\lab04\build\Release\print.lib
  Building Custom Rule C:/projects/lab04/CMakeLists.txt
cmake --build . --target install --config Release
Microsoft (R) Build Engine version 16.11.6+a918ceb31 for .NET Framework
Copyright (C) Microsoft Corporation. All rights reserved.
  print.vcxproj -> C:\projects\lab04\build\Release\print.lib
  1>
  -- Install configuration: "Release"
  -- Installing: C:/projects/lab04/install/lib/print.lib
  -- Installing: C:/projects/lab04/install/include
  -- Installing: C:/projects/lab04/install/include/print.hpp
  -- Installing: C:/projects/lab04/install/cmake/print-config.cmake
  -- Installing: C:/projects/lab04/install/cmake/print-config-release.cmake
Build success
```
