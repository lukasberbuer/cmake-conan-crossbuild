# Cross-building with CMake + Conan

Minimal example using CMake and Conan to cross-build with CMake toolchain files.

Problem: The CMake toolchain file is not sufficient for cross-building. An additional Conan profile file must be specified for the host.

**Is it possible to infer the Conan profile from the CMake toolchain file settings?**

Goals:
- manage dependencies with Conan (fmt in this case)
- use CMake and CMake toolchain files for cross compilation:
  ```
  mkdir build
  cd build
  cmake -DCMAKE_TOOLCHAIN_FILE=toolchain-armv7.cmake ..
  make
  ```
- Get rid of an additional Conan profile file
  ```cmake
  conan_cmake_run(
      CONANFILE conanfile.txt
      BASIC_SETUP
      PROFILE ${CMAKE_SOURCE_DIR}/profile-armv7.conan  # <- neccessary?
      PROFILE_AUTO ALL
      BUILD missing
  )
  ```

## Edit 2021-01-24

It's possible. Link to issue: https://github.com/conan-io/cmake-conan/issues/307

The arch variable needs to be specified in the CMake toolchain file:
```cmake
set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_SYSTEM_PROCESSOR arm)
set(CONAN_ARCHITECTURE armv7)
...
```

Everything else can be derived from CMake. The Conan profile file is not necessary any more:

```cmake
if(CMAKE_CROSSCOMPILING AND NOT CONAN_ARCHITECTURE)
    message(FATAL_ERROR "The variable CONAN_ARCHITECTURE must be specified for cross-compiling")
endif()

conan_cmake_run(
    CONANFILE conanfile.txt
    BASIC_SETUP
    ARCH ${CONAN_ARCHITECTURE}
    ENV CC=${CMAKE_C_COMPILER}
    ENV CXX=${CMAKE_CXX_COMPILER}
    PROFILE_AUTO ALL
    BUILD missing
)
```
