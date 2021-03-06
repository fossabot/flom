#
# Copyright 2018 coord.e
#
# This file is part of Flom.
#
# Flom is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# Flom is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with Flom.  If not, see <http://www.gnu.org/licenses/>.
#

cmake_minimum_required(VERSION 3.2)
cmake_policy(SET CMP0048 NEW)

project(flom VERSION 0.1 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

if(NOT CONFIG)
  set(CONFIG Release)
endif()

set(PROTO_DIR ./proto)
set(PROTO_INCLUDE_DIR ${CMAKE_CURRENT_BINARY_DIR})

include(FindProtobuf)
set(Protobuf_IMPORT_DIRS ${PROTO_DIR})
find_package(Protobuf REQUIRED)
include_directories(${PROTOBUF_INCLUDE_DIR})
file(GLOB PROTO_FILES ${PROTO_DIR}/*.proto)
protobuf_generate_cpp(PROTO_SRC PROTO_HEADER ${PROTO_FILES})
add_library(flom_proto ${PROTO_HEADER} ${PROTO_SRC})
set_target_properties(flom_proto PROPERTIES POSITION_INDEPENDENT_CODE TRUE)
target_link_libraries(flom_proto ${PROTOBUF_LIBRARIES})
include_directories(SYSTEM ${PROTO_INCLUDE_DIR})

set(JSON_BuildTests OFF CACHE INTERNAL "")
add_subdirectory(third_party/nlohmann_json)
add_subdirectory(third_party/rapidcheck)
include_directories(SYSTEM third_party/nlohmann_json/single_include)
include_directories(SYSTEM third_party/rapidcheck/include)
include_directories(SYSTEM third_party/rapidcheck/extras/boost_test/include)

include(cmake/clang-format.cmake)
include(cmake/clang-tidy.cmake)
include(cmake/compile-options.cmake)
include(cmake/bin.cmake)
include(cmake/test.cmake)

add_subdirectory(include/)
include_directories(include/)

add_subdirectory(lib)
add_subdirectory(bin)

enable_testing()
add_subdirectory(test)
