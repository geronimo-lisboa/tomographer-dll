cmake_minimum_required(VERSION 2.8)

project(tomographer-dll)

set(ITK_DIR "C:/libs/itk/ITK_bin")
set(VTK_DIR "C:/libs/VTK-build")

set(D3D_LIBRARY "d3d9.lib")#necessário pra pegar o tamanho da memória da GPU.
set(WBEM_LIBRARY "wbemuuid.lib")#necessário pra pegar o tamanho da memória da GPU.

# Find ITK.
find_package(ITK REQUIRED)
include(${ITK_USE_FILE})
# Find VTK
find_package(VTK REQUIRED)
include(${VTK_USE_FILE})
#Pega os arquivos e poe no Medivolume, com o Medivolume como dll.
file(GLOB my_source_files "*.h" "*.cpp")
add_library(tomographer-dll SHARED ${my_source_files} )
#Bota a ITK, VTK e SDL2
target_link_libraries(tomographer-dll ${ITK_LIBRARIES} ${VTK_LIBRARIES} )
target_link_libraries(tomographer-dll ${D3D_LIBRARY} ${WBEM_LIBRARY})

#boost.org
SET (BOOST_INCLUDE_DIR "C:/libs/boost_1_64_0/stage/lib")
include_directories(${BOOST_INCLUDE_DIR})
target_link_libraries(tomographer-dll "C:/libs/boost_1_64_0/stage/lib/libboost_signals-vc140-mt-1_64.lib")
target_link_libraries(tomographer-dll "C:/libs/boost_1_64_0/stage/lib/libboost_exception-vc140-mt-gd-1_64.lib")

find_package(OpenGL REQUIRED)
target_link_libraries(tomographer-dll  ${OPENGL_LIBRARIES})

set_property(TARGET tomographer-dll APPEND PROPERTY LINK_FLAGS /LARGEADDRESSAWARE)
add_definitions(-D_SCL_SECURE_NO_WARNINGS)

#Remoção de flags q n podem existir pra CLR funcionar
if(CMAKE_CXX_FLAGS_DEBUG MATCHES "/RTC1")
   string(REPLACE "/RTC1" " " CMAKE_CXX_FLAGS_DEBUG 
"${CMAKE_CXX_FLAGS_DEBUG}")
   message(STATUS ${PROJECT_NAME}  " CMAKE_CXX_FLAGS_DEBUG removing /RTC1")
endif()
if(CMAKE_CXX_FLAGS MATCHES "/EHsc")
   string(REPLACE "/EHsc" "" CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}")
   message(STATUS ${PROJECT_NAME}  " CMAKE_CXX_FLAGS removing /EHsc")
endif()
SET_TARGET_PROPERTIES(${PROJECT_NAME} PROPERTIES COMPILE_FLAGS "/clr")  
