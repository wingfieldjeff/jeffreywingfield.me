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

# Arduino Code
```c
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

# Second attempt
![[Pasted image 20250211140130.png]]
On my next go, I caved to common sense. 4 bearings, with one on each side of each axel. This means there are not plastic on plastic wear surfaces and the weight of the disk is not transferred into the motor.

<html><div id="stl-viewer" style="width:800px; height:600px;"></div>

<!-- Define the import map -->
<script type="importmap">
{
  "imports": {
    "three": "https://unpkg.com/three@0.162.0/build/three.module.js",
    "three/addons/": "https://unpkg.com/three@0.162.0/examples/jsm/"
  }
}
</script>

<!-- Use ES modules to import and run the code -->
<script type="module">
  import * as THREE from 'three';
  import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
  import { STLLoader } from 'three/addons/loaders/STLLoader.js';

  const width = 800, height = 600;

  // Create scene, camera, and renderer
  const scene = new THREE.Scene();
  const camera = new THREE.PerspectiveCamera(45, width / height, 0.1, 1000);
  const renderer = new THREE.WebGLRenderer({ antialias: true });
  renderer.setSize(width, height);
  document.getElementById('stl-viewer').appendChild(renderer.domElement);

  // Set up OrbitControls for interactivity
  const controls = new OrbitControls(camera, renderer.domElement);

  // Add basic lighting
  scene.add(new THREE.AmbientLight(0x404040));
  const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
  directionalLight.position.set(0, 0, 1).normalize();
  scene.add(directionalLight);

  // Load the STL file (ensure "shell.stl" is in the same directory)
  const loader = new STLLoader();
  loader.load('shell.stl', function (geometry) {
    const material = new THREE.MeshPhongMaterial({ color: 0xB0C4DE });
    const mesh = new THREE.Mesh(geometry, material);
    scene.add(mesh);

    // Center the model
    geometry.computeBoundingBox();
    const center = new THREE.Vector3();
    geometry.boundingBox.getCenter(center);
    mesh.geometry.translate(-center.x, -center.y, -center.z);

    // Adjust the camera based on the model size
    const maxDim = Math.max(
      geometry.boundingBox.max.x - geometry.boundingBox.min.x,
      geometry.boundingBox.max.y - geometry.boundingBox.min.y,
      geometry.boundingBox.max.z - geometry.boundingBox.min.z
    );
    camera.position.set(0, 0, maxDim * 2);
    controls.update();
  });

  // Animation loop to render the scene
  function animate() {
    requestAnimationFrame(animate);
    controls.update();
    renderer.render(scene, camera);
  }
  animate();
</script>


</html>


