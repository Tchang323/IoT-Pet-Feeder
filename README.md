# IoT Pet Feeder using Raspberry Pi and Blynk App!😻




## 🐾Table of Contents
#### ● Project Concept and Motivation
#### ● Project Materials Requirements
#### ● Setting up your Raspberry Pi
#### ● About Blynk
#### ● Connect Raspberry Pi to Blynk
#### ● Activate servo with Blynk
#### ● Activate load cell hx711 with Blynk 
#### ● Assembly servo and load cell hx711 to Raspberry pi 
#### ● Assembly the pet feeder
#### ● Create a python script to run petfeeder
#### ● Watch Demo Video!
#### ● Project Summary and Areas for Improvement
---

## 🐾Project Concept and Motivation
The reason I wanted to create a remote-controlled pet feeder is because of a very adorable cat I have at home. I usually refill her dry food either when she finishes eating or when we are about to leave the house. However, since **her daily food intake varies**, there's **often some leftover dry food** which has to be thrown away by the end of the day, resulting in quite a bit of waste.

In addition, when we go on a two or three-day trip, we tend to leave a larger amount of food. But this leads to two problems: First, **we can't track how much she eats** and worry whether she has enough food. Also, the dry food might get damp or go bad, which isn't good for the cat's health.

Therefore, I hope to build an automatic **pet feeder that can proactively replenish her food.** By tracking the daily feeding records, I can adjust the amount of dry food dispensed each time, reducing food waste.

