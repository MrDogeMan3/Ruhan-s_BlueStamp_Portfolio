# Automatic Pet Feeder
Leaving your pet at home for an extended period can be stressful especially when it comes to your feeding them. This simple system yet effective system provides a solution by automatically dispensing food at scheduled intervals, keeping your pet well-fed and happy no matter where you are. It’s a very practical and affordable way to give your pet the care they need even without you being there.
(Scroll to the bottom for instructions.)

<div>
  <table style="margin: 0 auto;">
    <thead>
      <tr>
        <th>Engineer</th>
        <th>School</th>
        <th>Area of Interest</th>
        <th>Grade</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>Ruhan D</td>
        <td>Amador Valley</td>
        <td>Buisness/CS</td>
        <td>Rising Sophomore</td>
      </tr>
    </tbody>
  </table>
</div>



<div style="text-align: center;">
  <img src="IMG_5093.jpg" width="400" height="500">
</div>

# Final Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/az72qlODCaQ?si=PFK4gkVayK0n753S" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

#Overview
In this milestone, I upgraded my system from an Arduino Uno to an ESP32, which allowed me to connect the project to Wi-Fi. This enabled web-based control — for example, I can now trigger actions like scoring or feeding directly from a website or mobile dashboard.


To fix the sensitivity issue, I implemented a calibration routine:

1. Check if the Monitor updated correctly.
2. If it went up by more than 2, increase the delay (to reduce oversensitivity).
3. If it didn’t register a score, decrease the delay (to make it more responsive).
4. Repeat steps 1–3 until accurate detection was achieved.

#Challenge
The hardest part of this milestone was debugging the code after switching to the ESP32. At first, nothing seemed to work — the code was confusing and the sensor wasn't detecting correctly. Eventually, I realized I had missed a critical step: adding my Wi-Fi credentials and Adafruit IO configuration.

#Outcome
Once I fixed this, everything started to work. I was able to:

-Send sensor events to my online feed
-Control the system remotely
-Log data with timestamps


# Second Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/2Nvly2hs4aM?si=KoH5SAP4h9i86c8d" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

After successfully completing the software setup for my project, I realized I needed a stable and organized platform for the hardware. Initially, I planned to create a custom 3D-printed holder using Onshape. However, I had realized that the idea just wasn't feasable. 3D printing lacked the durability and was very costly which wasn't what I wanted for this part. After brainstorming, I concluded that wood was a better alternative. Wood offered strength, ease of assembly, and flexibility for mounting all of my stuff. I glued the water bottle and servo to the main board and screwed in the Arduino. To complete the structure, I added three more wooden planks to form a sturdy frame. This resulted in a four-plank base attached with a total of 7 brackets that effectively supported the full hardware setup. With the physical assembly finished, I now can focus on enhancing the software and adding hardware modifications before I am done with my project.

# First Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/VYfw38KZGkk?si=vkkNii-y88ocAph8" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

When I first decided to build an automatic pet feeder, I knew what I wanted it to do: feed my pet when my family and I weren't around. Then I had to figure out how to keep track of time so it would feed at the right moment. At first, I tried using something interrupts at first I used TIMER0_COMPA_vect and made it run a bit of code every second to keep track of time at first it was working but then it started to turn unrealiable by sometimes overheating and not moving when supposed to. After a bit of trial and error, I found out that using millis() (a built-in timer) inside an interrupt wasn't the best idea because they both use the same system. So I decided to just used millis() inside the loop() instead. It was way easier and everything started working smoothly with no interuptions. Now my code just waits, checks if enough time has passed, and activates servo, and resets the timer.




# Code
<!--Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs.-->

