# Samplerate-rs

[![Docs](https://docs.rs/samplerate-rs/badge.svg)](https://docs.rs/samplerate-rs/)

A samplerate conversion library for Rust.

This is a fork of the [samplerate](https://github.com/Prior99/rust-samplerate) crate the uses the pure C2Rust transpiled version of [libsamplerate](https://github.com/RamiHg/rust-libsamplerate) in place of the bindings to the [C library](http://www.mega-nerd.com/SRC/api.html). This allows compilation without needing to have CMake installed.

# Example

```rust
extern crate samplerate;
extern crate hound;

use samplerate_rs::{convert, ConverterType};
use hound::{WavSpec, WavWriter, SampleFormat};

fn main() {
    // Generate a 880Hz sine wave for 1 second in 44100Hz with one channel.
    let freq = std::f32::consts::PI * 880f32 / 44100f32;
    let input: Vec<f32> = (0..44100 * 5).map(|i| (freq * i as f32).sin()).collect();

    // Resample the input from 44100Hz to 48000Hz.
    let resampled = convert(44100, 48000, 1, ConverterType::SincBestQuality, &input).unwrap();

    // Write the resampled pcm data to disk.
    let mut writer = WavWriter::create("resampled.wav", WavSpec {
        channels: 1,
        sample_rate: 48000,
        bits_per_sample: 32,
        sample_format: SampleFormat::Float,
    }).unwrap();
    resampled.iter().for_each(|i| writer.write_sample(*i).unwrap());
}
```
