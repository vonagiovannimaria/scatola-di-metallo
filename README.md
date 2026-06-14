# Scatola di Metallo

**Scatola di Metallo** is a hybrid algorithmic reverb processor developed in **Max/MSP** and **RNBO**.

The project was created as an academic work for the *Psychoacoustics* course within the First Level Academic Diploma in **Sound Engineering / Tecnico del Suono** at the Conservatorio Statale di Musica “O. Respighi” in Latina.

The aim of the project is to design a reverb system that combines structural control, numerical stability and a distinctive timbral character, while avoiding delay-time modulation and FFT-based convolution.

## Overview

Scatola di Metallo is based on a deterministic DSP architecture divided into two main sections:

- **Early Reflections (ER)**  
  Generated either through algorithmic all-pass filter networks or through short FIR structures based on discrete delay taps.

- **Late Reverb (LR)**  
  Generated through parallel banks of Feedback Comb Filters, inter-filter feedback structures and stereo mixing matrices.

The processor is designed to produce a spatial response that is controllable and timbrally characterized, with particular attention to stereo decorrelation, reverberant density and stable real-time behavior.

## Signal Flow

```text
Input
  ↓
Pre-Delay
  ↓
Early Reflections
  ↓
Late Reverb
  ↓
Dry/Wet Mix
```

## Main Features

- Hybrid algorithmic reverb architecture
- Early reflections based on:
  - All-Pass Filters inspired by Schroeder structures
  - FIR delay-tap impulse responses
- Late reverberation based on parallel Feedback Comb Filters
- Inter-FBCF feedback for increased tail density
- Stereo decorrelation through delay offsets, mixing matrices and selective phase inversion
- No dynamic modulation of delay times
- Deterministic DSP structure
- Real-time parameter control in Max/MSP
- RNBO-based implementation
- C++ source export generated from the RNBO implementation
- VST3 / AU plugin builds distributed through GitHub Releases

## Early Reflections

The early reflection section can operate in two different modes.

### All-Pass Filter Mode

This mode uses serial chains of All-Pass Filters to increase temporal diffusion while preserving a relatively transparent spectral behavior.

The basic APF structure is based on the equation:

```text
y[n] = -g · x[n] + x[n-d] + g · y[n-d]
```

where:

- `g` is the diffusion / feedback coefficient
- `d` is the delay time in samples

### FIR Mode

The FIR mode uses manually designed short impulse responses based on discrete delay taps.

Three impulse response configurations are included:

- **Metal Box** — dense, irregular and strongly decorrelated reflections
- **Plate** — more uniform distribution and homogeneous decay
- **Room** — more realistic temporal spacing between reflections

The FIR structure is based on:

```text
y[n] = Σ ak · x[n-dk]
```

where:

- `ak` is the amplitude of each tap
- `dk` is the delay time of each tap

This approach avoids FFT-based convolution and keeps the system lightweight and controllable inside RNBO.

## Late Reverb

The late reverb section generates the reverberant tail through banks of Feedback Comb Filters.

Each stereo channel includes multiple parallel FBCF banks. Each bank uses different and decorrelated delay times in order to reduce periodic spectral reinforcement and artificial resonances.

The basic FBCF structure is:

```text
y[n] = x[n] + g · y[n-d]
```

where `g` controls the energetic decay of the reverb.

## Inter-FBCF Feedback

One of the main developments of the project is the introduction of feedback between consecutive comb filters inside each bank.

The extended structure can be described as:

```text
y[n] = x[n] + g · y[n-d] + fbx · z[n]
```

where:

- `x[n]` is the input signal
- `g` is the internal feedback coefficient
- `y[n-d]` is the delayed output of the current filter
- `z[n]` is the output of the previous filter in the bank
- `fbx` is the inter-filter feedback coefficient

The `fbx` coefficient is automatically derived from the global decay parameter and limited in order to preserve numerical stability.

## Stereo Decorrelation

The stereo image is generated through:

