
TensileCreateLibraryFiles(
    "${CMAKE_CURRENT_SOURCE_DIR}/../configs/lite_configs"
    "${CMAKE_CURRENT_BINARY_DIR}/test_kernels_lite"
    TENSILE_ROOT "${TENSILE_SCRIPT_ROOT}"
    EMBED_LIBRARY test_kernels_lite
    EMBED_KEY     kernels_lite
    VAR_PREFIX LITE NO_MERGE_FILES
    )

message("LITE_ALL_FILES: ${LITE_ALL_FILES}")

file(MAKE_DIRECTORY "${TEST_DATA_DIR}/kernels_lite")
file(COPY ${LITE_ALL_FILES} DESTINATION "${TEST_DATA_DIR}/kernels_lite")

TensileCreateLibraryFiles(
    "${CMAKE_CURRENT_SOURCE_DIR}/../configs/lite_configs_mixed"
    "${CMAKE_CURRENT_BINARY_DIR}/test_kernels_lite_mixed"
    TENSILE_ROOT "${TENSILE_SCRIPT_ROOT}"
    EMBED_LIBRARY test_kernels_lite_mixed
    EMBED_KEY     kernels_lite_mixed
    VAR_PREFIX LITE_MIXED
    NO_MERGE_FILES
    )

file(MAKE_DIRECTORY "${TEST_DATA_DIR}/kernels_lite_mixed")
file(COPY ${LITE_MIXED_ALL_FILES} DESTINATION "${TEST_DATA_DIR}/kernels_lite_mixed")

# Link a second copy of the lite_configs library to test that there are no issues with duplicate symbols.
TensileCreateLibraryFiles(
    "${CMAKE_CURRENT_SOURCE_DIR}/../configs/lite_configs"
    "${CMAKE_CURRENT_BINARY_DIR}/test_kernels_lite_2"
    TENSILE_ROOT "${TENSILE_SCRIPT_ROOT}"
    EMBED_LIBRARY test_kernels_lite_2
    EMBED_KEY     kernels_lite_2
    VAR_PREFIX LITE_2
    )

file(MAKE_DIRECTORY "${TEST_DATA_DIR}/kernels_lite_2")
file(COPY ${LITE_2_ALL_FILES} DESTINATION "${TEST_DATA_DIR}/kernels_lite_2")

TensileCreateLibraryFiles(
    "${CMAKE_CURRENT_SOURCE_DIR}/../configs/tile_aware_selection"
    "${CMAKE_CURRENT_BINARY_DIR}/test_tile_aware_selection"
    TENSILE_ROOT "${TENSILE_SCRIPT_ROOT}"
    EMBED_LIBRARY test_tile_aware_selection
    EMBED_KEY     tile_aware_selection
    )

file(MAKE_DIRECTORY "${TEST_DATA_DIR}/tile_aware_selection")
file(COPY "${CMAKE_CURRENT_BINARY_DIR}/test_tile_aware_selection/library" DESTINATION "${TEST_DATA_DIR}/tile_aware_selection")
#file(COPY ${TILE_AWARE_SELECTION_ALL_FILES} DESTINATION "${TEST_DATA_DIR}/tile_aware_selection")

set(test_yaml rocblas_sgemm_asm_single_kernel.yaml)
file(COPY ${test_yaml} DESTINATION .)
add_custom_target(test_code_object
    COMMAND python ${TENSILE_ROOT}/Tensile/Tensile.py ${test_yaml} code_object
    DEPENDS ${test_yaml})

set(test_sources ${test_sources}
    hip/HipSolutionAdapter_test.cpp
    hip/CopyTensor_test.cpp
    hip/RunGEMMKernel_test.cpp PARENT_SCOPE)

set(HIP_TEST_LIBRARIES
    hip::host
    test_kernels_lite
    test_kernels_lite_2
    test_kernels_lite_mixed
    test_tile_aware_selection
    TensileClient PARENT_SCOPE)

