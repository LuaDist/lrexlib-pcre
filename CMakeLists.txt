# Copyright (C) 2007-2012 LuaDist.
# Created by Peter Kapec, Peter Drahos
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

project ( lrexlib-pcre C )
cmake_minimum_required ( VERSION 2.8 )
include ( cmake/dist.cmake )
include ( lua )

add_definitions ( -DVERSION=\"${DIST_VERSION}\" )

# Find PCRE
find_package ( PCRE REQUIRED )
include_directories ( ${PCRE_INCLUDE_DIRS} )

install_lua_module ( rex_pcre 
  src/common.c 
  src/pcre/lpcre.c 
  src/pcre/lpcre_f.c 
  LINK ${PCRE_LIBRARIES} )
