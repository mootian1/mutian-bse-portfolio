**Mutian's AMAZING ball tracking robot**

|Have you ever chased something to chase your big red ball? Wanted a dog but didn't want to clean up its poop? Well, you are in great luck! Introducing the AMAZING ball tracking robot, also known as the ABTR 2000! With a brand new ABTR 2000, your ball can be tracked into the sunset! This robot includes revolutionary features like two ultrasonic sensors and a high-definition camera to follow the ball wherever it rolls. Two massive motors power this robot allowing it great maneuverability and speed. Interested? Contact Mutian today!|

You should comment out all portions of your portfolio that you have not completed yet, as well as any instructions:
```HTML 
<!--- This is an HTML comment in Markdown -->
<!--- Anything between these symbols will not render on the published site -->
```

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Mutian W | Leigh High School | considering between electrical and mechanical engineering | Incoming Junior|

**Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.**

![Headstone Image](logo.svg)
  
# Final Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/iwcLb2Ay0eE?si=HvonVb2Dy3TZOiym" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE



# Second Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/GGrmzXq1tao?si=ut21n--gggfZCzGq" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

For your second milestone, explain what you've worked on since your previous milestone. You can highlight:
- Technical details of what you've accomplished and how they contribute to the final goal
- What has been surprising about the project so far
- Previous challenges you faced that you overcame
- What needs to be completed before your final milestone 

# First Milestone: Set Up Raspberry Pi and Sensors:

<iframe width="560" height="315" src="https://www.youtube.com/embed/FkshiS4EVYc?si=SvQSFMKy0P1sfe7s" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

- Connected the Raspberry Pi to the home pc through SSH
- Used RealVNC viewer to Raspberry Pi screen through home display
- Created a circuit for both ultrasonic sensors using a breadboard 
- Created test code for the camera that displays what the camera sees

