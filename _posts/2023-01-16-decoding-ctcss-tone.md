---
layout: post
title:  "Decoding CTCSS tone"
published: true
author: nemanjan00
image: /assets/ctcss.jpg
hero_image: /assets/ctcss.jpg
hero_darken: true
show_sidebar: true
---

# What is CTCSS?

There are many misconceptions about what CTCSS is on the internet. Some people think it is a form of encryption, authentification or security measure, which it is not. In reality, CTCSS is a form of sub-audible frequency tone used to reduce interference on shared radio frequencies. It allows multiple radios to use the same channel without interfering with each other's transmissions.

CTCSS (Continuous Tone-Coded Squelch System) is is transmitted along with an analog audio signal. CTCSS helps to eliminate interference from other users on the same frequency and prevents unwanted transmissions from being received. When the CTCSS tone is received by the radio, it will open the squelch and allow the audio signal to be heard. When the CTCSS tone is not received, the radio will not open the squelch and the audio signal will not be heard.

# Why are FM radio repeaters using it?

FM radio repeaters use CTCSS to distinguish transmissions they need to repeat from unwanted noise. The repeater receiver can be programmed to only respond to signals that contain the specific tone. This way, the repeater will only repeat transmissions that have the specific tone, and ignore other signals that don't contain the tone, including noise.

# How can we find out what CTCSS is used for certain repeater?

CTCSS codes for ham radio repeaters should be public knowledge, as this helps to ensure that the repeater can be used by all users in the area. However, if you cannot find the CTCSS code for a particular repeater, one of ways is to figure it out from transmissions intended for that repeater.

A software defined radio (SDR) is a type of radio that can be programmed and controlled with software, rather than hardware components, to listen to and record a specific frequency or range of frequencies. To listen to and record a narrow FM signal, you will need a SDR that is capable of receiving and demodulating the signal, and then a software application that can record the signal to a WAV file. Some SDRs that can do this include the RTL-SDR, HackRF, and AirSpy. Additionally, there are several software applications that can be used to demodulate and record the signal, such as SDR#, GQRX, and CubicSDR.

A WAV file (Waveform Audio File Format) is an audio file format that is used to store audio data within a digital audio file. It preserves the original signal without any compression. This means that the original sound is not altered in any way. WAV files are typically used in applications where signal integrity is important.

![Recorded FM transmission with CTCSS](/assets/NFM_signal.png)

When a wav file is opened in Audacity, the audio can be seen as a waveform. In the silent parts of a recording, a sinusoid or sine wave should be visible. A sinusoid is a smooth, continuous line that represents a perfect sine wave.

![Audio signal](/assets/audio_signal.png)

Measuring the time interval of one oscillation of a sinusoid and dividing 1 second by it will give you the frequency of a CTCSS code. This is because in order to determine the frequency of a CTCSS code, you must measure the time it takes for one oscillation of the sinusoidal wave to occur. By dividing 1 second by this time interval, you will be able to calculate the frequency of the CTCSS code.
