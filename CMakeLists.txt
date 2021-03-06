#CMAKE FOR GLFW WINDOWING + OPENGL DEMOS

cmake_minimum_required(VERSION 3.0)

#if(COMMAND cmake_policy)
#  cmake_policy(SET CMP0003 OLD)
#endif(COMMAND cmake_policy)

if(SRC)
  get_filename_component(projectname ${SRC} NAME_WE)
else()
  set(projectname UaR_OpenGL)
endif(SRC)

SET(CMAKE_CXX_COMPILER "gcc")
SET(CMAKE_C_COMPILER "gcc")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -stdlib=libc++")

project(${projectname})
set( EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin )

message( "*****************" )
message( "system name is: " ${CMAKE_SYSTEM} )
message( "system processor is: " ${CMAKE_SYSTEM_PROCESSOR} )
message( "*****************" )

if(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
  SET( LINUX 1)
  SET( APPLE 0)
elseif(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
  SET( LINUX 0)
  SET( APPLE 1 )
elseif(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
  SET( WINDOWS 1)
endif()

find_package(OpenGL REQUIRED)

if(APPLE OR LINUX)
  SET (CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_SOURCE_DIR}/cmake/modules)
  message ("MODULE PATH:" ${CMAKE_MODULE_PATH}) #MODULE_PATH})
  find_package(GLEW)
  if (GLEW_FOUND)
    message ("GLEW LIBRARY: " ${GLEW_LIBRARY})
    message ("GLEW INCLUDE DIR: " ${GLEW_INCLUDE_DIR})
  endif()
endif(APPLE OR LINUX)

#open gl libraries includes glu . . .
set( GLFW_BUILD_EXAMPLES OFF CACHE BOOL "turn off build examples")
add_subdirectory(glfw)
link_directories(${PROJECT_BINARY_DIR})
link_directories(${GLFW_BINARY_DIR})

if(WINDOWS)
link_directories(${PROJECT_SOURCE_DIR}/win/glew/lib/)
endif(WINDOWS)

if(SRC)
  get_filename_component(target ${SRC} NAME_WE)
  add_executable(${target} ${SRC} )
	if(WINDOWS)
		target_link_libraries(${target} glfw glew32s ${GLFW_LIBRARIES} )
		else()
		target_link_libraries(${target} glfw ${GLEW_LIBRARY} ${GLFW_LIBRARIES} )
	endif(WINDOWS)
  #target_link_libraries(${target} ${GLEW_LIBRARY} ${GLUT_LIBRARY} ${OPENGL_LIBRARIES})
else()
  file( GLOB EXAMPLES examples/*.cpp )
  foreach( srcfile ${EXAMPLES} )
    get_filename_component(target ${srcfile} NAME_WE)
    if(target)
    add_executable(${target} ${srcfile} )
	if(WINDOWS)
		target_link_libraries(${target} glfw glew32s ${GLFW_LIBRARIES} )
		else()
		target_link_libraries(${target} glfw ${GLEW_LIBRARY} ${GLFW_LIBRARIES} )
	endif(WINDOWS)

	endif(target)
# target_link_libraries(${target} ${GLEW_LIBRARY} ${GLUT_LIBRARY} ${OPENGL_LIBRARIES})
  endforeach( srcfile ${EXAMPLES} )
endif()

message ("ROOT PATH: " ${CMAKE_FIND_ROOT_PATH})
include_directories(/usr/include/)
include_directories(/usr/local/include/)
include_directories(${PROJECT_SOURCE_DIR}/include/)
include_directories(${PROJECT_SOURCE_DIR})
include_directories(${PROJECT_SOURCE_DIR}/glfw/include/)

if(WINDOWS)
include_directories(${PROJECT_SOURCE_DIR}/win/glew/include/)
endif(WINDOWS)

if(APPLE)
include_directories(${GLEW_INCLUDE_DIR})
endif(APPLE)

#add_subdirectory(glfw)
#link_directories(${PROJECT_BINARY_DIR})
#link_directories(${GLFW_BINARY_DIR})
#target_link_libraries(${target} glfw ${GLFW_LIBRARIES} ${GLEW_LIBRARY} )# ${GLUT_LIBRARY} ${OPENGL_LIBRARY})


get_property(dirs DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY LINK_DIRECTORIES)
foreach(dir ${dirs})
  message(STATUS "dir='${dir}'")
endforeach()

message ("CMAKE FLAGS " ${CMAKE_CXX_FLAGS})
