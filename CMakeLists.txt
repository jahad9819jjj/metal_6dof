cmake_minimum_required(VERSION 3.23)

project(a.out VERSION 0.0.1)  # a.outやバージョンはお好きに設定して下さい。

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release) # Release, Debug, RelWithDebInfo, MinSizeRel and Custom.
endif()

# glob files depended on 'cpp'
file(GLOB SAMPLE_FILES
    "src/learn-metal/*/*.cpp"
)

# generate executable file at each samples
foreach(SAMPLE_FILE ${SAMPLE_FILES})
    get_filename_component(SAMPLE_NAME ${SAMPLE_FILE} NAME_WE)  # ファイル名の拡張子を除いた部分を取得
    add_executable(${SAMPLE_NAME} ${SAMPLE_FILE})
    target_include_directories(${SAMPLE_NAME}
        PUBLIC
        ${PROJECT_SOURCE_DIR}/include
        ${PROJECT_SOURCE_DIR}/include/metal-cpp
        ${PROJECT_SOURCE_DIR}/include/metal-cpp-extensions
    )
    target_compile_options(${SAMPLE_NAME} PRIVATE -O2 -Wall)
    target_link_libraries(${SAMPLE_NAME}
        "-framework Cocoa"
        "-framework AppKit"
        "-framework CoreData"
        "-framework Foundation"
        "-framework MetalKit"
        "-framework Metal"
    )
    target_compile_features(${SAMPLE_NAME} PRIVATE cxx_std_20)
endforeach()

# Check whether dynamic library is referred or not
foreach(SAMPLE_NAME ${SAMPLE_NAMES})
    add_custom_target(-${SAMPLE_NAME}- ALL
        COMMAND "date" "-R"
        COMMAND "lipo" "-archs" "${SAMPLE_NAME}"
        COMMAND "otool" "-L" "${SAMPLE_NAME}"
    )
endforeach()