```c++
#include "config.h"
#include "AdafruitIO_WiFi.h"
#include <WiFi.h>
#include <ESP32Servo.h>
#include <time.h>

// Setup Adafruit IO client
AdafruitIO_WiFi io(AIO_USERNAME, AIO_KEY, WIFI_SSID, WIFI_PASS);

// Servo setup
Servo servo;
const int SERVO_PIN = 13;

#define FEED_INTERVAL_MINUTES 10
const unsigned long FEED_INTERVAL = FEED_INTERVAL_MINUTES * 60UL * 1000UL;
unsigned long lastFeedTime = 0;

// NTP Timezone Config
const char* ntpServer1 = "pool.ntp.org";
const char* ntpServer2 = "time.nist.gov";
const char* timezone = "PST8PDT";  // Update as needed

// Adafruit IO feeds
AdafruitIO_Feed* feedNow = io.feed("feed-now");
AdafruitIO_Feed* feedLog = io.feed("feed-log");

int pos = 0;

void feederOpen() {
  for (pos = 0; pos <= 180; pos += 5) {
    servo.write(pos);
    delay(15);
  }
  Serial.println("Servo: Open");
}

void feederClose() {
  for (pos = 180; pos >= 0; pos -= 5) {
    delay(15);
    servo.write(pos);
  }
  Serial.println("Servo: Close");
}

void handleFeedNow(AdafruitIO_Data* data) {
  Serial.println("Emma fed Manually!");
  feederOpen();
  feederClose();

  // Log time to Adafruit IO (cleaned up)
  time_t now;
  time(&now);
  String timestamp = ctime(&now);
  timestamp.trim();  // removes the trailing newline
  feedLog->save(timestamp);
  lastFeedTime = millis();
}

void connectToWiFi() {
  WiFi.begin(WIFI_SSID, WIFI_PASS);
  Serial.print("Connecting to WiFi");
  while (WiFi.status() != WL_CONNECTED) {
    Serial.print(".");
    delay(500);
  }
  Serial.println("\nConnected! IP: ");
  Serial.println(WiFi.localIP());
}

void configureTime() {
  configTzTime(timezone, ntpServer1, ntpServer2);
  struct tm timeinfo;
  while (!getLocalTime(&timeinfo)) {
    Serial.print(".");
    delay(500);
  }
  Serial.println("\nTime synced!");
}

void setup() {
  Serial.begin(115200);

  servo.setPeriodHertz(50);
  servo.attach(SERVO_PIN, 1000, 2000);
  servo.write(90);

  connectToWiFi();
  configureTime();

  io.connect();
  while (io.status() < AIO_CONNECTED) {
    Serial.print(".");
    delay(500);
  }
  Serial.println("\nConnected to Adafruit IO");

  feedNow->onMessage(handleFeedNow);
  lastFeedTime = millis();
}

void loop() {
  io.run();

  unsigned long currentTime = millis();
  if (currentTime - lastFeedTime >= FEED_INTERVAL) {
    Serial.println("Emma fed Automatically!");
    feederOpen();
    feederClose();

    time_t now;
    time(&now);
    String timestamp = ctime(&now);
    timestamp.trim();  // Clean up newline
    feedLog->save(timestamp);

    lastFeedTime = currentTime;
  }

  delay(1000);
}

```

# Bill of Materials
<!--Here's where you'll list the parts in your project. To add more rows, just copy and paste the example rows below.
Don't forget to place the link of where to buy each component inside the quotation marks in the corresponding row after href =. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize this to your project needs.-->

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Uno R3 | This item is the microcontroller for my project  | $16.99 | <a href="mv6OIH9LIr_WhUFObq______AAAAAQAAAAAAAAAAAAAAAQAAABsO4OlFpYZdU0RQREc7wYUia4w9wmYhLc9jf18iHhFI_SLj9IFUyDZpMdzg"> Link </a> |
| 9g Micro Servo | The 9g Micro Servo is used for precise movements for moving the cardboard piece of my project | $3.50 | <a href="https://www.dfrobot.com/product-255.html"> Link </a> |
| 1/2 in. x 3 in. x 3 ft. S4S Poplar Board | Holding Hardware Setup together | $4.98 | <a href="https://www.homedepot.com/p/Weaber-1-2-in-x-3-in-x-3-ft-S4S-Poplar-Board-27365/207058986#overlay"> Link </a> |
| ESP32 | New microcontroller used for connecting to my website. | $15.19 for 2 | <a href="[https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/](https://www.amazon.com/dp/B09KLS2YB3/ref=sspa_dk_offsite_brave_1?psc=1&aaxitk=4a07e45f98f219f6b3ddd734272a8eb1&tqtoken=AgR4jNMhjyUzyFDbCQ3_47hQUCT8tWiocixQbTspXwkh41kAGgABAAlyZWNpcGllbnQAC0FtYXpvbkFkc1RRAAEAEWF3cy1rbXMtaGllcmFyY2h5ACRiM2NlMjE3OS0wMzJhLTQ5NTAtOWFlZC00MTczZDMxZmNhMmEAXOghkNlOh0fuzZ-o6DnOWA8hxa3heV3y-HrYzUXtK8nXMwFE6ZdgvB9GW5UIVL3iYcuNhlcA9Fe4MIoEwQYs3Po-t1zrtCNhN0iyyIwthq_pUbxZd5nyMJA48a2IAgAAEAAZ_ADTHczhoHP3RhwebudmlbvwjsClK82G_YubflTrR16t2zuZoc15QWwIxvAp01D_____AAAAAQAAAAAAAAAAAAAAAQAAABvk3Io6HlTe2XY4otYr3CUfOSTrwgv8RT2I4dGV3UEAqd3nb7yJYMK_jTDw&offsiteFlag=1)"> Link </a> |
| 12x12 wood planks | Frame of the weight senor | $7.99 for 5 | <a href="[https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/](https://www.amazon.com/Plywood-Basswood-Unfinished-Cutting-Engraving/dp/B0D491GJ5T/ref=sr_1_7?sr=8-7)"> Link </a> |
| Male-Male Wires | Connecting my breadboard to my microcontrollers. | $3.99 for 40 | <a href="[https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/](https://www.amazon.com/California-JOS-Breadboard-Optional-Multicolored/dp/B0BRTJQZRD/ref=sr_1_3?sr=8-3)"> Link </a> |
| Total Price | --Not Including Tax-- | $52.64 | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |

