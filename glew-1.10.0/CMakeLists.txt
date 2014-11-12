#Specify the version being used aswell as the language
cmake_minimum_required( VERSION 3.0 )
#Name your project here
project( glew-1.10 C )

set( INCLUDE_DIR ${PROJECT_SOURCE_DIR}/include/ )
set( SOURCE_DIR ${PROJECT_SOURCE_DIR}/src )
set( RUNTIME_OUTPUT_DIR ${CMAKE_SOURCE_DIR}/bin )
set( LIBRARY_OUTPUT_DIR ${CMAKE_SOURCE_DIR}/lib )
set( ARCHIVE_OUTPUT_DIR ${CMAKE_SOURCE_DIR}/lib )

set( CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${ARCHIVE_OUTPUT_DIR} )
set( CMAKE_LIBRARY_OUTPUT_DIRECTORY ${LIBRARY_OUTPUT_DIR} )
set( CMAKE_RUNTIME_OUTPUT_DIRECTORY ${RUNTIME_OUTPUT_DIR} ) # dll and exe


if(MSVC)

    # define some macros/constants
    set( PROJECT_DEFINITIONS $<$<CONFIG:Debug>:GLEW_STATIC>
                             $<$<CONFIG:Release>:GLEW_STATIC> )
    
    # get from: http://stackoverflow.com/questions/7747857/in-cmake-how-do-i-work-around-the-debug-and-release-directories-visual-studio-2
    foreach( OUTPUTCONFIG ${CMAKE_CONFIGURATION_TYPES} )
        string( TOUPPER ${OUTPUTCONFIG} OUTPUTCONFIG )
        set( CMAKE_RUNTIME_OUTPUT_DIRECTORY_${OUTPUTCONFIG} ${RUNTIME_OUTPUT_DIR} )
        set( CMAKE_LIBRARY_OUTPUT_DIRECTORY_${OUTPUTCONFIG} ${LIBRARY_OUTPUT_DIR} )
        set( CMAKE_ARCHIVE_OUTPUT_DIRECTORY_${OUTPUTCONFIG} ${ARCHIVE_OUTPUT_DIR} )
    endforeach( OUTPUTCONFIG CMAKE_CONFIGURATION_TYPES )
    
    # add the _d on the and of binary :p
    set( CMAKE_DEBUG_POSTFIX "_d" )
    
else()

    set( PROJECT_DEFINITIONS debug GLEW_STATIC
                             optimized GLEW_STATIC )
endif()

file( GLOB PROJECT_SOURCES "${SOURCE_DIR}/*.c" ) # get all .cpp files
file( GLOB_RECURSE PROJECT_INCLUDES "${INCLUDE_DIR}/*.h" ) # get all .hpp files

include_directories( ${INCLUDE_DIR} )

# link_directories( ${PHOBOS_LIB_DIR} )

add_library( glew STATIC ${PROJECT_SOURCES} ${PROJECT_INCLUDES} )

target_compile_definitions( glew PRIVATE ${PROJECT_DEFINITIONS} )
