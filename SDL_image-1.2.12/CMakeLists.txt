#Specify the version being used aswell as the language
cmake_minimum_required( VERSION 3.0 )
#Name your project here
project( SDL_Image-1.2.12 C )

set( INCLUDE_DIR ${CMAKE_SOURCE_DIR} )
set( SOURCE_DIR ${CMAKE_SOURCE_DIR} )
set( EXTERNAL_INCLUDE_DIR ${CMAKE_SOURCE_DIR}/VisualC/external/include )

set( RUNTIME_OUTPUT_DIR ${CMAKE_SOURCE_DIR}/bin )
set( LIBRARY_OUTPUT_DIR ${CMAKE_SOURCE_DIR}/lib )
set( ARCHIVE_OUTPUT_DIR ${CMAKE_SOURCE_DIR}/lib )

set( CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${ARCHIVE_OUTPUT_DIR} )
set( CMAKE_LIBRARY_OUTPUT_DIRECTORY ${LIBRARY_OUTPUT_DIR} )
set( CMAKE_RUNTIME_OUTPUT_DIRECTORY ${RUNTIME_OUTPUT_DIR} ) # dll and exe

set( PHOBOS_DEPS_DIR "" CACHE PATH "Path to phobos dependencies dir" )
set( PHOBOS_DEPS_LIB_DIR ${PHOBOS_DEPS_DIR}/lib )
set( SDL_INCLUDE_DIR ${PHOBOS_DEPS_DIR}/SDL-1.2.15/include )


if ( PHOBOS_DEPS_DIR STREQUAL "" )
    message(FATAL_ERROR "The path to phobos dependencies is not properly configured!")
endif()

if(MSVC)

    set( SDL_LIB $<$<CONFIG:Debug>: SDL_d>
                 $<$<CONFIG:Release>: SDL> )
                 
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
    set( SDL_LIB debug SDL_d
                 optimized SDL )
endif()

# define some macros/constants
set( PROJECT_DEFINITIONS SDLIMAGE_EXPORTS 
                         LOAD_BMP 
                         LOAD_GIF 
                         LOAD_JPG 
                         LOAD_JPG_DYNAMIC="libjpeg-8.dll"
                         LOAD_LBM
                         LOAD_PCX
                         LOAD_PNG
                         LOAD_PNG_DYNAMIC="libpng15-15.dll"
                         LOAD_PNM
                         LOAD_TGA
                         LOAD_TIF
                         LOAD_TIF_DYNAMIC="libtiff-5.dll"
                         LOAD_WEBP
                         LOAD_WEBP_DYNAMIC="libwebp-2.dll"
                         LOAD_XPM
                         LOAD_XV
                         PNG_USE_DLL
                         ZLIB_DLL )

file( GLOB PROJECT_SOURCES "${SOURCE_DIR}/IMG*.c" ) # get all .c files
file( GLOB PROJECT_INCLUDES "${INCLUDE_DIR}/SDL_image.h" ) # get all .h files

include_directories( ${INCLUDE_DIR} 
                     ${EXTERNAL_INCLUDE_DIR}
                     ${SDL_INCLUDE_DIR} )

link_directories( ${PHOBOS_DEPS_LIB_DIR} )

add_library( SDL_Image SHARED ${PROJECT_SOURCES} ${PROJECT_INCLUDES} "version.rc")

target_link_libraries( SDL_Image ${SDL_LIB} )
target_compile_definitions( SDL_Image PRIVATE ${PROJECT_DEFINITIONS} )
