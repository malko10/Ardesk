<pre><code>

#include <Wire.h>
#include <LiquidCrystal_I2C.h>

// I2C 주소는 보통 0x27 또는 0x3F입니다. 1602 LCD(16칸 2줄) 기준입니다.
LiquidCrystal_I2C lcd(0x27, 16, 2); 

const int ledPin = 13;     // 밝기 조절을 위해 ~표시가 있는 9번 핀 사용
const int buttonPin = 2; 
int brightness = 30;      // LED 밝기 (0~255)

unsigned long pressStartTime = 0;
unsigned long previousBlinkTime = 0;
bool isWaiting = false;
bool isLedOn = false;
bool lcdDisplayed = false; // LCD에 문구가 이미 출력되었는지 확인하는 플래그

void setup() {
  pinMode(ledPin, OUTPUT);
  pinMode(buttonPin, INPUT);

  // LCD 초기화
  lcd.init();
  lcd.backlight(); // 백라이트 켜기
  lcd.clear();     // 화면 지우기
}

void loop() {
  if (digitalRead(buttonPin) == HIGH) {
    if (!isWaiting) {
      pressStartTime = millis(); 
      isWaiting = true;
    }

    // 10초가 지났을 때
    if (millis() - pressStartTime >= 3000) {
      
      // LCD 문구 출력 (한 번만 실행되도록 처리)
      if (!lcdDisplayed) {
        lcd.setCursor(0, 0);       // 첫 번째 줄 첫 번째 칸
        lcd.print("Drink water!");
        lcdDisplayed = true;
      }

      // 0.5초 간격 LED 깜박임
      if (millis() - previousBlinkTime >= 500) {
        previousBlinkTime = millis(); 
        isLedOn = !isLedOn;
        
        if (isLedOn) {
          digitalWrite(ledPin, brightness);
        } else {
          digitalWrite(ledPin, 0);
        }
      }
    }
  } else {
    // 버튼을 떼면 모든 상태 초기화
    digitalWrite(ledPin, 0);
    isWaiting = false;
    isLedOn = false;
    
    // LCD 화면 초기화
    if (lcdDisplayed) {
      lcd.clear();
      lcdDisplayed = false;
    }
  }
}
</code></pre>
