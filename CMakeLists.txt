cmake_minimum_required(VERSION 3.22)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

message("Finding Intel MKL")
set(MKL_LINK "static")
find_package(MKL CONFIG REQUIRED PATHS $ENV{MKLROOT} MKL_LINK=static)
message(STATUS "${MKL_IMPORTED_TARGETS}") #Provides available list of targets based on input


project(mkl_plugin VERSION 0.1)

set(SRC ../src)
set(LIB ../lib)

set_property(GLOBAL PROPERTY USE_FOLDERS ON)
set(LibsFolderName "Libraries")

set(JUCE_PATH "./JUCE")

set(JUCE_COPY_PLUGIN_AFTER_BUILD ON)
set(JUCE_ENABLE_MODULE_SOURCE_GROUPS ON)

message("JUCE_PATH=${JUCE_PATH}")
add_subdirectory(${JUCE_PATH} build_juce)

juce_add_plugin(
    mkl_plugin
    VERSION ${PROJECT_VERSION}
    FORMATS Standalone VST3 AU
    PLUGIN_MANUFACTURER_CODE Juce               # A four-character manufacturer id with at least one upper-case character
    PLUGIN_CODE Dem0                            # A unique four-character plugin id with exactly one upper-case character
)

target_sources(mkl_plugin PRIVATE
    src/PluginProcessor.h
    src/PluginProcessor.cpp
    src/PluginEditor.h
    src/PluginEditor.cpp
)

target_compile_options(mkl_plugin PUBLIC $<TARGET_PROPERTY:MKL::MKL,INTERFACE_COMPILE_OPTIONS>)
target_include_directories(mkl_plugin PUBLIC  $<TARGET_PROPERTY:MKL::MKL,INTERFACE_INCLUDE_DIRECTORIES>)


target_compile_definitions(mkl_plugin
    PUBLIC
    # JUCE_WEB_BROWSER and JUCE_USE_CURL would be on by default, but you might not need them.
    JUCE_WEB_BROWSER=0  # If you remove this, add `NEEDS_WEB_BROWSER TRUE` to the `juce_add_plugin` call
    JUCE_USE_CURL=0     # If you remove this, add `NEEDS_CURL TRUE` to the `juce_add_plugin` call
    JUCE_VST3_CAN_REPLACE_VST2=0
    PRIVATE
    CMAKE_BUILD_TYPE="${CMAKE_BUILD_TYPE}"
    JUCE_DSP_USE_INTEL_MKL=1
)

target_link_libraries(mkl_plugin
    PRIVATE
    juce::juce_audio_utils
    juce::juce_dsp
    PUBLIC
    $<LINK_ONLY:MKL::MKL>
    juce::juce_recommended_config_flags
    juce::juce_recommended_lto_flags
    juce::juce_recommended_warning_flags
)

set( CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -Wl,--no-undefined" )
