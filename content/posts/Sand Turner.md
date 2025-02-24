# The Idea
I have this desk toy.

![[Pasted image 20250210210547.png]]Sand flows between bubbles, making patterns. When the sand runs out, you flip it over and the process repeats. This takes between 1 and 5 minutes. The problem, at least for me, is that it is fascinating for about a day before the friction of flipping over is just enough to leave it on a shelf and forget about it.

Wouldn't it be cool if it flipped itself? What if it was more kinetic sculpture and less toy. It could be mounted on a wall, run all the time, and be different every time you look at it. 

# First Swing
![[20250117_172519_1.mp4]]The quickest thing I could come up with was essentially 2 plates with m3 bolts through them. One bolt had a bearing on it and the other had a plastic roller and stepper motor. An old arduino controls the stepper and a usbc breakout supplies 5v to both. This worked surprising well, so well that I fell for it. Thinking the job was done, I slapped it into a case that held the plates.
![[Pasted image 20250210212632.png]]
I added some LEDs that brighten when the disk is moving and dim when it is not. I put a lid on it, plugged it in, and called it good.
![[Pasted image 20250210213256.png]]
It ran continuously for about a week before the plastic pins welded themselves into plastic holes and the motor siezed.
# Second attempt
![[Pasted image 20250211140130.png]]
On my next go, I caved to common sense. 4 bearings, with one on each side of each axel. This means there are not plastic on plastic wear surfaces and the weight of the disk is not transferred into the motor.![[sand_turner.jpg]]

![[wiring.jpg]]

So far, so good. This version has been running continuously on my desk for a couple weeks now.

# [Github](https://github.com/wingfieldjeff/sand_turner)

## Arduino Code
```c++
#include <AccelStepper.h>

// Define step type and steps per revolution
#define FULLSTEP 4
#define STEP_PER_REVOLUTION 2048

// Define step counts for 90 to 270 degrees
#define MIN_STEPS 3800   // 90 degrees
#define MAX_STEPS 11400  // 270 degrees
#define STEPS_TO_DEGREES (180.0 / 7600.0) // Convert steps to degrees

AccelStepper stepper_1(FULLSTEP, 11, 9, 10, 8);

// Define the LED pin (PWM-enabled pin)
#define LED_PIN 6

// Direction toggle variable
bool directionForward = true;

// Define brightness levels
#define BRIGHTNESS_MIN 64  // 25% brightness
#define BRIGHTNESS_MAX 255 // 100% brightness
#define BRIGHTNESS_STEP 5  // Smoothness step size

void setup() {
  Serial.begin(9600);

  // Configure stepper_1 with faster speed and acceleration
  stepper_1.setMaxSpeed(600.0);   // Max speed (steps per second)
  stepper_1.setAcceleration(500.0); // Acceleration (steps per second squared)
  stepper_1.setSpeed(100);         // Initial speed
  stepper_1.setCurrentPosition(0); // Set starting position to 0

  // Initialize the LED pin
  pinMode(LED_PIN, OUTPUT);
  analogWrite(LED_PIN, BRIGHTNESS_MIN); // Set LED to 25% brightness initially
}

void smoothTransition(int fromBrightness, int toBrightness) {
  if (fromBrightness < toBrightness) {
    // Gradually increase brightness
    for (int brightness = fromBrightness; brightness <= toBrightness; brightness += BRIGHTNESS_STEP) {
      analogWrite(LED_PIN, brightness);
      delay(10); // Adjust delay for smoother or faster transitions
    }
  } else {
    // Gradually decrease brightness
    for (int brightness = fromBrightness; brightness >= toBrightness; brightness -= BRIGHTNESS_STEP) {
      analogWrite(LED_PIN, brightness);
      delay(10); // Adjust delay for smoother or faster transitions
    }
  }
}

void loop() {
  // Randomize the number of steps for the rotation (90 to 270 degrees)
  long randomSteps = random(MIN_STEPS, MAX_STEPS + 1); // +1 to include MAX_STEPS in range

  // Adjust direction based on toggle
  if (!directionForward) {
    randomSteps = -randomSteps; // Make the steps negative for reverse direction
  }

  // Calculate the degrees rotated
  float rotationDegrees = abs(randomSteps) * STEPS_TO_DEGREES;

  // Calculate the proportional wait time in milliseconds
  long waitTime = rotationDegrees * 666.67; // Degrees-to-ms conversion (1° -> ~666.67 ms)

  // Smoothly increase LED brightness to 100% during rotation
  smoothTransition(BRIGHTNESS_MIN, BRIGHTNESS_MAX);

  // Rotate stepper_1
  stepper_1.enableOutputs();
  stepper_1.move(randomSteps); // Use move() for relative movement
  while (stepper_1.distanceToGo() != 0) {
    stepper_1.run();
  }

  // Smoothly decrease LED brightness back to 25% after rotation
  smoothTransition(BRIGHTNESS_MAX, BRIGHTNESS_MIN);

  Serial.print(F("Stepper_1 rotated by "));
  Serial.print(abs(randomSteps)); // Display positive value for steps
  Serial.print(F(" steps (~"));
  Serial.print(rotationDegrees); // Display rotation in degrees
  Serial.print(F(" degrees). Waiting for "));
  Serial.print(waitTime / 1000); // Display wait time in seconds
  Serial.println(F(" seconds."));

  // Toggle direction for the next rotation
  directionForward = !directionForward;

  // Wait proportional to the rotation
  stepper_1.disableOutputs();
  delay(waitTime); // Proportional wait time
}
```


