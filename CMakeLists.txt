cmake_minimum_required(VERSION 3.19)
project(NVIDIA_SGEMM_PRACTICE LANGUAGES CXX CUDA)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# FindCUDA has been deprecated since cmake 3.27
find_package(CUDAToolkit REQUIRED)

# ensure cuda is available
include(CheckLanguage)
check_language(CUDA)

set(CMAKE_CXX_STANDARD 20)
set(CUDA_COMPUTE_CAPABILITY 86)

# in debug mode, add debug symbols to device code
# this disables most optimizations and kills performance
add_compile_options("$<$<AND:$<CONFIG:Debug>,$<COMPILE_LANGUAGE:CUDA>>:-G;-src-in-ptx>")

# add_compile_options("--ptxas-options=-v")

# Configure header file search paths
include_directories(${CUDAToolkit_INCLUDE_DIRS})
include_directories(${PROJECT_SOURCE_DIR}/src)

# Configure the source file path to be compiled
aux_source_directory(${PROJECT_SOURCE_DIR}/src SRC)

# generate executables
add_executable(sgemm sgemm.cu ${SRC})
set_target_properties(sgemm PROPERTIES CUDA_ARCHITECTURES ${CUDA_COMPUTE_CAPABILITY})
target_link_libraries(sgemm CUDA::cudart CUDA::cublas)

add_executable(cuBLAS_sgemm cuBLAS_sgemm.cu)
set_target_properties(sgemm PROPERTIES CUDA_ARCHITECTURES ${CUDA_COMPUTE_CAPABILITY})
target_link_libraries(cuBLAS_sgemm CUDA::cudart CUDA::cublas)

add_executable(simplest_kernel simplest_kernel.cu)
set_target_properties(sgemm PROPERTIES CUDA_ARCHITECTURES ${CUDA_COMPUTE_CAPABILITY})
target_link_libraries(simplest_kernel CUDA::cudart)
