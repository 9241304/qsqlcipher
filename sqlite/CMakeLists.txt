# Copyright (C) 2022 The Qt Company Ltd.
# SPDX-License-Identifier: BSD-3-Clause

#####################################################################
## QSQCipherDriverPlugin Plugin:
#####################################################################

qt_internal_add_plugin(QSQCipherDriverPlugin
    OUTPUT_NAME qsqlcipher
    PLUGIN_TYPE sqldrivers
    SOURCES
        qsql_sqlite.cpp qsql_sqlite_p.h
        qsql_sqlite_vfs.cpp qsql_sqlite_vfs_p.h
        smain.cpp
    DEFINES
        QT_NO_CAST_FROM_ASCII
        QT_NO_CAST_TO_ASCII
        QT_NO_CONTEXTLESS_CONNECT
    LIBRARIES
        Qt::Core
        Qt::CorePrivate
        Qt::SqlPrivate
)

## Scopes:
#####################################################################

qt_internal_extend_target(QSQCipherDriverPlugin CONDITION QT_FEATURE_system_sqlite
    LIBRARIES
        SQLite::SQLite3
)

if (NOT QT_FEATURE_system_sqlite)
    # On newer compilers compiling sqlite.c produces warnings
    qt_disable_warnings(QSQCipherDriverPlugin)
endif()

if(QT_FEATURE_system_sqlite)
    qt_internal_force_macos_intel_arch(QSQCipherDriverPlugin)
endif()

qt_internal_extend_target(QSQCipherDriverPlugin CONDITION NOT QT_FEATURE_system_sqlite
    SOURCES
        ${SQLITE3_SRC_AM_DIR}/sqlite3.c
    DEFINES
        SQLITE_ENABLE_COLUMN_METADATA
        SQLITE_ENABLE_FTS3
        SQLITE_ENABLE_FTS3_PARENTHESIS
        SQLITE_ENABLE_FTS4
        SQLITE_ENABLE_FTS5
        SQLITE_ENABLE_GEOPOLY
        SQLITE_ENABLE_JSON1
        SQLITE_ENABLE_MATH_FUNCTIONS
        SQLITE_ENABLE_RTREE
        SQLITE_OMIT_COMPLETE
        SQLITE_HAS_CODEC
        SQLITE_TEMP_STORE=3
    INCLUDE_DIRECTORIES
        ${SQLITE3_SRC_AM_DIR}
        ${OPENSSL_INSTALL_DIR}/include
    LIBRARIES
        debug ${OPENSSL_INSTALL_DIR}/lib/VC/static/libcrypto64MDd.lib
        optimized ${OPENSSL_INSTALL_DIR}/lib/VC/static/libcrypto64MD.lib
        wsock32
        ws2_32
        crypt32
)

qt_internal_extend_target(QSQCipherDriverPlugin CONDITION CMAKE_BUILD_TYPE STREQUAL Release AND NOT QT_FEATURE_system_sqlite
    DEFINES
        NDEBUG
)

qt_internal_extend_target(QSQCipherDriverPlugin CONDITION NOT QT_FEATURE_largefile AND NOT QT_FEATURE_system_sqlite
    DEFINES
        SQLITE_DISABLE_LFS
)

qt_internal_extend_target(QSQCipherDriverPlugin CONDITION QT_FEATURE_posix_fallocate AND NOT QT_FEATURE_system_sqlite
    DEFINES
        HAVE_POSIX_FALLOCATE=1
)

qt_internal_extend_target(QSQCipherDriverPlugin CONDITION QNX AND NOT QT_FEATURE_system_sqlite
    DEFINES
        _QNX_SOURCE
)

qt_internal_extend_target(QSQCipherDriverPlugin CONDITION UNIX AND NOT QT_FEATURE_system_sqlite
    DEFINES
        HAVE_USLEEP=1
)

qt_internal_extend_target(QSQCipherDriverPlugin CONDITION INTEGRITY
    COMPILE_OPTIONS -include qplatformdefs.h
)

qt_internal_extend_target(QSQCipherDriverPlugin CONDITION QT_FEATURE_dlopen AND NOT QT_FEATURE_system_sqlite
    LIBRARIES
        ${CMAKE_DL_LIBS}
)

qt_internal_extend_target(QSQCipherDriverPlugin CONDITION NOT QT_FEATURE_dlopen AND NOT QT_FEATURE_system_sqlite
    DEFINES
        SQLITE_OMIT_LOAD_EXTENSION
)

qt_internal_extend_target(QSQCipherDriverPlugin CONDITION NOT QT_FEATURE_thread AND NOT QT_FEATURE_system_sqlite
    DEFINES
        SQLITE_THREADSAFE=0
)
