# ckProfiler
set(PROFILER_SOURCES
    profiler.cpp
    profile_gemm.cpp
    #profile_reduce.cpp
    #profile_groupnorm_bwd_data.cpp
    #profile_groupnorm_fwd.cpp
    #profile_layernorm_bwd_data.cpp
    #profile_layernorm_bwd_gamma_beta.cpp
    #profile_groupnorm_bwd_gamma_beta.cpp
    #profile_layernorm_fwd.cpp
    #profile_max_pool2d_fwd.cpp
    #profile_pool3d_fwd.cpp
    #profile_avg_pool3d_bwd.cpp
    #profile_max_pool3d_bwd.cpp
    #profile_avg_pool2d_bwd.cpp
    #profile_max_pool2d_bwd.cpp
    #profile_softmax.cpp
    #profile_batchnorm_fwd.cpp
    #profile_batchnorm_bwd.cpp
    #profile_batchnorm_infer.cpp
    #profile_conv_tensor_rearrange.cpp
    #profile_transpose.cpp
    #profile_permute_scale.cpp
)

list(APPEND PROFILER_SOURCES profile_gemm_universal_preshuffle.cpp)
#if(SUPPORTED_GPU_TARGETS MATCHES "gfx9")
 # if(SUPPORTED_GPU_TARGETS MATCHES "gfx94")
  #endif()
#endif()

set(PROFILER_EXECUTABLE ckProfiler)
add_executable(${PROFILER_EXECUTABLE} ${PROFILER_SOURCES})
target_link_libraries(${PROFILER_EXECUTABLE} PRIVATE utility getopt::getopt)

#target_link_libraries(${PROFILER_EXECUTABLE} PRIVATE device_gemm_multiply_multiply_instance)

# add_executable(${PROFILER_EXECUTABLE} ${PROFILER_SOURCES})
target_compile_options(${PROFILER_EXECUTABLE} PRIVATE -Wno-global-constructors)
# flags to compress the library
if(NOT WIN32 AND ${hip_VERSION_FLAT} GREATER 600241132)
  message("Adding --offload-compress flag for ${PROFILER_EXECUTABLE}")
  target_compile_options(${PROFILER_EXECUTABLE} PRIVATE --offload-compress)
endif()



#if(SUPPORTED_GPU_TARGETS MATCHES "gfx9")
target_link_libraries(${PROFILER_EXECUTABLE} PRIVATE 
  device_gemm_universal_instance)
#endif()

rocm_install(TARGETS ${PROFILER_EXECUTABLE} COMPONENT profiler)
