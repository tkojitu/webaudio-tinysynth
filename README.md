# webaudio-tinysynth
WebAudio Tiny GM Synthesizer [Polymer]

## Overview

**webaudio-tinysynth** is a small synthesizer written in JavaScript with GM like timbre map.

- webaudio-tinysynth is a Polymer dom-module. It can be used with just a Tag, 'webaudio-tinysynth'.
- Receive MIDI message data and generate sounds
- All sounds are generated algolithmically, without any external samples.
- Two timbre set is available. 1 oscillator per 1 note ('quality=0', light-weight, chiptune like sounds), or 2 oscillators for 1 note ('quality=1', FM based sounds).
- built-in MIDI-SMF (.mid file) sequencer. It is initiated by MIDI file drag & drop.

## Live Demo
![./tinysynth.png](./tinysynth.png)  

Test Page is here :  
 [soundedit.html](https://g200kg.github.io/webaudio-tinysynth/soundedit.html)  (Playable with MIDI keyboard)  
 [simple.html](https://g200kg.github.io/webaudio-tinysynth/simple.html)  (Most simple sample)

## Usage

Install Polymer and webaudio-tinysynth

*  **webaudio-tinysynth** is a polymer module. You should prepare polymer related library first. If you know bower, use the command > `bower install --save g200kg/webaudio-tinysynth`. Otherwise, download zipped file and deploy appropriately.


*  load webcomponents.js and polymer in your HTML :
  - `<script src="bower_components/webcomponentsjs/webcomponents-lite.min.js"></script>`
  - `<link rel="import" href="bower_components/polymer/polymer.html">`  


* load webaudio-tinysynth in your HTML
  - `<link rel="import" href="bower_components/webaudio-tinysynth/webaudio-tinysynth.html">`
  - if you use bower, webaudio-tinysynth.html is placed to bower_components/webaudio-tinysynth/ folder, otherwise specify path to webaudio-tinysynth.html  


*  place webaudio-pianoroll element
  - `<webaudio-tinysynth></webaudio-tinysynth>`






## Attributes

|Attribute          |Options|Default   |Description               |
|-------------------|-------|----------|--------------------------|
|**width**          |String |"256px"   | width of element         |
|**height**         |String |"64px"    | height of element        |
|**masterVol**      |Number | 1.0      | master volume            |
|**reverbLev**      |Number | 0.3      | reverb level             |
|**quality**        |Number | 1        | 0: 1osc/note chiptune like<br/> 1: 2osc/note FM based|
|**src**            |String |null      | .mid file url            |
|**loop**           |Number | 0        | loop playMIDI            |
|**disableDrop**    |Number | 0        | disable MIDI file drop   |
|**graph**          |Number | 1        | enable waveform graph    |
|**internalContext**|Number | 1        | Use internal audioContext|
|**tsmode**         |Number | 0        | default timestamp mode   |

* In default, necessary audioContext will be created internally. `internalContext="0"` will prevent this and should provide audioContext with `setAudioContext()` function.
* Note that the webaudio-tinysynth may not be ready yet immediately after 'window.onload'. especially be careful for Firefox/Edge because of webcomponents may be polyfilled. `isReady` flag can be used for confirming the synth is ready.

```
// in my test code, access to webaudio-tinysynth is invoked after check :
synth=document.getElementById("tinysynth");
timerid=setInterval(function(){
  console.log("Initialize checking.");
  if(synth.isReady){
    clearInterval(timerid);
    console.log("Initialized");
    // ... start access to webaudio-tinysynth
    // ...
    // ...
  }
},200);
```

## Functions
**setAudioContext(audioContext, destinationNode)**  
> In default, though audioContext is internally created and used, this function can specify `audioContext` should be used.  
> All sounds are routed to specified `destinationNode`, or audioContext.destination is used if destinationNode is not specified.  
> the audioContext in use currently can be accessed with `.audioContext` property of webaudio-tinysynth.

**send([midi-message], t, timestampmode)**
> midi-message is an array of midi data bytes for one message. For example,  
> `send([0x90, 60, 100], t)` is for NoteOn ch=1 note#=60 velocity=100.  
> `t` is a timestamp that this message should be processed.  
> If timestampmode == 0, `t` is a time (sec) in timeline of the in use audioContext.currentTime.  If timestampmode == 1, `t` is a time (msec) in HighResolutionTime (performance.now()) timeline.  
> In both timestamp mode, this message will be immediately processed if `t`=0 or omitted.
> If timestampmode is omitted, the mode depends on `tsmode` in Attributes.

**loadMIDI(mididata)**
> load MIDI data to built-in sequencer. mididata is a arraybuffer of SMF (.mid file contents).

**loadMIDIUrl(url)**
> load MIDI data from specified url

**playMIDI()**
> play loaded MIDI data.

**stopMIDI()**
> stop playing MIDI data.

**getTimbreName(m,n)**
> get name of specified timbre. m=0:normal channel voice,n=prog#. or m=1:drum track,n=note#

## MIDI implimentation chart

|                        |Recognized|Description                     |
|------------------------|----------|--------------------------------|
|**Basic Channel**       | Yes      | 1-16. ch10 = drum track        |
|**NoteOn / NoteOff**    | Yes      | note# 0-127 / velocity 0-127   |
|**Polyphonic Pressure** | No       |                                |
|**Control Change**      | Yes      | see bellow                     |
|**Program Change**      | Yes      | program 0-127                  |
|**Channel Pressure**    | No       |                                |
|**Pitch Bend**          | Yes      | -8192 - +8191                  |
|------------------------|----------|--------------------------------|
|**Control Number**      |          |                                |
|**1**                   | Yes      | Modulation                     |
|**6 / 38**              | Yes      | Data Entry                     |
|**7**                   | Yes      | Channel Volume                 |
|**10**                  | Yes      | Pan                            |
|**11**                  | Yes      | Expression                     |
|**64**                  | Yes      | Sustain                        |
|**100/101**             | Yes      | RPN Index                      |
|------------------------|----------|--------------------------------|
|**Channel Mode Message**|          |                                |
|**120**                 | Yes      | all sound off                  |
|**121**                 | Yes      | reset all controller           |
|**123**                 | Yes      | all note off                   |
|------------------------|----------|--------------------------------|
|**RPN**                 |          |                                |
|**0**                   | Yes      | Bend Range                     |
|------------------------|----------|--------------------------------|

## License

webaudio-pianoroll is licensed under the Apache License, Version 2.0
