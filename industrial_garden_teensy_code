/*
Industrial Garden code for Teensy 4.1
Requires the use of the Teensy Audio Shield
Libraries are used in accordance to their licenses, which are included in the libraries.
Written by Colin Thomas Nichols

Be sure to save a backup before modifying any code or making changes - but feel free to change or replace anything you'd like.

*/


#include <Audio.h>
#include <Wire.h>
#include <SPI.h>
#include <SD.h>
#include <SerialFlash.h>
#include <BlockNot.h>
#include <ArduinoTapTempo.h>

#include <Audio.h>
#include <Wire.h>
#include <SPI.h>
#include <SD.h>
#include <SerialFlash.h>

// GUItool: begin automatically generated code
AudioInputI2S            i2s1;           //xy=85,167
AudioMixer4              mixer1;         //xy=217,179
AudioAnalyzePeak         peak1;          //xy=334,92
AudioAmplifier           amp1;           //xy=336,182
AudioFilterBiquad        biquad1;        //xy=492,186
AudioMixer4              mixer3;         //xy=649,247
AudioEffectDelay         delay1;         //xy=662,399
AudioEffectFreeverb      freeverb1;      //xy=858,313
AudioMixer4              mixer2;         //xy=973,195
AudioFilterStateVariable filter1;        //xy=1109,195
AudioMixer4              mixer4;         //xy=1237,186
AudioOutputI2S           i2s2;           //xy=1376,184
AudioConnection          patchCord1(i2s1, 0, mixer1, 0);
AudioConnection          patchCord2(i2s1, 1, mixer1, 1);
AudioConnection          patchCord3(mixer1, amp1);
AudioConnection          patchCord4(mixer1, peak1);
AudioConnection          patchCord5(amp1, biquad1);
AudioConnection          patchCord6(biquad1, 0, mixer3, 0);
AudioConnection          patchCord7(mixer3, delay1);
AudioConnection          patchCord8(mixer3, freeverb1);
AudioConnection          patchCord9(mixer3, 0, mixer2, 0);
AudioConnection          patchCord10(delay1, 0, mixer3, 1);
AudioConnection          patchCord11(freeverb1, 0, mixer2, 1);
AudioConnection          patchCord12(mixer2, 0, filter1, 0);
AudioConnection          patchCord13(filter1, 0, mixer4, 0);
AudioConnection          patchCord14(mixer4, 0, i2s2, 0);
AudioConnection          patchCord15(mixer4, 0, i2s2, 1);
// GUItool: end automatically generated code

AudioControlSGTL5000 audioShield;
const int myInput = AUDIO_INPUT_LINEIN;

BlockNot rateTimer(100);
boolean timerState = false;
BlockNot serialTimer(100);
BlockNot tapTimer(100);
boolean tapState = false;

float fbLevel = 0.0;
int delayTime = 0.0;
float verbLevel = 0.0;
float verbMix = 0.0;
float signalVal;
int toneLevel;
float volLevel;
int MuteState = 1;
int tapTime = 0.0;
int potTime;
int lastPot;
float verbLEDval;
float filterLEDval;

ArduinoTapTempo tapTempo;

#define ratePin A17
#define fbPin A16
#define verbPin A15
#define volPin A14
#define tonePin A10
#define tapButton 35
#define muteButton 31
#define rateLED 30
#define signalLED 33
#define muteLED 32
#define gateIN 34
#define CV1 A12
#define CV2 A11
#define verbLED 29
#define filterLED 28

// Rounding for smoothing purposes
int round100 (int a)
{
  return (a / 100 * 100);
}
int round25 (int a)
{
  return (a / 25 * 25);
}

