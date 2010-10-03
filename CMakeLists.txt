# Copyright (C) 2007-2009 LuaDist.
# Created by Peter Kapec <kapecp@gmail.com>
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

PROJECT(lrexlib C)
CMAKE_MINIMUM_REQUIRED(VERSION 2.6)
INCLUDE(dist.cmake)

FIND_PACKAGE (Lua51 REQUIRED)
INCLUDE_DIRECTORIES( ${LUA_INCLUDE_DIR})

# Find PCRE
FIND_PACKAGE(PCRE)
IF (PCRE_FOUND)
	INCLUDE_DIRECTORIES(${PCRE_INCLUDE_DIRS})
ENDIF()

# Find posix (RXSpencer on Windows)
IF (WIN32 AND NOT CYGWIN)
	FIND_PACKAGE(RXSpencer REQUIRED)
	INCLUDE_DIRECTORIES(${RXSPENCER_INCLUDE_DIRS})
ENDIF()

# Find Oniguruma
FIND_PACKAGE(Oniguruma)
IF (Oniguruma_FOUND)
	INCLUDE_DIRECTORIES(${ONIGURUMA_INCLUDE_DIRS})
ENDIF()

IF (MSVC)
   ADD_DEFINITIONS("-DREX_API=__declspec(dllexport)")
ENDIF()

# common lrexlib modules
SET (COMMON_SRC src/common.c )

# pcre module
IF (PCRE_FOUND)
	SET(PCRE_SRCS src/pcre/lpcre.c src/pcre/lpcre_f.c ${COMMON_SRC})
	ADD_LUA_MODULE(rex_pcre ${PCRE_SRCS})
	TARGET_LINK_LIBRARIES(rex_pcre ${PCRE_LIBRARIES})
ENDIF()

# posix module
IF (1)
	SET(POSIX_SRCS src/posix/lposix.c ${COMMON_SRC})
	ADD_LUA_MODULE(rex_posix ${POSIX_SRCS})
	IF (WIN32 AND NOT CYGWIN)
		TARGET_LINK_LIBRARIES(rex_posix ${RXSPENCER_LIBRARIES})
		ADD_DEFINITIONS(-Doff_t=_off_t)
	ENDIF()
ENDIF()

# oniguruma module
FIND_PACKAGE(Oniguruma)
IF (Oniguruma_FOUND)
	SET(ONIGURUMA_SRCS src/oniguruma/lonig.c src/oniguruma/lonig_f.c ${COMMON_SRC})
	ADD_LUA_MODULE(rex_onig ${ONIGURUMA_SRCS})
	TARGET_LINK_LIBRARIES(rex_onig ${ONIGURUMA_LIBRARIES})

ENDIF()

# Install lrexlib modules
INSTALL(TARGETS rex_posix DESTINATION ${INSTALL_CMOD})
IF (PCRE_FOUND)
	INSTALL(TARGETS rex_pcre DESTINATION ${INSTALL_CMOD})
ENDIF()
IF (Oniguruma_FOUND)
	INSTALL(TARGETS rex_onig DESTINATION ${INSTALL_CMOD})
ENDIF()
INSTALL(FILES ChangeLog LICENSE NEWS README DESTINATION ${INSTALL_DATA})
INSTALL(DIRECTORY test/ DESTINATION ${INSTALL_TEST})
INSTALL(DIRECTORY doc/ DESTINATION ${INSTALL_DOC})
