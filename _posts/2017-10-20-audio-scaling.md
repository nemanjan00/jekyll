---
layout: post
title:  "Audio scaling"
published: true
author: nemanjan00
show_sidebar: true
---

Hi guys, what I had to do few days ago is dynamically change speed of audio playback. 

## Terminology

Sample is sound amplitude. 

Audio file is just encoded/compressed list of samples. 

Samplerate is number of samples per second. (the one I will be using is 44100)

Buffer is Array of samples that are going to go out of your speaker. (Common sizes are 256, 512, 1024, etc. sample rates)

## What did I end up using? 

Technology I was using for that is [Web Audio API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API). 

## AudioNode

AudioNode is basicaly Object which you pipe audio into and/or get audio out of it. 

AudioNodes connect to each other just like any audio equipment would in real life. 

For example you maybe have microphone, speaker, spectrum analyzer with passthrough and amplifier. 

You can connect for example microphone -> amplifier -> spectrum analyzer -> speaker

## Getting audio inside system

What most of the people in Web Audio API would use if they wanted to play music inside it is ``MediaElementAudioSourceNode`` (implementation of AudioNode that gets sound from HTML5 ``audio`` tag). 

It simply plays audio and that is it. 

Problem with this one is that it is impossible to change speed. (might be possible to slow it down, but I did not find any ways to make it play music faster)

### decodeAudioData

What I ended up doing is getting mp3 using XHR as ArrayBuffer and running decodeAudioData from audio context on it. 

That gave me back buffer containing whole audio: 

```javascript
module.exports = function(url){
	return new Promise((resolve, reject) => {
		const audioCtx = new (window.AudioContext || window.webkitAudioContext)(); // Getting audio context. Anything you do with Web Audio API requires use of audio context

		const xhr = new XMLHttpRequest();

		xhr.open('GET', url, true);
		xhr.responseType = 'arraybuffer'; // This will make sure I get response in format I need for passing it to Web Audio API

		xhr.onload = function(e) {
			var audioData = xhr.response;

			audioCtx.decodeAudioData(audioData, function(buffer) { // This will decode that audio file and return buffer using callback. 
				resolve(buffer);
			}, function(e){
				reject(e);
			});
		};

		xhr.send();
	});
}
```

What I can do now is take data from buffer at any rate. 

## Scaling audio using input audio buffer

Now that I have list of amplitudes I can play that audio back. 

Web Audio API does not have any way to generate sound, just process it (at least I did not find anything like that). 

What I ended up doing is using ScriptProcessor (AudioNode for transforming sound). 

I did not hook up any audio sources to it, just hooked it up to sink. 

transformationFunction is function that is getting called each time you need to process audio. Inside it I am taking audio from music buffer and putting it into output buffer. 

### Calculating sample in output buffer

What I have is huge buffer that contains song and I have very small buffer I am writing to. 

I have counter ``i`` which tells me where I am inside input buffer. 

What I do is iterate inside output buffer and calculate what I want to take from input buffer for that sample. 

Let's say playback speed is 1.5. 

What I need to put into output buffer are samples 1.5, 3, 4.5, etc. 

But, there are samples 1 and 2, and there is no sample 1.5, what do I do now? 

Well, what you can do is: 

```
sample = 0.5 * input[0] + 0.5 * input[1];
```

```javascript
module.exports = function(audioCtx, buffer, outputNode){
	let i = 0;
	let cnt = 0;

	let speed = {
		speed: 1,
		playing: 1,
		buffer: undefined,
		transformationFunction: function(audioProcessingEvent) {
			let outputBuffer = audioProcessingEvent.outputBuffer;

			let buffer = speed.buffer;

			let oldI = i;

			let end = false;

			for(chan = 0; chan < 2; chan++){
				i = oldI;
				let position = 0;

				let input = buffer.getChannelData(chan);
				let out = outputBuffer.getChannelData(chan);

				do {
					i += speed.speed * speed.playing;

					if(i < 0 || i >= input.length){
						end = true;
						speed.playing = false;
					}

					if(!end){
						let floor = Math.floor(i);
						let diff = i - floor;

						out[position] = input[floor] * diff + input[floor + 1] * (1 - diff);
					} else {
						out[position] = 0;
					}
					
					position++;
				} while(position < out.length);
			}
		},
		init: function(audioCtx, buffer, outputNode){
			const scriptNode = audioCtx.createScriptProcessor(512, 0, 2);

			speed.buffer = buffer;

			scriptNode.onaudioprocess = speed.transformationFunction;

			scriptNode.connect(outputNode);

			return speed;
		}
	}


	return speed.init(audioCtx, buffer, outputNode);
}
```

