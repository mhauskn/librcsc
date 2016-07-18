cmake_minimum_required (VERSION 2.8.12)
project(librcsc)

include_directories(${CMAKE_CURRENT_SOURCE_DIR})
add_definitions(-DHAVE_CONFIG_H -DUSE_VECTOR)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -fPIC")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC")

find_package(Boost REQUIRED)
include_directories(${Boost_INCLUDE_DIRS})

set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

set(RCSC_DIR ${CMAKE_CURRENT_SOURCE_DIR}/rcsc)
set(SRC_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)

file(GLOB RCSC_ACTION_SOURCES ${RCSC_DIR}/action/*.cpp)
file(GLOB RCSC_ANN_SOURCES ${RCSC_DIR}/ann/*.cpp)
file(GLOB RCSC_COACH_SOURCES ${RCSC_DIR}/coach/*.cpp)
file(GLOB RCSC_COMMON_SOURCES ${RCSC_DIR}/common/*.cpp)
file(GLOB RCSC_FORMATION_SOURCES ${RCSC_DIR}/formation/*.cpp)
file(GLOB RCSC_GZ_SOURCES ${RCSC_DIR}/gz/*.cpp)
file(GLOB RCSC_MONITOR_SOURCES ${RCSC_DIR}/monitor/*.cpp)
file(GLOB RCSC_NET_SOURCES ${RCSC_DIR}/net/*.cpp)
file(GLOB RCSC_PARAM_SOURCES ${RCSC_DIR}/param/*.cpp)
file(GLOB RCSC_PLAYER_SOURCES ${RCSC_DIR}/player/*.cpp)
file(GLOB RCSC_RCG_SOURCES ${RCSC_DIR}/rcg/*.cpp)
file(GLOB RCSC_TIME_SOURCES ${RCSC_DIR}/time/*.cpp)
file(GLOB RCSC_TRAINER_SOURCES ${RCSC_DIR}/trainer/*.cpp)
file(GLOB RCSC_UTIL_SOURCES ${RCSC_DIR}/util/*.cpp)

set(RCSC_GEOM_CPP angle_deg.cpp circle_2d.cpp composite_region_2d.cpp
  convex_hull.cpp delaunay_triangulation.cpp line_2d.cpp matrix_2d.cpp
  polygon_2d.cpp ray_2d.cpp rect_2d.cpp sector_2d.cpp segment_2d.cpp
  triangle_2d.cpp triangulation.cpp vector_2d.cpp
  voronoi_diagram_original.cpp voronoi_diagram_triangle.cpp)
foreach(src ${RCSC_GEOM_CPP})
  list(APPEND RCSC_GEOM_SOURCES ${RCSC_DIR}/geom/${src})
endforeach(src ${RCSC_GEOM_CPP})
list(APPEND RCSC_GEOM_SOURCES ${RCSC_DIR}/geom/triangle/triangle.c)

add_library(rcsc_ann STATIC ${RCSC_ANN_SOURCES})
add_library(rcsc_geom STATIC ${RCSC_GEOM_SOURCES})
add_library(rcsc_gz STATIC ${RCSC_GZ_SOURCES})
add_library(rcsc_net STATIC ${RCSC_NET_SOURCES})
add_library(rcsc_param STATIC ${RCSC_PARAM_SOURCES})
add_library(rcsc_rcg STATIC ${RCSC_RCG_SOURCES})
add_library(rcsc_time STATIC ${RCSC_TIME_SOURCES})
add_library(rcsc_agent STATIC ${RCSC_ACTION_SOURCES}
  ${RCSC_COACH_SOURCES} ${RCSC_COMMON_SOURCES}
  ${RCSC_FORMATION_SOURCES} ${RCSC_MONITOR_SOURCES}
  ${RCSC_PLAYER_SOURCES} ${RCSC_TRAINER_SOURCES} ${RCSC_UTIL_SOURCES})
add_executable(rclmscheduler ${SRC_DIR}/scheduler.cpp)
add_executable(rcmltableprinter ${SRC_DIR}/tableprinter.cpp)
add_executable(rcg2txt ${SRC_DIR}/rcg2txt.cpp)
add_executable(rcgrenameteam ${SRC_DIR}/rcgrenameteam.cpp)
add_executable(rcgresultprinter ${SRC_DIR}/resultprinter.cpp)
add_executable(rcgreverse ${SRC_DIR}/rcgreverse.cpp)
add_executable(rcgverconv ${SRC_DIR}/rcgverconv.cpp)
add_executable(rcgversion ${SRC_DIR}/rcgversion.cpp)

# Geom library needs several defines to compile correctly
target_compile_definitions(rcsc_geom PUBLIC TRILIBRARY REDUCED CDT_ONLY VOID=int REAL=double)

add_dependencies(rcg2txt rcsc_gz rcsc_rcg)
add_dependencies(rcgrenameteam rcsc_gz rcsc_rcg)
add_dependencies(rcgresultprinter rcsc_gz rcsc_rcg)
add_dependencies(rcgreverse rcsc_gz rcsc_rcg)
add_dependencies(rcgverconv rcsc_gz rcsc_rcg)
add_dependencies(rcgversion rcsc_gz)

target_link_libraries(rcg2txt rcsc_gz rcsc_rcg z)
target_link_libraries(rcgrenameteam rcsc_gz rcsc_rcg z)
target_link_libraries(rcgresultprinter rcsc_gz rcsc_rcg z)
target_link_libraries(rcgreverse rcsc_gz rcsc_rcg z)
target_link_libraries(rcgverconv rcsc_gz rcsc_rcg z)
target_link_libraries(rcgversion rcsc_gz z)

target_include_directories(rcsc_ann PUBLIC ${RCSC_DIR}/ann)
target_include_directories(rcsc_geom PUBLIC ${RCSC_DIR}/geom ${RCSC_DIR}/geom/triangle)
target_include_directories(rcsc_gz PUBLIC ${RCSC_DIR}/gz)
target_include_directories(rcsc_net PUBLIC ${RCSC_DIR}/net)
target_include_directories(rcsc_param PUBLIC ${RCSC_DIR}/param)
target_include_directories(rcsc_rcg PUBLIC ${RCSC_DIR}/rcg)
target_include_directories(rcsc_time PUBLIC ${RCSC_DIR}/time)
target_include_directories(rcsc_agent PUBLIC ${RCSC_DIR}/action
  ${RCSC_DIR}/coach ${RCSC_DIR}/common ${RCSC_DIR}/formation
  ${RCSC_DIR}/monitor ${RCSC_DIR}/player ${RCSC_DIR}/trainer
  ${RCSC_DIR}/util)
