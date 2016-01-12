cmake_minimum_required(VERSION 3.0)

set(ANTTWEAKBAR_LIBRARY ${CMAKE_CURRENT_LIST_DIR}/lib/AntTweakBar)
add_library(AntTweakBar SHARED IMPORTED)

set_property(TARGET AntTweakBar APPEND PROPERTY INTERFACE_INCLUDE_DIRECTORIES ${CMAKE_CURRENT_LIST_DIR}/include)

if(MSVC)
    if(CMAKE_SIZEOF_VOID_P EQUAL 8)
        set(ANTTWEAKBAR_IMP_LIBRARY "${ANTTWEAKBAR_LIBRARY}64.lib")
        set(ANTTWEAKBAR_SHR_LIBRARY "${ANTTWEAKBAR_LIBRARY}64.dll")
    else()
        set(ANTTWEAKBAR_IMP_LIBRARY "${ANTTWEAKBAR_LIBRARY}.lib")
        set(ANTTWEAKBAR_SHR_LIBRARY "${ANTTWEAKBAR_LIBRARY}.dll")
    endif()
else()
    if(APPLE)
        add_custom_target(build_anttweakbar ALL COMMAND "${CMAKE_MAKE_PROGRAM} -f Makefile.osx"
                    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/extern/AntTweakBar/src
                    COMMENT "Call AntTweakBar OSX Makefile")
        set(ANTTWEAKBAR_SHR_LIBRARY "${ANTTWEAKBAR_LIBRARY}.dylib")
    else()
        add_custom_target(build_anttweakbar ALL COMMAND ${CMAKE_MAKE_PROGRAM}
                    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/extern/AntTweakBar/src
                    COMMENT "Call AntTweakBar Makefile")
        set(ANTTWEAKBAR_SHR_LIBRARY "${ANTTWEAKBAR_LIBRARY}.so")
    endif()

    set(ANTTWEAKBAR_IMP_LIBRARY "${ANTTWEAKBAR_LIBRARY}.a")
    add_dependencies(AntTweakBar build_anttweakbar)
endif()

set_property(TARGET AntTweakBar PROPERTY IMPORTED_LOCATION "${ANTTWEAKBAR_SHR_LIBRARY}")
set_property(TARGET AntTweakBar PROPERTY IMPORTED_IMPLIB "${ANTTWEAKBAR_IMP_LIBRARY}")
