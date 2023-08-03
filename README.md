                                                            ////TRANSMITTER SECTION CODE////




#include <RH_ASK.h>

#include <SPI.h> // Not actually used but needed to compile

#include <SPFD5408_Adafruit_GFX.h>    // Core graphics library

#include <SPFD5408_Adafruit_TFTLCD.h> // Hardware-specific library

#include <SPFD5408_TouchScreen.h>

const char *msg ;

RH_ASK driver;

#define YP A1  // must be an analog pin, use "An" notation!

#define XM A2  // must be an analog pin, use "An" notation!

#define YM 7   // can be a digital pin

#define XP 6   // can be a digital pin

#define TS_MINX 125

#define TS_MINY 85

#define TS_MAXX 965

#define TS_MAXY 905

TouchScreen ts = TouchScreen(XP, YP, XM, YM, 300);

#define LCD_CS A3

#define LCD_CD A2

#define LCD_WR A1

#define LCD_RD A0

// optional

#define LCD_RESET A4

#define REDBAR_MINX 80

#define GREENBAR_MINX 130

#define BLUEBAR_MINX 180

#define BAR_MINY 30

#define BAR_HEIGHT 250

#define BAR_WIDTH 30

Adafruit_TFTLCD tft(LCD_CS, LCD_CD, LCD_WR, LCD_RD, LCD_RESET);

#define BLACK   0x0000

int BLUE = tft.color565(50, 50, 255);

#define DARKBLUE 0x0010

#define VIOLET 0x8888

#define RED     0xF800

#define GREEN   0x07E0

#define CYAN    0x07FF

#define MAGENTA 0xF81F

#define YELLOW  0xFFE0

#define WHITE   0xFFFF

#define GREY   tft.color565(64, 64, 64);

#define GOLD 0xFEA0

#define BROWN 0xA145

#define SILVER 0xC618

#define LIME 0x07E0
//dish prices
#define dp1 50
#define dp2 60
#define dp3 70

void drawHome()

{

  tft.fillScreen(WHITE);

  tft.drawRoundRect(0, 0, 319, 240, 8, WHITE);     //Page border

  tft.fillRoundRect(30, 40, 100, 40, 8, GOLD);

  tft.drawRoundRect(30, 40, 100, 40, 8, WHITE);  //Dish1

  tft.fillRoundRect(30, 90, 100, 40, 8, GOLD);

  tft.drawRoundRect(30, 90, 100, 40, 8, WHITE);  //Dish2

  tft.fillRoundRect(30, 140, 100, 40, 8, GOLD);   //CANCLE

  tft.drawRoundRect(30, 140, 100, 40, 8, WHITE);

  tft.fillRoundRect(10, 190, 190, 40, 8, CYAN);

  tft.drawRoundRect(10, 190, 190, 40, 8, WHITE); //Confirm order

  tft.fillRoundRect(180, 40, 100, 40, 8, GOLD);

  tft.drawRoundRect(180, 40, 100, 40, 8, WHITE);  //CANCEL1

  tft.fillRoundRect(180, 90, 100, 40, 8, GOLD);

  tft.drawRoundRect(180, 90, 100, 40, 8, WHITE); //CANCEL2

  tft.fillRoundRect(180, 140, 100, 40, 8, GOLD);

  tft.drawRoundRect(180, 140, 100, 40, 8, WHITE); //CANCEL

  tft.fillRoundRect(210, 190, 100, 40, 8, GREEN);

  tft.drawRoundRect(210, 190, 100, 40, 8, WHITE); //Bill

  tft.setCursor(60, 0);

  tft.setTextSize(3);

  tft.setTextColor(LIME);

  tft.print("DIGITAL MENU");

  tft.setTextSize(2);

  tft.setTextColor(WHITE);

  tft.setCursor(37, 47);

  tft.print(" Dish1");

  tft.setCursor(37, 97);

  tft.print(" Dish2");

  tft.setCursor(37, 147);

  tft.print(" Dish3");

  tft.setCursor(23, 197);

  tft.print(" Confirm order");

  tft.setCursor(187, 47);

  tft.print(" CANCEL");

  tft.setCursor(187, 97);

  tft.print(" CANCEL");

  tft.setCursor(187, 147);

  tft.print(" CANCEL");

  tft.setCursor(227, 197);

  tft.print(" Bill");

    //delay(500);

}

int oldcolor, currentcolor, currentpcolour;

