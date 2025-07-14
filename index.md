# Automatic Pet Feeder
Leaving your pet at home for an extended period can be stressful especially when it comes to your feeding your furry friends. This simple system yet effective system provides a solution by automatically dispensing food at scheduled intervals, keeping your pet well-fed and happy no matter where you are. It’s a very practical and affordable way to give your pet the care they need even without you being there.

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


# Second Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/2Nvly2hs4aM?si=KoH5SAP4h9i86c8d" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

After successfully completing the software setup for my project, I realized I needed a stable and organized platform for the hardware. Initially, I planned to create a custom 3D-printed holder using Onshape. However, I had realized that the idea just wasn't feasable. 3D printing lacked the durability and was very costly which wasn't what I wanted for this part. After brainstorming, I concluded that wood was a better alternative. Wood offered strength, ease of assembly, and flexibility for mounting all of my stuff. I glued the water bottle and servo to the main board and screwed in the Arduino. To complete the structure, I added three more wooden planks to form a sturdy frame. This resulted in a four-plank base attached with a total of 7 brackets that effectively supported the full hardware setup. With the physical assembly finished, I now can focus on enhancing the software and adding hardware modifications before I am done with my project.

# First Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/VYfw38KZGkk?si=vkkNii-y88ocAph8" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

When I first decided to build an automatic pet feeder, I knew what I wanted it to do: feed my pet when my family and I weren't around. Then I had to figure out how to keep track of time so it would feed at the right moment. At first, I tried using something interrupts at first I used TIMER0_COMPA_vect and made it run a bit of code every second to keep track of time at first it was working but then it started to turn unrealiable by sometimes overheating and not moving when supposed to. After a bit of trial and error, I found out that using millis() (a built-in timer) inside an interrupt wasn't the best idea because they both use the same system. So I decided to just used millis() inside the loop() instead. It was way easier and everything started working smoothly with no interuptions. Now my code just waits, checks if enough time has passed, and activates servo, and resets the timer.




# Code
<!--Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs.-->

```c++
#include <Servo.h>

#define FEED_INTERVAL_MINUTES 5
const byte servoPin = 9;
const unsigned long FEED_INTERVAL = FEED_INTERVAL_MINUTES * 60UL * 1000UL;  // in milliseconds

Servo servo;
unsigned long lastFeedTime = 0;

void feederOpen() {
  servo.write(0);
  delay(175);
  servo.write(90);
}

void feederClose() {
  servo.write(180);
  delay(175);
  servo.write(90);
}

void setup() {
  Serial.begin(9600);
  servo.attach(servoPin);
  servo.write(90);  // Neutral position
  lastFeedTime = millis();
  Serial.println("System initialized");
}

void loop() {
  unsigned long currentTime = millis();

  if (currentTime - lastFeedTime >= FEED_INTERVAL) {
    Serial.println("Feeding the pet :)");
    feederOpen();
    delay(150);
    feederClose();
    lastFeedTime = currentTime;
  }

  // Optional: Print waiting time
  Serial.print("Waiting... ");
  Serial.print((FEED_INTERVAL - (currentTime - lastFeedTime)) / 1000);
  Serial.println(" seconds remaining");
  
  delay(1000);  // Reduce serial spamming
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
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |
| Total Price | --Not Including Tax-- | $25.47 | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |

# My Starter Project - Retro Arcade Console
<iframe width="560" height="315" src="https://www.youtube.com/embed/js7R6Dc6IFs?si=QcVjojFgPuVrgVKV" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

I decided to make the Retro Arcade Console(RAC) for the starter project. The RAC has five unique games: 1. Tetris, 2. Snake, 3. Space encounters, 4. Space Invaders, and 5. Slot Machine. These five games can easily controlled by six easy buttons: up, down, left, right, return(exit the game), and fire(shooting for space invaders). If you are having trouble beating the game you're playing, no problem; with the other two buttons, you can easily choose any of the four remaining games in the convenience of being in your pocket.

# Challenges
While making my starter project I faced my  share of problems the biggest challenges that had become aparent after starting was learning how to solder considering the fact that I had never learnt how to solder after learning the basics and a couple desolders I was able to adapt a decent method to solder easily after I was able to solder properly I soon ran into my next obstacle, which involved the instructions, unfortunatly for me I had trouble with the instuctions leading to some small issues with the screws and batteries. The last big struggle for the starter project was with the acrylic case which for my short nails to longer than I would like to admit to peel of the protective layer.
<!-- Other Resources/Examples
One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Example 1](https://trashytuber.github.io/YimingJiaBlueStamp/)
- [Example 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)

To watch the BSE tutorial on how to create a portfolio, click here.-->
