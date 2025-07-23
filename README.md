# 🛡️ STM32F405RGT6-Based Wearable Health Monitoring & Authentication System

A wearable real-time health monitoring system using the **STM32F405RG (Cortex-M4)** microcontroller. The system tracks **heart rate**, **SpO₂**, and **body temperature**, and includes secure **user authentication** via a 4-digit code stored in SPI EEPROM. It features live display on 7-segment LEDs, LCD output, buzzer alerts, health status indication via LED bar graph, and UART data transmission to a PC.

---

## 🎯 Objective

- Authenticate the user securely before use
- Monitor vital signs (HR, SpO₂, Temperature)
- Detect physical activity levels
- Display and transmit all health metrics in real time
- Trigger alarms for abnormal readings

---

## 🧾 Features

### 🔐 1. Secure User Authentication
- LCD shows `"WELCOME"` on power-up
- Press `*` on 4x4 keypad to enter 4-digit code
- Password is verified from **SPI EEPROM**
- ✅ If correct:
  - LCD → "Access Granted"
  - Green LED ON
  - Main menu displayed
- ❌ If incorrect:
  - LCD → "Access Denied. Try Again!"
  - Red LED ON

### 📊 2. Health Monitoring
- **Heart Rate (HR)** and **SpO₂**
  - Measured using **MAX30102**
  - Displayed on two 4-digit **7-segment LED displays**
  - Buzzer alerts for abnormal readings
- **Temperature**
  - Measured via **IR Temperature Sensor**
  - Displayed on **16x2 LCD**
- **LED Bar Graph**
  - Indicates health state: Relaxed / Normal / Intense
- **Buzzer**
  - Triggers if:
    - HR < 60 or > 100 bpm
    - SpO₂ < 95%
    - Temp < 36.5°C or > 37.5°C
- **UART**
  - Sends data to PC in this format:
    ```
    Pulse Rate: 78 bpm
    SpO₂: 97 %
    Temp: 36.7 °C
    ```

---

## 🧰 Hardware Required

| Component                           | Quantity | Notes                               |
|------------------------------------|----------|-------------------------------------|
| STM32F405RG                        | 1        | Cortex-M4 based MCU                 |
| MAX30102                           | 1        | I2C interface                       |
| IR Temp Sensor (MLX90614 or similar) | 1        | I2C based or analog                 |
| 4-Digit 7-Segment Displays         | 2        | One for HR, one for SpO₂ (TM1637 or shift reg) |
| 16x2 LCD (HD44780)                 | 1        | 4-bit or I2C mode                   |
| SPI EEPROM (25LC256)              | 1        | For password storage                |
| 4x4 Matrix Keypad                  | 1        | For user input                      |
| LED Bar Graph Display              | 1        | GPIO-based bar display              |
| Buzzer                             | 1        | Active buzzer                       |
| Buck Converter                     | 1        | For 3.3V/5V power supply            |

---

## 🧪 Test Cases

### ✅ Normal Operation
1. Startup: LCD → `"WELCOME"`
2. Press `*` to enter password
3. Correct password: Access Granted → Menu → Monitoring starts
4. Place finger on MAX30102 → HR & SpO₂ readings on display
5. Temperature displayed on LCD
6. UART Output on PC

### ⚠️ Alerts
- HR > 100 or < 60 → Buzzer ON
- SpO₂ < 95% → Buzzer ON
- Temp < 36.5°C or > 37.5°C → Buzzer ON
- LED bar changes color/state based on variation

---

---

## ⚙️ UART Configuration

| Parameter   | Value      |
|-------------|------------|
| Baud Rate   | 9600       |
| Data Bits   | 8          |
| Stop Bits   | 1          |
| Parity      | None       |
| Flow Control| None       |

---

## 💻 Software Tools

- [STM32CubeIDE](https://www.st.com/en/development-tools/stm32cubeide.html)
- PuTTY / TeraTerm / RealTerm (for UART output)
- HAL Libraries for:
  - I2C, SPI, USART
  - GPIO, TIM, EXTI


## 🛡️ EEPROM Password Sample Code

```c
void save_password_to_eeprom(uint8_t pass[4]) {
    for (int i = 0; i < 4; i++) {
        EEPROM_Write(0x0000 + i, pass[i]);  // 0x0000 is starting address
    }
}

bool verify_password(uint8_t entered[4]) {
    uint8_t stored[4];
    for (int i = 0; i < 4; i++) {
        stored[i] = EEPROM_Read(0x0000 + i);
        if (entered[i] != stored[i])
            return false;
    }
    return true;
}

Name:Salman Mjeeed

BOSCH

