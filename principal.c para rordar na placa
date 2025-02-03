#include <stdio.h>
#include "pico/stdlib.h"
#include "pico/time.h"

const uint LED_PIN_BLUE = 12;
const uint LED_PIN_RED = 13;
const uint LED_PIN_GREEN = 11;
const uint BUTTON_PIN = 5;

bool led_active = false;
bool button_pressed = false;
uint32_t last_press_time = 0;
const uint32_t debounce_delay = 50; // Ajuste o tempo de debounce conforme necessário

// Declaração de encaminhamento
int64_t turn_blue_callback(alarm_id_t id, void *user_data);
int64_t turn_red_callback(alarm_id_t id, void *user_data);
int64_t turn_green_callback(alarm_id_t id, void *user_data);

// Funções de callback para alternar entre os estados dos LEDs.
int64_t turn_off_callback(alarm_id_t id, void *user_data) {
    gpio_put(LED_PIN_BLUE, false);
    gpio_put(LED_PIN_RED, false);
    gpio_put(LED_PIN_GREEN, false);
    led_active = false;
    return 0;
}

int64_t turn_blue_callback(alarm_id_t id, void *user_data) {
    gpio_put(LED_PIN_BLUE, true);
    gpio_put(LED_PIN_RED, false);
    gpio_put(LED_PIN_GREEN, false);
    add_alarm_in_ms(3000, turn_red_callback, NULL, false);
    return 0;
}

int64_t turn_red_callback(alarm_id_t id, void *user_data) {
    gpio_put(LED_PIN_BLUE, false);
    gpio_put(LED_PIN_RED, true);
    gpio_put(LED_PIN_GREEN, false);
    add_alarm_in_ms(3000, turn_green_callback, NULL, false);
    return 0;
}

int64_t turn_green_callback(alarm_id_t id, void *user_data) {
    gpio_put(LED_PIN_BLUE, false);
    gpio_put(LED_PIN_RED, false);
    gpio_put(LED_PIN_GREEN, true);
    add_alarm_in_ms(3000, turn_off_callback, NULL, false);
    return 0;
}

void init_gpio() {
    gpio_init(LED_PIN_BLUE);
    gpio_set_dir(LED_PIN_BLUE, GPIO_OUT);

    gpio_init(LED_PIN_RED);
    gpio_set_dir(LED_PIN_RED, GPIO_OUT);

    gpio_init(LED_PIN_GREEN);
    gpio_set_dir(LED_PIN_GREEN, GPIO_OUT);

    gpio_init(BUTTON_PIN);
    gpio_set_dir(BUTTON_PIN, GPIO_IN);
    gpio_pull_up(BUTTON_PIN);
}

bool debounce() {
    static uint32_t last_state = 1;
    static uint32_t last_debounce_time = 0;
    uint32_t current_state = gpio_get(BUTTON_PIN);

    if (current_state != last_state) {
        last_debounce_time = to_ms_since_boot(get_absolute_time());
    }

    if ((to_ms_since_boot(get_absolute_time()) - last_debounce_time) > debounce_delay) {
        if (current_state == 0 && !button_pressed) {
            button_pressed = true;
            return true;
        } else if (current_state == 1 && button_pressed) {
            button_pressed = false;
        }
    }

    last_state = current_state;
    return false;
}

int main() {
    stdio_init_all();
    init_gpio();

    while (true) {
        if (debounce() && !led_active) {
            gpio_put(LED_PIN_BLUE, false);
            gpio_put(LED_PIN_RED,  false);
            gpio_put(LED_PIN_GREEN, false);
            
            led_active = true;
            add_alarm_in_ms(100, turn_blue_callback, NULL, false);
        }
        sleep_ms(10);
    }
    return 0;
}
