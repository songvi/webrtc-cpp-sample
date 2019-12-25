cmake_minimum_required(VERSION 3.1)
project(webrtc-cpp-sample)

set(CMAKE_CXX_STANDARD 14)
set(LIBWEBRTC_PATH "/home/tran/webrtc/webrtc-cpp-sample/local")
include_directories("${LIBWEBRTC_PATH}/include")
include_directories("${LIBWEBRTC_PATH}/include/third_party/abseil-cpp")
include_directories("/usr/include/jsoncpp")

link_directories("${LIBWEBRTC_PATH}/lib")


# Linux
find_package(PkgConfig)

# set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -stdlib=libc++")
# set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -D_GLIBCXX_USE_CXX11_ABI=0")

list(APPEND webrtc_libs "-Wl,--start-group")
file(READ ${LIBWEBRTC_PATH}/exports_libwebrtc.txt webrtc_read_tmp)
string(REGEX REPLACE "lib([^.]+).a[\r\n]*" "\\1;" webrtc_read_tmp "${webrtc_read_tmp}")
list(APPEND webrtc_libs "${webrtc_read_tmp}")
list(APPEND webrtc_libs "-Wl,--end-group")

pkg_search_module(X11 REQUIRED x11)
list(APPEND webrtc_libs ${X11_LIBRARIES})
list(APPEND webrtc_libs "dl")
list(APPEND webrtc_libs "pthread")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DWEBRTC_LINUX=1 -DWEBRTC_POSIX=1")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -W -Wall -Wno-unused-parameter")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O0 -g")

# Disable RTTI
set_source_files_properties(main.cc PROPERTIES
  COMPILE_FLAGS " -fno-rtti "
  )

file(
        GLOB_RECURSE
        source_files
        ./*.cc ./*.h
)

add_executable(sample
  ${source_files}
  )

target_link_libraries(sample
  ${webrtc_libs}
  )
