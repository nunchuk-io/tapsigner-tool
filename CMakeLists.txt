cmake_minimum_required(VERSION 3.5)

project(key-utils VERSION 0.1 LANGUAGES CXX)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

find_package(QT NAMES Qt6 Qt5 COMPONENTS Widgets REQUIRED)
find_package(Qt${QT_VERSION_MAJOR} COMPONENTS Widgets REQUIRED)

add_subdirectory(contrib/bitcoin-core)

set(PROJECT_SOURCES
        main.cpp
        mainwindow.cpp
        mainwindow.h
        mainwindow.ui
        xprivDialog.cpp
        xprivDialog.h
        xprivDialog.ui
        verifiedDialog.cpp
        verifiedDialog.h
        verifiedDialog.ui
        tapsigner_utils.h
        tapsigner_utils.cpp
)

if(${QT_VERSION_MAJOR} GREATER_EQUAL 6)
    qt_add_executable(key-utils
        MANUAL_FINALIZATION
        ${PROJECT_SOURCES}
    )
else()
    if(ANDROID)
        add_library(key-utils SHARED
            ${PROJECT_SOURCES}
        )
    else()
        add_executable(key-utils WIN32
            ${PROJECT_SOURCES}
            resources.qrc
        )
    endif()
endif()

target_link_libraries(key-utils PRIVATE Qt${QT_VERSION_MAJOR}::Widgets bitcoin-core)

if (APPLE)
    SET_TARGET_PROPERTIES(key-utils PROPERTIES MACOSX_BUNDLE TRUE)
endif()

set_target_properties(key-utils PROPERTIES
    MACOSX_BUNDLE_GUI_IDENTIFIER nunchuk.io
    MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION}
    MACOSX_BUNDLE_SHORT_VERSION_STRING ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}
)

if(QT_VERSION_MAJOR EQUAL 6)
    qt_finalize_executable(key-utils)
endif()
