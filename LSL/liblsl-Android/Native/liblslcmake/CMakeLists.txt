# Redirect file to the liblsl src

cmake_minimum_required (VERSION 2.6)
project (liblsl)

# LSL sources + Local boost

set(SOURCES_ROOT_DIR "../../../liblsl")
message("${SOURCES_ROOT_DIR}/src/")
File( GLOB sources
    "./${SOURCES_ROOT_DIR}/src/*.cpp"
    "./${SOURCES_ROOT_DIR}/src/pugixml/pugixml.cpp"
    "./${SOURCES_ROOT_DIR}/external/src/atomic/src/*.cpp"
    ".//${SOURCES_ROOT_DIR}/external/src/chrono/src/*.cpp"
    ".//${SOURCES_ROOT_DIR}/external/src/filesystem/src/*.cpp"
    ".//${SOURCES_ROOT_DIR}/external/src/serialization/src/*.cpp"
    ".//${SOURCES_ROOT_DIR}/external/src/system/src/*.cpp"
    ".//${SOURCES_ROOT_DIR}/external/src/thread/src/*.cpp"
    ".//${SOURCES_ROOT_DIR}/external/src/thread/src/pthread/*.cpp"
    )

# Header files for boost
# The dependent LSL header files are in ./include/ but are referenced as such.
include_directories("${SOURCES_ROOT_DIR}/external/")

# List out all of the source files that are going to be compiled in.
message(${sources})

# Compile Option Flags
add_compile_options(-std=c++11)
add_compile_options(-frtti)
add_compile_options(-fexceptions)

# If Android log is needed for debugging link in the Android Log lib
find_library( log-lib log )
add_library (lsl SHARED ${sources} )

# Linker options-Wl, -z muldef for boost thread. This causes it to use the first declaration of
# symbols that have multiple
target_link_libraries (lsl ${log-lib}
    "-Wl, -z muldefs"
    )


# add_subdirectory( ${SOURCES_ROOT_DIR} )