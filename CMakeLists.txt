# Copyright (C) 2007-2012 LuaDist.
# Created by Peter Kapec, Peter Drahos
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

project ( lrexlib C )
cmake_minimum_required ( VERSION 2.8 )
include ( cmake/dist.cmake )
include ( lua )

# Find PCRE
find_package ( PCRE )
if ( PCRE_FOUND )
  include_directories ( ${PCRE_INCLUDE_DIRS} )
endif ( )

# Find posix (RXSpencer on Windows)
if ( WIN32 AND NOT CYGWIN )
  add_definitions ( -Doff_t=_off_t )
  find_package ( RXSpencer REQUIRED )
  include_directories ( ${RXSPENCER_INCLUDE_DIRS} )
  if ( MSVC )
    add_definitions ( "-DREX_API=__declspec(dllexport)" )
  endif ( )
endif ( )

# Find Oniguruma
find_package ( Oniguruma )
if ( Oniguruma_FOUND )
  include_directories ( ${ONIGURUMA_INCLUDE_DIRS} )
endif ( )

# common lrexlib modules
set ( COMMON_SRC src/common.c )

# pcre module
if ( PCRE_FOUND )
  set ( PCRE_SRCS src/pcre/lpcre.c src/pcre/lpcre_f.c ${COMMON_SRC} )
  install_lua_module ( rex_pcre ${PCRE_SRCS} LINK ${PCRE_LIBRARIES} )
endif ( )

# posix module
set ( POSIX_SRCS src/posix/lposix.c ${COMMON_SRC} )
install_lua_module ( rex_posix ${POSIX_SRCS} LINK ${RXSPENCER_LIBRARIES} )

# oniguruma module
find_package ( Oniguruma )
if ( Oniguruma_FOUND )
  set ( ONIGURUMA_SRCS src/oniguruma/lonig.c src/oniguruma/lonig_f.c ${COMMON_SRC} )
  install_lua_module ( rex_onig ${ONIGURUMA_SRCS} LINK ${ONIGURUMA_LIBRARIES} )
endif ( )

# Install lrexlib modules
install_data ( ChangeLog LICENSE NEWS README )
install_test ( test/ )
install_doc ( doc/ )
