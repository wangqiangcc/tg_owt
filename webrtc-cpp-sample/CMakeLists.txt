cmake_minimum_required(VERSION 3.16)
project(webrtc-cpp-sample)

set(CMAKE_CXX_STANDARD 17)

#include(${CMAKE_CURRENT_SOURCE_DIR}/../cmake/libabsl.cmake)
#include(${CMAKE_CURRENT_SOURCE_DIR}/../cmake/libcrc32c.cmake)

link_directories("${CMAKE_CURRENT_BINARY_DIR}/../libcrc32c.dir/${CMAKE_BUILD_TYPE}" "${CMAKE_CURRENT_BINARY_DIR}/../libabsl.dir/${CMAKE_BUILD_TYPE}")

list(APPEND webrtc_libs "tg_owt")

if(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
  # macOS
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DWEBRTC_MAC=1 -DWEBRTC_POSIX=1")
  find_library(FOUNDATION Foundation)
  list(APPEND webrtc_libs ${FOUNDATION})

elseif(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
  # Linux
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DWEBRTC_LINUX=1 -DWEBRTC_POSIX=1")

  # set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -stdlib=libc++")
  # set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -D_GLIBCXX_USE_CXX11_ABI=0")
  list(APPEND webrtc_libs "pthread")

elseif(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DWEBRTC_WIN=1 -DNOMINMAX")
list(APPEND webrtc_libs "libabsl.lib" "libcrc32c.lib" "ws2_32.lib" "Secur32.lib" "Iphlpapi.lib" "Winmm.lib")
endif()

if (NOT WIN32)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -W -Wall -Wno-unused-parameter")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O0 -g")
endif()

if(MSVC)
    add_compile_options(
        $<$<CONFIG:Debug>:/MTd>
        $<$<CONFIG:Release>:/MT>
    )
endif()

add_executable(sample
  main.cpp
  )


target_link_libraries(sample
  ${webrtc_libs}
  )
