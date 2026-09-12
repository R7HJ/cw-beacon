/*
  Arduino Morse Beacon
  R7HJ 12.09.2026
  
  Arduino pro mini ATmega168 5V 16MHz
*/

#define SPEED (20)           // Скорость в WPM
#define DOTLEN (1200/SPEED)
#define DASHLEN (3*DOTLEN)
#define PAUSE (600000UL)      // UL для корректной работы long timeout

const uint8_t txPin = 10;    // Управление PTT
const uint8_t ledPin = 13;   // Встроенный светодиод
const uint8_t tonePin = 5;   // Выход звука
const int toneFreq = 800;   // Частота звука Гц

// Компактная таблица Морзе (0 = точка, 1 = тире). 
// Старший бит определяет длину последовательности.
const uint8_t morseTable[] PROGMEM = {
    0b01000010, // A: .-    (длина 2, маска 010)
    0b10001000, // B: -...  (длина 4, маска 1000)
    0b10001010, // C: -.-.  (длина 4, маска 1010)
    0b01100100, // D: -..   (длина 3, маска 100)
    0b00100000, // E: .     (длина 1, маска 0)
    0b10000010, // F: ..-.  (длина 4, маска 0010)
    0b01100110, // G: --.   (длина 3, маска 110)
    0b10000000, // H: ....  (длина 4, маска 0000)
    0b01000000, // I: ..    (длина 2, маска 00)
    0b10000111, // J: .---  (длина 4, маска 0111)
    0b01100101, // K: -.-   (длина 3, маска 101)
    0b10000100, // L: .-..  (длина 4, маска 0100)
    0b01000011, // M: --    (длина 2, маска 11)
    0b01000010, // N: -.    (длина 2, маска 10)
    0b01100111, // O: ---   (длина 3, маска 111)
    0b10000110, // P: .--.  (длина 4, маска 0110)
    0b10001011, // Q: --.-  (длина 4, маска 1011)
    0b01100010, // R: .-.   (длина 3, маска 010)
    0b01100000, // S: ...   (длина 3, маска 000)
    0b00100001, // T: -     (длина 1, маска 1)
    0b01100001, // U: ..-   (длина 3, маска 001)
    0b10000001, // V: ...-  (длина 4, маска 0001)
    0b01100011, // W: .--   (длина 3, маска 011)
    0b10001001, // X: -..-  (длина 4, маска 1001)
    0b10001101, // Y: -.--  (длина 4, маска 1101)
    0b10001100  // Z: --..  (длина 4, маска 1100)
};

// Цифры от 0 до 9 (всегда 5 знаков)
const uint8_t morseNumTable[] PROGMEM = {
    0b00011111, // 0: -----
    0b00001111, // 1: .----
    0b00000111, // 2: ..---
    0b00000011, // 3: ...--
    0b00000001, // 4: ....-
    0b00000000, // 5: .....
    0b00010000, // 6: -....
    0b00011000, // 7: --...
    0b00011100, // 8: ---..
    0b00011110  // 9: ----.
};

void sendElement(uint8_t isDash) {
    digitalWrite(ledPin, HIGH);
    tone(tonePin, toneFreq);
    delay(isDash ? DASHLEN : DOTLEN);
    digitalWrite(ledPin, LOW);
    noTone(tonePin);
    delay(DOTLEN); // Межзнаковый интервал
}

void sendMsg(const char *str) {
    delay(500);
    while (*str) {
        char c = *str++;
        
        if (c == ' ') {
            delay(DOTLEN * 4); // Пауза между словами (всего 7 DOTLEN с учетом конца знака)
            continue;
        }
        
        if (c >= 'A' && c <= 'Z') {
            uint8_t data = pgm_read_byte(&morseTable[c - 'A']);
            uint8_t len = data >> 5; // Три старших бита — это длина
            for (int8_t bit = len - 1; bit >= 0; bit--) {
                sendElement((data >> bit) & 1);
            }
        } 
        else if (c >= '0' && c <= '9') {
            uint8_t data = pgm_read_byte(&morseNumTable[c - '0']);
            for (int8_t bit = 4; bit >= 0; bit--) {
                sendElement((data >> bit) & 1);
            }
        }
        delay(DOTLEN * 2); // Пауза между буквами (всего 3 DOTLEN)
    }
}

void setup() {
    pinMode(ledPin, OUTPUT);
    pinMode(txPin, OUTPUT); 
}

void loop() {
    digitalWrite(txPin, HIGH);
    delay(700); // txdelay
  
    sendMsg("R7HJ LN05XA");

    delay(20);
    digitalWrite(txPin, LOW);
    delay(PAUSE);
}
