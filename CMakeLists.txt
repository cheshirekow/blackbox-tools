cmake_minimum_required(VERSION 2.8.11)
project(blackbox-tools)

find_package(PkgConfig)
find_package(Threads)

set(decoder_src )

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=gnu99 -Wall -pedantic -Wextra -Wshadow")

pkg_check_modules(freetype2 freetype2)
pkg_check_modules(cairo cairo)

include_directories(src)

add_library(blackbox_common STATIC
            src/blackbox_fielddefs.c
            src/decoders.c
            src/parser.c
            src/platform.c
            src/stream.c
            src/tools.c
            src/units.c)

set(COMMON_LIBS blackbox_common
               ${CMAKE_THREAD_LIBS_INIT}
               m)

add_executable(blackbox_decoder
               src/battery.c
               src/blackbox_decode.c
               src/gpxwriter.c
               src/imu.c
               src/stats.c)
target_link_libraries(blackbox_decoder ${COMMON_LIBS})

install(TARGETS blackbox_decoder
        RUNTIME DESTINATION bin)

add_executable(encoder_testbed
               src/encoder_testbed.c
               src/encoder_testbed_io.c)
target_link_libraries(encoder_testbed ${COMMON_LIBS})

if((freetype2_FOUND) AND (cairo_FOUND))
  include_directories(${freetype2_INCLUDE_DIRS}
                      ${cairo_INCLUDE_DIRS})

  add_executable(blackbox_render
                 src/blackbox_render.c
                 src/datapoints.c
                 src/embeddedfont.c
                 src/expo.c
                 src/imu.c)

  target_link_libraries(blackbox_render ${COMMON_LIBS}
                                        ${cairo_LDFLAGS}
                                        ${freetype2_LDFLAGS})
  install(TARGETS blackbox_render
          RUNTIME DESTINATION bin)
endif()


