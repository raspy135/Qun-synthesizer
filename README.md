# Qun-synthesizer
Qun-synthesizer is an analog modeling synthesizer engine for ESP32 Lyrat, worked with Nunomo's daughter board.

![qun_synth](./manual_images/qun_synth.jpg)

### Overview
- Connectivity: Standard MIDI(TRS A type), BLE MIDI, UART MIDI(Supports MAC OS X and Windows through my SerialMIDI https://github.com/raspy135/serialmidi .

* ESP32 Lyrat
  * Originally designed for smart speaker.

* Analog Modeling engine
  * The engine is made from scratch.
  * All analog modeling engine can be configured through MIDI. 
  * 2 Oscillators, it can be used as dual tone. 
  * Flexible MOD CV source
  * 4 Envelove Generators
  * FM (4 Operators x 2)
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
    
### MAJOR MODES

 The synth has three major modes, it can be changed by pressing ESP32 Lyrat's three touch buttons. Parameter mode is the main mode of the synthesizer. :

* “Play” Button = Play Mode. It’s for playing and sequencer.
* “Set” Button = Setting. For load / save / system setting.
* “Vol+” Button = Parameter Mode. Change synthesizer’s parameters.
(VOL- button is disabled by hardware)
* ![major_mode_change](manual_images/major_mode_change.gif)

### BASIC OPERATION
Each major mode has sub modes. Select sub mode (For example, `Prm:OSC1` is a sub mode for Oscillator 1.
* To switch sub mode, press Mode button (on the top board) + rotate dial. Keep pressing Mode button while you are selecting sub mode.
* ![sub_mode_change](manual_images/sub_mode_change.gif)
* If you press Mode button and released, sticky mode button is activated(Square is indicated next to mode name)). If you press one of 8 buttons, this will change first 8 of Sub mode in the Major mode. For example in Parameter mode, press Mode button, then press button 2(Second button of the 8 buttons) will switch to OSC2 sub mode.

After you enter the sub mode you want, then next you need to select and change the parameter.
* Press one of 8 buttons + rotate dial = Change parameter
If you just press one of 8 buttons and release, then it indicates the current value of the parameter.
Once you selected the parameter, the parameter is assigned to the dial.
* Rotate the dial = Change the current parameter. 
* ![parameter_select_change](manual_images/parameter_select_change.gif)

*Some parameters does action just by pressing the button. (e.g. load preset)

### Dumping current preset
All tone configuration can be specified as sort of MIDI CC signals.Pressing “REC” button will dump all configuration.
If you record the MIDI signals to your MIDI recorder or DAW, it can be used as tone preset.

### All note off
Pressing “Mode” button on the base board (not Red top board) will turn on / turn off receiving MIDI signal. It can be used as MIDI Panic button. 

### PRM:OSCILLATOR1,2
![diagram_osc](manual_images/diagram_osc.jpg)

(Blue : sources, Green: output) This diagram shows OSC1 and OSC2 features. (Actually all parameters can be controlled via MIDI even it's shown as dial)

The oscillator is a hybrid of classic analog synth and FM. FM has its own parameters, it can be configured in FM conf / ENV3/4 Other Mode.

CV input routing is very flexible.
One CV is connected to LFO (For tune/width)
Another one is selectable. It could be one of EG1 to EG4, AUX (Audio in), OSC2 signal, OSC2 signal after EG processing, Freq level out from OSC1, or Freq level out from OSC2.

SHAPE
	`Saw, Sine, S&H, Square, Triangle, W Noise, P Noise, FM, AUX`
	*AUX means audio input from LINE or MIC.
	*FM has 4 operator inside. See FM Conf Sub mode for detail.
PULSE WIDTH
TUNE
OCTAVE
MOD TUNE
	MOD bus feedback to tune.
MOD WIDTH
	MOD bus feedback to pulse width.
LFO TUNE
	LFO feedback to tune.
LFO WIDTH
	LFO feedback to pulse width.

### PRM:MIX
Mix controls mixer and effects.

![diagram_mix_aux](manual_images/diagram_mix_aux.jpg)

OSC MIX
	Balance between Osc1 and Osc2
FM
	FM modulation Osc2 to Osc1
VCF VOLUME
	Volume to VCF. It can be used as generic volume control.
AUDIO IN GAIN
	GAIN from Line in / Mic In

![diagram_effector](manual_images/diagram_effector.jpg)

EFFECT TYPE
	Delay, Chorus1, Chorus2, Flanger1, Flanger2
EFFECT SPEED
	Controls Effect LFO rate
EFFECT DEPTH
	Amount of effect
EFFECT FEEDBACK
	Feedback for Delay

Delay can set very short. Very short delay will make an interesting to simulate flute or violin. 

### PRM:Env1/2
The synth has four Envelope generators. ENV1 and ENV2 are independently and fully configurable.ENV3 and ENV4 shares the parameter. Also ENV3 / ENV4 is connected to FM operators.
ENV1 and ENV2 are grouped to first voice with Duo mode. Env3 and Env4 are grouped to second voice with Duo mode.

![diagram_eg](manual_images/diagram_eg.jpg)

ATTACK
	Attach time
DECAY
	Decay time
SUSTAIN
	Sustain level
RELEASE
	Release time

### PRM:OSC Switches
OSC has many switches to change its behavior.

ENV Sel
	Selects Envelope(VCA) for the oscillator.
	`EG1, EG2, EG3, EG4, AUX, OSC2, OSC2EG, FRQ1, FRQ2`
WIDE TUNE
	When it is ON,you can tune wider range by OSC’s tune parameter.
MOD SEL
	Select Mod signal source. It is connected to OSC's tune or width.
	`EG1, EG2, EG3, EG4, AUX, OSC2, OSC2EG, FRQ1, FRQ2`

ENV INV SW
	It inverts Envelope Generator’s polarity. Env1 and Env2 can change its polarity.

### PRM:LFO
LFO is similar to OSC, although selectable shape is slightly different.

![diagram_lfo](manual_images/diagram_lfo.jpg)

LFO TUNE
	Tune
LFO SHAPE
	`Saw, Sine, RevSaw, S&H, Square, Triangle, AUX, OSC2, OSC2EG`
	*AUX is audio input from LINE / MIC.
	*You can use OSC2 as LFO source. Even you can modulate OSC2 by OSC2.
	*OSC2EG is the signal from OSC after Envelope Generator processing.
LFO PULSE WIDTH
	Pulse width
LFO MOD TUNE
	Envelope generator feedback to tune.
LFO MOD WIDTH
	Envelope generator feedback to pulse width.
KEYSPLIT
	This is very unique feature of the synth.
	When it is not zero,  then note number above the parameter becomes LFO rate controller.
	(If you set 60, then C4 or higher note becomes LFO controller)
	Higher notes will generate higher LFO rate.
	If you press multiple notes, it doubles and triples the rate.
	It is great for live playing.
LFO GATESYNC
	LFO’s phase is reset by every note hit when it is ON.
LFO MOD SEL
	`EG1, EG2, EG3, EG4, AUX, OSC2, OSC2EG, FRQ1, FRQ2`

### PRM:VCF
The synth has one filter.

![diagram_vcf](manual_images/diagram_vcf.jpg)

CUTOFF
	Set the cutoff frequency.
RESONANCE
	Resonance
VCF TYPE
	`LPF,BPF,HPF,Notch`
VCF MOD CUTOFF
	Mod feedback to cutoff. Mod source selection is located in `Prm:Key / Other` sub mode.
VCF LFO CUTOFF
	LFO to cutoff feedback.
VCF LFO Volume
	LFO feedback to VCF’s input volume.
OSC1 BYPASS
	Filter bypass switch for OSC1
VCF KEYSYNC
	When it is not zero, VCF’s cutoff will follow s the playing note.  Higher value is more sensitive.
	Great with Mono, but not working well with Duo tone because it has only one VCF.

### PRM:KEY/OTHER
Configures other parameters.

VCF 4/2 POLE SW
	Change filter's number of poles. 2 poles(12dB/Oct) when it is ON. 4 poles(24dB/Oct) when it is OFF.
VCF MOD SEL
	`EG1, EG2, EG3, EG4, AUX, OSC2, OSC2EG, FRQ1, FRQ2`
GLIDE
	Tune changes gradient when the value is not zero. Works with Mono.
BEND RANGE
	Pitch bend range.
MOD MODE
	Modulation mode.
VELOCITY SW
	Envelope generators become velocity sensitive when  it’s ON.
MONO/DUO/POLY
	* Mono = Mono (2OSCs per voice)
	* Duo = Duo Tone (1 OSC per voice)
	With duo tone mode, MIX should be middle and set the same parameters to both EGs.
	Poly Mono and Poly Duo is for multiple device stacked configuration. You can use multiple Qun Synthesizers to combine polyphonic synth.
	Please see  “Poly operation” for detail.
LFO SYNC SW
	LFO rate will synchronize with MIDI clock when it is ON.

### PRM:FM CONF
![diagram_fm](manual_images/diagram_fm.jpg)

The synth has 2 x 4 operator FM engine. OSC1 or OSC2 can be set to FM mode.
Each operator has sine wave.
OSC1 and OSC2 share the FM parameter.
On top of two FM engines, you can do FM modulation between oscillators.

FM ALGORITHM
	Selects FM algorithm
FM (ABCD) AMP
	Oscillator (ABCD)’s amplitude.
FM (ABC) FREQ
	Oscillator (ABC)’s frequency (multiple of frequency of D)
PRM:ENV3/4 / Other

OSC1 KEYSYNC SW
	Frequency lock SW for OSC1. When it's OFF, it will ignore MIDI note.
OSC2 KEYSYNC SW
	Frequency lock SW for OSC2
FM ENV3 CONN
	Select operator(s) to connect ENV3
		FM engine's operaters can use Envelope generator (Env3/Env4).
	Part of Operators can be connected to ENV3. The connection is configurable by this parameter.
	Technically ENV4 exists for OSC2’s FM engine, but the parameter is shared with ENV3.
	After the processing it will be modulated by ENV1 or ENV2.
ENV3/4 ATTACK
ENV3/4 DECAY
ENV3/4 SUSTAIN
ENV3/4 RELEASE

### PLY:PLAY
Simple playing mode by using 8buttons. Scale will be determined by the scale setting. Useful to check the sound. Probably not useful for live performance. The sequencer is more practical for live performance (Or just use external sequencer / DAW).

### PLY:SEQ PLAY
Main Sequencer control mode.
Some buttons will do the action just by pressing button, without rotating the dial.

Button 1: Play/Stop the sequencer.
Button 2: Transpose
Button 3: Width(Note length)
Button 4: Note Randomness
Button 5: Arpeggiator

Button 6 to Button 8 are for Looper. It always synchronizes with the sequencer.

Button 6: Record / Overdub
Button 7: Play the looper
Button 8: Stop the playback or delete the recording.

### PLY:SEQ ON/OFF
The sequencer has 8 steps, but it has more modes than ON/OFF.

Left-filled circle : One note in the step.
Right-filled circle : One note in the step but it plays at the second half.
Full-filled circle : Two notes in the step(1/16th)
Striped circle : 3 notes in the step (Triplet)

### PLY:SEQ BANK
The sequencer has 7 banks, you can switch the bank while playing.
This will not be saved unless you press button 8.

Button 1 to 7: Change bank 1 to 7
Button 8: Save all banks to flash memory.

### PLY:SEQ TUNE
Press one of the eight buttons and turn the dial, then it will modify tune offset for each step.

### PLY:SEQ WIDTH
Press one of the eight buttons and turn the dial, then it will modify width(note length) offset for each step.

### PLY:SEQ CONFIG
Button 1: BPM
Button 2: Key (For Scale)
Button 3: Scale. Playing note will be quantized by this scale.
Button 4: Sequencer loop count. Default is 8.

### SET:LOAD(Bank 1 to 4)
Load from the saved preset.
Pressing button 1 to 8 will load preset 1 to 8. It has 4 banks.

### SET:SAVE(Bank 1 to 4)
Save current parameters to the preset.
Pressing button 1 to 8 will save to preset 1 to 8. It has 4 banks.

### SET:SYSTEM
System Setting is the setting that is not included in the patch setting. To change the parameter, press the one of 8 buttons and rotate the dial.

AUX:Audio source select. Mic(the board has two onboard microphones) or Line in. To avoid confusion, the setting will not be saved. LINE in is the default.

Number of devices: Number of devices for poly mode. Set 1 if you don’t have multiple devices. It will be stored in the flash memory.

Device Index : Device Index. Set 1 if you don’t have multiple devices. It will be stored in the flash memory.

LINE in THRU: If it is off, it is automatically turn on or off LINE IN pass through by mono / poly setting. If it is on, the synth always passes the signal. This setting will be stored in the flash memory.

LINE in HPF: Off is default. LINE in has external(On the Lyrat board) and internal(In the ES8388 codec chip) high-pass filter. This setting changes internal HPF. Technically you can get DC-coupled LINE in by changing ESP32 Lyrat board. This is useful when you use LINE IN as CV from external modules. See `DC coupling` section for detail.

### POLYPHONIC SETUP
The synth can be used as Mono or Duo tone if you have one device.
The voice number can be increased up to 8(with Duo mode), by stacking up the synths.

Connection / Configuration:

Polyphonic setup won’t work as standalone.

It assumes you connect the synths to the DAW or MIDI controller that will echo the synth’s MIDI OUT to MIDI IN. Normally it can be done by just turning on recording mode in the DAW.
Some hardware MIDI keyboard might have the feature.
Set up MIDI to receive exact same data for all devices. It can be done by any way of MIDI connection, but using UART or classic MIDI will be easier than BLE MIDI.
If you use UART or classic MIDI, use the 4 pin located above the OLED screen.

Pin connection is showing as below:
```
V   T   R   G
C   X   X   N
C           D
```
Determine master and slave devices. Slave device can be headless.
Connect Master’s RX to slave’s RX, Master’s GND to slave’s GND. This will connect MIDI-IN signal between devices. VCC and TX are not used.
Power both devices. Sometimes ground-loop noise can be happened. This case use separated power supply.
Connect MIDI to the master.
Configure master device (MIDI keyboard or DAW software) to echo all received MIDI signal. Typically it means set the channel to recording mode. All DAW should have the capability.Some MIDI keyboard may not have the setting.

If the set up is correct, you will hear the sound from both devices when you play notes.
After you confirmed it, connect slave’s output to Master’s line in. Then you don’t need two audio inputs for audio interface.

Set number of devices and Device index in the System configuration. Device index = 1 is master. This configuration will be stored in the flash memory.
Configure master’s tone, get your favorite sound.
If you use Mono mode, set MONO/DUO/POLY config to “PolyMono” mode. Then you will get two voices if the number of device is two.
If you use Duo mode, set MONO/DUO/POLY config to “PolyDuo” mode. Then you will get four voices if the number of device is two.
At the point the parameters are not synchronized state, only master is configured properly. To synchronize all parameters, press “Rec” button to dump all parameters. It will be sent to slave devices.
You can play notes.
Often time you may forget to set the channel to recording mode on DAW, then parameter will be out of sync state. Pressing “Rec” button will sync the parameters.



### TIPS/TROUBLESHOOT
* Trouble with Duo Mode: You need to set up properly to play duo mode properly.
	Mix (set to center)
	Associated Envelope (OSC1 -> EG1 or 3, OSC2 -> EG2 or 4. Normally OSC1 uses EG1, OSC2 uses EG2)
	Set OSCs setting identical to get the same tone.
	VCF’s Keysync will not work well.
	I suggest to start with simple patch.
* Suddenly NO SOUND!
	Reset the device by pressing RST button if you are OK with it.
	Reset the preset. Long pressing (4 seconds) of REC button will initialize the preset.
	Probably it’s because of last parameter you changed, or some unexpected MIDI cc signal. See the 2nd line of the display, it indicates the parameter received at last.
	Level overflow may cause the silent (e.g. Giving massive delay feedback).
	Maybe it’s not worth to spend time to figure out why, reset the device.
	Save your preset on DAW by pressing REC button to dump MIDI data. It’s a series of CC changes. Then you don’t loose the preset.
* BLE trouble with Windows: We don’t support WINDOWS for BLE MIDI connection. Please use UART MIDI or MIDI TRS A.
* A Noise when you connect multiple devices (chained audio)
	Probably it is because of ground loop. Use separated power supply. 
* I hear the noise but I don’t connect anything to line in.
	Toggle MIC/LINE select (in Prm:System), set to Line in.
	Toggle Line THRU to off.
	Check Mono/Poly Mode setting. If it’s poly mode, LINE IN pass through is ON.
	Use different power supply. Basically it’s less noise by using separated charger.
* Use hardware effectors to get better result. That’s one of the benefit using hardware synth.
* Don’t be afraid to get clipped. Clipping could be the entrance to the new sound.
* I want to use AUX as CV IN
	AUX is connected to a lot of CV controls so you can use AUX to control tune/width/LFO and others. However, the LINE in has capacitor in the path, it means the signal is AC. Using it as LFO should work, probably down to 5Hz. But DC signal (e.g. hold the same voltage 5 seconds) might not work.

### DC Coupling (ADVANCED and HIGH RISK of BREAK)
ESP32 Lyrat board has capacitors for HPF.
https://dl.espressif.com/dl/schematics/esp32-lyrat-v4.3-schematic.pdf
Technically you can get DC-coupled input by removing and shorting C61 and C63.
![dc_coupling](manual_images/dc_coupling.jpg)

They are extremely small chips so put extra caution not to break the board.

It is OK to short even between channels, the signal will be downmixed. Using hot air for SMD reworking is recommended.




### Supported MIDI Control numbers
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
                            "ENV3/4 Attack", //9
                            "ENV3/4 Decay", //a
                            "ENV3/4 Sustain", //b
                            "ENV3/4 Release", //c
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
                            "LFO Mod Tune", //0x0
                            "LFO Mod Width", //1
                            "FM Algorithm", //2
                            "FM Env3 Conn", //3
                            "", //4
                            "OSC1 Tune(LSB)", //5
                            "Cutoff(LSB)", //6
                            "OSC2 Tune(LSB)", //7
                            "LFO Tune(LSB)", //8
                            "FM A Amp", // 9
                            "FM A Freq", //a
                            "FM B Amp", //b
                            "FM B Freq", //c
                            "FM C Amp", //d
                            "FM C Freq", //e
                            "FM D Amp", //f
                            //-----------------------0x40
                            "", //0x0
                            "OSC2 Mod Sel", //1
                            "LFO Mod Sel", //2
                            "Env1 Inv SW", //3
                            "OSC1 Env Sel", //4
                            "OSC2 Env Sel", //5
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
                            "Mono/Duo/Polly", //3
                            "VCF LFO Volume", //4
                            "VCF Mod Cutoff", //5
                            "LFO Keysplit", //6
                            "VCF LFO cutoff", //7
                            "OSC1 Mod Sel", //8
                            "VCF Mod Sel", //9
                            "VCF Volume", //a
                            "VCF Resonance", //b
                            "OSC Mix", //c
                            "OSC1 Shape", //d
                            "OSC2 Shape", //e
                            "LFO Shape", //f
                            //-----------------------0x60
                            "ENV1 Attack", //0x0
                            "AUX In Gain", //1
                            "", //2 (Won't be dumped)
                            "", //3 (Won't be dumped)
                            "Mod Wheel Mode", //4
                            "", //5 (Won't be dumped)
                            "ENV1 Decay", //6
                            "ENV1 Sustain", //7
                            "ENV1 Release", //8
                            "ENV2 Attack", //9
                            "ENV2 Decay", //a
                            "ENV2 Sustain", //b
                            "ENV2 Release", //c
                            "OSC1 Mod Tune", //d
                            "OSC1 Mod Width", //e
                            "VCF 4/2 Pole SW", //f
                            //-----------------------0x70
                            "OSC2 Mod Tune", //0x0
                            "OSC2 Mod Width", //1
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
