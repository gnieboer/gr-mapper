# Copyright 2011,2012 Free Software Foundation, Inc.
#
# This file is part of GNU Radio
#
# GNU Radio is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 3, or (at your option)
# any later version.
#
# GNU Radio is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with GNU Radio; see the file COPYING.  If not, write to
# the Free Software Foundation, Inc., 51 Franklin Street,
# Boston, MA 02110-1301, USA.

########################################################################
# Setup library
########################################################################
include(GrPlatform) #define LIB_SUFFIX

#include_directories(${Boost_INCLUDE_DIR})
#link_directories(${Boost_LIBRARY_DIRS})
list(APPEND mapper_sources
    constellation.cc
    mapper_impl.cc
    demapper_impl.cc
    demapper_soft_impl.cc
    mapper_msg_impl.cc
    demapper_msg_impl.cc
    preamble_insert_bb_impl.cc
    preamble_sync_cc_impl.cc
    pream_sync_correlator.cc
    preamble_generator.cc
    preamble_sync_demapper_impl.cc
    preamble_sync_demapper_hard_impl.cc
    preamble_strip_impl.cc
    timeslot_demux_impl.cc
)

set(mapper_sources "${mapper_sources}" PARENT_SCOPE)
if(NOT mapper_sources)
	MESSAGE(STATUS "No C++ sources... skipping lib/")
	return()
endif(NOT mapper_sources)


add_library(gnuradio-mapper SHARED ${mapper_sources})
target_link_libraries(gnuradio-mapper ${Boost_LIBRARIES} gnuradio::gnuradio-runtime )
target_include_directories(gnuradio-mapper
    PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../include>
    PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/>
    PUBLIC $<INSTALL_INTERFACE:include>
  )
set_target_properties(gnuradio-mapper PROPERTIES DEFINE_SYMBOL "gnuradio_mapper_EXPORTS")

if(APPLE)
    set_target_properties(gnuradio-mapper PROPERTIES
        INSTALL_NAME_DIR "${CMAKE_INSTALL_PREFIX}/lib"
    )
endif(APPLE)

########################################################################
# Install built library files
########################################################################
include(GrMiscUtils)
GR_LIBRARY_FOO(gnuradio-mapper)

########################################################################
# Build and register unit test
########################################################################
include(GrTest)

list(APPEND test_mapper_sources
)
# Anything we need to link to for the unit tests go here
list(APPEND GR_TEST_TARGET_DEPS gnuradio-mapper)

if(NOT test_mapper_sources)
    MESSAGE(STATUS "No C++ unit tests... skipping")
    return()
endif(NOT test_mapper_sources)

foreach(qa_file ${test_mapper_sources})
    GR_ADD_CPP_TEST("mapper_${qa_file}"
        ${CMAKE_CURRENT_SOURCE_DIR}/${qa_file}
    )
endforeach(qa_file)

GR_ADD_TEST(test_mapper test-mapper)
########################################################################
# Print summary
########################################################################
message(STATUS "Using install prefix: ${CMAKE_INSTALL_PREFIX}")
message(STATUS "Building for version: ${VERSION} / ${LIBVER}")

#add_executable(test-mapper ${test_mapper_sources})
#
#target_link_libraries(
#  test-mapper
#  ${GNURADIO_RUNTIME_LIBRARIES}
#  ${Boost_LIBRARIES}
#  ${CPPUNIT_LIBRARIES}
#  gnuradio-mapper
#)
#
#GR_ADD_TEST(test_mapper test-mapper)
