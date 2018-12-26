### Arduino Projects: LED Light Patterns -- LED图案编程

LED Light Project as the most basic Arduino project has its simplicity, flexibility and extensibility. It is easy and friendly for new learner to get hands on Arduino programming. The following tutorial will introduce several LED Light Projects from basic to advanced.

Board: Arduino Uno / Arduino Educato

LED: Single LED Light Bulbs

Resistor: 220 OHM / 1K OHM

##### Single LED Blink-- 单个LED闪烁

Open your Arduino IDE, go to File -> Examples -> 01.Basics -> Blink, it is a code can blink the led on Arduino board:

直接打开Arduino的IDE应用程序，File -> Examples -> 01.Basics -> Blink 就是一个可以让Uno板上LED闪烁的代码：


    // set up led pin
    int led = 13; 

    void setup() {                
      // initialize the digital pin as an output.
      pinMode(led, OUTPUT);     
    }

    // the loop routine runs over and over again forever:
    void loop() {
      digitalWrite(led, HIGH);   // turn the LED on (HIGH is the voltage level)
      delay(1000);               // wait for a second
      digitalWrite(led, LOW);    // turn the LED off by making the voltage LOW
      delay(1000);               // wait for a second
    }


  You can adjust the example to build you own led blink pattern, for example:


    void loop() {
      digitalWrite(led, HIGH);   // turn the LED on (HIGH is the voltage level)
      delay(20);               // wait for 20/1000 second
      digitalWrite(led, LOW);    // turn the LED off by making the voltage LOW
      delay(200);               // wait for 200/1000 second
    }




##### Single LED Fade -- 单个LED呼吸灯

Fade an LED need using the analogWrite() function instead of digitalWrite(). The analogWrite() function uses PWM to simulate analog signal. The PWM capable pins on Uno are identified with a "~" sign, like ~3, ~5, ~6, ~9, ~10 and ~11. The signal range of a PWM pin is from 0 to 255.


    int led = 9;           // the PWM pin the LED is attached to
    int brightness = 0;    // how bright the LED is
    int fadeAmount = 5;    // how many points to fade the LED by

    // the setup routine runs once when you press reset:
    void setup() {
      // declare pin 9 to be an output:
      pinMode(led, OUTPUT);
    }

    // the loop routine runs over and over again forever:
    void loop() {
      // set the brightness of pin 9:
      analogWrite(led, brightness);

      // change the brightness for next time through the loop:
      brightness = brightness + fadeAmount;

      // reverse the direction of the fading at the ends of the fade:
      if (brightness <= 0 || brightness >= 255) {
        fadeAmount = -fadeAmount;
      }
      // wait for 30 milliseconds to see the dimming effect
      delay(30);
    }


Adjust **fadeAmount** and **delay** to change the led fading speed.



##### Multiple LED Blink -- 多个LED 闪烁

Adding more LEDs to your board, and make them blink in different patterns.

Six LEDs Blink in order -- Pattern 1:


    void setup(){
      unsigned char i;
      for(i = 1; i<=6; i++)
        pinMode(i,OUTPUT);
    }

    void loop(){
      unsigned char j;
      for(j=1; j<=6; j++){
        digitalWrite(j, HIGH);
        delay(200);
       }
       
      for(j=6; j>=1; j--){
        digitalWrite(j,LOW);
        delay(200);
       }
    }


Six LEDs Blick in order -- Pattern 2:


    void setup(){
      unsigned char i;
      for(i = 1; i<=6; i++)
        pinMode(i,OUTPUT);
    }

    void loop(){
      unsigned char j, k;
      k = 5;
      for(j=1; j<=3; j++){
        digitalWrite(j, HIGH);
        digitalWrite(j+k, HIGH);
        delay(400);
        digitalWrite(j, LOW);
        digitalWrite(j + k, LOW);
        k -= 2;
      }
      k = 3;
      for(j=2;j>=1;j--){
        digitalWrite(j,HIGH);
        digitalWrite(j+k,HIGH);
        delay(400);
        digitalWrite(j,LOW);
        digitalWrite(j+k,LOW);
        k+=2;
      }
    }


If we want to combine these two patterns in a single script, we'd better make a function for each pattern so the code is more organized:


    void style_1(void){
      unsigned char j;
      for(j=1; j<=6; j++){
        digitalWrite(j, HIGH);
        delay(200);
       }
       
      for(j=6; j>=1; j--){
        digitalWrite(j,LOW);
        delay(200);
       }
    }

    void style_2(void){
      unsigned char j, k;
      k = 5;
      for(j=1; j<=3; j++){
        digitalWrite(j, HIGH);
        digitalWrite(j+k, HIGH);
        delay(400);
        digitalWrite(j, LOW);
        digitalWrite(j + k, LOW);
        k -= 2;
      }
      k = 3;
      for(j=2;j>=1;j--){
        digitalWrite(j,HIGH);
        digitalWrite(j+k,HIGH);
        delay(400);
        digitalWrite(j,LOW);
        digitalWrite(j+k,LOW);
        k+=2;
      }
    }

    void setup(){
      unsigned char i;
      for(i = 1; i<=6; i++)
        pinMode(i,OUTPUT);
    }

    void loop(){
      style_1();
      style_2();
    }