void setup() {
  // put your setup code here, to run once:
  AudioMemory(896);
  audioShield.enable();
  audioShield.volume(0.7);
  delay(100);
  audioShield.inputSelect(myInput);
  audioShield.unmuteLineout();

  //audioShield.adcHighPassFilterDisable();
  audioShield.lineInLevel(4); //default input

  analogWriteFrequency(28, 50000); //set the PWM freq high to remove noise

  //Initializing objects
  mixer1.gain(0, 0.5); //Input mixer, stereo to mono
  mixer1.gain(1, 0.5);

  mixer4.gain(0, 1.0); //output mixer / volume control

//Setting up volume
  amp1.gain(20.0); //boost volume

//Setting up Delay effect
  mixer3.gain(0, 0.5);
  mixer3.gain(1, 0.0); //no feedback on startup

//Filtering for noise and bad frequencies
  biquad1.setNotch(0, 60.0, 0.3);
  biquad1.setNotch(1, 7000, 0.4);
  biquad1.setHighpass(2, 80, 0.5);
  biquad1.setNotch(3, 1600, 0.5);

  audioShield.enhanceBassEnable();

//Setting up constants for Tone control
  filter1.resonance(0.5);
  filter1.octaveControl(3.0);

//Setting up constant for Reverb effect
  freeverb1.damping(0.5);
  mixer2.gain(0, 1.0); //Reverb dry
  mixer2.gain(1, 0.0); //Reverb wet

//Initializing pins
  pinMode(ratePin, INPUT); //Rate Pot
  pinMode(fbPin, INPUT); //Feedback Pot
  pinMode(verbPin, INPUT); //Reverb Pot
  pinMode(volPin, INPUT); //Volume Pot
  pinMode(tapButton, INPUT_PULLUP); //Button!
  digitalWrite(tapButton, HIGH);
  pinMode(muteButton, INPUT_PULLUP); //Another Button!
  digitalWrite(muteButton, HIGH);
  pinMode(rateLED, OUTPUT);
  digitalWrite(rateLED, LOW);
  pinMode(signalLED, OUTPUT);
  digitalWrite(signalLED, LOW);
  pinMode(muteLED, OUTPUT);
  digitalWrite(muteLED, LOW);
  pinMode(gateIN, INPUT); //Input for Gate/Trigger jack
  pinMode(CV1, INPUT); //Input for CV1 jack
  pinMode(CV2, INPUT); //Input for CV2 jack
  pinMode(verbLED, OUTPUT);
  pinMode(filterLED, OUTPUT);

  delayTime = potTime;

  tapTempo.setMaxBeatLengthMS(2500);

  Serial.begin(9600);

  //light show initialization
  digitalWrite(rateLED, HIGH);
  delay(100);
  digitalWrite(rateLED, LOW);
  delay(100);
  digitalWrite(muteLED, HIGH);
  delay(100);
  analogWrite(signalLED, 255);
  delay(100);
  analogWrite(verbLED, 255);
  delay(100);
  analogWrite(filterLED, 255);
  delay(100);
  digitalWrite(rateLED, LOW);
  digitalWrite(muteLED, LOW);
  analogWrite(signalLED, 0);
  analogWrite(verbLED, 0);
  analogWrite(filterLED, 0);
  delay(500);
  digitalWrite(rateLED, HIGH);
  digitalWrite(muteLED, HIGH);
  analogWrite(signalLED, 255);
  analogWrite(verbLED, 255);
  analogWrite(filterLED, 255);
  delay(500);
  digitalWrite(rateLED, LOW);
  digitalWrite(muteLED, LOW);
  analogWrite(signalLED, 0);
  analogWrite(verbLED, 0);
  analogWrite(filterLED, 0);
  delay(800);
}

