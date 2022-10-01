cmake_minimum_required(VERSION 3.5.1)
project(CrossGuid)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${PROJECT_SOURCE_DIR}/cmake")

option(XG_TESTS "Build test runner" OFF)

IF(${UNIX})
    set(MYGE_TARGET_PLATFORM "Linux")
ELSEIF(${WIN32})
    set(MYGE_TARGET_PLATFORM "Windows")
ENDIF(${UNIX})

include_directories(.)
set(CMAKE_CXX_STANDARD 11)
include(PlatformDependencies)

add_library(xg Guid.cpp)
__add_xg_platform_dependencies(xg)

if (XG_TESTS)
	add_executable(xgtest test/TestMain.cpp test/Test.cpp)
	target_link_libraries(xgtest xg)
	__add_xg_platform_dependencies(xgtest)
endif()

set(MYGE_EXTERNAL_ROOT ${PROJECT_BINARY_DIR}/../../${MYGE_TARGET_PLATFORM})

set_target_properties(xg
PROPERTIES PUBLIC_HEADER Guid.hpp
)

install(TARGETS xg
    PUBLIC_HEADER DESTINATION ${MYGE_EXTERNAL_ROOT}/include/crossguid
    ARCHIVE DESTINATION ${MYGE_EXTERNAL_ROOT}/lib/Debug
    CONFIGURATIONS Debug
)

install(TARGETS xg
    ARCHIVE DESTINATION ${MYGE_EXTERNAL_ROOT}/lib/Release
    CONFIGURATIONS Release
)

if(MSVC)
    install(FILES ${PROJECT_BINARY_DIR}/Debug/xg.pdb
        DESTINATION ${MYGE_EXTERNAL_ROOT}/lib/Debug
        CONFIGURATIONS Debug
        )
endif(MSVC)