##### Ten LEDs with Eight Patterns Switched by a Button -- 十LED八图案一按钮控制

In order to control the switch of led patterns, a [Momentary Switch](http://digitalmedia.risd.edu/pbadger/PhysComp/index.php?n=Devices.Switch) was added. See [here](http://digitalmedia.risd.edu/pbadger/PhysComp/index.php?n=Devices.Switch), know how to connect a button to your board. Try following code, see if you can create more interesting pattern by 10 LEDs:

    int buttonPin = 2; // set switch button pin
    int buttonPushCounter = 0; // counter to count the button been pushed
    int buttonState = 0; // current button state
    int lastButtonState = 0; // last button state

    // 10 led pins are from pin 4 to pin 13, 8 funtions to represent 8 patterns

    void style_1(void) {
      unsigned char j;
      for(j=4; j<=13; j++){
        digitalWrite(j, HIGH);
      }
      delay(100);

      for(j=4; j<=13; j++){
        digitalWrite(j, LOW);
      }
      delay(100);
    }

    void style_2(void){
      unsigned char j;
      for(j=4; j<=13; j++){
        digitalWrite(j, HIGH);
        delay(20);
        digitalWrite(j, LOW);
        delay(20);
      }

      for(j=12; j>=4; j--){
        digitalWrite(j, HIGH);
        delay(20);
        digitalWrite(j, LOW);
        delay(20);
      }
      delay(20);
    }

    void style_3(void) {
      unsigned char j;
      for(j=4; j<=13; j+=2){
        digitalWrite(j, HIGH);
        delay(20);
      }
      for(j=13; j>=4; j-=2){
        digitalWrite(j, HIGH);
        delay(20);
      }
      for(j=5; j<=13; j+=2){
        digitalWrite(j, LOW);
        delay(20);
      }
      for(j=12; j>=4; j-=2){
        digitalWrite(j, LOW);
        delay(20);
      }
      delay(20);
    }

    void style_4(void) {
      unsigned char j;
      for(j=4; j<=13;j++) {
        digitalWrite(j, HIGH);
        delay(20);
      }
      for(j=13; j>=4;j--) {
        digitalWrite(j, LOW);
        delay(20);
      }
      delay(20);
    }

    void style_5(void) {
      unsigned char j, k;
      k=1;
      for(j=8; j>=4; j--) {
        digitalWrite(j, HIGH);
        digitalWrite(j+k,HIGH);
        delay(20);
        k += 2;
      }
      k=9;
      for (j=4; j<=8; j++) {
        digitalWrite(j,LOW);
        digitalWrite(j+k,LOW);
        delay(20);
        k -= 2;
      }
      delay(20);
    }

    void style_6(void) {
      unsigned char j,k;
      k=9;
      for(j=4; j<=8; j++) {
        digitalWrite(j,HIGH);
        digitalWrite(j+k, HIGH);
        delay(20);
        digitalWrite(j,LOW);
        digitalWrite(j+k, LOW);
        k-=2;
      }
      k=3;
      for(j=7; j>=4; j--) {
        digitalWrite(j,HIGH);
        digitalWrite(j+k, HIGH);
        delay(20);
        digitalWrite(j,LOW);
        digitalWrite(j+k, LOW);
        k+=2;
      }
      delay(20);
    }

    void style_7(void) {
      unsigned char i;
      unsigned char n;
      for(i=5; i>=1; i--) {
        for(n=i; n>=1; n--) {
          style_1();
        }
        delay(400);
      }
      delay(20);
    }

    void style_8(void) {
      unsigned int i;
      for(i=0; i<255; i+=2) {
        analogWrite(9, i);
        analogWrite(10, i);
        analogWrite(11, i);
        delay(20);
      }

      for(i=255; i>0; i-=2) {
        analogWrite(9, i);
        analogWrite(10, i);
        analogWrite(11, i);
        delay(20);
      }
      delay(20);
    }

    void setup() {
      unsigned char i;
      for(i=4; i<=13; i++) {
        pinMode(i,OUTPUT);
      }  
       pinMode(buttonPin, INPUT);
       pinMode(A3, OUTPUT);
    }

    void loop() {
      buttonState=digitalRead(buttonPin);
      if(buttonState != lastButtonState){
        if(buttonState == HIGH){
          buttonPushCounter++;
          if(buttonPushCounter>8){
            buttonPushCounter = 1;
          }}}else{
          
          if(buttonPushCounter == 1) {
              style_1();
              delay(50);
            }
          
          if(buttonPushCounter == 2) {
              style_2();
              delay(50);
            }

          if(buttonPushCounter == 3) {
              style_3();
              delay(50);
            }

          if(buttonPushCounter == 4) {
              style_4();
              delay(50);
            }

          if(buttonPushCounter == 5) {
              style_5();
              delay(50);
            }

          if(buttonPushCounter == 6) {
              style_6();
              delay(50);
            }

          if(buttonPushCounter == 7) {
              style_7();
              delay(50);
            }
          if(buttonPushCounter == 8) {
              style_8();
              delay(50);
            }
          }
      lastButtonState = buttonState;
    }

The code above could be simplified by a function array to include all the patterns in, and add randomness to break the order when pushing the button.