# Supplies
-  1x Arduino Nano (Or any Arduino compatible microcontroller)
-  4x [608 (8mm x 22mm x 7mm) bearings](https://www.amazon.com/%EF%BC%BB10-Pack%EF%BC%BD-608-Ball-Bearings/dp/B08XVFSZTF?crid=18RL5GU4B9PTV&dib=eyJ2IjoiMSJ9.cDCFSSZO25t7JZQqLsFiI4dI0ch9in_r_OramH59qAMDkM7iz472v4pCSNwHxceKOlWN6lrhS-S3-c7zDlWrzALVI8nVhPOd6Fa7yleE2Vdofb463KyvANPwt1lPdTnG6uYLQ-TWrz4YTMHC9Cw7WXNmF9wLj0HDfWaJCWHdM_h3HKbwu2uW6kkAq0ciH7CSyEwkodNRCZ4lFlgkRi-Qe2x5uM8qYUpilbw3_zqmPFk.7qmof2PuQ6S6J2oQgvPp_-Io_poqOTDvoRdeU5JWmds&dib_tag=se&keywords=bearings&qid=1740413160&sprefix=bearingf%2Caps%2C151&sr=8-11&th=1)
-  1x  [28BYJ-48 Stepper Motor](https://www.amazon.com/ELEGOO-28BYJ-48-ULN2003-Stepper-Arduino/dp/B01CP18J4A?crid=2ELA3TY34RIJK&dib=eyJ2IjoiMSJ9.6bwZgnYtcN_NH0492YUVI0YuwQASAQpiRezWZMpccTs6E90iUkInCfoSVWh9slRMTybzi_XU6aC-7NHUmJrIhy7P4vslPeScOM04TV8BCAoRL4q_keTYwpyv_RV1yoCVIjH4EqDFLPiv1qkgfSJsvyFHVajqPetA9HQChSZqDDBaCl9LtIX6Rzv7nynXy1wRR0Fc4DeAqEjz8CbCV3WWdFYQu1t5gec_ZYzX06L9kcU.bmW1JHCv_KybTVJHVBBSQ9g1H2hp9jqPmkZiaO5DmBg&dib_tag=se&keywords=stepper+motor&qid=1740411662&sprefix=stepper+%2Caps%2C166&sr=8-4)
-  1x  [ULN2003 Stepper Driver](https://www.amazon.com/ELEGOO-28BYJ-48-ULN2003-Stepper-Arduino/dp/B01CP18J4A?crid=2ELA3TY34RIJK&dib=eyJ2IjoiMSJ9.6bwZgnYtcN_NH0492YUVI0YuwQASAQpiRezWZMpccTs6E90iUkInCfoSVWh9slRMTybzi_XU6aC-7NHUmJrIhy7P4vslPeScOM04TV8BCAoRL4q_keTYwpyv_RV1yoCVIjH4EqDFLPiv1qkgfSJsvyFHVajqPetA9HQChSZqDDBaCl9LtIX6Rzv7nynXy1wRR0Fc4DeAqEjz8CbCV3WWdFYQu1t5gec_ZYzX06L9kcU.bmW1JHCv_KybTVJHVBBSQ9g1H2hp9jqPmkZiaO5DmBg&dib_tag=se&keywords=stepper+motor&qid=1740411662&sprefix=stepper+%2Caps%2C166&sr=8-4)
-  1x [USBc Female breakout](https://www.amazon.com/Cermant-Breakout-Serial-Connector-Converter/dp/B0CB2VFJ54?crid=HJYWZ321WYWR&dib=eyJ2IjoiMSJ9.V0VvXGB7bPvgM5eOvKUKrYrQ3DMt2lLP7Pa90M8P7QJI9kfU41Muc_pVcJa6PD33tKB-VqXPe3JEiv7mBBosScBQvw0dK7O_nELsT5-RWEH9yMfB2Tlk-f_SlEb563x3Y8Pefhap1BKGiSsEoczuQD_4voyyVRv7lTVhB0DvycwEOw9bnQwHjtlSDciHNPBY6JutgLA-3Y6kUFqyXXaGagM_rVnADJKejgcCQZRmvD8.6PyCiYcuWt2o-6cAf5jU_VWyrftBI-6JRuuEgae9zQM&dib_tag=se&keywords=usb+breakout+board&qid=1740411875&sprefix=usbc+break%2Caps%2C159&sr=8-13)
-  2x M3x12 bolts
-  22 AWG wire
-  CA Glue or m2x5 self tapping screws
-  2x [Silicone rings](https://www.amazon.com/dp/B06XC41JJW?ref_=ppx_hzsearch_conn_dt_b_fed_asin_title_4&th=1) (optional)
-  2x Wago 3 wire connectors (optional) 
-  5v LED strip (optional)
# Printed Parts
- 1x *Shell Front.stl*
- 1x *Shell Back.stl*
- 2x *pin.stl*
- 2x *roller.stl*

# Assembly 
1. Print the above parts.
2. Place the bearing in the slots in the front shell. They should be a friction fit and this is easiest to do with the shell horizontal.
3. If using the silicone rings, place them on the rollers
4. Place the rollers between the bearings and pass the pins through.
5. On the driven roller (the side with motor mounting holes), peal back the silicone ring (if used) and place either CA Glue or a self tapping m2 screw in the locking hole to secure it to the pin.
6. Wire and flash the arduino as shown here: diagram here (found): https://newbiely.com/images/tutorial/arduino-nano-uln2003-28byj-48-stepper-wiring-diagram.jpg
7. Attach the stepper motor with the m3 bolts. They are simply strewed into the plastic. m3 nuts can be used as spacers but are not strictly necessary .
8. (optionally) solder your LEDs to ground and D6 on the arduino.
9. Attach the usbc 5v breakout to the back shell with double sided tape or hot glue
10. Close the shell halves. I left plenty of room for even very messy wiring.