- different delay times between left and right channels
- stereo mixing matrices derived from Schroeder-style structures
- selective phase inversions
- controlled correlation between channels

The goal is to obtain a wide and stable stereo field without relying on delay modulation.

## Parameters

The main exposed parameters include:

- **Mix** — balance between dry and processed signal
- **Pre-delay** — delay before the onset of early reflections
- **ER Mode** — selection between APF and FIR early reflection modes
- **IR Type** — selection between Metal Box, Plate and Room FIR configurations
- **Size** — global scaling of delay times
- **Decay** — control of the reverberation decay
- **Correlation** — stereo correlation / decorrelation control
- **Side Boost** — gain control for the side component of the processed signal

## Repository Structure

```text
scatola-di-metallo/
├── README.md
├── LICENSE
├── max/
│   └── scatola_di_metallo.maxpat
├── rnbo/
│   └── Scatola_di_metallo_V2.rnbopat
├── cpp/
│   ├── README.md
│   ├── dependencies.json
│   ├── description.json
│   ├── presets.json
│   ├── rnbo_source.cpp
│   └── rnbo/
│       └── [RNBO C++ source export and support files]
└── docs/
    ├── SdM-Presentazione.pdf
    └── SdM-Tesina.pdf
```

## Requirements

To open and edit the project:

- Max/MSP
- RNBO package for Max

The project was developed and tested in a Max/MSP + RNBO environment.

## C++ Source Export

The repository includes a C++ source export generated from the RNBO implementation of the processor.

```text
cpp/rnbo_export/
```

This export is provided as a technical reference and as a basis for future development outside the Max/MSP environment.

The C++ source export documents how the RNBO patch can be translated into a lower-level audio programming context. It may require additional configuration depending on the target compiler, operating system, build system or plugin framework.

The Max/MSP and RNBO patch files remain the primary editable version of the project.

## Plugin Builds

Compiled plugin builds are distributed through the repository Releases section rather than being committed directly to the main source tree.

Available plugin formats may include:

```text
VST3
AU
```

The plugin builds are provided as release assets in order to keep the repository lightweight and avoid storing platform-specific binary files directly in the source tree.

Download the latest plugin builds from:

[Scatola di Metallo — Releases](https://github.com/vonagiovannimaria/scatola-di-metallo/releases)

## Usage

1. Open the main Max/MSP patch located in the `max/` folder.
2. Make sure the RNBO patcher is correctly loaded.
3. Send an audio signal through the processor.
4. Adjust the exposed parameters to control the spatial response, decay, early reflection model and stereo image.

### Using the plugin builds

Compiled VST3 / AU builds are distributed through GitHub Releases.

1. Open the repository Releases section.
2. Download the plugin build for your operating system and plugin format.
3. Install the plugin according to the requirements of your DAW.
4. Load the plugin on an audio track and test it with an appropriate input signal.

The plugin builds are derived from the RNBO implementation of the processor. The Max/MSP and RNBO files remain the primary editable version of the project.

## Project Status

Academic prototype / sound design tool.

The project is intended as a study on algorithmic reverberation, psychoacoustic control of spatial response and deterministic DSP structures for real-time audio processing.

Compiled VST3 / AU plugin builds are distributed through GitHub Releases.

## Possible Future Developments

- Additional FIR early reflection models
- Expanded stereo decorrelation strategies
- Controlled nonlinearities inside the feedback network
- refinement of the RNBO-generated C++ source export
- further testing of VST3 / AU plugin builds
- standalone application build
- extended documentation for plugin installation and usage
- Multichannel / spatial audio extension
- Adaptive parameter control

## License

Code, Max/MSP patches, RNBO patches and DSP files are released under the MIT License.  
Documentation, images and audio examples are released under CC BY-NC 4.0.  

See `LICENSE.md` for details.

## Author

**Giovanni Maria Vona**  
Sound Engineering student  
Conservatorio Statale di Musica “O. Respighi” — Latina  
DSP · Sound Design · Electroacoustic Music · Audio Programming