#Specify the version being used aswell as the language
cmake_minimum_required( VERSION 3.0 )
#Name your project here
project( DeimosDeps CXX )

add_subdirectory(glew-1.10.0)
add_subdirectory(SDL_image-1.2.12)

