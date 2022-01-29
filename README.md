#include "Arduino.h"
#include "Adafruit_TLC5947.h"

///////////////////////////////////////////////////////////////////////////////
byte alphabets[256][3] = { 
  
{0b0,0b0},//ligne 0
{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},//ligne10           
{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},//ligne20    
{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},//ligne30    
{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},//ligne40    
{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},//ligne50    
{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},//ligne60   
{0b0,0b0},{0b0,0b0},{0b0,0b0},{0b0,0b0},//ligne64  

{0b00000001,0b00111001},            //Char A lihne65
{0b00000000,0b10111011},            //Char B ligne66   
{0b00000000,0b00010111},            //Char C ligne67   
{0b00010001,0b00100011},            //Char D ligne68   
{0b00000000,0b00110111},            //Char E ligne69   
{0b00000000,0b00110101},            //Char F ligne70   
{0b00010001,0b00110110},            //Char G ligne71   
{0b00010001,0b00110001},            //Char H ligne72   
{0b00010001},                       //Char I ligne73   
{0b00010001,0b10000000},            //Char J ligne75   
{0b00010000,0b10110001},            //Char K ligne75   
{0b00000000,0b00010011},            //Char L ligne76   
{0b00000001,0b00011000,0b00011001}, //Char M ligne77     
{0b00010001,0b00011001},            //Char N ligne78   
{0b00010001,0b00010111},            //Char O ligne79   
{0b00010000,0b00110101},            //Char P ligne80   
{0b00010001,0b10010111},            //Char Q ligne81   
{0b00010000,0b10110101},            //Char R ligne82   
{0b00000001,0b00110110},            //Char S ligne83   
{0b00000000,0b00110011},            //Char T ligne84   
{0b00010001,0b00010011},            //Char U ligne85   
{0b00010001,0b10010000},            //Char V ligne86   
{0b00010001,0b10000001,0b10010000}, //Char W ligne87   
{0b00000000,0b10010001,0b00001000}, //Char X ligne88   
{0b00010001,0b00110010},            //Char Y ligne89   
{0b00010000,0b00100111},            //Char Z ligne90 
};
///////////////////////////////////////////////////////////////////////////////
///////////////////////////////////////////////////////////////////////////////

#define NUM_TLC5974 4
#define _dat   4
#define _clk   5
#define _lat   6
//#define oe  -1  // set to -1 to not use the enable pin (its optional)
int g = 4095;
int r = 4095;
int b = 4095;
int scrollspeed = 800;
//////////////////////////////////////////////////////////////////////////////////////////////
uint16_t *pwmbuffer = (uint16_t *)malloc(2 * 24*NUM_TLC5974);
//////////////////////////////////////////////////////////////////////////////////////////////
Adafruit_TLC5947 tlc = Adafruit_TLC5947(NUM_TLC5974, _clk, _dat, _lat);
//////////////////////////////////////////////////////////////////////////////////////////////
void setup() {

  Serial.begin(115200);
  tlc.begin();
  delay(500);
  
  //clear
  for (int i = 0; i < 24*NUM_TLC5974 ; i++)  
  {
  tlc.setPWM(i, 0);
  }
  tlc.write();
  delay(500);

}
//////////////////////////////////////////////////////////////////////////////////////////////   
void loop() {

NicoClear();
delay(500);
NicoSlideText_1("ABCDEFGHIJKLMNOPQRSTUVWXYZ");
delay(5000);
//slide();

}
/////////////////////////////////////////////////////////////////////////////// 
/////////////////////////////////////////////////////////////////////////////// 
/*void slide(){
  
  //A
  NicoShiftout(0b00111001);
  NicoWrite();
  NicoLatch();
  delay(1000);
  NicoShiftout(0b00000001);
  NicoWrite();
  NicoLatch();
  delay(1000);
  
  //B
  NicoShiftout(0b10111011);
  NicoWrite();
  NicoLatch();
  delay(1000);
  NicoShiftout(0b00000000);
  NicoWrite();
  NicoLatch();
  delay(1000);
 
  //M
  NicoShiftout(0b00011001);
  NicoWrite();
  NicoLatch();
  delay(1000);
  NicoShiftout(0b00011000);
  NicoWrite();
  NicoLatch();
  delay(1000);
  NicoShiftout(0b00000001);
  NicoWrite();
  NicoLatch();
  delay(1000);
}
*/

/////////////////////////////////////////////////////////////////////////////// 
void NicoSlideText_1(String text) { 

for(unsigned int k=0; k<text.length(); k++){ 
  NicoShiftAlpha_1(text.charAt(k));
  }
}  
/////////////////////////////////////////////////////////////////////////////// 
void NicoShiftAlpha_1(byte idx) {  

int i =2;
if (idx == 32||idx == 73) {i=1;}
if (idx == 77||idx == 87||idx == 88) {i=3;}
   
  for (int col = i-1; col >= 0; col--) {   
    NicoShiftout_1(alphabets[idx][col]);
    delay(scrollspeed);
  }
}
///////////////////////////////////////////////////////////////////////////////
void NicoShiftout_1(unsigned int pattern) {
  for (int j =0; j < 8; j++) { 
      boolean mask = bitRead(pattern, j );

      switch (mask) {
        case 0:
        NicoSetLED(j, 0, 0, 0);
        break;
        case 1:
        NicoSetLED(j, g, r, b);
        break;
    }
  }
NicoWrite();
NicoLatch();
}
///////////////////////////////////////////////////////////////////////////////
///////////////////////////////////////////////////////////////////////////////
void NicoShiftout(unsigned int pattern) {
  for (int j =0; j < 8; j++) { 
      boolean mask = bitRead(pattern, j );

      switch (mask) {
        case 0:
        NicoSetLED(j, 0, 0, 0);
        break;
        case 1:
        NicoSetLED(j, g, r, b);
        break;
    }
  }
}
///////////////////////////////////////////////////////////////////////////////
void NicoSetLED(uint16_t lednum, uint16_t r, uint16_t g,uint16_t b) {
  NicoSetPWM(lednum * 3, r);
  NicoSetPWM(lednum * 3 + 1, g);
  NicoSetPWM(lednum * 3 + 2, b);
}
///////////////////////////////////////////////////////////////////////////////
void NicoSetPWM(uint16_t chan, uint16_t pwm) {
  if (pwm > 4095)
    pwm = 4095;
if (chan >= 24) 
    return;
  pwmbuffer[chan] = pwm;
}
///////////////////////////////////////////////////////////////////////////////
void NicoWrite() { 
for (int16_t c = 24-1; c >= 0; c--) { 
   for (int8_t b = 11; b >= 0; b--) {

      digitalWrite(_clk, LOW);
        if (pwmbuffer[c] & (1 << b)) //peut etre ici queqchose a faire
        digitalWrite(_dat, HIGH);
        else
        digitalWrite(_dat, LOW);
      digitalWrite(_clk, HIGH);
    }
  }
}
///////////////////////////////////////////////////////////////////////////////
void NicoLatch() {
  digitalWrite(_clk, LOW);
  //delayMicroseconds(50);
  digitalWrite(_lat, HIGH);
  //delayMicroseconds(50);
  digitalWrite(_lat, LOW);  
  //delayMicroseconds(50);
}
///////////////////////////////////////////////////////////////////////////////
void NicoClear() {  
  for (int i = 0; i < 24*NUM_TLC5974 ; i++)  {
  tlc.setPWM(i, 0);
  }
  tlc.write();
}
/////////////////////////////////////////////////////////////////////////////// 
