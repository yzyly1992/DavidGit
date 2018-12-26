### Arduino Project: LED Strip -- LED灯带编程

The type LED Strip we are using is **5050 RGB LED with built-in WS2812B driver IC**. Each pixel can have its own color and brightness. You can control them individually and set them to any color or animation you want. 256 brightness display and 24-bit color display.

To run this LED Strip, we need specific library. Following examples are all using [[FastLED Library]](https://github.com/FastLED/FastLED/releases). [Download](https://github.com/FastLED/FastLED/releases) the latest library before running these codes. 

#### Basic Blink

    #include "FastLED.h"

    // How many leds in your strip?
    #define NUM_LEDS 77

    // What pin are you using for data (in series with 470 ohm resistor)?
    #define DATA_PIN 7

    // Define the array of leds
    CRGB leds[NUM_LEDS];

    void setup() {
      FastLED.addLeds<NEOPIXEL, DATA_PIN>(leds, NUM_LEDS);
    }

    void loop() {
      FastLED.clear(); // set them all to black
        
      //light up each led on the strip in order
      for (int i = 0; i < NUM_LEDS; i++) {
        leds[i] = CRGB(0, 100, 60);   // you can change the light color here
     
        FastLED.show();  
        delay(30);    // controls the speed
      }
    }


#### Traveling Pixel

    #include "FastLED.h"

    // How many leds in your strip?
    #define NUM_LEDS 77

    // What pin are you using for data (in series with 470 ohm resistor)?
    #define DATA_PIN 7

    // Define the array of leds
    CRGB leds[NUM_LEDS];

    void setup() {
      FastLED.addLeds<NEOPIXEL, DATA_PIN>(leds, NUM_LEDS);
    }

    void loop() {
      static byte offset;
      offset = offset + 1; // will rollover (go back to zero) at 255

      for (int i = 0; i < NUM_LEDS; i++) {
        if (i == offset) {
          leds[i] = CRGB(100, 0, 100);   // try this: leds[i] = CRGB(0, 100, 100);
        }
        else {
          leds[i] = CRGB(0, 0, 0);
        }

      }
      FastLED.show();
      delay(10);    // controls the speed
    }



#### Color Change Theatre Marquis

    #include "FastLED.h"

    // How many leds in your strip?
    #define NUM_LEDS 77

    // What pin are you using for data (in series with 470 ohm resistor)?
    #define DATA_PIN 7

    // Define the array of leds
    CRGB leds[NUM_LEDS];

    void setup() {
      FastLED.addLeds<NEOPIXEL, DATA_PIN>(leds, NUM_LEDS);
    }


    void loop() {
      static float offset1;
      static float offset2;

      offset1 = offset1 + 0.25; // note that these are floats
      offset2 = offset2 - 0.6;  // we will need to cast them back to
      //                        // ints before using modulo operator

      FastLED.clear();  // set them all to black before beginning
      
      for (int i = 0; i < NUM_LEDS; i++) {

        if ((i + (int)offset1) % 7 == 0 ) {   // note cast back to int
          leds[i] = CHSV(i*2.5, 200, 100);
        }

        if ((i + (int)offset2) % 12 == 0 ) {  // note cast back to int
          leds[i] += CHSV(70, 200, 100);
        }
      }
      FastLED.show();
      delay(20);    // controls the speed
    }


#### Ramdom Pixels

    /* Random Pixel Effect
       Choose pixels at random till the strip is full
       and repeat the process. Colors are chosen from
       an HSV "random walk" - see below
    */

    #include "FastLED.h"

    // How many leds in your strip?
    #define NUM_LEDS 77

    // What pin are you using for data (in series with 470 ohm resistor)?
    #define DATA_PIN 7

    // Define the array of leds
    CRGB leds[NUM_LEDS];
    int chosenSlots[NUM_LEDS + 1];

    CHSV colorP[] = {CHSV(0, 150, 100), CHSV(20, 255, 15), CHSV(50, 150, 50), CHSV(120, 150, 50), CHSV(140, 200, 50),
                     CHSV(200, 150, 50), CHSV(220, 150, 50)
                    }; // add as many as you like - nothing should break!
                    // color palette not used in this demo!

    int maxIndex = sizeof(colorP) / sizeof(colorP[0]);


    void setup() {
      Serial.begin(9600);
      FastLED.addLeds<NEOPIXEL, DATA_PIN>(leds, NUM_LEDS);
      fillUpArray();
    }

    /* Random pixels filled with colors chosen from an array.
        A random pixel is chosen and written each time thorugh the loop
    */

    void fillUpArray() {
      for (int i = 0; i < NUM_LEDS; i++) {
        chosenSlots[i] = i; // fill up the array with index numbers
      }
    }

    void loop() {
      static int pixIndex = NUM_LEDS;   // index for pixels
      static int arrayIndex = 0; // index for the color array
      static CHSV color = CHSV(0, 200, 150);

      int randNo = random(pixIndex); // choose a random pixel from array
      int randPix = chosenSlots[randNo]; // get its value

      leds[randPix] = color;  // write the pixel with color
      // compare with the random walk above with the random pixel line below
      // leds[randPix] =   CHSV(random(255),random(255), random(255));

      /* update the color with HSV random walk
         Note that random(17) will return values
         from 0 to 16, if we subtract 8 we get values
         for -8 to 8, so the odds on whether the number
         will get larger or smaller are even. Here is a simplified
         representation of the same thing:
         (global or static) int x;
         x = x + (random(7) - 3);   // random walk
         x = x + (random(oddNumber) - (oddNumber / 2)); // generalized random walk
      */
      color.hue = color.hue + (random(17) - 8); // random walk
      color.sat = color.sat + (random(7) - 3); // random walk
      color.value = color.value + (random(11) - 5); // random walk
      FastLED.show();
      delay(1);

      --pixIndex;  // decrement index each time
      // move the contents of the last slot into the just chosen slot
      chosenSlots[randNo] = chosenSlots[pixIndex];

      if (pixIndex == 0) {    // if all done - start over
        fillUpArray();        // reset array
        pixIndex = NUM_LEDS;  // reset pixIndex
        FastLED.show();
      }
    }



#### Noise Plus Palette

    // One of the demos in the FastLED library, Adjust some settings
    #include<FastLED.h>

    #define LED_PIN     7
    #define BRIGHTNESS  48
    #define LED_TYPE    WS2812
    #define COLOR_ORDER GRB

    const uint8_t kMatrixWidth  = 16;
    const uint8_t kMatrixHeight = 16;
    const bool    kMatrixSerpentineLayout = false;

    #define NUM_LEDS (kMatrixWidth * kMatrixHeight)
    #define MAX_DIMENSION ((kMatrixWidth>kMatrixHeight) ? kMatrixWidth : kMatrixHeight)

    // The leds
    CRGB leds[kMatrixWidth * kMatrixHeight];

    // The 16 bit version of our coordinates
    static uint16_t x;
    static uint16_t y;
    static uint16_t z;

    // We're using the x/y dimensions to map to the x/y pixels on the matrix.  We'll
    // use the z-axis for "time".  speed determines how fast time moves forward.  Try
    // 1 for a very slow moving effect, or 60 for something that ends up looking like
    // water.
    uint16_t speed = 1; // speed is set dynamically once we've started up

    // Scale determines how far apart the pixels in our noise matrix are.  Try
    // changing these values around to see how it affects the motion of the display.  The
    // higher the value of scale, the more "zoomed out" the noise iwll be.  A value
    // of 1 will be so zoomed in, you'll mostly see solid colors.
    uint16_t scale = 16; // scale is set dynamically once we've started up

    // This is the array that we keep our computed noise values in
    uint8_t noise[MAX_DIMENSION][MAX_DIMENSION];

    CRGBPalette16 currentPalette( PartyColors_p );
    uint8_t       colorLoop = 1;

    void setup() {
      delay(3000);
      LEDS.addLeds<LED_TYPE, LED_PIN, COLOR_ORDER>(leds, NUM_LEDS);
      LEDS.setBrightness(BRIGHTNESS);

      // Initialize our coordinates to some random values
      x = random16();
      y = random16();
      z = random16();
      SetupRandomPalette();
    }



    // Fill the x/y array of 8-bit noise values using the inoise8 function.
    void fillnoise8() {
      // If we're runing at a low "speed", some 8-bit artifacts become visible
      // from frame-to-frame.  In order to reduce this, we can do some fast data-smoothing.
      // The amount of data smoothing we're doing depends on "speed".
      uint8_t dataSmoothing = 1;
      if ( speed < 50) {
        dataSmoothing = 200 - (speed * 4);
      }

      for (int i = 0; i < MAX_DIMENSION; i++) {
        int ioffset = scale * i;
        for (int j = 0; j < MAX_DIMENSION; j++) {
          int joffset = scale * j;

          uint8_t data = inoise8(x + ioffset, y + joffset, z);

          // The range of the inoise8 function is roughly 16-238.
          // These two operations expand those values out to roughly 0..255
          // You can comment them out if you want the raw noise data.
          data = qsub8(data, 16);
          data = qadd8(data, scale8(data, 39));

          if ( dataSmoothing ) {
            uint8_t olddata = noise[i][j];
            uint8_t newdata = scale8( olddata, dataSmoothing) + scale8( data, 256 - dataSmoothing);
            data = newdata;
          }

          noise[i][j] = data;
        }
      }

      z += speed;

      // apply slow drift to X and Y, just for visual variation.
      x += speed / 16;
      y -= speed / 32;
    }

    void mapNoiseToLEDsUsingPalette()
    {
      static uint8_t ihue = 0;

      for (int i = 0; i < kMatrixWidth; i++) {
        for (int j = 0; j < kMatrixHeight; j++) {
          // We use the value at the (i,j) coordinate in the noise
          // array for our brightness, and the flipped value from (j,i)
          // for our pixel's index into the color palette.

          uint8_t index = noise[j][i];
          uint8_t bri =   noise[i][j];

          // if this palette is a 'loop', add a slowly-changing base value
          if ( colorLoop) {
            index += ihue;
          }

          // brighten up, as the color palette itself often contains the
          // light/dark dynamic range desired
          if ( bri > 127 ) {
            bri = 255;
          } else {
            bri = dim8_raw( bri * 2);
          }

          CRGB color = ColorFromPalette( currentPalette, index, bri);
          leds[XY(i, j)] = color;
        }
      }

      ihue += 1;
    }

    void loop() {
      // Periodically choose a new palette, speed, and scale
      ChangePaletteAndSettingsPeriodically();

      // generate noise data
      fillnoise8();

      // convert the noise data to colors in the LED array
      // using the current palette
      mapNoiseToLEDsUsingPalette();

      LEDS.show();
      // delay(10);
    }



    // There are several different palettes of colors demonstrated here.
    //
    // FastLED provides several 'preset' palettes: RainbowColors_p, RainbowStripeColors_p,
    // OceanColors_p, CloudColors_p, LavaColors_p, ForestColors_p, and PartyColors_p.
    //
    // Additionally, you can manually define your own color palettes, or you can write
    // code that creates color palettes on the fly.

    // 1 = 5 sec per palette
    // 2 = 10 sec per palette
    // etc
    #define HOLD_PALETTES_X_TIMES_AS_LONG 1

    void ChangePaletteAndSettingsPeriodically()
    {
      return;

      uint8_t secondHand = ((millis() / 1000) / HOLD_PALETTES_X_TIMES_AS_LONG) % 60;
      static uint8_t lastSecond = 99;

      if ( lastSecond != secondHand) {
        lastSecond = secondHand;
        if ( secondHand ==  0)  {
          currentPalette = RainbowColors_p;
          speed = 20;
          scale = 30;
          colorLoop = 1;
        }
        if ( secondHand ==  5)  {
          SetupPurpleAndGreenPalette();
          speed = 10;
          scale = 50;
          colorLoop = 1;
        }
        if ( secondHand == 10)  {
          SetupBlackAndWhiteStripedPalette();
          speed = 20;
          scale = 30;
          colorLoop = 1;
        }
        if ( secondHand == 15)  {
          currentPalette = ForestColors_p;
          speed =  8;
          scale = 120;
          colorLoop = 0;
        }
        if ( secondHand == 20)  {
          currentPalette = CloudColors_p;
          speed =  4;
          scale = 30;
          colorLoop = 0;
        }
        if ( secondHand == 25)  {
          currentPalette = LavaColors_p;
          speed =  8;
          scale = 50;
          colorLoop = 0;
        }
        if ( secondHand == 30)  {
          currentPalette = OceanColors_p;
          speed = 20;
          scale = 90;
          colorLoop = 0;
        }
        if ( secondHand == 35)  {
          currentPalette = PartyColors_p;
          speed = 20;
          scale = 30;
          colorLoop = 1;
        }
        if ( secondHand == 40)  {
          SetupRandomPalette();
          speed = 20;
          scale = 20;
          colorLoop = 1;
        }
        if ( secondHand == 45)  {
          SetupRandomPalette();
          speed = 50;
          scale = 50;
          colorLoop = 1;
        }
        if ( secondHand == 50)  {
          SetupRandomPalette();
          speed = 90;
          scale = 90;
          colorLoop = 1;
        }
        if ( secondHand == 55)  {
          currentPalette = RainbowStripeColors_p;
          speed = 30;
          scale = 20;
          colorLoop = 1;
        }
      }
    }

    // This function generates a random palette that's a gradient
    // between four different colors.  The first is a dim hue, the second is
    // a bright hue, the third is a bright pastel, and the last is
    // another bright hue.  This gives some visual bright/dark variation
    // which is more interesting than just a gradient of different hues.
    void SetupRandomPalette()
    {
      currentPalette = CRGBPalette16(
                         CHSV( random8(), 200, 32),
                         CHSV( random8(), 255, 180),
                         CHSV( random8(), 128, 255),
                         CHSV( random8(), 50, 150),
                         CHSV( random8(), 200, 32),
                         CHSV( random8(), 255, 180),
                         CHSV( random8(), 128, 255),
                         CHSV( random8(), 50, 150),
                         CHSV( random8(), 200, 32),
                         CHSV( random8(), 255, 180),
                         CHSV( random8(), 128, 255),
                         CHSV( random8(), 50, 150),
                         CHSV( random8(), 200, 32),
                         CHSV( random8(), 255, 180),
                         CHSV( random8(), 128, 255),
                         CHSV( random8(), 50, 150));
    }

    // This function sets up a palette of black and white stripes,
    // using code.  Since the palette is effectively an array of
    // sixteen CRGB colors, the various fill_* functions can be used
    // to set them up.
    void SetupBlackAndWhiteStripedPalette()
    {
      // 'black out' all 16 palette entries...
      fill_solid( currentPalette, 16, CRGB::Black);
      // and set every fourth one to white.
      currentPalette[0] = CRGB::White;
      currentPalette[4] = CRGB::White;
      currentPalette[8] = CRGB::White;
      currentPalette[12] = CRGB::White;

    }

    // This function sets up a palette of purple and green stripes.
    void SetupPurpleAndGreenPalette()
    {
      CRGB purple = CHSV( HUE_PURPLE, 255, 255);
      CRGB green  = CHSV( HUE_GREEN, 255, 255);
      CRGB black  = CRGB::Black;

      currentPalette = CRGBPalette16(
                         green,  green,  black,  black,
                         purple, purple, black,  black,
                         green,  green,  black,  black,
                         purple, purple, black,  black );
    }


    //
    // Mark's xy coordinate mapping code.  See the XYMatrix for more information on it.
    //
    uint16_t XY( uint8_t x, uint8_t y)
    {
      uint16_t i;
      if ( kMatrixSerpentineLayout == false) {
        i = (y * kMatrixWidth) + x;
      }
      if ( kMatrixSerpentineLayout == true) {
        if ( y & 0x01) {
          // Odd rows run backwards
          uint8_t reverseX = (kMatrixWidth - 1) - x;
          i = (y * kMatrixWidth) + reverseX;
        } else {
          // Even rows run forwards
          i = (y * kMatrixWidth) + x;
        }
      }
      return i;
    }


