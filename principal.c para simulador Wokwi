#include <Arduino.h>
#include "pico/stdlib.h"
#include "pico/time.h"

const uint LED_PIN_BLUE = 11;
const uint LED_PIN_RED = 12;
const uint LED_PIN_GREEN = 13;
const uint BUTTON_PIN = 5;

bool led_active = false;
bool button_pressed = false;
const uint32_t debounce_delay = 50; // Ajuste o tempo de debounce conforme necessário

// Funções de callback para alternar entre os estados dos LEDs.
int64_t turn_off_callback(alarm_id_t id, void *user_data) {
    digitalWrite(LED_PIN_BLUE, LOW);
    digitalWrite(LED_PIN_RED, LOW);
    digitalWrite(LED_PIN_GREEN, LOW);
    led_active = false;
    return 0;
}

int64_t turn_blue_callback(alarm_id_t id, void *user_data) {
    digitalWrite(LED_PIN_BLUE, HIGH);
    digitalWrite(LED_PIN_RED, LOW);
    digitalWrite(LED_PIN_GREEN, LOW);
    add_alarm_in_ms(3000, turn_red_callback, NULL, false);
    return 0;
}

int64_t turn_red_callback(alarm_id_t id, void *user_data) {
    digitalWrite(LED_PIN_BLUE, LOW);
    digitalWrite(LED_PIN_RED, HIGH);
    digitalWrite(LED_PIN_GREEN, LOW);
    add_alarm_in_ms(3000, turn_green_callback, NULL, false);
    return 0;
}

int64_t turn_green_callback(alarm_id_t id, void *user_data) {
    digitalWrite(LED_PIN_BLUE, LOW);
    digitalWrite(LED_PIN_RED, LOW);
    digitalWrite(LED_PIN_GREEN, HIGH);
    add_alarm_in_ms(3000, turn_off_callback, NULL, false);
    return 0;
}

void init_gpio() {
    pinMode(LED_PIN_BLUE, OUTPUT);
    pinMode(LED_PIN_RED, OUTPUT);
    pinMode(LED_PIN_GREEN, OUTPUT);
    pinMode(BUTTON_PIN, INPUT_PULLUP);
}

bool debounce() {
    static uint32_t last_state = HIGH;
    static uint32_t last_debounce_time = 0;
    uint32_t current_state = digitalRead(BUTTON_PIN);

    if (current_state != last_state) {
        last_debounce_time = millis();
    }

    if ((millis() - last_debounce_time) > debounce_delay) {
        if (current_state == LOW && !button_pressed) {
            button_pressed = true;
            return true;
        } else if (current_state == HIGH && button_pressed) {
            button_pressed = false;
        }
    }

    last_state = current_state;
    return false;
}

void setup() {
    Serial.begin(9600);
    init_gpio();
}

void loop() {
    if (debounce() && !led_active) {
        digitalWrite(LED_PIN_BLUE, LOW);
        digitalWrite(LED_PIN_RED, LOW);
        digitalWrite(LED_PIN_GREEN, LOW);
        
        led_active = true;
        add_alarm_in_ms(100, turn_blue_callback, NULL, false);
    }
    delay(10);
}
