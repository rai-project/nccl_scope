OPTION(ENABLE_NCCL "Enable NCCL|Scope (github.com/c3sr/nccl_scope)" OFF)
if (NOT ENABLE_NCCL)
  return()
endif()

cmake_minimum_required(VERSION 3.12)

# Look for sugar.cmake files in the plugin src directory
sugar_include("src")

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_LIST_DIR}/cmake")
#message(STATUS ${CMAKE_MODULE_PATH})
# The plugin is a static library that Scope will be linked with
scope_add_library(nccl_scope OBJECT ${nccl_SOURCES} ${nccl_CUDA_SOURCES} )
target_include_directories(nccl_scope PRIVATE 
    "src"
)
target_include_scope_directories(nccl_scope)
target_compile_features(nccl_scope PUBLIC cxx_std_11)
set_property(TARGET nccl_scope PROPERTY CUDA_STANDARD 11)
target_link_libraries(nccl_scope PRIVATE benchmark::benchmark)

#Nccl
find_package(NCCL REQUIRED)
if(NCCL_FOUND)
  target_include_directories(nccl_scope PRIVATE ${NCCL_INCLUDE_DIR})
  target_link_libraries(nccl_scope PUBLIC ${NCCL_LIBRARY})
else()
  message(WARNING "didn't find NCCL, benchmarks will not be available.")
endif(NCCL_FOUND)

