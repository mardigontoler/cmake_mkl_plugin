This is essentilly just the CMake AudioPlugin example from [JUCE](https://github.com/juce-framework/JUCE),
linked against the Intel MKL library to demonstrate an error on linux when
loading the resulting VST3 plugin with 
pluginval or most other plugin hosts.

The example was modified by linking with MKL and 
setting `JUCE_DSP_USE_INTEL_MKL` and adding a usage of juce::dsp::FFT,
which should use the MKL implementation if available.

I've tried to follow the CMake Config documentation for MKL found
[here](https://www.intel.com/content/www/us/en/docs/onemkl/developer-guide-macos/2024-0/cmake-config-for-onemkl.html);

When pluginval attempts to load this plugin on Linux, there's an error in juce_VST3PluginFormat.cpp.
```c++
    VSTComSmartPtr<IPluginFactory> getPluginFactory()
    {
        if (factory == nullptr)
            if (auto* proc = (GetFactoryProc) getFunction (factoryFnName))
                factory = becomeVSTComSmartPtrOwner (proc());

        // The plugin NEEDS to provide a factory to be able to be called a VST3!
        // Most likely you are trying to load a 32-bit VST3 from a 64-bit host
        // or vice versa.
        jassert (factory != nullptr);
        return factory;
    }
```
