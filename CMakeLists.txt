# Generated from sqldrivers.pro.

# special case begin
cmake_minimum_required(VERSION 3.16)

if (NOT DEFINED Qt6_DIR)
	message(FATAL_ERROR "Qt6_DIR is not defined")
endif()

set(Qt6_ROOT ${Qt6_DIR})
set(Qt6_DIR ${Qt6_DIR}/lib/cmake/Qt6)

if (NOT DEFINED SQLITE3_SRC_AM_DIR)
	message(FATAL_ERROR "SQLITE3_SRC_AM_DIR is not defined")
endif()

if (PLUGIN_TYPE STREQUAL "sqlite")
    set(PLUGIN_NAME QSQLiteDriverPlugin)
elseif (PLUGIN_TYPE STREQUAL "sqlcipher")
    set(PLUGIN_NAME QSQLCipherDriverPlugin)
    if (NOT DEFINED OPENSSL_INSTALL_DIR)
	    message(FATAL_ERROR "OPENSSL_INSTALL_DIR is not defined")
    endif()
else()
    message(FATAL_ERROR "PLUGIN_TYPE should be sqlite or sqlcipher")
endif()

add_compile_definitions(PLUGIN_TYPE="${PLUGIN_TYPE}")

if (NOT CMAKE_PROJECT_NAME STREQUAL "QtBase" AND NOT CMAKE_PROJECT_NAME STREQUAL "Qt")
    include(.cmake.conf)
    project(QSQLiteDriverPlugins
        VERSION "${QT_REPO_MODULE_VERSION}"
        DESCRIPTION "Qt6 SQL driver plugins"
        HOMEPAGE_URL "https://qt.io/"
        LANGUAGES CXX C ASM
    )
    find_package(Qt6 ${PROJECT_VERSION} CONFIG REQUIRED COMPONENTS
        BuildInternals
        Core
        Sql
     )
	 
	qt_prepare_standalone_project()
else()
    qt_internal_upgrade_cmake_policies()
endif()

# Currently handled completely manually.

# TODO sqldrivers_standalone {
#     _QMAKE_CACHE_ = $$shadowed($$SQLDRV_SRC_TREE)/.qmake.conf
#     load(qt_configure)
# }

qt_feature_module_begin(
    NO_MODULE
    PUBLIC_FILE "qtsqldrivers-config.h"
    PRIVATE_FILE "qtsqldrivers-config_p.h"
)
include(configure.cmake)
qt_feature_module_end(NO_MODULE)

if (CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
  set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /Od")
endif()

# Generated from sqlite.pro.

#####################################################################
## QSQLiteDriverPlugin Plugin:
#####################################################################

qt_internal_add_plugin(${PLUGIN_NAME}
    OUTPUT_NAME ${PLUGIN_TYPE}
    PLUGIN_TYPE sqldrivers
    SOURCES
        qsql_sqlite.cpp qsql_sqlite_p.h
        smain.cpp
    DEFINES
        QT_NO_CAST_FROM_ASCII
        QT_NO_CAST_TO_ASCII
    LIBRARIES
        Qt::Core
        Qt::CorePrivate
        Qt::SqlPrivate
)

#### Keys ignored in scope 1:.:.:sqlite.pro:<TRUE>:
# OTHER_FILES = "sqlite.json"
# QT_FOR_CONFIG = "sqldrivers-private"

## Scopes:
#####################################################################

qt_internal_extend_target(${PLUGIN_NAME} CONDITION QT_FEATURE_system_sqlite
    LIBRARIES
        SQLite::SQLite3
)

# special case begin
if (NOT QT_FEATURE_system_sqlite)
    # On newer compilers compiling sqlite.c produces warnings
    qt_disable_warnings(${PLUGIN_NAME})
endif()

if(QT_FEATURE_system_sqlite)
    qt_internal_force_macos_intel_arch(${PLUGIN_NAME})
endif()
# special case end

qt_internal_extend_target(${PLUGIN_NAME} CONDITION NOT QT_FEATURE_system_sqlite
    SOURCES
        ${SQLITE3_SRC_AM_DIR}/sqlite3.c
    DEFINES
        SQLITE_ENABLE_COLUMN_METADATA
        SQLITE_ENABLE_FTS3
        SQLITE_ENABLE_FTS3_PARENTHESIS
        SQLITE_ENABLE_FTS5
        SQLITE_ENABLE_JSON1
        SQLITE_ENABLE_RTREE
        SQLITE_OMIT_COMPLETE
    INCLUDE_DIRECTORIES
        ${SQLITE3_SRC_AM_DIR}
)

qt_internal_extend_target(${PLUGIN_NAME} CONDITION PLUGIN_TYPE STREQUAL sqlcipher 
    DEFINES
        SQLITE_HAS_CODEC
    INCLUDE_DIRECTORIES
        ${OPENSSL_INSTALL_DIR}/include
    LIBRARIES
        debug ${OPENSSL_INSTALL_DIR}/lib/VC/static/libcrypto64MTd.lib
        optimized ${OPENSSL_INSTALL_DIR}/lib/VC/static/libcrypto64MT.lib
        wsock32
        ws2_32
        crypt32
)

# target_link_libraries(${PLUGIN_NAME} wsock32 ws2_32)

qt_internal_extend_target(${PLUGIN_NAME} CONDITION CMAKE_BUILD_TYPE STREQUAL Release AND NOT QT_FEATURE_system_sqlite
    DEFINES
        NDEBUG
)

qt_internal_extend_target(${PLUGIN_NAME} CONDITION NOT QT_FEATURE_largefile AND NOT QT_FEATURE_system_sqlite
    DEFINES
        SQLITE_DISABLE_LFS
)

qt_internal_extend_target(${PLUGIN_NAME} CONDITION QT_FEATURE_posix_fallocate AND NOT QT_FEATURE_system_sqlite
    DEFINES
        HAVE_POSIX_FALLOCATE=1
)

qt_internal_extend_target(${PLUGIN_NAME} CONDITION QNX AND NOT QT_FEATURE_system_sqlite
    DEFINES
        _QNX_SOURCE
)

qt_internal_extend_target(${PLUGIN_NAME} CONDITION UNIX AND NOT QT_FEATURE_system_sqlite
    DEFINES
        HAVE_USLEEP=1
)

qt_internal_extend_target(${PLUGIN_NAME} CONDITION INTEGRITY
    COMPILE_OPTIONS -include qplatformdefs.h
)

qt_internal_extend_target(${PLUGIN_NAME} CONDITION QT_FEATURE_dlopen AND NOT QT_FEATURE_system_sqlite
    LIBRARIES
        ${CMAKE_DL_LIBS}
)

qt_internal_extend_target(${PLUGIN_NAME} CONDITION NOT QT_FEATURE_dlopen AND NOT QT_FEATURE_system_sqlite
    DEFINES
        SQLITE_OMIT_LOAD_EXTENSION
)


if(NOT CMAKE_PROJECT_NAME STREQUAL "QtBase" AND NOT CMAKE_PROJECT_NAME STREQUAL "Qt")
    qt_print_feature_summary()
endif()

# special case end
