### Arduino Project: Sound Generator -- 声音产生器编程

In order to generate sound from Arduino and control it, we need to [[hook up a speaker]](http://digitalmedia.risd.edu/pbadger/PhysComp/?n=Devices.Speaker) and some potentiometers. And these potentiometers could become different controllers to change volume, pitch, frequency, fade, noise, speed and so on. 

There are some useful sound sketches written by Prof. Paul Badger:

[[Simple Tone]](http://digitalmedia.risd.edu/pbadger/PhysComp/index.php?n=Sketches.SimpleTone) [[Simple Siren]](http://digitalmedia.risd.edu/pbadger/PhysComp/index.php?n=Sketches.SimpleSiren) [[White Noise Generator]](http://digitalmedia.risd.edu/pbadger/PhysComp/index.php?n=Sketches.Noise) [[Noise Siren]](http://digitalmedia.risd.edu/pbadger/PhysComp/index.php?n=Sketches.NoiseSiren) [[Double Siren]](http://digitalmedia.risd.edu/pbadger/PhysComp/index.php?n=Sketches.DoubleSiren) [[Noise Fade]](http://digitalmedia.risd.edu/pbadger/PhysComp/index.php?n=Code.NoiseFade) [[Pitch Shift]](http://digitalmedia.risd.edu/pbadger/PhysComp/index.php?n=Sketches.PitchShift) [[Freqout Simple Square]](http://digitalmedia.risd.edu/pbadger/PhysComp/index.php?n=Code.Freqout) [[Sine Wave Generator]](http://digitalmedia.risd.edu/pbadger/PhysComp/index.php?n=Code.ArduinoSineWaveGenerator) [[Melody]](http://digitalmedia.risd.edu/pbadger/PhysComp/index.php?n=Sketches.Melody) 



#### Sound Generator with 4 Controllers -- 声音产生器和四个控制器

The following sketch is adding 4 potentiometer controllers to make a playable sound generator.

	const int speakerPin = 10; // set speaker pin 10
	const int sensPin1 = A0; // set potentiometer controller 1 pin A0
	const int sensPin2 = A1; // set potentiometer controller 2 pin A1
	const int sensPin3 = A2; // set potentiometer controller 3 pin A2
	const int sensPin4 = A3; // set potentiometer controller 4 pin A3
	
	int sensVal1; // potentiometer controller 1 value
	int sensVal2; // potentiometer controller 2 value
	int sensVal3; // potentiometer controller 3 value
	int sensVal4; // potentiometer controller 4 value
	
	int mapVal1; // mapped potentiometer controller 1 value
	int mapVal2; // mapped potentiometer controller 2 value
	int mapVal3; // mapped potentiometer controller 3 value
	int mapVal4; // mapped potentiometer controller 4 value
	
	// super mario melody notes
	int melody[] = {10, 10, 10, 8, 10, 12, 5, 8, 5, 3, 6, 7, 7, 6, 5, 10, 12, 13, 11, 12, 10, 8, 9, 7, 8, 5, 3, 6, 7, 7, 6, 5, 10, 12, 13, 11, 12, 10, 9, 8, 7, 12, 11, 11, 10, 10, 5, 6, 8, 6, 8, 9, 12, 11, 11, 10, 10, 15,15,12,11,11,10,10,5,6,8,6,8,9,10,9,8,8,8,8,8,9,10,8,6,5,8,8,8,8,9,10};
	 
	// find the last note in the array index
	int melodyMaxIndex = sizeof(melody) / sizeof(melody[0]);   


```shell
void setup() {       
    Serial.begin(9600);
}

void loop() {
// read the sensor
sensVal1 = analogRead(sensPin1);   
sensVal2 = analogRead(sensPin2);
sensVal3 = analogRead(sensPin3);
sensVal4 = analogRead(sensPin4);

// map melody to whole pot rotation
mapVal1 = map(sensVal1, 0, 1023, 0, melodyMaxIndex);
mapVal2 = map(sensVal2, 0, 1023, 100, 500);
mapVal3 = map(sensVal3, 0, 1023, 0, 1);
mapVal4 = map(sensVal4, 0, 1023, 0, 100);

// val1 control the order note playing, val2 val3 adjust the tune and pitch, val4 control the speed
float note = pow(2+(float)mapVal3, ((float)( melody[mapVal1] + sensVal2)) / 12); 
// play the sound
tone(speakerPin, note);       
// time each note playing
delay(mapVal4);
}
```






