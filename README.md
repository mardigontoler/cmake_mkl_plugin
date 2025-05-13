This is a starter project for a JUCE audio plugin, using CMake and a
linked with Intel MKL.

This is essentilly just the CMake AudioPlugin example from [JUCE](https://github.com/juce-framework/JUCE).

The example was modified by linking with MKL,
setting `JUCE_DSP_USE_INTEL_MKL`, and adding appropriate
linker and compiler settings.
I've tried to follow the CMake Config documentation for MKL found
[here](https://www.intel.com/content/www/us/en/docs/onemkl/developer-guide-macos/2024-0/cmake-config-for-onemkl.html);

For testing MKL, I've added some usages of juce::dsp::FFT,
which should use MKL FFTs if support is enabled.
