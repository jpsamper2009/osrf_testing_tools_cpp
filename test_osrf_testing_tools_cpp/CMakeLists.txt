cmake_minimum_required(VERSION 3.5)

project(test_osrf_testing_tools_cpp)

# Default to C++11
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 11)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic -Werror)
endif()

if(BUILD_TESTING)
  include(CTest)
  find_package(osrf_testing_tools_cpp REQUIRED)
  osrf_testing_tools_cpp_require_googletest(VERSION_GTE 1.8)  # ensures target gtest_main exists

  # memory_tools doesn't work on Windows right now
  # TODO(wjwwood): disable on aarch64 for now, until fixed, see:
  #   https://github.com/osrf/osrf_testing_tools_cpp/issues/3
  if(NOT WIN32 AND NOT CMAKE_SYSTEM_PROCESSOR STREQUAL "aarch64")
    add_executable(test_example_memory_tools_gtest test/test_example_memory_tools.cpp)
    target_link_libraries(test_example_memory_tools_gtest
      gtest_main
      osrf_testing_tools_cpp::memory_tools
    )
    get_target_property(extra_env_vars
      osrf_testing_tools_cpp::memory_tools LIBRARY_PRELOAD_ENVIRONMENT_VARIABLE)
    osrf_testing_tools_cpp_add_test(test_example_memory_tools
      COMMAND "$<TARGET_FILE:test_example_memory_tools_gtest>"
      ENV ${extra_env_vars}
    )
  endif()
endif()
