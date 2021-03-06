include(operators)
register_operators()

if(WITH_GPU)
    file(GLOB OPS RELATIVE "${CMAKE_CURRENT_SOURCE_DIR}" "*.part.cu")
    string(REPLACE ".part.cu" "" OPS "${OPS}")

    foreach(src ${OPS})
        if (EXISTS ${CMAKE_CURRENT_SOURCE_DIR}/${src}.part.cu)
            set(CUDA_KERNEL_FILE ${CMAKE_CURRENT_SOURCE_DIR}/${src}.part.cu)
            file(READ ${CUDA_KERNEL_FILE} TARGET_CONTENT)
            string(REGEX MATCH "REGISTER_OP_CUDA_KERNEL\\(\\n?([^,]+),.*" MATCHED ${TARGET_CONTENT})
            if (MATCHED)
                string(STRIP ${CMAKE_MATCH_1} MATCHED)
                file(APPEND ${pybind_file} "USE_OP_DEVICE_KERNEL(${MATCHED}, CUDA);\n")
            endif()

        endif()
    endforeach()
endif()
