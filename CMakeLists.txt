cmake_minimum_required(VERSION 3.22.1)

project(LTDFS)

add_executable(${PROJECT_NAME} src/main.cpp)

set(THREADS_PREFER_PTHREAD_FLAG ON)
find_package(Threads REQUIRED)
target_link_libraries(${PROJECT_NAME} PRIVATE Threads::Threads)
