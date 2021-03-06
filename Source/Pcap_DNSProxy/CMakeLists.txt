# This code is part of Pcap_DNSProxy
# A local DNS server based on WinPcap and LibPcap
# Copyright (C) 2012-2016 Chengr28
# 
# This program is free software; you can redistribute it and/or
# modify it under the terms of the GNU General Public License
# as published by the Free Software Foundation; either
# version 2 of the License, or (at your option) any later version.
# 
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
# 
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 675 Mass Ave, Cambridge, MA 02139, USA.


# CMake minimum version
IF(PLATFORM_OPENWRT)
	cmake_minimum_required(VERSION 2.8.12.2)
ELSE(PLATFORM_OPENWRT)
	cmake_minimum_required(VERSION 3.0.2)
ENDIF(PLATFORM_OPENWRT)

# Project name and build type
project(Pcap_DNSProxy)
set(CMAKE_BUILD_TYPE "Release")

# All source files
set(MAIN_SRC_FILES
		"Base.h"
		"Capture.h"
		"Configuration.h"
		"DNSCurve.h"
		"Initialization.h"
		"Main.h"
		"Monitor.h"
		"Network.h"
		"PacketData.h"
		"Platform.h"
		"PrintLog.h"
		"Process.h"
		"Protocol.h"
		"Proxy.h"
		"Service.h"
		"Structures.h"
		"Base.cpp"
		"Capture.cpp"
		"Configuration.cpp"
		"DNSCurve.cpp"
		"Initialization.cpp"
		"Main.cpp"
		"Monitor.cpp"
		"Network.cpp"
		"PacketData.cpp"
		"PrintLog.cpp"
		"Process.cpp"
		"Protocol.cpp"
		"Proxy.cpp"
		"ReadHosts.cpp"
		"ReadIPFilter.cpp"
		"ReadParameter.cpp"
		"Service.cpp")

# Libraries linking(Part 1)
find_package(Threads REQUIRED)

# Compiling and linking flags
## C++ 11 standard definitions
IF(CMAKE_VERSION VERSION_LESS "3.1")
	add_definitions(-std=c++11)
ELSE(CMAKE_VERSION VERSION_LESS "3.1")
	set(CMAKE_CXX_STANDARD 11)
ENDIF(CMAKE_VERSION VERSION_LESS "3.1")
## Platform definitions
IF(PLATFORM_OPENWRT)
	add_definitions(-DPLATFORM_OPENWRT)
ELSE(PLATFORM_OPENWRT)
	add_definitions(-flto -fpic)
ENDIF(PLATFORM_OPENWRT)
## Library definitions
IF(ENABLE_PCAP)
	add_definitions(-DENABLE_PCAP)
ENDIF(ENABLE_PCAP)
IF(ENABLE_LIBSODIUM)
	add_definitions(-DENABLE_LIBSODIUM)
ENDIF(ENABLE_LIBSODIUM)

# Link to static libraries if needed
IF(STATIC_LIB)
	SET(CMAKE_FIND_LIBRARY_SUFFIXES ".a")
	SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -s -static -Wl,--whole-archive -lpthread -Wl,--no-whole-archive")
ENDIF(STATIC_LIB)

# Executable file name
add_executable(Pcap_DNSProxy ${MAIN_SRC_FILES})
IF(PLATFORM_OPENWRT)
	INSTALL(TARGETS Pcap_DNSProxy RUNTIME DESTINATION sbin)
ENDIF(PLATFORM_OPENWRT)

# Libraries linking(Part 2)
target_link_libraries(Pcap_DNSProxy ${CMAKE_THREAD_LIBS_INIT})
IF(ENABLE_PCAP)
	target_link_libraries(Pcap_DNSProxy pcap)
ENDIF(ENABLE_PCAP)
IF(ENABLE_LIBSODIUM)
	target_link_libraries(Pcap_DNSProxy sodium)
ENDIF(ENABLE_LIBSODIUM)