void setup(void) { 

  tft.reset();

  tft.begin(tft.readID());

  Serial.begin(9600);

  Serial.println();

  Serial.print("reading id...");

  delay(500);

  Serial.println(tft.readID(), HEX);  

  tft.fillScreen(BLACK);

  tft.setRotation(1);

  tft.setTextSize(3);

  tft.setTextColor(WHITE);

  tft.setCursor(50, 140);

  tft.print("PREPARING...");

  tft.setTextColor(tft.color565(255, 255, 0));

  
  tft.setCursor(30, 70);

  tft.print("By:");

  tft.setCursor(10, 100);
  
  tft.print("TEAM BUG SLAYERS");

  for (int i; i < 250; i++)

  {

    tft.fillRect(BAR_MINY - 10, BLUEBAR_MINX, i, 10, RED);

    delay(0.01);

  }

  tft.fillScreen(BLACK);
  if (!driver.init())

      Serial.println("init failed");

  drawHome();

  pinMode(13, OUTPUT);

}

#define MINPRESSURE 10

#define MAXPRESSURE 1000

void transmit()

{  

  driver.send((uint8_t *)msg, strlen(msg));

  driver.waitPacketSent();

  delay(1000);

  }
  void bill(int pn){
  tft.fillScreen(DARKBLUE);
  tft.setCursor(20,20);
  tft.setTextSize(3);
  tft.setTextColor(WHITE);
  tft.print("Total Bill:");
  tft.println(pn);
    delay(1500);
    pn=0;
  



}
void confirm(){
  tft.fillScreen(DARKBLUE);
  tft.setCursor(20,20);
  tft.setTextSize(3);
  tft.setTextColor(WHITE);
  tft.print("Order Confirmed");
  delay(1500);
  drawHome();
  

}
void loop()

