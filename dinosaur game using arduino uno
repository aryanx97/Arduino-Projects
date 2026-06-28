#include <Arduino.h>
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <EEPROM.h> // Included to save the high score permanently

// Initialize the LCD with I2C address 0x27, 16 columns, and 2 rows
LiquidCrystal_I2C lcd(0x27, 16, 2);  

const int buttonPin = 8;
const int buzzerPin = 9;

// Custom character for the Dinosaur
byte dino[8] = {
  B00111,
  B00101,
  B00111,
  B10100,
  B11111,
  B01110,
  B01010,
  B01010
};

// Custom character for the Cactus
byte cactus[8] = {
  B00100,
  B10101,
  B11111,
  B00100,
  B00100,
  B00100,
  B00100,
  B00100
};

// Game variables
int dinoPos = 1; 
int cactusPos = 15;
int score = 0;
int highScore = 0; // Variable to track the high score
bool playing = false;
bool jumping = false;
int jumpTime = 0;

void setup() {
  pinMode(buttonPin, INPUT_PULLUP);
  pinMode(buzzerPin, OUTPUT); 
  
  // Initialize the I2C LCD and turn on the backlight
  lcd.init();
  lcd.backlight();
  
  lcd.createChar(0, dino);
  lcd.createChar(1, cactus);

  // Read the saved high score from EEPROM memory address 0
  EEPROM.get(0, highScore);
  
  // If the EEPROM is blank/unread (returns a weird number), set high score to 0
  if(highScore < 0 || highScore > 9999) {
    highScore = 0;
  }
  
  showStartScreen();
}

void loop() {
  // If game is not active, wait for button press to start
  if (!playing) {
    if (digitalRead(buttonPin) == LOW) {
      playing = true;
      score = 0;
      cactusPos = 15;
      lcd.clear();
      tone(buzzerPin, 1000, 100); 
      delay(200); 
    }
    return;
  }

  // Handle Jumping Logic
  if (digitalRead(buttonPin) == LOW && !jumping) {
    jumping = true;
    jumpTime = 0;
    tone(buzzerPin, 1500, 50); 
  }

  if (jumping) {
    dinoPos = 0; 
    jumpTime++;
    if (jumpTime > 3) { 
      jumping = false;
      dinoPos = 1; 
    }
  } else {
    dinoPos = 1;
  }

  // Move Cactus
  cactusPos--;
  if (cactusPos < 0) {
    cactusPos = 15;
    score++;
  }

  // Check for Collision
  if (cactusPos == 1 && dinoPos == 1) {
    gameOver();
    return;
  }

  // --- Draw Game Screen ---
  lcd.clear();
  
  // Draw Dino
  lcd.setCursor(1, dinoPos);
  lcd.write(byte(0));
  
  // Draw Cactus
  lcd.setCursor(cactusPos, 1);
  lcd.write(byte(1));
  
  // Draw Current Score during gameplay
  lcd.setCursor(13, 0);
  lcd.print(score);

  delay(150); 
}

void showStartScreen() {
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Dino! HI:");
  lcd.print(highScore); // Show High Score on start
  lcd.setCursor(0, 1);
  lcd.print("Press to Start");
}

void gameOver() {
  playing = false;
  bool newHighScore = false;

  // Check if current score beats the high score
  if (score > highScore) {
    highScore = score;
    EEPROM.put(0, highScore); // Save new high score to permanent memory
    newHighScore = true;
  }
  
  // Game Over Sound Effects
  if (newHighScore) {
    // Triumphant sound for a new high score!
    tone(buzzerPin, 800, 150); delay(150);
    tone(buzzerPin, 1000, 150); delay(150);
    tone(buzzerPin, 1200, 300);
  } else {
    // Sad sound for regular game over
    tone(buzzerPin, 300, 250); delay(250);
    tone(buzzerPin, 150, 400); 
  }
  
  // Display Game Over and Scores
  lcd.clear();
  if (newHighScore) {
    lcd.setCursor(0, 0);
    lcd.print("NEW HIGH SCORE!!");
  } else {
    lcd.setCursor(0, 0);
    lcd.print("GAME OVER!");
  }
  
  lcd.setCursor(0, 1);
  lcd.print("S:");
  lcd.print(score);
  lcd.print(" HI:");
  lcd.print(highScore);
  
  delay(1000); // Prevent accidental immediate restart
}
