cmake_minimum_required(VERSION 3.0)
set(project munch)

find_package(noms REQUIRED COMPONENTS fruit)
find_package(noms REQUIRED COMPONENTS veg)

add_executable(${project} src/main.cpp)

target_include_directories(${project}
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:$include>
)

target_link_libraries(${project}
  PUBLIC
    noms::fruit
)

install(TARGETS ${project}
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
)

# ...
