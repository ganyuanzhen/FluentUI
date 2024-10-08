cmake_minimum_required(VERSION 3.20)

project(FluentUI VERSION 1.0)

find_package(ECM REQUIRED NO_MODULE)
list(APPEND CMAKE_MODULE_PATH ${ECM_MODULE_PATH} ${ECM_KDE_MODULE_DIR})
message(STATUS "ECM version: ${ECM_VERSION}")

set(QT_SDK_DIR "${Qt${QT_VERSION_MAJOR}_DIR}")
cmake_path(GET QT_SDK_DIR PARENT_PATH QT_SDK_DIR)
cmake_path(GET QT_SDK_DIR PARENT_PATH QT_SDK_DIR)
cmake_path(GET QT_SDK_DIR PARENT_PATH QT_SDK_DIR)

if(ECM_VERSION VERSION_LESS 5.93.0)
    include(ECMQueryQmake)
    query_qmake(INSTALL_QMLDIR QT_INSTALL_QML)
else()
    include(ECMQueryQt)
    ecm_query_qt(INSTALL_QMLDIR QT_INSTALL_QML)
endif()


if (MSVC)
    #让Release也生成pdb文件
    set(CMAKE_SHARED_LINKER_FLAGS_RELEASE "${CMAKE_SHARED_LINKER_FLAGS_RELEASE} /DEBUG /OPT:REF /OPT:ICF")
    set(CMAKE_EXE_LINKER_FLAGS_RELEASE "${CMAKE_EXE_LINKER_FLAGS_RELEASE} /DEBUG /OPT:REF /OPT:ICF")
endif ()

list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_LIST_DIR}/.cmake/)

include(GetGitRevisionDescription)

option(FLUENTUI_BUILD_EXAMPLES "Build FluentUI demo applications." OFF)
option(FLUENTUI_BUILD_STATIC_LIB "Build static library." OFF)

find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Core)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Core)

add_subdirectory(src)

#Release也支持日志打印代码位置
target_compile_definitions(fluentuiplugin
        PRIVATE
        QT_MESSAGELOGCONTEXT
)

if (FLUENTUI_BUILD_EXAMPLES)
    add_subdirectory(example)
endif ()

message("------------------------ FluentUI ------------------------")
message("Build FluentUI demo applications.: ${FLUENTUI_BUILD_EXAMPLES}")
message("Build static library.: ${FLUENTUI_BUILD_STATIC_LIB}")
message("Path to FluentUI plugin.: ${FLUENTUI_QML_PLUGIN_DIRECTORY}")

# Configuration for cmake
include(CMakePackageConfigHelpers)
set(CMAKECONFIG_INSTALL_DIR "${CMAKE_INSTALL_LIBDIR}/cmake/FluentUI")
configure_package_config_file(
        "${CMAKE_CURRENT_SOURCE_DIR}/FluentUIConfig.cmake.in"
        "${CMAKE_CURRENT_BINARY_DIR}/FluentUIConfig.cmake"
        INSTALL_DESTINATION
        "${CMAKECONFIG_INSTALL_DIR}"
        PATH_VARS
        CMAKECONFIG_INSTALL_DIR CMAKE_INSTALL_PREFIX
)
write_basic_package_version_file(
        ${CMAKE_CURRENT_BINARY_DIR}/FluentUIConfigVersion.cmake
        VERSION 1.7.7
        COMPATIBILITY AnyNewerVersion
)

install(
        FILES
        "${CMAKE_CURRENT_BINARY_DIR}/FluentUIConfig.cmake"
        "${CMAKE_CURRENT_BINARY_DIR}/FluentUIConfigVersion.cmake"
        DESTINATION
        "${CMAKECONFIG_INSTALL_DIR}"
)