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