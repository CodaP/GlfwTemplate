cmake_minimum_required(VERSION 3.5)
project(Template)

message("Source " "${CMAKE_SOURCE_DIR}")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
set(INCLUDE "${CMAKE_SOURCE_DIR}/include")

if (APPLE)
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -framework Cocoa -framework OpenGL -framework IOKit -framework CoreVideo")
elseif(WIN32)
    find_package(OpenGL REQUIRED)
    include_directories(${OPENGL_INCLUDE_DIRS})
endif()

add_definitions(-DGLEW_STATIC -D_USE_MATH_DEFINES)
if (WIN32)
    add_definitions(-DWINDOWS -DPERF) # perf doesn't work anywhere other than windows yet
endif()

include_directories(${INCLUDE})

set(SOURCE_FILES main.cpp gl_includes.h Perf.h Perf.cpp stb_image_impl.cpp)
add_executable(Template ${SOURCE_FILES})

if (APPLE)
    target_link_libraries(Template ${GLEW_LIBRARY})
    target_link_libraries(Template glfw3)
else()
    set(LIB "${CMAKE_SOURCE_DIR}/lib")
    link_directories(${LIB})
    target_link_libraries(Template ${LIB}/libglew32.a)
    target_link_libraries(Template ${LIB}/libglfw3.a)
    target_link_libraries(Template ${OPENGL_LIBRARIES})
    target_link_libraries(Template -static-libgcc -static-libstdc++)
endif()

file(COPY ${CMAKE_SOURCE_DIR}/assets DESTINATION ${CMAKE_BINARY_DIR}/)
