# Qun-synthesizer
Qun-synthesizer is an analog modeling synthesizer engine for ESP32 Lyrat, worked with Nunomo's daughter board.
(Dauther board is making in progress)

### Overview
- Connectivity: Standard MIDI(TRS A type), BLE MIDI, UART MIDI(Supports MAC OS X and Windows through my SerialMIDI https://github.com/raspy135/serialmidi .

* ESP32 Lyrat
  * It can be battery operated.
  * It can drive small speakers.

* Analog Modeling engine
  * The engine is made from scratch.
  * All analog modeling engine can be configured through MIDI. 
  * 2 Alias free sscillators, it can be used as dual tone. 
  * 2 Envelove Generators
  * FM
  * 1 LFO, rate can be controlled by MIDI notes
  * 1 Organic sounding VCF
    * 2/4 Poles, Low-pass, Band-pass, High-pass, Notch)
    * Keysync
  * 1 Effect (Delay, Chrus, Flanger)
  * A lot of module interconnections, such as (LFO, EG) to (VCF, OSC)
  * MIDI clock sync
* Player / Sequencer
  * Player (Piano mode)
  * Random mind inspiring 8 step sequencer will generate beats for you
    * Note On/Off/Double/Triple
    * Transpose / Note width(Length)
    * Randomness control
    * Scale quantize
    * Arpeggiate steps
  * Looper
    * Looper can record the sound up to 30 sec, synchronized with the sequencer.
    * Play / Rec / Overdub
    
### Supported MIDI Control codes
```
                            "Save Preset", //0x0
                            "Mod Wheel", //1
                            "", //2
                            "", //3
                            "", //4
                            "", //5
                            "", //6
                            "Volume", //7
                            "", //8
                            "", //9
                            "", //a
                            "VCF Volume", //b
                            "", //c
                            "", //d
                            "", //e
                            "", //f
                            //-----------------------0x10
                            "", //0x0
                            "", //1
                            "", //2
                            "", //3
                            "", //4
                            "OSC1 Tune(MSB)", //5
                            "VCF Cutoff(MSB)", //6
                            "OSC2 Tune(MSB)", //7
                            "LFO Tune(MSB)", //8
                            "", //9
                            "", //a
                            "", //b
                            "", //c
                            "", //d
                            "", //e
                            "", //f
                            //-----------------------0x20
                            "", //0x0
                            "FM", //1
                            "Efct Feedback", //2
                            "LFO Pulse Width", //3
                            "OSC1 Pulse Width", //4
                            "OSC2 Pulse Width", //5
                            "LFO Pulse Width", //6
                            "Efct Speed", //7
                            "VCF Type", //8
                            "Efct Depth", //9
                            "", //a
                            "", //b
                            "Efct Type", //c
                            "VCF OSC1 bypass SW", //d
                            "", //e
                            "", //f
                            //-----------------------0x30
                            "LFO Env Tune", //0x0
                            "LFO Env Width", //1
                            "", //2
                            "", //3
                            "", //4
                            "OSC1 Tune(LSB)", //5
                            "Cutoff(LSB)", //6
                            "OSC2 Tune(LSB)", //7
                            "LFO Tune(LSB)", //8
                            "", //9
                            "", //a
                            "", //b
                            "", //c
                            "", //d
                            "", //e
                            "", //f
                            //-----------------------0x40
                            "", //0x0
                            "OSC2 Mod Env SW", //1
                            "LFO Env SW", //2
                            "Env1 Inv SW", //3
                            "OSC1 Env SW", //4
                            "OSC2 Env SW", //5
                            "Env2 Inv SW", //6
                            "LFO Gatesync SW", //7
                            "OSC2 Keysync SW", //8
                            "OSC1 Wide Tune SW", //9
                            "OSC2 Wide Tune SW", //a
                            "OSC2 Octave", //b
                            "OSC1 Octave", //c
                            "OSC1 Keysync SW", //d
                            "Velocity SW", //e
                            "OSC1 LFO Tune", //f
                            //-----------------------0x50
                            "OSC1 LFO Width", //0x0
                            "OSC2 LFO Tune", //1
                            "OSC2 LFO Width", //2
                            "Duo Tone SW", //3
                            "VCF LFO Volume", //4
                            "VCF Env Cutoff", //5
                            "LFO Keysplit", //6
                            "VCF LFO cutoff", //7
                            "OSC1 Mod Env SW", //8
                            "VCF Env SW", //9
                            "VCF Volume", //a
                            "VCF Resonance", //b
                            "OSC Mix", //c
                            "OSC1 Shape", //d
                            "OSC2 Shape", //e
                            "LFO Shape", //f
                            //-----------------------0x60
                            "ENV1 Attack", //0x0
                            "", //1
                            "", //2
                            "", //3
                            "Mod Wheel Mode", //4
                            "", //5
                            "ENV1 Decay", //6
                            "ENV1 Sustain", //7
                            "ENV1 Release", //8
                            "ENV2 Attack", //9
                            "ENV2 Decay", //a
                            "ENV2 Sustain", //b
                            "ENV2 Release", //c
                            "OSC1 Env Tune", //d
                            "OSC1 Env Width", //e
                            "VCF 2/4 Pole SW", //f
                            //-----------------------0x70
                            "OSC2 Env Tune", //0x0
                            "OSC2 Env Width", //1
                            "Glide", //2
                            "Bend Range", //3
                            "Mod Mode", //4
                            "VCF Keysync", //5
                            "Voice Number", //6
                            "LFO Sync SW", //7
                            "All Notes Off", //8
                            "", //9
                            "", //a
                            "All Notes Off", //b
                            "", //c
                            "", //d
                            "", //e
                            "" //f
```
