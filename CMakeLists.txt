cmake_minimum_required(VERSION 3.14)

# https://stackoverflow.com/a/50360945/8242705
# https://code.visualstudio.com/docs/cpp/faq-cpp#_how-do-i-get-intellisense-to-work-correctly
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

list(APPEND VCPKG_FEATURE_FLAGS "versions")
# Setup vcpkg script with CMake (note: should be placed before project() call)
if(DEFINED ENV{VCPKG_ROOT})
    set(CMAKE_TOOLCHAIN_FILE "$ENV{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake" CACHE STRING "Vcpkg toolchain file")
else()
    set(CMAKE_TOOLCHAIN_FILE "./vcpkg/scripts/buildsystems/vcpkg.cmake" CACHE STRING "Vcpkg toolchain file")
endif()

set(PROJECT "obj-viewer")
project(${PROJECT} LANGUAGES CXX)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# QtCreator supports the following variables for Android, which are identical to qmake Android variables.
# Check https://doc.qt.io/qt/deployment-android.html for more information.
# They need to be set before the find_package(...) calls below.

#if(ANDROID)
#    set(ANDROID_PACKAGE_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/android")
#    if (ANDROID_ABI STREQUAL "armeabi-v7a")
#        set(ANDROID_EXTRA_LIBS
#            ${CMAKE_CURRENT_SOURCE_DIR}/path/to/libcrypto.so
#            ${CMAKE_CURRENT_SOURCE_DIR}/path/to/libssl.so)
#    endif()
#endif()

file(GLOB_RECURSE SRC_LIST RELATIVE ${CMAKE_SOURCE_DIR}
    "src/*.cc"
    "src/*.qrc")
# https://stackoverflow.com/a/57928919/8242705
file(GLOB_RECURSE HEADER_LIST RELATIVE ${CMAKE_SOURCE_DIR}
    "src/*.h")
foreach(header_path ${HEADER_LIST})
    GET_FILENAME_COMPONENT(header_dir ${header_path} PATH)
    SET(header_dir_list ${header_dir_list} ${header_dir})
endforeach()
LIST(REMOVE_DUPLICATES header_dir_list)
message(STATUS "SRC_LIST: ${SRC_LIST}")
message(STATUS "header_dir_list: ${header_dir_list}")

add_executable(${PROJECT}
    ${SRC_LIST}
)


find_package(tinyobjloader CONFIG REQUIRED)
target_link_libraries(${PROJECT} PRIVATE tinyobjloader::tinyobjloader)

target_include_directories(${PROJECT}
    PRIVATE
    ${header_dir_list})

find_package(GLEW REQUIRED)
target_link_libraries(${PROJECT} PRIVATE GLEW::GLEW)

find_package(glfw3 CONFIG REQUIRED)
target_link_libraries(${PROJECT} PRIVATE glfw)
