cmake_minimum_required (VERSION 3.2)

set (PACKAGE            "libmad")
set (PACKAGE_NAME       "MPEG Audio Decoder 0.15.1b")
set (PACKAGE_TARNAME    "libmad")
set (PACKAGE_VERSION    "0.15.1b")
set (VERSION            "0.15.1b")
set (STDC_HEADERS       1)

#set (OPT_SPPED          1)

project (mad)
include (CheckIncludeFiles)
include (CheckFunctionExists)
include (CheckLibraryExists)
include (CheckTypeSize)

check_include_files (assert.h HAVE_ASSERT_H)
check_include_files (dlfcn.h HAVE_DLFCN_H)
check_include_files (errno.h HAVE_ERRNO_H)
check_include_files (fcntl.h HAVE_FCNTL_H)
check_include_files (inttypes.h HAVE_INTTYPES_H)
check_include_files (limits.h HAVE_LIMITS_H)
check_include_files (memory.h HAVE_MEMORY_H)
check_include_files (stdint.h HAVE_STDINT_H)
check_include_files (stdlib.h HAVE_STDLIB_H)
check_include_files (string.h HAVE_STRING_H)
check_include_files (strings.h HAVE_STRINGS_H)
check_include_files (unistd.h HAVE_UNISTD_H)
check_include_files (sys/stat.h HAVE_SYS_STAT_H)
check_include_files (sys/types.h HAVE_SYS_TYPES_H)
check_include_files (sys/wait.h HAVE_SYS_WAIT_H)

check_function_exists (fcntl HAVE_FCNTL)
check_function_exists (fork HAVE_FORK)
check_function_exists (pipe HAVE_PIPE)
check_function_exists (waitpid HAVE_WAITPID)

check_type_size (int SIZEOF_INT)
check_type_size (long SIZEOF_LONG)
check_type_size ("long long" SIZEOF_LONG_LONG)

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/cmake/config.h.in ${CMAKE_CURRENT_BINARY_DIR}/config.h)

include_directories (${CMAKE_CURRENT_BINARY_DIR})

set(mad_root_dir ${CMAKE_CURRENT_LIST_DIR})
set(mad_source_files 
    ${mad_root_dir}/huffman.c
    ${mad_root_dir}/synth.c
    ${mad_root_dir}/layer12.c
    ${mad_root_dir}/layer3.c
    ${mad_root_dir}/fixed.c
    ${mad_root_dir}/frame.c
    ${mad_root_dir}/decoder.c
    ${mad_root_dir}/version.c
    ${mad_root_dir}/bit.c
    ${mad_root_dir}/timer.c
    ${mad_root_dir}/stream.c
)

set(mad_cflags "-DHAVE_CONFIG_H")

if(BUILD MATCHES ANDROID_armeabi-v7a)
    set(mad_cflags "${mad_cflags} -DFPM_ARM")
elseif(BUILD MATCHES ANDROID_arm64-v8a)
    set(mad_cflags "${mad_cflags} -DFPM_64BIT")
else()
    set(mad_cflags "${mad_cflags} -DFPM_DEFAULT")
endif()

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${mad_cflags}")

include_directories(${CMAKE_CURRENT_SOURCE_DIR})

add_library (mad STATIC ${mad_source_files})

install (TARGETS mad DESTINATION lib)
install (FILES mad.h DESTINATION include/mad)
