cmake_minimum_required(VERSION 2.8)

###### macro for install

macro (direct_install target)
get_target_property (target_location ${target} LOCATION)
file (TO_CMAKE_PATH "${ARGN}" destination_path)
string (REGEX REPLACE "(.*/)?([^/]+)$" \\2 ${target_location} target_basename)

#message ("Direct install ${target} to ${destination_path}")
add_custom_command (TARGET ${target} POST_BUILD
                    # This is pretty assinine, but I can't find a copy command.
                    COMMAND ${CMAKE_COMMAND} ARGS -E make_directory "${destination_path}"
                    COMMAND ${CMAKE_COMMAND} ARGS -E copy_if_different ${target_location} "${destination_path}/${target_basename}"
                    COMMENT "Installing target ${target} to ${destination}")
                    
install (TARGETS ${target} DESTINATION ${destination_path}) 
endmacro (direct_install target)

#######################
SET(CMAKE_BUILD_TYPE "Release")

SET(CMAKE_VERBOSE_MAKEFILE 1)
PROJECT(p563)

INCLUDE_DIRECTORIES(./include)

file(GLOB src_file ./source/*.c)
file(GLOB header_file ./include/*.h)

message(${src_file})

add_executable(mos_feature ${src_file} ${header_file} ./src/p563_feature.c)
target_link_libraries(mos_feature m)
#direct_install(mos_feature ${CMAKE_CURRENT_SOURCE_DIR/../bin/})
