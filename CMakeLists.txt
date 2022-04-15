###
# Compile-time timestamping library
#

#CMake 3.12 made using OBJECT libraries much nicer, so we use that.
cmake_minimum_required(VERSION 3.12)

# Compile the library that contains the global variables
add_library( timestamp OBJECT timestamp.c)


##########################################################
# Generate the compile-time data.
# Each data item is saved as a CMake variable, that is passed
# as a preprocessor define to the compiler.
#
# If you add new data items, they must be added to timestamp.c,
# timestamp.h and the target_compile_definitions below.


# Set CMake variable BUILD_TIME to 'now'.
# This variable drives the CMake auto-update scripts, so
# this must not be commented out!
string(TIMESTAMP BUILD_TIME "%Y-%m-%d %H:%M:%S" UTC)



# optionaly, get also the git hash & refspec using
# Ryan Pavlik's CMake scripts at
# https://github.com/rpavlik/cmake-modules
#include(GetGitRevisionDescription.cmake)
#get_git_head_revision(GIT_REFSPEC GIT_SHA1)


##########################################################
# Only building of the timestamp library below this point.
# No configuration should be necessary.
#

target_compile_definitions(
	timestamp
	PRIVATE -DBUILD_TIME="${BUILD_TIME}"
	#PRIVATE -DGIT_REFSPEC="${GIT_REFSPEC}"
	#PRIVATE -DGIT_SHA1="${GIT_SHA1}"
	PRIVATE -DBUILD_NO="${CPACK_PACKAGE_VERSION_MAJOR}.${CPACK_PACKAGE_VERSION_MINOR}.${CPACK_PACKAGE_VERSION_PATCH}"
)
target_include_directories( timestamp
	PUBLIC
		$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
)

#Add a dummy target that removes the CMake variable BUILD_TIME from CMake's cache.
#this forces CMake to be-rerun when we hit this target.
add_custom_target(
	clear_cache
	COMMAND ${CMAKE_COMMAND} -U BUILD_TIME ${CMAKE_BINARY_DIR}
)

#Have the cache clearing be run before trying to build the timestamp library.
#This (I think) is the same as a PRE_BUILD custom_command. But PRE_BUILD is
#available for VS generators only, on others it is synonymous to PRE_LINK,
#i.e. "post compile"
add_dependencies(timestamp clear_cache)

