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
├── max/
│   └── scatola_di_metallo.maxpat
├── rnbo/
│   └── Scatola_di_metallo_V2.rnbopat
├── docs/
│   └── SdM-Presentazione.pdf
│   └── SdM-Tesina.pdf
└── LICENSE
```

## Requirements

To open and edit the project:

- Max/MSP
- RNBO package for Max

The project was developed and tested in a Max/MSP + RNBO environment.

## Usage

1. Open the main Max/MSP patch located in the `max/` folder.
2. Make sure the RNBO patcher is correctly loaded.
3. Send an audio signal through the processor.
4. Adjust the exposed parameters to control the spatial response, decay, early reflection model and stereo image.

## Project Status

Academic prototype / sound design tool.

The project is intended as a study on algorithmic reverberation, psychoacoustic control of spatial response and deterministic DSP structures for real-time audio processing.

## Possible Future Developments

- Additional FIR early reflection models
- Expanded stereo decorrelation strategies
- Controlled nonlinearities inside the feedback network
- Standalone or plugin export
- Multichannel / spatial audio extension
- Adaptive parameter control

## Author

**Giovanni Maria Vona**  
Sound Engineering student  
Conservatorio Statale di Musica “O. Respighi” — Latina  
DSP · Sound Design · Electroacoustic Music · Audio Programming

## License

Code, Max/MSP patches, RNBO patches and DSP files are released under the MIT License.  
Documentation, images and audio examples are released under CC BY-NC 4.0.  

See `LICENSE.md` for details.