# LED Maze Game Project Guide

Hey there, young inventor! Today we're going to make a cool LED maze game using an Arduino. Let's get started!

## What You'll Need

- Arduino Uno
- Breadboard
- 4 LEDs (different colors if possible)
- 4 220-ohm resistors
- 1 momentary pushbutton (4-pin)
- 1 10k-ohm resistor
- Some jumper wires

## Step 1: Set Up Your Circuit

### LED Setup
1. Connect the long leg (positive) of each LED to pins 2, 3, 4, and 5 on the Arduino through a 220-ohm resistor.
2. Connect the short leg (negative) of each LED to the Arduino's GND.

### Pushbutton Setup
The pushbutton setup is a bit tricky, so let's break it down:

1. Place the 4-pin momentary pushbutton on your breadboard. Make sure it's oriented correctly:
   - The button should have 4 pins in a rectangle shape.
   - The longer side of the rectangle should be parallel to the long side of the breadboard.

2. Connect one pin on one side of the button to Arduino's pin 7.
3. Connect the pin diagonally opposite to the one you just connected to Arduino's GND (ground) through a 10k-ohm resistor.
4. Connect the same pin (connected to GND through the resistor) to Arduino's 5V.

Here's a diagram to help you visualize the setup:

```
    +5V
     |
     |
   10k Ω
     |
     |------+
     |      |
  +--|--+   |
  |     |   |
  |  o  |   |
  |     |   |
  +--|--+   |
     |      |
     |      |
    GND    Pin 7
```

In this diagram:
- The square represents the pushbutton.
- The circle in the middle represents the actual button part you press.
- The lines coming out of the square are the button's pins.
- Make sure to align your button as shown in the diagram.

## Step 2: Write the Code

Now, let's program our game! Copy this code into your Arduino IDE:

```cpp
const int ledPins[] = {2, 3, 4, 5};  // LED pins
const int buttonPin = 7;             // Button pin
int currentLed = 0;                  // Current LED in the sequence
int mazeSequence[4];                 // Our maze sequence
bool gameOver = false;

void setup() {
  // Set up LED pins as outputs
  for (int i = 0; i < 4; i++) {
    pinMode(ledPins[i], OUTPUT);
  }
  
  // Set up button pin as input with internal pull-up resistor
  pinMode(buttonPin, INPUT_PULLUP);
  
  // Initialize random number generator
  randomSeed(analogRead(0));
  
  // Generate random maze sequence
  for (int i = 0; i < 4; i++) {
    mazeSequence[i] = random(4);
  }
  
  // Show the first LED in the sequence
  digitalWrite(ledPins[mazeSequence[0]], HIGH);
}

void loop() {
  // Check if button is pressed
  if (digitalRead(buttonPin) == LOW && !gameOver) {
    delay(50);  // Debounce
    if (digitalRead(buttonPin) == LOW) {
      // Button is pressed, check if it's the correct LED
      if (currentLed == mazeSequence[currentLed]) {
        // Correct! Move to next LED
        digitalWrite(ledPins[mazeSequence[currentLed]], LOW);
        currentLed++;
        if (currentLed == 4) {
          // Game won!
          victoryBlink();
          gameOver = true;
        } else {
          // Show next LED
          digitalWrite(ledPins[mazeSequence[currentLed]], HIGH);
        }
      } else {
        // Wrong LED, game over
        gameOverBlink();
        gameOver = true;
      }
    }
    // Wait for button release
    while (digitalRead(buttonPin) == LOW) {}
  }
}

void victoryBlink() {
  for (int i = 0; i < 5; i++) {
    for (int j = 0; j < 4; j++) {
      digitalWrite(ledPins[j], HIGH);
    }
    delay(200);
    for (int j = 0; j < 4; j++) {
      digitalWrite(ledPins[j], LOW);
    }
    delay(200);
  }
}

void gameOverBlink() {
  for (int i = 0; i < 3; i++) {
    digitalWrite(ledPins[mazeSequence[currentLed]], HIGH);
    delay(500);
    digitalWrite(ledPins[mazeSequence[currentLed]], LOW);
    delay(500);
  }
}
```

## Step 3: Upload and Play!

1. Connect your Arduino to your computer.
2. Click the "Upload" button in the Arduino IDE to send the code to your Arduino.
3. Once uploaded, the game will start automatically!

## How to Play

1. The game will light up one LED to start.
2. Press the button when you think you're on the right LED.
3. If you're correct, the next LED in the sequence will light up.
4. If you're wrong, the game will end with a sad blink.
5. If you make it through all 4 LEDs, you win! The LEDs will do a happy blink.

## What's Happening in the Code?

- We create a random "maze" sequence of 4 LEDs.
- The game shows you the LEDs one at a time.
- You need to remember the sequence and press the button at the right time.
- If you press when the wrong LED is lit, game over!
- If you make it through the whole sequence, you win!

## Understanding the Button Setup

In our code, we use `pinMode(buttonPin, INPUT_PULLUP);`. This activates an internal pull-up resistor in the Arduino, which means:

1. When the button is not pressed, the pin reads HIGH (5V).
2. When the button is pressed, it connects the pin directly to ground, so the pin reads LOW (0V).

That's why in our code, we check for `digitalRead(buttonPin) == LOW` to detect a button press.

Have fun playing your LED maze game! Can you beat it every time?
