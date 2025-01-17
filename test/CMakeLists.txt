################################################################################
# Test utilities

add_library(tuvx_test_utils unit/test_utils.F90)
target_link_libraries(tuvx_test_utils musica::musicacore)

set_target_properties(tuvx_test_utils PROPERTIES
  Fortran_MODULE_DIRECTORY ${PROJECT_BINARY_DIR}/test_include
)

target_include_directories(tuvx_test_utils 
  PUBLIC  
    ${PROJECT_BINARY_DIR}/test_include
)

include(test_util)

################################################################################
# Copy test data

add_custom_target(copy_test_data ALL ${CMAKE_COMMAND} -E copy_directory
  ${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_BINARY_DIR}/test)

################################################################################
# Add subdirectories

add_subdirectory(unit)
if(NOT ${CMAKE_Fortran_COMPILER_ID} MATCHES "NAG")
  # oldtuv doesn't build with NAG, so bypass the regression tests
  add_subdirectory(oldtuv)
  add_subdirectory(regression)
endif()

################################################################################
# Run examples as tests

add_custom_target(make-tuv54-example-dir ALL COMMAND ${CMAKE_COMMAND}
                  -E make_directory ${CMAKE_BINARY_DIR}/example_tuv_5_4)
add_custom_target(link-tuv54-example-data ALL COMMAND ${CMAKE_COMMAND}
                  -E copy_directory ${CMAKE_CURRENT_SOURCE_DIR}/../data ${CMAKE_BINARY_DIR}/example_tuv_5_4/data)
add_test(NAME TUV_5_4 COMMAND tuv-x ../examples/tuv_5_4.json
         WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/example_tuv_5_4)
add_custom_target(make-ts1-tsmlt-example-dir ALL COMMAND ${CMAKE_COMMAND}
                  -E make_directory ${CMAKE_BINARY_DIR}/example_ts1_tsmlt)
add_custom_target(link-ts1-tsmlt-example-data ALL COMMAND ${CMAKE_COMMAND}
                  -E copy_directory ${CMAKE_CURRENT_SOURCE_DIR}/../data ${CMAKE_BINARY_DIR}/example_ts1_tsmlt/data)
add_test(NAME TS1_TSMLT COMMAND tuv-x ../examples/ts1_tsmlt.json
         WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/example_ts1_tsmlt)

################################################################################
