## リンク一覧

Shop page : https://shop.nunomo.com/products/qun-pocket-synthesizer

[Wiki](https://github.com/raspy135/Qun-synthesizer/wiki) for ideas and tips

[Patches](./patches) for tone patches

[Issues](https://github.com/raspy135/Qun-synthesizer/issues) : Report issues when you have problems or questions.

[Firmware](https://github.com/raspy135/Qun-synthesizer/tree/master/firmware) : For a firmware update.

**We are looking for Pull requests for patches, we will add it to this repository**

**We are looking for your feedback! For generic impression / feedback, email to info@nunomo.com**

# Qun-synthesizer

Qun-synthesizerはESP32LyraTをベースとしたアナログモデリングシンセで、Nunomoのドーターボードと組み合わせて動作します。

![qun_synth](./manual_images/qun_synth.jpg)

## 概要
- 接続: Standard MIDI (TRS A type), BLE MIDI, MIDI UART(UART経由のMIDI) supports macOS and Windows through my SerialMIDI https://github.com/raspy135/serialmidi .

* ESP32 Lyrat
  * ESP32 Lyratは元々スマートスピーカー開発用のボードです。QUNシンセサイザーはESP32LyraTを使用することで、コストを削減しています。ESP32LyraTはシンセサイザー向けにデザインされていない為、幾つかの制限があります（例えばUSB端子がありますが、USB to MIDIではなくMIDI UARTです）

* **アナログモデリングエンジン**
  * アナログモデリングエンジンは**完全にオリジナル**で、一から作られています。最新のアルゴリズムを使用してオーガニックなサウンドを実現しています。このエンジンはクラッシックシンセのエミュレートではなく、現代的なサウンドの為にデザインされています。
  * サウンドエンジンはESP32のCPUパワーを余すところなく使用しています。
  * 1ms未満の超低レイテンシです。（エンジン自体のレイテンシ。MIDI接続方法によってトータルのレイテンシは異なります）。この低レイテンシはCPUがサウンドのみを処理しており、PCのように予期しない処理の為のバッファが必要ないので実現可能です。
  * すべての音源パラメータはMIDI CCメッセージとして表現可能です。つまりすべてのパラメータがCCメッセージで制御できます。
  * クリーンな2つのオシレータを搭載。低価格のシンセサイザーはここを妥協していることがありますが、QUNはエイリアスノイズのないオシレータを使用し、すべての内部演算は音質のため、浮動小数点で行われます。
  * 個々のオシレータは別々に使ってDual Toneとして演奏できます。
  * 複数のQUNを用いれば、連携してポリフォニックにもできます。
  * **柔軟な MOD (CV) ルーティング**. 信号経路はとてもフレキシブルで、モジュラーシンセのようにルーティングすることができます。通常のようにも使用できますし、ラディカルにもできます！
  * 2チャンネルの AUX(R/L) を外部オーディオ入力あるいは**CV Input**として使用できます。(CV InputはACカップリング）
  * 4 つのEnvelope Generators
  * FM 変調(4 オペレータ x 2 オシレータ。更にオシレータ間のFM変調も可能)
  * 1 LFO, レートをMIDIノートで制御することも可能
  * 1つの オーガニックなサウンドのVCF
    * 2/4 Poles, Low-pass, Band-pass, High-pass, Notch)
    * **Organic linear もしくはクラッシックな non-linear**
    * Key Sync
  * 1 Effect (Delay, Chorus, Flanger)
  * MIDI clock sync
  * **3 advanced クリッピングアルゴリズム** はオーガニックなクリッピングサウンドを作ります。
* Player / Sequencer
  * Player (piano mode)
  * 8-step シーケンサはランダム機能でインスピレーションを与えます。
    * Note On/Off/Double/Triple
    * Transpose / Note width (Length)
    * Randomness control
    * スケールクォンタイズ
    * Arpeggiate steps
  * ルーパ
    * ルーパーは30秒まで録音可能で、シーケンサと同期します。
    
    * Play / Rec / Overdub

## CONNECTIONS

* Power : 良質の USB 電源を使用してください。USBケーブルをPOWERと表記されている方のUSBポートに接続してください。
BLE MIDI : Bluetooth MIDIはiOSまたはmacOSに対応しています。Windowsには非対応です。BLE MIDIのレイテンシは一般的に15～20msとなるため、低レイテンシーで使用したい場合にはMIDIまたはMIDI UARTを使用してください。
* MIDI : MIDIケーブルの接続には**TRS A**型のMIDIアダプタ（別売）を使用します。TRS AタイプのアダプターはKORGやAKAI、MAKE NOISEのアダプターと同タイプです。
* MIDI UART: 従来のMIDIインターフェースの代わりにMIDI UARTを使用することもできます。専用のプログラムとMIDIブリッジ（WindowsのLoopMIDI、macOSのIACなど）が必要ですが、一度設定してしまえばUSB-MIDIのように使うことができます。LyratボードののUARTと表記されたUSB端子とコンピュータに接続します。UARTドライバをインストールする必要があります (https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)MIDI UARTの詳細については、https://github.com/raspy135/serialmidi プロジェクトを参照してください。MIDIのbaud rateは31250bpsに設定します。
* 本機は外部のオーディオ信号を処理することができます。簡易的なマイクもボード上に搭載しています。      
* 右側にLINE INとPHONE OUT端子があります。出力はステレオプラグ対応ですが、左右のチャンネルは同じ信号を出力しています。

## MAJOR MODES

本機には大きく分けて3つのモードがあり、ESP32 Lyrat上の3つのタッチボタンを押すことで変更することができます。パラメータモードはシンセサイザーのメインモードです。

**【ご注意】本機は汎用のESP32 Lyratメインボードを利用している為、ボード上の表記がそのまま本機の機能ではない場合がございます。例えばボード上にはPLAY、SET、VOL-、VOL+などの印字がありますが、印字通りの機能ではないことをご確認ください。つまりVOL-、VOL+のボタンを操作しても音量が調整できるというわけではありません。**

* “Play” Button = プレイモード。演奏やシーケンサーをするためのモード

* “Set” Button = 設定のためのモード。ロード/セーブ/システム設定に使用

* “Vol+” Button = パラメータモード。シンセサイザーのパラメータを変更します。(注意！-　VOL-ボタンは使用しません。ハード的に接続されていません)

* RSTボタンはボードをリセットします。Bootボタンはファームウェアのアップデートにのみ使用します。


* ![major_mode_change](manual_images/major_mode_change.gif)

## BASIC OPERATION
各メジャーモードにはサブモードが用意されています。例：Prm:OSC1は Oscillator 1のサブモードです。

* サブモードを切り替えるには、Modeボタンを押してダイヤルを回します。サブモード選択中はモードボタンを押し続けてください。
* ![sub_mode_change](manual_images/sub_mode_change.gif)
* サブモードを切り替える別の方法として、Modeボタンを押して離すと、スティッキーモードとなります（モード名の横に四角が表示されます）。8つのボタンのうち1つを押すと、メジャーモードのサブモードの最初の8つが切り替わります。例えば、パラメータモードでモードボタンを押した後、ボタン2（8つのボタンのうち2番目のボタン）を押すと、OSC2のサブモードに切り替わります。位置を覚えてしまえば、この方が時間を短縮できます。

目的のサブモードに入ったら、次にパラメータの選択と変更を行います。

* 8つのボタンのうち1つを押す＋ダイヤルを回す＝8つのボタンのうち1つを押すと、現在のパラメータの値が表示されます。パラメータを選択するとダイヤルにパラメーターがアサインされます。
* ダイヤルを回転させる＝現在のパラメータを変更します。

* ![parameter_select_change](manual_images/parameter_select_change.gif)

* ボタンを押すだけで動作するパラメータもあります。（例： load presetなど）

### Dumping current preset

プレイモード以外では、すべての音色の設定をMIDI CC信号などから指定することができます。また"REC"と表記されているボタンをPRMモードで押すと、すべての設定をダンプできます。MIDI信号をMIDIレコーダーやDAWに録音すれば、トーンプリセットとして使用することができます。

### All note off

プレイモード以外の時は、ベースボード側（赤‎いボードではなく）の "Mode "と表記されているボタンを押すと、MIDI信号の受信をオン/オフに設定することができます。MIDIパニックボタンとしても利用できます。

## Parameter Mode

### PRM:OSCILLATOR1,2
![diagram_osc](manual_images/diagram_osc.jpg)

**Blue : sources, Green: output**. 
この図はOSC1とOSC2の機能を示しています。この図は説明のためのもので、OSCの全ての機能を網羅しているわけではありません。本物のハードウェアシンセとは異なり、ダイヤルを含む全てのパラメーターをMIDIでコントロールすることができます。

オシレーターは、クラシックなシンセとFM、グラニュラーのハイブリッドです。FMには独自のパラメーターがあり、FM conf / ENV3/4 Other Modeで各種設定が可能です。

CV入力のルーティングはとても柔軟にデザインされています。1つのCVはLFOに接続されています。もう1つは選択することが可能です。EG1からEG4、AUX（オーディオ入力）、OSC2、EG処理後のOSC2、OSC1からのFreqレベルアウト、OSC2からのFreqレベルアウトのいずれかを選択できます。

1. SHAPE

Saw, Sine, S&H, Square, Triangle, W Noise, P Noise, FM, AUX から選択できます。
*AUXはオーディオ入力（ラインまたはマイク）を指します。 means audio input from LINE or MIC.
*FM は4つのオペレーターを持ちます。詳細はFM Conf Sub modeの項をご参照ください。
*GranularはGranularの項をご参照ください。

2. PULSE WIDTH
	
本機では矩形波に限らずパルス幅を調整できるため、たいへんユニークなサウンドを得る事ができます
    Triangle（三角波）
    SAW（ノコギリ波）
    Square（矩形波）

3. TUNE

4. OCTAVE

5. MOD TUNE

 MOD で選択されているCVをTuneにフィードバックする量
 
6. MOD WIDTH

 MOD で選択されているCVをWidthにフィードバックする量

7. LFO TUNE

　LFOをTuneにフィードバックする量

8. LFO WIDTH

　LFOをWidthにフィードバックする量

### PRM:MIX
Mix controls mixer and effects.

![diagram_mix_aux](manual_images/diagram_mix_aux.jpg)

1. OSC MIX

	Balance between OSC1 and OSC2

2. FM

	FM modulation OSC2 to OSC1. 
	That means that each oscillator has internal 4 operator FM, on top of it, you can modulate OSC1 by OSC2.

3. VCF VOLUME

	Volume to VCF. It can be used as generic volume control.

4. AUDIO IN GAIN

	GAIN from Line in / Mic In

![diagram_effector](manual_images/diagram_effector.jpg)

5. EFFECT TYPE

	Delay, Chorus1, Chorus2, Flanger1, Flanger2

6. EFFECT SPEED

	Controls Effect LFO rate

7. EFFECT DEPTH

	Amount of effect

8. EFFECT FEEDBACK

	Feedback for Delay

Delay can set very short. Very short delay will make an interesting to simulate flute or violin. 

### PRM:ENV1/2
The synth has four Envelope generators. ENV1 and ENV2 are both independently and fully configurable. ENV3 and ENV4 shares the parameter. Also ENV3 / ENV4 is connected to FM operators.
ENV1 and ENV2 are grouped to first voice with Duo mode. ENV3 and ENV4 are grouped to second voice with Duo mode.

![diagram_eg](manual_images/diagram_eg.jpg)

1/5 . ATTACK

	Attack time

2/6 . DECAY

	Decay time

3/7 . SUSTAIN

	Sustain level

4/8 . RELEASE

	Release time

### PRM:OSC Switches
OSC has many switches to change its behavior.

1/5 . ENV Sel

	Selects Envelope(VCA) for the oscillator.
	`EG1, EG2, EG3, EG4, AUX, OSC2, OSC2EG, FRQ1, FRQ2, ON`
2/6 . WIDE TUNE

	When it is ON, you can tune wider range by OSC’s tune parameter.
3/7 . MOD SEL

	Select Mod signal source. It is connected to OSC's tune or width.
	`EG1, EG2, EG3, EG4, AUX, OSC2, OSC2EG, FRQ1, FRQ2, ON`

4/8 . ENV INV SW

	It inverts Envelope Generator’s polarity. ENV1 and ENV2 can change its polarity.

### PRM:LFO
LFO is similar to OSC, although selectable shape is slightly different.

External clock sync setting is available in "PRM:KEY/OTHER" menu.

![diagram_lfo](manual_images/diagram_lfo.jpg)

1. LFO TUNE

	Tune

2. LFO SHAPE

	`Saw, Sine, RevSaw, S&H, Square, Triangle, AUX, OSC2, OSC2EG`
	*AUX is audio input from LINE / MIC.
	*You can use OSC2 as LFO source. Even you can modulate OSC2 by OSC2.
	*OSC2EG is the signal from OSC after Envelope Generator processing.

3. LFO PULSE WIDTH

	Pulse width

4. LFO MOD TUNE

	Envelope generator feedback to tune.

5. LFO MOD WIDTH

	Envelope generator feedback to pulse width.

6. KEYSPLIT

	This is a unique feature of the synth.
	When it is not zero, then note number above the parameter becomes LFO rate controller.
	(If you set 60, then C4 or higher note becomes LFO controller)
	Higher notes will generate higher LFO rate.
	If you press multiple notes, it doubles and triples the rate.
	It is great for live playing.

7. LFO GATESYNC

	LFO’s phase is reset by every note hit when it is ON.

8. LFO MOD SEL

	`EG1, EG2, EG3, EG4, AUX, OSC2, OSC2EG, FRQ1, FRQ2`

### PRM:VCF
The synth has one filter. LPF / BPF/ HPF / Notch can be selected. It also has a 4/2 poles switch, and linear and non-linear switch. Linear gives organic resonance, Non-linear gives more character.

![diagram_vcf](manual_images/diagram_vcf.jpg)

1. CUTOFF

	Set the cutoff frequency.

2. RESONANCE

	Resonance.

3. VCF TYPE

	`LPF,BPF,HPF,Notch`.

4. VCF MOD CUTOFF

	Mod feedback to cutoff. Mod source selection is located in `Prm:Key / Other` sub mode.

5. VCF LFO CUTOFF

	LFO to cutoff feedback.

6. VCF LFO Volume

	LFO feedback to VCF’s input volume.

7. OSC1 BYPASS

	Filter bypass switch for OSC1.

8. VCF KEY SYNC

	When it is not zero, VCF’s cutoff will follows the playing note. Higher value is more sensitive.
	Great with Mono, but not working well with Duo tone because it has only one VCF.

### PRM:KEY/OTHER
Configures other parameters.

1. VCF 4/2 POLE / NoLinear

	Change filter's number of poles, and selects linear or non-linear. 2 Poles is -12db/oct, 4 Poles is -24db/oct. 2 Poles filter gives brighter character. Linear(LI) has less character, but linear's resonance is sharp and more organic like real instruments. Non-linear(NL) has more character, has more harmonics, close to other synthesizers. 
	If you use cutoff envelope or copying patch from other synths, typically Non-linear is suitable.
	If you try to achieve something close to real instrument or you want clear sound, linear is suitable.

2. VCF MOD SEL

	`EG1, EG2, EG3, EG4, AUX, OSC2, OSC2EG, FRQ1, FRQ2, ON`.

3. GLIDE

	Tune changes gradient when the value is not zero. Works with Mono.

4. BEND RANGE

	Pitch bend range.

5. MOD MODE

	Modulation mode. `OSC1 and OSC2 (Pulse Width), OSC1 only, OSC2 only, or VCF's cutoff.`

6. VELOCITY SW

	Envelope generators become velocity sensitive when it’s ON.

7. MONO/DUO/POLY

	One synthesizer can be used as Monophonic or Duophonic.
	* Mono = Mono (2 OSCs per voice)
	* Duo = Duo Tone (1 OSC per voice)
	With duo tone mode, MIX should be middle and set the same parameters to both EGs.
	Poly Mono and Poly Duo is for multiple device stacked configuration. You can use multiple Qun Synthesizers to build polyphonic synth.
	Please see “Polyphonic setup” for detail.

8. LFO SYNC SW

	LFO rate will synchronize with MIDI clock when it is ON.

### PRM:FM CONF
![diagram_fm](manual_images/diagram_fm.jpg)

The synth has 2 x 4 operator FM engine. OSC1 or OSC2 can be set to FM mode.
Each operator has sine wave.
OSC1 and OSC2 share the FM parameter.
On top of two FM engines, you can do FM modulation between oscillators.

1. FM ALGORITHM

	Selects FM algorithm. "ABCD" indicates each operator.
	Vertical relationship means upper operator modulates lower operator.
	For example,
```
	    A
	B C D
```
	This means that operator A modulates operator D. B, C and modulated D will be mixed in parallel.
```
	A
	B
	C
	D
```
	This means A modulates B, the result modulates C, the result modulates D.

2/4/6/8. FM (ABCD) AMP

	Oscillator (ABCD)’s amplitude.

3/5/7. FM (ABC) FREQ

	Oscillator (ABC)’s frequency (multiple of frequency of D)
	When FM FREQ SNAP (In ENV3/4 Other sub-menu) is off, it be adjust to the exact frequency of harmonics (2,3,4,5..). If you want clean FM sound, turn FM FREQ SNAP on.

### PRM:ENV3/4 / Other

1. OSC1/2 KEY SYNC

	Frequency lock SW for OSC1. When it's OFF(Indicated as "N"), it will ignore MIDI note.

2. FM FREQ SNAP SW

	Frequency snapping for FM. When it's ON, FM sound will be clear, no detune.
	When it's OFF, FM has more character, signature sound.

3. Clipping

	Clipping algorithm. "GRAIN", "MID" or "SOFT". The clipping algorithm will give a good distortion sound, you can even use the synth as multi-effector. See "External Audio processing" for further details.

4. FM ENV3 CONN

	Select operator(s) to connect ENV3
		FM engine's operators can use Envelope generator (ENV3/ENV4).
	Part of Operators can be connected to ENV3. The connection is configurable by this parameter.
	Technically ENV4 exists for OSC2’s FM engine, but the parameter is shared with ENV3.
	After the processing it will be modulated by ENV1 or ENV2.

5. ENV3/4 ATTACK

6. ENV3/4 DECAY

7. ENV3/4 SUSTAIN

8. ENV3/4 RELEASE

## PLAY MODE

### PLY:PLAY
The mode is simple piano playing mode. Scale will be determined by the scale setting. Useful to check the sound. This is probably not useful for live performance. The sequencer is more practical for live performance (or just use external sequencer / DAW).

### PLY:SEQ PLAY
Main Sequencer control mode.
Some buttons will do the action just by pressing button, without rotating the dial.

Button 1: Play/Stop the sequencer.

Button 2: Transpose

Button 3: Width (note length)

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

Full-filled circle : Two notes in the step (1/16th)

Striped circle : 3 notes in the step (triplet)

### PLY:SEQ BANK
The sequencer has 7 banks, you can switch the bank while playing.
This will not be saved unless you press button 8.

Button 1 to 7: Change bank 1 to 7

Button 8: Save all banks to flash memory.

### PLY:SEQ TUNE
Press one of the eight buttons and turn the dial, then it will modify tune offset for each step.

### PLY:SEQ WIDTH
Press one of the eight buttons and turn the dial, then it will modify width (note length) offset for each step.

### PLY:SEQ CONFIG

Button 1: BPM

Button 2: Key (for scale)

Button 3: Scale. Playing note will be quantized by this scale.

Button 4: Sequencer loop count. Default is 8.

## SETTING MODE

### SET:LOAD(Bank 1 to 4)
Load from the saved preset.
Pressing button 1 to 8 will load preset 1 to 8. It has 4 banks.

### SET:SAVE(Bank 1 to 4)
Save current parameters to the preset.
Pressing button 1 to 8 will save to preset 1 to 8. It has 4 banks.

### SET:SYSTEM
System Setting is the setting that is not included in the patch setting. To change the parameter, press the one of 8 buttons and rotate the dial.

AUX: Audio source select. It's Lyrat's microphone, the microphone is not high quality. Mic (the board has two onboard microphones) or Line in. It will be stored in the flash memory. When you use LINE IN, set this setting to "LINE IN(2CH)".

Number of devices: Number of devices for poly mode. Set 1 if you don’t have multiple devices. It will be stored in the flash memory.

Device Index : Device Index. Set 1 if you don’t have multiple devices. It will be stored in the flash memory.

RECV MIDI Ch: Configure receiving MIDI channel. Default is ALL. If you use it with Polyphonic setup, set the same channel or ALL for all devices.

MIDI Forwarding: MIDI message forwarding for standalone polyphonic configuration. When the option is ON, the message forwarding is active with PolyMono or PolyDuo configuration. It's not for generic MIDI forwarding like MIDI THRU. Do not turn this option with DAW configuration. Most of DAW will echo received MIDI message, it will make infinite MIDI message loop. 

LINE in THRU: If it is off, it is automatically turn on or off LINE IN pass through by mono / poly setting. If it is on, the synth always passes the signal. This setting will be stored in the flash memory. It only takes Right channel. Gain is fixed to 1.

LINE in HPF: On is default. LINE in has two HPFs, one is external, one is internal HPF in the chip. This setting turns internal HPF. Turning this off will reduce HPF effect.

Sync Mode: `STOP, MIDI, 2PPQ, 4PPQ, 24PPQ`. Select clock source for sequencer and LFO. See `Clock synchronization` for detail. This could make some confusion so this setting will not be saved to flash memory.

## POLYPHONIC SETUP
The synth can be used as Mono or Duo tone if you have more than one device.
The voice number can be increased up to 8 (with Duo mode), by stacking up the synths.

_Tested well with two devices, using more than 2 devices is experimental at this stage_

### Audio connection

There are two ways for audio setup:

1. Connect Slave's audio out to Master's Line IN. It is recommended for most of cases. It will save audio input to the mixer or audio interface, and Master will process Slave's audio digitally, so it will get a nice soft clipping. That is great for polyphonic (Polyphonic is easier to reach clipping level). It has a little latency by the process, but probably it is not noticable. 
2. Connect all audio signals to your mixer. Technically there is no latency but it will use more audio inputs.

### Method A. Use DAW as MIDI router. 

It assumes you connect the synths to the DAW or MIDI controller that will echo the synth’s MIDI OUT to MIDI IN. Normally it can be done by just turning on recording mode in the DAW.
Some hardware MIDI keyboard might have the feature.

Make sure MIDI forwarding is OFF before you connect it to DAW, otherwise it will make infinite MIDI message loop.

Set up MIDI to receive exact same data for all devices. It can be done by any method of MIDI connection, but using UART or classic MIDI will be easier than BLE MIDI.
If you use UART or classic MIDI, also you can use the 4 pin located above the OLED screen to connect between devices.

Pin connection is showing as below:

```
1   2   3   4
V   T   R   G
C   X   X   N
C           D
```
Determine master and slave devices. 

Connect Master’s RX to slave’s RX, Master’s GND to slave’s GND (pin 3 to 3, pin 4 to 4). This will connect MIDI-IN signal between devices. VCC and TX are not used. The benefit of this method is that it's no latency, the connection is true MIDI through. However it will be easier to be out-of-sync state because MIDI data forwarding is relying on DAW.

Alternatively, you can use 2 MIDI OUT port from DAW to Master and Slave. 

Sometimes ground-loop noise can be happened. This case use separated power supply.

Connect DAW's MIDI OUT to Master's MIDI IN.

Configure DAW to echo all received MIDI signal. 

### Method B. Use the synth as MIDI router

It's simpler setup than Method A. 

Use TRS cable to connect Master's MIDI out to Slave's MIDI IN. This will not make any ground connection, it will prevent ground loop noise.

Turn on MIDI Forwarding in System menu. All received MIDI signal will be forwarded to Slave device. This is more reliable than DAW setup since it's not relying on DAW's MIDI routing, but you will lose MIDI out function to DAW. (Still you may be able to use Slave's MIDI out though)

Also the MIDI forwarding is done by software so it will have a bit of latency to slave device.

Please make sure you turn off MIDI forwarding when you connect MIDI OUT to DAW next time. Normally DAW echoes the received MIDI packet, so MIDI forwarding setting will cause MIDI message flood.

### Preset setup for Polyphonic

Minimum setup to achieve PolyMono (2 Oscillators per voice) will be the following with 2 devices setup:

1. In System menu , "Num of devices" should be 2 for all devices. Set Dev Index=1 for Master device, 2 for Slave device. This will be saved to flash memory.
2. Initialize a preset (4 second press of Rec button) on Master device.
3. Make sure it plays initial SAW wave sound.
4. Go Key / Other sub-menu and set MonoDuoPolyMode to "PolyMono"
5. Press "Rec" button on Master device. It will dump all preset parameters as MIDI CC messages. After the dump, all preset state should be in sync between devices.
6. Play multiple notes. You should hear two voices.

Minimum setup to achieve PolyDuo (1 Oscillator per voice) will be the following with 2 devices setup:

1. In System menu, "Num of devices" should be 2 for all devices. Set Dev Index=1 for Master device, 2 for Slave device.
2. Initialize a preset (4 second press of Rec button) on Master device.
3. Make sure it can play initial SAW wave sound.
4. We are going to configure Duo mode first. Set "MIX" parameter in Mix menu to 64. It should mix OSC1 and OSC2.
5. Set "OSC Env SEL" to use EG2.
6. Go Key / Other sub-menu and set MonoDuoPolyMode to "PolyDuo"
7. Press "Rec" button on Master device. It will dump all preset commands. After the dump, all preset state should be in sync between devices.
8. Play multiple notes. You should hear four voices.

### Polyphonic tips

Two devices can lose sync for a variety of reasons.
To synchronize all parameters one more time, press “Rec” button to dump all parameters. It will be sent to slave devices. If you still see issues like out of tune in slave device, try MIDI RECV toggle switch (Lyrat's "Mode" button, next to Rec button). It will reset pitch bend or other controller values.

If it starts making ground loop noise, use separated power supply and use standalone setup or use separated MIDI cable to avoid MIDI signal noise.


## TIPS/TROUBLESHOOT

* Sound engine stopped when saving preset
  * When system writes to Flash memory, CPU power is taken by this, and it may cause glitches. Avoid any writing to Flash while playing.

* MIDI CC dump is not properly saved to my DAW	
	* Some DAWs suppress MIDI CC messages when DAW believes the value was not changed. Ableton Live is one of this. To work around, press STOP key before the dump. This will reset CC values. 

* Unknown MIDI messages sent with device reset?
  * When booting some noise is sent (It's ESP32's boot message) . Please avoid to receive MIDI signals when you reset the device. Use initializing preset (4 seconds press of REC button), instead of hardware reset.
  
* Trouble with Duo Mode: You need to set up properly to play duo mode properly.
  * Mix in Mix sub menu has to be center (64).
  * Associated Envelope (OSC1 -> EG1 or 3, OSC2 -> EG2 or 4. Normally OSC1 uses EG1, OSC2 uses EG2)
  * Set OSCs setting identical to get the same tone. Associated Envelopes parameters also need to be identical. (e.g. EG1 and EG2 setting should be identical)
  * VCF’s key sync will not work well.
  * I suggest to start with simple patch.
     1. Initialize a patch. Long pressing (4 sec) Rec button.
     2. Set Mix to 64 (in Mix sub-menu). You can hear both OSC's sound.
     3. Set OSC2 Env Sel to EG2 (in OSC Switches). OSC2 will use EG2.
     4. Set Mono/Duo/Poly mode to "Duo". EG2 will be triggered separately with Duo mode.
     5. Now you should be able to play up to 2 voices.

* Suddenly NO SOUND!
	* Reset the preset. Long pressing (4 seconds) of REC button will initialize the preset.
	* Probably it’s because of last parameter you changed, or some unexpected MIDI cc signal. See the 2nd line of the display, it indicates the parameter received at last.
	* Level overflow may cause the silent (e.g. Giving massive delay feedback).
	* Maybe it’s not worth to spend time to figure out why, reset the preset.
	* Save your preset on DAW by pressing REC button to dump MIDI data. It’s a series of CC changes. Then you don’t lose the preset.
	* Check "Device Index" and "Number of Devices" in system menu. If the Device Index is 2 or more, and use it as primary device, then it may cause no sound.
	* Check MIDI Receiving status. To toggle it, press "Mode" button on Lyrat board (next to Rec button).
	
* BLE trouble with Windows: We don’t support WINDOWS for BLE MIDI connection. Please use MIDI UART or MIDI TRS A.

* A noise when you connect multiple devices (chained audio)
	
	* It is likely because of a ground loop. Use separated power supply. 
	
* I hear noise but I don’t connect anything to line in.
	* Toggle MIC/LINE select (in Prm:System), set to Line in.
	* Toggle Line THRU to off.
	* Initialize a preset.
	* Check Mono/Poly Mode setting. If it’s poly mode, LINE IN pass through is ON.
	
* Use different power supply. Basically it’s less noise by using separated charger.
	
* Use other hardware effectors!

* Don’t be afraid to get clipped! The synth has 3 great clipping algorithms. Clipping could be the gateway to a whole new sound.

* I want to use AUX as CV IN
	* CV signal from modular synthesizers may have **HIGH VOLTAGE**! Please attenuate the voltage to normal LINE level (1 to 1.5V).
	
	* AUX is connected to a lot of modules for CV control, so you can use AUX to control tune/width/LFO and others. However, the LINE in has capacitor in the path, it means the signal is AC. Using it as LFO should work, probably down to 2 to 5Hz. But DC signal (e.g. hold the same voltage 5 seconds) might not work.

* MIDI is flooding when I connect MIDI out to DAW.
	* MIDI forwarding is probably ON.

* No volume?
	* VCF Volume works for most of purpose. However in order to utilize clipping(overdrive) logic, attenuate volume at other equipments is recommended.
	* Other than this, also MIDI CC #7 will control hardware volume. It will lose dynamic range. It's not available to control through UI.

## External Audio processing

You can use LINE IN signal for various purposes.

* As CV input to control synth parameters
* As an audio signal. VCF / effector / Clipping will be applied.

If you want to use LINE IN signal as an audio signal, then set Oscillator(1/2)'s signal as "AUXR" or "AUXL", and set the ENV SEL as "ON" (in OSC Switches). Set the gain by changing "VCF Volume" and "AUX In Gain" in Mix submenu. 

The synth's clipping algorithm can be used as guitar distortion and it is really good. However the interfaces are not designed for guitar, so you need the following things:

* LINE IN's input impedance is low, it cannot take guitar signal directly. Insert buffer amp to get proper sound.
* LINE IN is stereo input. Use a proper cable to convert the signal path.

## Clock synchronization

The synth can take external clock sources from other synthesizers. When the sync is enabled, sequencer BPM / start / stop is synchronized with external synthesizer or sequencer.
The synth cannot be a master. Setting is available in the System menu. Default is OFF.

### MIDI clock
Set your DAW to send MIDI clock. We tested Ableton Live and Logic Pro X. It has some latency so please adjust latency setting in your DAW to match the timing.

### Sync IN
The synth can take 2PPQ, 4PPQ or 24 PPQ signals. Don't supply high voltage to the synth, it will break. The signal must be supplied to LEFT channel (TIP of TRS connector). Using TIP as a sync signal is compatible with Teenage engineering's Pocket Operator. Supply voltage needs to be more than 500mV. RIGHT channel(AUXR) still can be used as audio signal or CV in.





## Supported MIDI Control numbers

The synth is initially designed as a sound module without any user interface. All tone-related parameters can be controlled by MIDI CC signal.

A set of MIDI CC signal can be used as preset save data. Press "Rec" button to dump MIDI CC messages.
If the synth goes wrong state, you can use Lyrat's "Mode" button (next to Rec button) to toggle MIDI receiving status. This can be used as "Panic" button.

Here is some special command available through MIDI
* Program change will load a preset. Through UI, 32 presets can be accessed through UI, but actually it has 128 slots.
* For saving preset, use CC #0. It will save current preset to Flash (0-127). 

If you want to control parameters by your MIDI keyboard, use below chart to check the CC number.
Or, you can see the CC# in the screen at the top of the parameter name.
Assigned CC# cannot be configured.

When you change CC parameters through MIDI keyboard or any other devices, the changed parameter will be shown on the display in real time. So you will see what you are changing, the value and mode names just like when you change parameters on the device.

Suggested MIDI CC parameters to be assigned if your MIDI keyboard has some knobs:

- MIX (92) 
- FM(33)
- Cutoff(22)
- Resonance(91)
- LFO Tune(24)
- VCF Volume(72)




```
0                            "Save Preset", //0x0
1                            "Mod Wheel", //1
2                            "", //2
3                            "", //3
4                            "", //4
5                            "", //5
6                            "", //6
7                            "Volume", //7
8                            "", //8
9                            "", //9
10                            "", //a
11                            "VCF Volume", //b
12                            "", //c
13                            "", //d
14                            "", //e
15                            "", //f
                            //-----------------------0x10
16                            "", //0x0
17                            "", //1
18                            "", //2
19                            "", //3
20                            "", //4
21                            "OSC1 Tune(MSB)", //5
22                            "VCF Cutoff(MSB)", //6
23                            "OSC2 Tune(MSB)", //7
24                            "LFO Tune(MSB)", //8
25                            "ENV3/4 Attack", //9
26                            "ENV3/4 Decay", //a
27                            "ENV3/4 Sustain", //b
28                            "ENV3/4 Release", //c
29                            "", //d
30                            "", //e
31                            "", //f
                            //-----------------------0x20
32                            "", //0x0
33                            "FM", //1
34                            "Efct Feedback", //2
35                            "LFO Pulse Width", //3
36                            "OSC1 Pulse Width", //4
37                            "OSC2 Pulse Width", //5
38                            "LFO Pulse Width", //6
39                            "Efct Speed", //7
40                            "VCF Type", //8
41                            "Efct Depth", //9
42                            "Clipping", //a
43                            "", //b
44                            "Efct Type", //c
45                            "VCF OSC1 bypass SW", //d
46                            "", //e
47                            "", //f
                            //-----------------------0x30
48                            "LFO Mod Tune", //0x0
49                            "LFO Mod Width", //1
50                            "FM Algorithm", //2
51                            "FM ENV3 Conn", //3
52                            "", //4
53                            "OSC1 Tune(LSB)", //5
54                            "Cutoff(LSB)", //6
55                            "OSC2 Tune(LSB)", //7
56                            "LFO Tune(LSB)", //8
57                            "FM A Amp", // 9
58                            "FM A Freq", //a
59                            "FM B Amp", //b
60                            "FM B Freq", //c
61                            "FM C Amp", //d
62                            "FM C Freq", //e
63                            "FM D Amp", //f
                            //-----------------------0x40
64                            "", //0x0
65                            "OSC2 Mod Sel", //1
66                            "LFO Mod Sel", //2
67                            "ENV1 Inv SW", //3
68                            "OSC1 Env Sel", //4
69                            "OSC2 Env Sel", //5
70                            "ENV2 Inv SW", //6
71                            "LFO Gatesync SW", //7
72                            "OSC2 Keysync SW", //8
73                            "OSC1 Wide Tune SW", //9
74                            "OSC2 Wide Tune SW", //a
75                            "OSC2 Octave", //b
76                            "OSC1 Octave", //c
77                            "OSC1 Keysync SW", //d
78                            "Velocity SW", //e
79                            "OSC1 LFO Tune", //f
                            //-----------------------0x50
80                            "OSC1 LFO Width", //0x0
81                            "OSC2 LFO Tune", //1
82                            "OSC2 LFO Width", //2
83                            "Mono/Duo/Polly", //3
84                            "VCF LFO Volume", //4
85                            "VCF Mod Cutoff", //5
86                            "LFO Keysplit", //6
87                            "VCF LFO cutoff", //7
88                            "OSC1 Mod Sel", //8
89                            "VCF Mod Sel", //9
90                            "VCF Volume", //a
91                            "VCF Resonance", //b
92                            "OSC Mix", //c
93                            "OSC1 Shape", //d
94                            "OSC2 Shape", //e
95                            "LFO Shape", //f
                            //-----------------------0x60
96                            "ENV1 Attack", //0x0
97                            "AUX In Gain", //1
98                            "", //2 (Won't be dumped)
99                            "", //3 (Won't be dumped)
100                            "Mod Wheel Mode", //4
101                            "", //5 (Won't be dumped)
102                            "ENV1 Decay", //6
103                            "ENV1 Sustain", //7
104                            "ENV1 Release", //8
105                            "ENV2 Attack", //9
106                            "ENV2 Decay", //a
107                            "ENV2 Sustain", //b
108                            "ENV2 Release", //c
109                            "OSC1 Mod Tune", //d
110                            "OSC1 Mod Width", //e
111                            "VCF Pole / NoLinear", //f
                            //-----------------------0x70
112                            "OSC2 Mod Tune", //0x0
113                            "OSC2 Mod Width", //1
114                            "Glide", //2
115                            "Bend Range", //3
116                            "Mod Mode", //4
117                            "VCF Keysync", //5
118                            "Voice Number", //6
119                            "LFO Sync SW", //7
120                            "All Notes Off", //8
121                            "", //9
122                            "", //a
123                            "All Notes Off", //b
124                            "", //c
125                            "", //d
126                            "", //e
127                            "" //f
```