void loop() {

//Signal LED code, PWM pulses based on peak amplitude
  if (peak1.available()) {
  signalVal = peak1.read() * 100.0;
  signalVal = map(signalVal, 0, 100, 0, 255);
  analogWrite(signalLED, signalVal);
}

//Reading and mapping Rate pot
  potTime = analogRead(ratePin);
  potTime = round25(potTime);
  potTime = map(potTime, 0, 1023, 2500, 0);

//Reading and mapping Feedback pot
  fbLevel = analogRead(fbPin);
  fbLevel = round100(fbLevel);
  fbLevel = (map(fbLevel, 0, 1023, 10, 0) / 10.0);

//Reading and mapping Reverb Pot. As Reverb length increases, dry/wet mix increases to wet side
  verbLevel = analogRead(verbPin);
  verbLevel = round100(verbLevel);
  verbLevel = map(verbLevel, 1020, 0, 0, 1.0);
  verbMix = analogRead(verbPin);
  verbMix = round100(verbMix);
  verbMix = map(verbMix, 0, 1020, 1.0, 0.0);

  //Reverb LED mapping
  verbLEDval = map(verbLevel, 0, 1.0, 0, 255);
  if (verbLEDval <= 25) {
    analogWrite(verbLED, 0);
  }
  else {
  analogWrite(verbLED, verbLEDval);
  }

//Reading and mapping Volume pot
  volLevel = analogRead(volPin);
  volLevel = round100(volLevel);
  volLevel = (map(volLevel, 0, 1023, 10, 0) / 10);

//Reading and mapping Filter pot
  toneLevel = analogRead(tonePin);
  toneLevel = round100(toneLevel);
  toneLevel = map(toneLevel, 0, 1020, 10000, 250);

//Filter LED mapping
  filterLEDval = map(toneLevel, 250, 10000, 0, 255);
  if (filterLEDval <= 25) {
    analogWrite(filterLED, 0);
  }
  else {
  analogWrite(filterLED, filterLEDval);
  }

//Tap Tempo timer and function
boolean buttonDown = digitalRead(tapButton) == LOW;
tapTempo.update(buttonDown);
tapTime = tapTempo.getBeatLength();
delayTime = tapTime;

//sets a variable for the pot value when tapTempo is active
if (tapTempo.isChainActive() == true) {
  lastPot = potTime;
}

//if the pot gets moved, sets delay time to the pot
if (potTime != lastPot) {
  tapTime = 0.0;
  delayTime = potTime;
}

// Rate LED. If the time interval has passed and its more than 25mS, trigger the LED and set the interval to the delay time
if (rateTimer.TRIGGERED && delayTime >= 25) {
  timerState = !timerState;
  digitalWrite(rateLED, (timerState ? HIGH : LOW));
  //Serial.println("rateTimer triggered");
  rateTimer.setDuration(delayTime, MILLISECONDS);
}

// if the delay time is less than 100 mS, turn off LED
if (delayTime <= 100) {
  digitalWrite(rateLED, LOW);
}

 /*
Uncomment this block to read outputs on the Serial Monitor. Leave commented out if not debugging.

if (serialTimer.TRIGGERED) {
  Serial.print("Audio Memory Blocks Currently Used: ");
  Serial.println(AudioMemoryUsage());
  Serial.print("Rate: ");
  Serial.println(delayTime);
  Serial.print("Last Pot: ");
  Serial.println(lastPot);
  Serial.print("Feedback: ");
  Serial.println(fbLevel);
  Serial.print("Reverb: ");
  Serial.println(verbLevel);
  Serial.print("Verb Dry: ");
  Serial.println(1.0 - verbMix);
  Serial.print("Verb Wet: ");
  Serial.println(verbMix);
  Serial.print("Volume: ");
  Serial.println(volLevel);
  Serial.print("Tone: ");
  Serial.println(toneLevel);
  Serial.print("Tap Tempo: ");
  Serial.println(digitalRead(tapButton));
  Serial.print("Tap Timer: ");
  Serial.println(tapTime);
  Serial.print("Mute: ");
  Serial.println(digitalRead(muteButton));
  Serial.print("Signal: ");
  Serial.println(signalVal);
  Serial.println();
  Serial.print("Rate Pot: ");
  Serial.println(analogRead(ratePin));
  Serial.print("FB Pot: ");
  Serial.println(analogRead(fbPin));
  Serial.print("Verb Pot: ");
  Serial.println(analogRead(verbPin));
  Serial.print("Tone Pot: ");
  Serial.println(analogRead(tonePin));
  Serial.print("Gain Pot: ");
  Serial.println(analogRead(boostPin));
  Serial.println();
}
*/

//Mapping values to effect controls
  mixer3.gain(1, fbLevel); //Feedback level
  delay1.delay(0, delayTime); //Delay Rate
  freeverb1.roomsize(verbLevel); //Reverb length
  mixer2.gain(1, verbMix); //Reverb wet signal
  mixer2.gain(0, 1.0 - verbMix); //Reverb dry signal
  filter1.frequency(toneLevel); //Tone control frequency
  mixer4.gain(0, volLevel); //Output Volume level

//Mute LED and function
MuteState = digitalRead(muteButton);
if (MuteState == 0){
  digitalWrite(muteLED, HIGH);
  audioShield.muteLineout();
  mixer1.gain(0, 0.0);
  mixer1.gain(1, 0.0);
}
else{
  digitalWrite(muteLED, LOW);
  audioShield.unmuteLineout();
  mixer1.gain(0, 0.5);
  mixer1.gain(1, 0.5);
}

//Left to do: Map CV and Gate inputs to voltage ranges and digital HIGH/LOW and apply them to controls.
//Similar code to Rate, except that CV and Gate inputs ALWAYS override pots and tap tempo

}