#Instructions
Automatic Arduino Pet Feeder – Step-by-Step Guide

Parts needed(THIS IS FOR BASIC PROJECT MODIFICATIONS NOT INCLUDED)
  
  1.Arduino Uno	1	Or any compatible board (e.g. Nano)
  
  2.Servo Motor (SG90 or MG90S)	1	Controls the dispenser
  
  3.Plastic Bottle	1	For food reservoir
  
  4.Cardboard piece	1	Acts as a flap to open/close
  
  5.Jumper Wires	~5	Male-to-female or male-to-male
  
  6.Hot glue / Tape	1	To attach components
  
  7.Power source	1	USB or 5V adapter

Step 1: Hardware Setup
 
  <img src="Screenshot 2025-07-25 at 1.28.13 PM.png" width="550" height="315">

Servo Wire	Connects To Arduino
Red --->	5V
Brown --->	GND
Orange --->	D9

Use hot glue to attach the servo to the bottle, and connect a piece of cardboard (3d printing can also work but make sure the material is safe for your pet) to the horn of the servo so it blocks and unblocks the mouth.

Step 2: Software Setup
  1. Install Arduino IDE
Download: https://www.arduino.cc/en/software
  2.Install with all drivers checked (especially for USB)
  3.Copy Paste code found above.

Step 3: Uploading Code
  
  1. Connect Arduino Uno via USB
  2. In Arduino IDE:
    -Go to Tools > Board → Select Arduino Uno
    -Go to Tools > Port → Choose correct COM port
  3. Click the Upload arrow

Step 4: Assembly
  
  1. Cut a hole in the side of the bottle for the food to drop out.
  2. Glue the servo to the bottle near that opening.
  3. Attach a cardboard flap to the servo horn — it should rotate to block or unblock the hole.
  4. Place the bottle over your pet’s bowl so food drops directly into it.

With that you are done with your project but there are some possible modifications you may want to add.

Possible Modificartions:
  
  1. Replace your arduino uno with a ESP32 so you can connect it to your adafruit dashboard.
  2. Add RTC (Real-Time Clock) module for better time accuracy and change code to 24 hour feeding system.
  3. Add a weight sensor to monitor when your pet is actually eating their food.
  4. Add a display (LED) and ultra sonic sensor to tell the levels of your food.


# My Starter Project - Retro Arcade Console
<iframe width="315" height="550" src="https://www.youtube.com/embed/js7R6Dc6IFs?si=QcVjojFgPuVrgVKV" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

I decided to make the Retro Arcade Console(RAC) for the starter project. The RAC has five unique games: 1. Tetris, 2. Snake, 3. Space encounters, 4. Space Invaders, and 5. Slot Machine. These five games can easily controlled by six easy buttons: up, down, left, right, return(exit the game), and fire(shooting for space invaders). If you are having trouble beating the game you're playing, no problem; with the other two buttons, you can easily choose any of the four remaining games in the convenience of being in your pocket.

# Challenges
While making my starter project I faced my  share of problems the biggest challenges that had become aparent after starting was learning how to solder considering the fact that I had never learnt how to solder after learning the basics and a couple desolders I was able to adapt a decent method to solder easily after I was able to solder properly I soon ran into my next obstacle, which involved the instructions, unfortunatly for me I had trouble with the instuctions leading to some small issues with the screws and batteries. The last big struggle for the starter project was with the acrylic case which for my short nails to longer than I would like to admit to peel of the protective layer.
