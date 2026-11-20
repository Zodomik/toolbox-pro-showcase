# Toolbox Pro · Measurement calculations

## 1. Vector magnitude and a rolling RMS

Magnetic and acceleration vectors use Euclidean magnitude: `sqrt(x² + y² + z²)`. Motion history retains the latest 80 values. Vibration intensity uses `sqrt(sum(sample²) / count)` over that window, with a separately tracked peak.

This is a short rolling summary for a live instrument, not a stored high-frequency vibration recording. The current `totalG` property adds gravity magnitude and user-acceleration magnitude; it should not be described as the magnitude of their vector sum.

## 2. Digital audio level and running statistics

The input tap receives buffers of up to 2048 frames. `vDSP_rmsqv` computes RMS. The service calculates `20 × log10(max(RMS, ε))`, adds a fixed 94 offset and clamps the displayed value to 0–120.

The running average uses `mean ← mean + (newValue - mean) / sampleCount`. Maximum is tracked separately, while the visible graph retains 80 values. This average is an arithmetic average of displayed levels, not an acoustic energy-equivalent level calculation.

## 3. Frequency through zero crossings

The service counts sign changes in the microphone buffer and estimates `frequency = crossings × sampleRate / (2 × frameCount)`.

For a clean periodic signal this gives a lightweight estimate. Complex/noisy audio can create extra crossings. The implementation does not calculate a Fourier transform or frequency-bin spectrum; that distinction matters when describing what was built.

## 4. AR distance and height

A raycast supplies a 3D world point for each endpoint. Length is the Euclidean distance between the two points. Height mode uses the absolute difference of their world Y coordinates.

SceneKit draws a cylinder at the midpoint, gives it the measured length and rotates it from the vertical axis toward the endpoint vector with a quaternion. Markers and the line share the same world coordinates used for the numerical result.

## 5. GPS trip accumulation

The location delegate ignores locations with invalid horizontal-accuracy values. While tracking, it computes the distance from the previous location and adds increments smaller than 100 metres. That rejects large individual jumps; it is a simple heuristic, not a Kalman filter or map-matching algorithm.

## 6. Bounded live state and capability-aware views

Keeping only the recent graph window bounds the number of values displayed during a long session. Capability checks distinguish missing hardware or permissions from a valid zero reading. Sensor lifecycles can be started/stopped separately from the persistent history model.

[Architecture](ENGINEERING.md) · [Local data model](DATA_MODEL.md)
