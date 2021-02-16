cmake_minimum_required(VERSION 3.12)
project(HelloC LANGUAGES C VERSION 0.1.0)

set(CMAKE_C_STANDARD 11)

set(ignoreMe "${CMAKE_CXX_COMPILER}")

file(GLOB_RECURSE helloc_SRC
        include/*.h
        src/*.c)

add_executable(HelloC ${helloc_SRC})

message("CMAKE_BUILD_TYPE = ${CMAKE_BUILD_TYPE}")
if(CMAKE_BUILD_TYPE MATCHES Debug)
        add_definitions(-DCHECK_MEMORY_LEAKS=1)
        message("Debug build.")
else()
        add_definitions(-DCUSTOM_ALLOCATOR=1)
endif()

if (WIN32)
        set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Xclang -fopenmp")
endif (WIN32)

if(APPLE)
        find_package(OpenMP)
        if (OPENMP_FOUND)
                set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
        endif()
endif()

if(UNIX AND NOT APPLE)
        # for Linux, BSD, Solaris, Minix
        find_package(OpenMP)
        if (OPENMP_FOUND)
                set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
        endif()
endif()

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wno-visibility -Wno-pragma-pack -Wno-deprecated-declarations -mavx -mavx2")

set(includeList
        ${CMAKE_CURRENT_SOURCE_DIR}/include
        ${CMAKE_CURRENT_SOURCE_DIR}/lib/cstorage/include
        ${OpenMP_CXX_LIBRARIES})

target_include_directories(HelloC PUBLIC ${includeList})