{    int pr;

  digitalWrite(13, HIGH);

  TSPoint p = ts.getPoint();

  digitalWrite(13, LOW);

  // if sharing pins, you'll need to fix the directions of the touchscreen pins

  //pinMode(XP, OUTPUT);

  pinMode(XM, OUTPUT);

  pinMode(YP, OUTPUT);

  //pinMode(YM, OUTPUT);

  if (p.z > ts.pressureThreshhold)

    {

      p.x = map(p.x, TS_MAXX, TS_MINX, 0, 320);

      p.y = map(p.y, TS_MAXY, TS_MINY, 0, 240);      

      if (p.x > 180 && p.x < 280 && p.y > 190 && p.y < 230  && p.z > MINPRESSURE && p.z < MAXPRESSURE)

      {

        Serial.println("Dish1");

        msg = "Dish1 Ordered";
        pr=pr+dp1;

        transmit();

        tft.fillRoundRect(30, 40, 100, 40, 8, WHITE);

        delay(70);

        tft.fillRoundRect(30, 40, 100, 40, 8, GOLD);

        tft.drawRoundRect(30, 40, 100, 40, 8, WHITE);

        tft.setCursor(37, 47);

        tft.println(" Dish1");

        delay(70);

        }

      if (p.x > 180 && p.x < 280 && p.y > 140 && p.y < 180)

      {

        Serial.println("Dish2");

        msg = "Dish2 Ordered";
        pr=pr+dp2;

        transmit();

        tft.fillRoundRect(30, 90, 100, 40, 8, WHITE);

        delay(70);

        tft.fillRoundRect(30, 90, 100, 40, 8, GOLD);

        tft.drawRoundRect(30, 90, 100, 40, 8, WHITE);

        tft.setCursor(37, 97);

        tft.println(" Dish2");

        delay(70);      

      }

      if (p.x > 180 && p.x < 280 && p.y > 90 && p.y < 130)

      {

        Serial.println("Dish3");

        msg = "Dish3 Ordered";
        pr=pr+dp3;

        transmit();

        tft.fillRoundRect(30, 140, 100, 40, 8, WHITE);   //rgb led

        delay(70);

        tft.fillRoundRect(30, 140, 100, 40, 8, GOLD);   //rgb led

        tft.drawRoundRect(30, 140, 100, 40, 8, WHITE);   //rgb led

        tft.setCursor(37, 147);

        tft.print(" Dish3");

        delay(70);       

      }

      if (p.x > 210 && p.x < 310 && p.y > 40 && p.y < 80)

      {

        Serial.println("Confirm order");

        msg = "OrderConfirmed";

        transmit();

        tft.fillRoundRect(10, 190, 190, 40, 8, WHITE);

        delay(70);

        tft.fillRoundRect(10, 190, 190, 40, 8, CYAN);

        tft.drawRoundRect(10, 190, 190, 40, 8, WHITE);

        tft.setCursor(23, 197);

        tft.print(" Confirm order");

        delay(70);
        confirm();

      }

      if (p.x > 30 && p.x < 130 && p.y > 190 && p.y < 230)

      {

        Serial.println("CANCEL");
        if(pr>dp1){
          pr=pr-dp1;
        }

        msg = "Dish1 CANCELED";

        transmit();

        tft.fillRoundRect(30, 40, 100, 40, 8, WHITE);

        delay(70);

        tft.fillRoundRect(30, 40, 100, 40, 8, GOLD);

        tft.drawRoundRect(30, 40, 100, 40, 8, WHITE);

        tft.setCursor(187, 47);

        tft.print(" CANCEL");

        delay(70);

      }

      if (p.x > 30 && p.x < 130 && p.y > 140 && p.y < 180 )

      {

        Serial.println("CANCEL");
         if(pr>dp2){
          pr=pr-dp2;
        }

        msg = "Dish2 CANCELED";

        transmit();

        tft.fillRoundRect(180, 90, 100, 40, 8, WHITE);

        delay(70);

        tft.fillRoundRect(180, 90, 100, 40, 8, GOLD);

        tft.drawRoundRect(180, 90, 100, 40, 8, WHITE);

        tft.setCursor(187, 97);

        tft.print(" CANCEL");

        delay(70);

      }

      if (p.x > 30 && p.x < 130 && p.y > 90 && p.y < 130)

      {

        Serial.println("CANCEL");
         if(pr>dp3){
          pr=pr-dp3;
        }

        msg = "Dish3 Canceled";

        transmit();

        tft.fillRoundRect(180, 140, 100, 40, 8, WHITE);

        delay(70);

        tft.fillRoundRect(180, 140, 100, 40, 8, GOLD);

        tft.drawRoundRect(180, 140, 100, 40, 8, WHITE);

        tft.setCursor(187, 147);

        tft.print(" CANCEL");

        delay(70);

      }


      if (p.x > 10 && p.x < 210 && p.y > 40 && p.y < 80)

      {

        Serial.println("Bill");

        msg = "Send Bill";

        transmit();

        tft.fillRoundRect(210, 190, 100, 40, 8, WHITE);

        delay(70);

        tft.fillRoundRect(210, 190, 100, 40, 8, GREEN);

        tft.drawRoundRect(210, 190, 100, 40, 8, WHITE);

        tft.setCursor(227, 197);

        tft.print(" Bill");

        delay(70);
        bill(pr);
        pr=0;

      }

    }

}



                                                     ////RECEIVER SECTION CODE////





 LiquidCrystal Library - Hello World

 Demonstrates the use a 16x2 LCD display.  The LiquidCrystal
 library works with all LCD displays that are compatible with the
 Hitachi HD44780 driver. There are many of them out there, and you
 can usually tell them by the 16-pin interface.

 This sketch prints "Hello World!" to the LCD
 and shows the time.

  The circuit:
 * LCD RS pin to digital pin 12
 * LCD Enable pin to digital pin 10
 * LCD D4 pin to digital pin 5
 * LCD D5 pin to digital pin 4
 * LCD D6 pin to digital pin 3
 * LCD D7 pin to digital pin 1
 * LCD R/W pin to ground
 * LCD Vdd pin to 5v
 * 
 * 10K resistor:
 * ends to +5V and ground
 * wiper to LCD VO pin (pin 3)

 Library originally added 18 Apr 2008
 by David A. Mellis
 library modified 5 Jul 2009
 by Limor Fried (http://www.ladyada.net)
 example added 9 Jul 2009
 by Tom Igoe
 modified 22 Nov 2010
 by Tom Igoe
 modified 7 Nov 2016
 by Arturo Guadalupi

 This example code is in the public domain.

 http://www.arduino.cc/en/Tutorial/LiquidCrystalHelloWorld

*/

// include the library code:
#include <LiquidCrystal.h>

#include <RH_ASK.h>

#include <SPI.h> // Not actualy used but needed to compile

// initialize the library by associating any needed LCD interface pin
// with the arduino pin number it is connected to
const int rs = 12, en = 10, d4 = 5, d5 = 4, d6 = 3, d7 = 1;
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);
RH_ASK driver;

#define buzzer 2

void setup() {
  // set up the LCD's number of columns and rows:
  
   //Serial.begin(9600);  // Debugging only

    pinMode(buzzer, OUTPUT);
    lcd.begin(16, 2);
  // Print a message to the LCD.
  if (!driver.init())

         Serial.println("init failed");
}

void loop() {
  // set the cursor to column 0, line 1
  // (note: line 1 is the second row, since counting begins with 0):
   uint8_t buf[17];

    uint8_t buflen = sizeof(buf);

    if (driver.recv(buf, &buflen)) // Non-blocking

    {

      int i;

      digitalWrite(buzzer, HIGH);

      delay(1000);

      digitalWrite(buzzer, LOW);

      // Message with a good checksum received, dump it.

      Serial.print("Message: ");

      Serial.println((char*)buf);
      
      lcd.setCursor(0,0);
      lcd.print("T1:");
      lcd.print((char*)buf);

    }
  
  // print the number of seconds since reset:
}