# Schematics 
Here's where you'll put images of your schematics. [Tinkercad](https://www.tinkercad.com/blog/official-guide-to-tinkercad-circuits) and [Fritzing](https://fritzing.org/learning/) are both great resoruces to create professional schematic diagrams, though BSE recommends Tinkercad becuase it can be done easily and for free in the browser. 

# Code
Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs. 

```python
# import the necessary packages
from picamera2 import Picamera2
import RPi.GPIO as GPIO
import time
import cv2
import numpy as np

#hardware work
GPIO.setmode(GPIO.BOARD)

GPIO_TRIGGER1 = 33      #Left ultrasonic sensor
GPIO_ECHO1 = 35

GPIO_TRIGGER3 = 29      #Right ultrasonic sensor
GPIO_ECHO3 = 31

MOTOR1B=21  #Left Motor
MOTOR1E=19

MOTOR2B=22  #Right Motor
MOTOR2E=18

LED_PIN = 13  #If it finds the ball, then it will light up the led

# Set pins as output and input
GPIO.setup(GPIO_TRIGGER1,GPIO.OUT)  # Trigger
GPIO.setup(GPIO_ECHO1,GPIO.IN)      # Echo
GPIO.setup(GPIO_TRIGGER3,GPIO.OUT)  # Trigger
GPIO.setup(GPIO_ECHO3,GPIO.IN)
GPIO.setup(LED_PIN,GPIO.OUT)

# Set trigger to False (Low)
GPIO.output(GPIO_TRIGGER1, False)
GPIO.output(GPIO_TRIGGER3, False)

def sonar(GPIO_TRIGGER,GPIO_ECHO):
      start=0
      stop=0
      GPIO.setup(GPIO_TRIGGER,GPIO.OUT)
      GPIO.setup(GPIO_ECHO,GPIO.IN)
      GPIO.output(GPIO_TRIGGER, False)
      time.sleep(0.01)
      GPIO.output(GPIO_TRIGGER, True)
      time.sleep(0.00001)
      GPIO.output(GPIO_TRIGGER, False)
      begin = time.time()
      while GPIO.input(GPIO_ECHO)==0 and time.time()<begin+0.05:
            start = time.time()
      while GPIO.input(GPIO_ECHO)==1 and time.time()<begin+0.1:
            stop = time.time()
      elapsed = stop-start
      distance = elapsed * 34000
      distance = distance / 2
      print("Distance : %.1f" % distance)
      return distance

GPIO.setup(MOTOR1B, GPIO.OUT)
GPIO.setup(MOTOR1E, GPIO.OUT)
GPIO.setup(MOTOR2B, GPIO.OUT)
GPIO.setup(MOTOR2E, GPIO.OUT)

def forward():
      GPIO.output(MOTOR1B, GPIO.HIGH)
      GPIO.output(MOTOR1E, GPIO.LOW)
      GPIO.output(MOTOR2B, GPIO.HIGH)
      GPIO.output(MOTOR2E, GPIO.LOW)

def reverse():
      GPIO.output(MOTOR1B, GPIO.LOW)
      GPIO.output(MOTOR1E, GPIO.HIGH)
      GPIO.output(MOTOR2B, GPIO.LOW)
      GPIO.output(MOTOR2E, GPIO.HIGH)

def rightturn():
      GPIO.output(MOTOR1B, GPIO.LOW)
      GPIO.output(MOTOR1E, GPIO.HIGH)
      GPIO.output(MOTOR2B, GPIO.HIGH)
      GPIO.output(MOTOR2E, GPIO.LOW)

def leftturn():
      GPIO.output(MOTOR1B, GPIO.HIGH)
      GPIO.output(MOTOR1E, GPIO.LOW)
      GPIO.output(MOTOR2B, GPIO.LOW)
      GPIO.output(MOTOR2E, GPIO.HIGH)

def stop():
      GPIO.output(MOTOR1E, GPIO.LOW)
      GPIO.output(MOTOR1B, GPIO.LOW)
      GPIO.output(MOTOR2E, GPIO.LOW)
      GPIO.output(MOTOR2B, GPIO.LOW)

#Image analysis work
def segment_colour(frame):    #returns only the red colors in the frame
    hsv_roi = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
    mask_1 = cv2.inRange(hsv_roi, np.array([160, 160, 10]), np.array([180, 255, 255]))
    ycr_roi = cv2.cvtColor(frame, cv2.COLOR_BGR2YCrCb)
    mask_2 = cv2.inRange(ycr_roi, np.array((0., 165., 0.)), np.array((255., 255., 255.)))
    mask = mask_1 | mask_2
    kern_dilate = np.ones((8,8),np.uint8)
    kern_erode  = np.ones((3,3),np.uint8)
    mask= cv2.erode(mask,kern_erode)
    mask=cv2.dilate(mask,kern_dilate)
    return mask

def find_blob(blob):
    largest_contour=0
    cont_index=0
    contours, hierarchy = cv2.findContours(blob, cv2.RETR_CCOMP, cv2.CHAIN_APPROX_SIMPLE)
    for idx, contour in enumerate(contours):
        area=cv2.contourArea(contour)
        if (area > largest_contour):
            largest_contour=area
            cont_index=idx
    r=(0,0,2,2)
    if len(contours) > 0:
        r = cv2.boundingRect(contours[cont_index])
    return r, largest_contour

def target_hist(frame):
    hsv_img=cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
    hist=cv2.calcHist([hsv_img],[0],None,[50],[0,255])
    return hist

#CAMERA CAPTURE
camera = Picamera2()
config = camera.create_preview_configuration(main={"size": (160, 120), "format": "RGB888"})
camera.configure(config)
camera.start()

time.sleep(0.1)

flag=0

while True:
      frame = camera.capture_array()
      frame = cv2.flip(frame, 1)
      centre_x=0.
      centre_y=0.
      mask_red=segment_colour(frame)
      loct,area=find_blob(mask_red)
      x,y,w,h=loct

      distanceR = sonar(GPIO_TRIGGER3,GPIO_ECHO3)
      distanceL = sonar(GPIO_TRIGGER1,GPIO_ECHO1)

      if (w*h) < 10:
            found=0
      else:
            found=1
            simg2 = cv2.rectangle(frame, (x,y), (x+w,y+h), 255,2)
            centre_x=x+((w)/2)
            centre_y=y+((h)/2)
            cv2.circle(frame,(int(centre_x),int(centre_y)),3,(0,110,255),-1)
            centre_x-=80
            centre_y=60-centre_y
            print(centre_x, centre_y)
      initial=2000
      GPIO.output(LED_PIN,GPIO.LOW)

      if(found==0):
            if flag==0:
                  rightturn()
                  time.sleep(0.05)
            else:
                  leftturn()
                  time.sleep(0.05)
            stop()
            time.sleep(0.0125)

      elif(found==1):
            if(area<initial):
                  if(distanceR<10 or distanceL<10):
                        if distanceR>=8:
                              rightturn()
                              time.sleep(0.00625)
                              stop()
                              time.sleep(0.0125)
                              forward()
                              time.sleep(0.00625)
                              stop()
                              time.sleep(0.0125)
                              leftturn()
                              time.sleep(0.00625)
                        elif distanceL>=8:
                              leftturn()
                              time.sleep(0.00625)
                              stop()
                              time.sleep(0.0125)
                              forward()
                              time.sleep(0.00625)
                              stop()
                              time.sleep(0.0125)
                              rightturn()
                              time.sleep(0.00625)
                              stop()
                              time.sleep(0.0125)
                        else:
                              stop()
                              time.sleep(0.01)
                  else:
                        forward()
                        time.sleep(0.00625)
            elif(area>=initial):
                  initial2=6700
                  if(area<initial2):
                        if(distanceR>10 and distanceL>10):
                              if(centre_x<=-20 or centre_x>=20):
                                    if(centre_x<0):
                                          flag=1
                                          leftturn()
                                          time.sleep(0.025)
                                    elif(centre_x>0):
                                          flag=0
                                          rightturn()
                                          time.sleep(0.025)
                              forward()
                              time.sleep(0.00003125)
                              stop()
                              time.sleep(0.00625)
                        else:
                              stop()
                              time.sleep(0.01)
                  else:
                        GPIO.output(LED_PIN,GPIO.HIGH)
                        time.sleep(0.1)
                        stop()
                        time.sleep(0.1)

      #Debugging Purposes, making sure the ball is seen
      cv2.imshow("Camera feed", frame)
      cv2.imshow("Red mask", mask_red)

      if(cv2.waitKey(1) & 0xff == ord('q')):
            break

camera.stop()
cv2.destroyAllWindows()
GPIO.cleanup()
```

# Bill of Materials
Here's where you'll list the parts in your project. To add more rows, just copy and paste the example rows below.
Don't forget to place the link of where to buy each component inside the quotation marks in the corresponding row after href =. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize this to your project needs. 

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| RasTech Raspberry Pi 4 4GB Starter Kit | Runs the programs that guides the ball tracking robot | $147.79 | <a href="https://www.amazon.com/RasTech-Raspberry-Starter-Heatsink-Screwdriver/dp/B0C8LV6VNZ/"> Raspberry Pi Kit </a> |
| Robot Chassis | Chassis for robot- has wheels for mobility and points to add sensors and more | $13.99 | <a href="https://www.amazon.com/Smart-Chassis-Motors-Encoder-Battery/dp/B01LXY7CM3/"> Chassis </a> |
| Ultrasonic Sensors | Detect the diostance the robot is from obstacles | $6.79 | <a href="https://www.amazon.com/MTDELE-HC-SR04-Ultrasonic-Mounting-Bracket/dp/B0G6ZDBTWR/"> Ultrasonic Sensors </a> |
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |

# Other Resources/Examples
One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Example 1](https://trashytuber.github.io/YimingJiaBlueStamp/)
- [Example 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)

To watch the BSE tutorial on how to create a portfolio, click here.
