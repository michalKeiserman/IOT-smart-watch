# IOT-smart-watch

 This application was built to help you workout! It uses the Blynk app, and CPX board.

 `USE THE APP FOR:`
 -You can set up your steps for the day goal -> when you will reach it, you will recieve a message to your smartphone via Blynk app.
 -You can set up your sweat goal for the day to make sure you trained well enough.
 -You can use the temperature sensor so you will know when the weather conditions are suitable to workour outside.
 -You can use the GPS sensor so when you are in your house radios -> you will recieve a message to your smartphone via Blynk app.

`CREATED BY:` Michal keiserman & Ido yael

**HAVE FUN WITH IT!**


```
//including librarys
#include <ESP8266_Lib.h>
#include <Adafruit_CircuitPlayground.h>
#define BLYNK_PRINT Serial
#include <ESP8266_Lib.h>
#include <BlynkSimpleShieldEsp8266.h>

//creating variables
bool step_check, notify_stop, notify_stop2 = true;
int temp, water, step_count, back_home;
int step_goal = 20;
float X, Y, Z;

//network settings
char auth[] = "INSERT YOUR BLYNK TEMPLATE AUTHENTICATION";
char ssid[] = "INSERT YOUR WIFI NETWORK NAME";
char pass[] = "INSERT YOUR WIFI PASSWARD";
#define EspSerial Serial1
#define ESP8266_BAUD 115200
ESP8266 wifi(&EspSerial);

//reading the step goal and GPS location
BLYNK_WRITE(V2)
{
  step_goal = param.asInt();
}
BLYNK_WRITE(V0)
{
  back_home = param.asInt();
}


void setup() {
  CircuitPlayground.begin();
  pinMode(13, OUTPUT);
  EspSerial.begin(ESP8266_BAUD);
  Blynk.run();
  Blynk.begin(auth, wifi, ssid, pass);
  delay(10);
}

void loop() {
  //reads the temprature, angle and water level
  temp = CircuitPlayground.temperature();
  X = CircuitPlayground.motionX();
  water = analogRead(A0);
  //write data to the app
  Blynk.virtualWrite(V4, temp);
  Blynk.virtualWrite(V1, water);
  Blynk.virtualWrite(V3, step_count);
  //detecting a step
  if (X >= 5 and step_check) {
    step_count++;
    step_check = false;
  }
  if (X < 0 and step_check == false) {
    step_count++;
    step_check = true;
  }
  //checking if you've reached your step goal
  if (step_count == step_goal) {
    Blynk.notify("step goal reached!");
  }
  //checks if you got back home
  digitalWrite(13, back_home);
  if (((back_home == 1) and notify_stop2) or CircuitPlayground.rightButton() == 1) {
    Blynk.notify("welcome back!!!");
    notify_stop2 = false;
  }
  delay(10);
}
```
