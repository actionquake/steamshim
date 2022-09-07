cmake_minimum_required(VERSION 3.16)

project(AQtion)

set(STEAMWORKS_PATH "sdk" CACHE PATH "Path to Steamworks folder")
set(GAME_LAUNCH_NAME "q2pro" CACHE STRING "Game launch name")
set(STEAMWORKS "sdk")
option(DEBUG "Debug mode" OFF)

set(SOURCES
	steamshim_parent.cpp
)

include_directories(${STEAMWORKS_PATH}/public)

add_definitions(-DGAME_LAUNCH_NAME="${GAME_LAUNCH_NAME}")

IF(DEBUG)
	add_definitions(-DSTEAMSHIM_DEBUG)
ENDIF()

IF(APPLE)
	find_library(steamworks NAMES steam_api steam_api64 PATHS ${STEAMWORKS_PATH}/redistributable_bin/osx32)
ELSEIF(WIN32)
	find_library(steamworks NAMES steam_api steam_api64 PATHS ${STEAMWORKS_PATH}/redistributable_bin/win64)
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -mwindows")
ELSEIF(UNIX AND NOT APPLE)
	find_library(steamworks NAMES steam_api steam_api64 PATHS ${STEAMWORKS_PATH}/redistributable_bin/linux64)
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -m64")
ENDIF()

add_executable(steamshim
	${SOURCES}
)

set_target_properties(steamshim PROPERTIES LINK_FLAGS "-Wl,-rpath,$ORIGIN -no-pie")
target_link_libraries(steamshim ${steamworks})
