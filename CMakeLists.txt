cmake_minimum_required(VERSION 3.10)
project(myproject CXX)

set(CMAKE_BUILD_TYPE Release)

# Download automatically, you can also just copy the conan.cmake file
if(NOT EXISTS "${CMAKE_BINARY_DIR}/conan.cmake")
    message(STATUS "Downloading conan.cmake from https://github.com/conan-io/cmake-conan")
    file(DOWNLOAD "https://raw.githubusercontent.com/conan-io/cmake-conan/master/conan.cmake"
                  "${CMAKE_BINARY_DIR}/conan.cmake")
endif()

include(${CMAKE_BINARY_DIR}/conan.cmake)

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

add_executable(main src/main.cpp)
target_link_libraries(main ${CONAN_LIBS})