![image](https://hackmd.io/_uploads/H1HbozF_p.png)
---

##  🐾Project Materials Requirements
### Hardware Materials
* Raspberry Pi 3 Model B *1
* MicroSD card for Raspberry Pi *1
* Jumper wires
* Servo motor *1
* Load cell Hx711 *1
* Load cell amplifier (small board) 
* PP board or Cardboard
* cardboard boxes (or any other materials to construct a support structure for a pet feeder.)
* pet feeder(or other container)
### Software
* Python 3.9.1
* Blynk App

##  🐾Setting up your Raspberry Pi
The first step involves **remote management** of your Raspberry Pi. You can control your Raspberry Pi using either **VNC Viewer** or **VSCode**. Personally, I prefer programming with VSCode. 
You can find more information on how to set it up in this reference link:
* [Getting started with your Raspberry Pi](https://www.raspberrypi.com/documentation/computers/getting-started.html)
* [Remote accessing your RPi via VNC](https://raspberrypi.com/documentation/computers/remote-access.html)
* [Programming Raspberry pi remotely using VS code (Romote-SSH) ](https://randomnerdtutorials.com/raspberry-pi-remote-ssh-vs-code/)

## 🐾About Blynk
![image](https://hackmd.io/_uploads/SJ6wpVKuT.png)
Blynk is a digital platform primarily used for building and managing IoT applications. It provides an easy-to-use interface for connecting various hardware devices to the internet, enabling users to control and monitor them remotely through mobile or web apps!

## 🐾Connect Raspberry Pi to Blynk
Some of the reference materials online are outdated. I hope that organizing these steps can help you more!

**1. Open the terminal window and update your Pi using:**
```
sudo apt-get update
```
**2. Install git-core:**
```
sudo apt-get install git-core
```
**3. Clone the WiringPi repository**
```
git clone https://github.com/WiringPi/WiringPi.git
```
**4. Clone the blynk repository for Python**
```
git clone https://github.com/vshymanskyy/blynk-library-py
```
*Note: Since the clone link could vary. If you encounter errors during cloning, try going directly to GitHub and find the latest URL to perform the clone.*

**5. Create a template on Blynk and set up your raspberry pi on it**

Whether you want to use a web app or mobile app with Blynk is fine. If you're unsure about how to create a template, you can refer to these two links.
* [Setting up Blynk 2.0 (Web app)](https://diyprojectslab.com/blynk-2-0-with-raspberry-pi-pico-w-micropython/)
* [Blynk on Raspberry Pi (mobile app)](https://roboindia.com/tutorials/blynk-raspberry-digitaloutput/)

**6. Run a test python script**
```
python3 "filename.py"
```
In the blynk-library-py folder, you can add the Python file you want to execute. Here is a simple Python script for **testing an LED**.

Remeber to fill in your devide info in the code, you can find it on Blynk app!
![image](https://hackmd.io/_uploads/B1n4ErYua.png)

```

import BlynkLib
import RPi.GPIO as GPIO
from BlynkTimer import BlynkTimer

#Fill in your device info
BLYNK_TEMPLATE_ID = " "
BLYNK_TEMPLATE_NAME =" "
BLYNK_AUTH_TOKEN =" "


led1 = 17
GPIO.setmode(GPIO.BCM)
GPIO.setup(led1, GPIO.OUT)


# Initialize Blynk
blynk = BlynkLib.Blynk(BLYNK_AUTH_TOKEN)

# Led control through V0 virtual pin
@blynk.on("V0")
def v0_write_handler(value):
#global led_switch
    if int(value[0]) is not 0:
        GPIO.output(led1, GPIO.HIGH)
        print('LED1 HIGH')
    else:
        GPIO.output(led1, GPIO.LOW)
        print('LED1 LOW')


#function to sync the data from virtual pins
@blynk.on("connected")
def blynk_connected():
    print("Raspberry Pi Connected to New Blynk") 

try:
	while True:
    	blynk.run()
except KeyboardInterrupt:
	GPIO.cleanup()
```

**7. If you see this image, it indicates a successful connection to the Blynk interface.**


![螢幕擷取畫面 2023-12-30 231818](https://hackmd.io/_uploads/SJGOoIFOp.png)


## 🐾Activate servo with Blynk
**1. Connect the servo to Raspberry Pi's pin using three jumper wires.**

* Red: 5.5v Power
* Orange: Signal/data (GPIO 12 pin)
* Brown: Ground (GND)


![image](https://hackmd.io/_uploads/S1mh9BFd6.png)

* GPIO pins(BCM/BOARD mode)
![GPIO pins](https://hackmd.io/_uploads/BysTCSYdp.png)


**2. Test your servo with blynk**

The following code can be used to test your servo. I'm using the GPIO pins in BCM mode. If you are using BOARD mode, remember to change your pin number accordingly.
```
import BlynkLib
import RPi.GPIO as GPIO
from time import sleep
import time
from gpiozero import AngularServo

#Fill in your device info
BLYNK_TEMPLATE_ID = " "
BLYNK_TEMPLATE_NAME =" "
BLYNK_AUTH_TOKEN =" "

# Initialize Blynk
blynk = BlynkLib.Blynk(BLYNK_AUTH_TOKEN)

#Initialize servo
servo = AngularServo(12, min_pulse_width=0.0006, max_pulse_width= 0.0023)

#servo motor control thyough V0 virtual pin
#create a datastream on blynk using V0 virtual pin
@blynk.on('V0')
def v0_write_handler(value):
    if int(value[0]) == 1:      
       servo.mid()
       sleep(1)
       servo.min()
       sleep(1)
       servo.mid()
       sleep(1)
       servo.max()
       sleep(1)

    else:
       print("off")
       GPIO.cleanup()
       
#continuously runs the Blynk application
while True:
   blynk.run()

```
## 🐾Activate load cell hx711 with Blynk
**1. Assembly load cell hx711**

For more detailed assemply steps: [Follow the reference link](https://tutorials-raspberrypi.com/digital-raspberry-pi-scale-weight-sensor-hx711/)  

* VCC to Raspberry Pi Pin 2 (5V)
* GND to Raspberry Pi Pin 34 (GND)
* DT to Raspberry Pi Pin 29 (GPIO 5)
* SCK to Raspberry Pi Pin 31 (GPIO 6)

![image](https://hackmd.io/_uploads/HkE0fUt_T.png)

Connecting the HX711 (green board) tp loadcell has a crucial aspect: your connector or wire ends must align with the holes. I encountered an issue that the holes on HX711 were too large, and I didn't have the black connectors for it.  Hence, **I cut one end of the male-female wire and joined the exposed wires with those of the load cell and it works**. Then, I inserted the male end of a jumper wire into the holes on the green board. 
![image](https://hackmd.io/_uploads/rJCmLLtOa.png)

To ensure a secure connection, I used tape and rubber bands for additional stability, ensuring consistent weight readings every time.

![image](https://hackmd.io/_uploads/SyuP7LYuT.png)

![image](https://hackmd.io/_uploads/ryPAIIF_a.png)

**2. Test your load cell hx711 with blynk**

Below is the example code from github: [click here to see the hx711 repository](https://github.com/tatobari/hx711py)

Note that Each load cell's reference unit may vary slightly. You can adjust to the most suitable reference unit through testing.

```
#! /usr/bin/python2

import time
import sys

EMULATE_HX711=False

referenceUnit = 495.77  #adjust to yours

if not EMULATE_HX711:
    import RPi.GPIO as GPIO
    from hx711 import HX711
else:
    from emulated_hx711 import HX711

def cleanAndExit():
    print("Cleaning...")

    if not EMULATE_HX711:
        GPIO.cleanup()
        
    print("Bye!")
    sys.exit()

hx = HX711(5, 6)

# I've found out that, for some reason, the order of the bytes is not always the same between versions of python, numpy and the hx711 itself.
# Still need to figure out why does it change.
# If you're experiencing super random values, change these values to MSB or LSB until to get more stable values.
# There is some code below to debug and log the order of the bits and the bytes.
# The first parameter is the order in which the bytes are used to build the "long" value.
# The second paramter is the order of the bits inside each byte.
# According to the HX711 Datasheet, the second parameter is MSB so you shouldn't need to modify it.
hx.set_reading_format("MSB", "MSB")

# HOW TO CALCULATE THE REFFERENCE UNIT
# To set the reference unit to 1. Put 1kg on your sensor or anything you have and know exactly how much it weights.
# In this case, 92 is 1 gram because, with 1 as a reference unit I got numbers near 0 without any weight
# and I got numbers around 184000 when I added 2kg. So, according to the rule of thirds:
# If 2000 grams is 184000 then 1000 grams is 184000 / 2000 = 92.
#hx.set_reference_unit(113)
hx.set_reference_unit(referenceUnit)

hx.reset()

hx.tare()

print("Tare done! Add weight now...")

# to use both channels, you'll need to tare them both
#hx.tare_A()
#hx.tare_B()

while True:
    try:
        # Prints the weight. Comment if you're debbuging the MSB and LSB issue.
        val = int(hx.get_weight(5)) #Convert the number to an interger
        print(abs(val))

        hx.power_down()
        hx.power_up()
        time.sleep(0.8)

    except (KeyboardInterrupt, SystemExit):
        cleanAndExit()

```

## 🐾Assembly the pet feeder
To me, it's the most interesting part of all.😆

The assembly and code for my implementation are based on the [content of this video,](https://www.youtube.com/watch?v=GGN8OJ8USKM) with modifications. I found the steps in the video to be quite clear. The difference in my approach is that I have separated out the container, as I need to measure weight.

**1. Assemble the PP board onto the servo's horn, and secure it with screws. Use glue to attach the horn to the PP board.**

![image](https://hackmd.io/_uploads/HkQH3DKup.png)

![image](https://hackmd.io/_uploads/SkYsnwF_T.png)


**2. Cut a hole in the PP board to allow food to pass through.**

Nevermind the color change of the PP board~

![image](https://hackmd.io/_uploads/SJlraDtd6.png)
 

**3. Create a base plate to insert the servo, ensuring a point of force for the servo's movement to prevent it from moving out of place.**

![image](https://hackmd.io/_uploads/HJ_qpwY_6.png)

**4. Install the servo and base plate inside a container.**

Pay attention to **the position of the horn's hole** during assembly, as it should only be exposed when the servo is turning.

![image](https://hackmd.io/_uploads/SJVaQdKOT.png)

![image](https://hackmd.io/_uploads/SJ7n0wt_T.png)


**5. Place a cardboard piece on top, designed to prevent food from falling out when the servo is not in motion.** 

![image](https://hackmd.io/_uploads/BJSUk_tOa.png)

**6. Stick the assembled pet feeder onto a cardboard box.**

![image](https://hackmd.io/_uploads/SJuQbuYup.png)

**7. Place the load cell underneath and prepare a bowl to catch the food that falls.**

![image](https://hackmd.io/_uploads/BkLMfuKOa.png)

**8. Prepare cat food or small individual items. And then you're good to go!**

## 🐾Create a python script to run petfeeder

**Web Dashboard on Blynk**

![image](https://hackmd.io/_uploads/rkNBE5tu6.png)

**Example code**
```
 # -*- coding: utf-8 -*-
import BlynkLib
import RPi.GPIO as GPIO
import time
from datetime import datetime

# Initialize your raspberry pi device on blynk
BLYNK_AUTH_TOKEN = " "

# Initialize Blynk
blynk = BlynkLib.Blynk(BLYNK_AUTH_TOKEN)

# Initialize servo
def setup_servo():
    servo_pin = 12
    GPIO.setmode(GPIO.BCM)
    GPIO.setup(servo_pin, GPIO.OUT)
    servo1 = GPIO.PWM(servo_pin, 50)  # frequency = 50 Hz
    return servo1

def angle_to_dutycycle(angle):
    return (angle / 180.0) * 10 + 2.5

#initialize the loadcell and hx711
EMULATE_HX711=False
referenceUnit = 498.77

if not EMULATE_HX711:
    from hx711 import HX711

def setup_hx():
    hx = HX711(5, 6)
    hx.set_reading_format("MSB", "MSB")
    hx.set_reference_unit(referenceUnit)
    hx.reset()
    hx.tare()
    print("Tare done! Add weight now...")
    return hx


# / --------determines the mode of the motor (large, medium, or small)--------- /
def control_servo(feed_type):
    hx1 = setup_hx()
    servo1 = setup_servo()
    servo1.start(0)   
    print("servo is ready")
    try:
        duty_cycle_180 = angle_to_dutycycle(180)  # 馬達轉動到 180 度
        servo1.ChangeDutyCycle(duty_cycle_180)
        if feed_type == 'large':     
            servo1.ChangeDutyCycle(2.5)  # Reset to 0 degrees
        elif feed_type == 'medium':
            servo1.ChangeDutyCycle(5.8)  # Reset to 60 degrees
        elif feed_type == 'small':
            servo1.ChangeDutyCycle(8.1)  # Reset to 100 degrees

        time.sleep(0.5)
        servo1.ChangeDutyCycle(duty_cycle_180)  # Move back to 180 degrees
        time.sleep(0.5)
        
        #start scaling
        val = abs(int(hx1.get_weight(5))) #Convert the number to an interger
        print("weight:" + str(val))
        hx1.power_down()
        hx1.power_up()
        

    finally:
        servo1.stop()
        GPIO.cleanup()
        update_food_weight(str(val))
        update_feeding_time()


# / ------------------ Feeding large portion ----------------------- /
@blynk.on('V0')
def v0_write_handler(value):
    if int(value[0]) == 1:
        control_servo('large')
        #scaling_weight()


# / ------------------ Feeding Medium portion ----------------------- /
@blynk.on('V1')
def v1_write_handler(value):
    if int(value[0]) == 1:
        control_servo('medium')
        #scaling_weight()


# / ------------------ Feeding Small portion ----------------------- /
@blynk.on('V2')
def v2_write_handler(value):
    if int(value[0]) == 1:
        control_servo('small')


# / ------------------ Manual control the portion----------------------- /
@blynk.on('V3')
def v3_write_handler(value):
    servo1 = setup_servo()
    servo1.start(0)
    try:
        angle = int(float(value[0]))
        duty_cycle_180 = angle_to_dutycycle(180)
        duty_cycle = angle_to_dutycycle(180-angle) #用減的，因為我用180度當作起始位置的角度
        servo1.ChangeDutyCycle(duty_cycle_180)
        servo1.ChangeDutyCycle(duty_cycle)  # Reset to 0 degrees
        blynk.virtual_write(3, angle)
        time.sleep(0.5)
    finally:
        servo1.stop()
        GPIO.cleanup()
        update_feeding_time()


# / ------------------ Update feeding time on Blynk ----------------------- /
def update_feeding_time():
    current_time = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    blynk.virtual_write(4,current_time)


# / ------------------ Update weight on Blynk ----------------------- /
def update_food_weight(weight):
    blynk.virtual_write(5,weight)



#check if blynk is connected
@blynk.on("connected")
def blynk_connected():
    print("Raspberry Pi Connected to New Blynk")


try:
    while True:
        blynk.run()
except KeyboardInterrupt:
    GPIO.cleanup()
	   
```
## 🐾Watch Demo Video!
https://youtu.be/SxklDhX65yk


## 🐾Project Summary and Areas for Improvement💜
This project taught me a lot about knowledge areas unfamiliar to me. It was my first deep dive into hardware implementation. Initially, I was quite confused, and I remember feeling thrilled just getting an LED to light up. Throughout this project, I faced numerous challenges, both big and small. Although the pet feeder may seem uncomplicated, I found the physical assembly to be the most challenging part. In my initial attempts, I couldn't assemble it successfully, leading to food being scattered everywhere. It was only after seeking help from my teacher and researching online that I managed to make improvements and achieve functioning results. Additionally, my programming skills are not very strong, so resolving errors often took a lot of time, but overcoming these challenges was incredibly rewarding.

I am also grateful to our teacher for allowing us to work on topics that interest us, fostering creativity without imposing pressure. This approach made the process of working on this final project genuinely enjoyable rather than being preoccupied with the grades it might receive.However, one aspect I wish I had managed better was the timing of my start on the project. Beginning later than ideal led to certain functionalities, such as incorporating a model for image recognition and implementing automatic weight-based feeding, remaining incomplete. Had these features been integrated, I believe my project would have reached a higher level of completeness and sophistication.

In this project, I learned not just the basics of the IoT field, but more importantly, how to approach and solve problems. I feel that in solving these problems, I have gained a lot more than I anticipated.