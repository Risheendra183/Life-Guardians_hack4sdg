# code-Life Guardians

Here’s a README file for the Arduino code you provided:

---

# Emergency Alert System with GSM Module

This project implements an emergency alert system using an Arduino, a GSM module, a button, and a buzzer. When the button is pressed, the system sends an SMS alert and makes a call to a predefined phone number, indicating an emergency situation. The buzzer sounds when the alert is triggered to indicate that the action has been activated.

## Components Used
- **Arduino**: Any Arduino board (such as Uno or Nano)
- **GSM Module**: For sending SMS and making calls
- **Button**: To activate the alert
- **Buzzer**: For sound notification when the alert is triggered
- **Wiring**: Jumper wires, breadboard (optional)

## Circuit Setup
1. **Connect the GSM Module**:
   - Connect the `RX` pin of the GSM module to pin `4` on the Arduino.
   - Connect the `TX` pin of the GSM module to pin `5` on the Arduino.
   - Make sure to power the GSM module according to its specifications.

2. **Connect the Button**:
   - Connect one terminal of the button to pin `7` on the Arduino.
   - Connect the other terminal to `GND`.

3. **Connect the Buzzer**:
   - Connect the positive pin of the buzzer to pin `3` on the Arduino.
   - Connect the negative pin of the buzzer to `GND`.

## Code Explanation

- **SoftwareSerial Setup**: The code uses the `SoftwareSerial` library to set up communication with the GSM module, specifying pins `4` and `5` as RX and TX respectively.
- **Alert Activation**: When the button is pressed, it triggers the buzzer, sends an SMS, and makes a call to the predefined phone number.
- **Functions**:
  - `sendSMS()`: Sends an SMS with the specified alert message to the predefined number.
  - `makeCall()`: Makes a phone call to the predefined number for a set duration and then hangs up.
  - `makeCallsAndSendSMS()`: Activates the alert by sending an SMS and making a call.

## Code

```cpp
#include <SoftwareSerial.h>

SoftwareSerial mySerial(4, 5); // RX, TX

const char phoneNumber1[] = "+918919378556"; // Replace with the phone number you want to send SMS and call
const char defaultMessage[] = "Emergency Alert! Unusual Activity detected in Room no 118"; // Default message

const int BUTTON_PIN = 7; // Arduino pin connected to button's pin
const int BUZZER_PIN = 3; // Arduino pin connected to Buzzer's pin

void setup() {
  mySerial.begin(9600);   // Set the baud rate for the GSM module
  Serial.begin(9600);    // Set the baud rate for Serial Monitor
  pinMode(BUTTON_PIN, INPUT_PULLUP); // Set Arduino pin to input pull-up mode
  pinMode(BUZZER_PIN, OUTPUT);       // Set Arduino pin to output mode
  digitalWrite(BUZZER_PIN, LOW);     // Ensure buzzer is off initially
  delay(100);
}

void loop() {
  int buttonState = digitalRead(BUTTON_PIN); // Read button state

  if (buttonState == LOW) {
    // Button is pressed
    Serial.println("The button is being pressed");
    
    // Turn on the buzzer
    digitalWrite(BUZZER_PIN, HIGH);
    
    // Perform the actions: send SMS and make call to the number
    makeCallsAndSendSMS();
    
    // Turn off the buzzer after both actions
    digitalWrite(BUZZER_PIN, LOW);
    
    // Wait to avoid continuous activation
    delay(10000);
    
  } else {
    // Button is not pressed
    Serial.println("The button is unpressed");
    digitalWrite(BUZZER_PIN, LOW);  // Ensure buzzer is off
  }

  // Continuously read data from GSM module and print it to Serial Monitor
  if (mySerial.available() > 0) {
    Serial.write(mySerial.read());
  }
}

void sendSMS(const char* phoneNumber, const char* message) {
  mySerial.println("AT+CMGF=1");    // Set GSM module to text mode
  delay(1000);  // Wait for the command to be processed
  mySerial.print("AT+CMGS=\"");
  mySerial.print(phoneNumber);
  mySerial.println("\"");
  delay(1000);
  mySerial.println(message); // SMS text
  delay(100);
  mySerial.write(26); // ASCII code for CTRL+Z to send the SMS
  delay(1000);
}

void makeCall(const char* phoneNumber) {
  Serial.println("Attempting to make a call..."); // Debug message
  mySerial.print("ATD");
  mySerial.print(phoneNumber);
  mySerial.println(";");
  
  delay(1000); // Short delay before checking call status
  
  if (mySerial.available()) {
    Serial.print("Call response: ");
    while (mySerial.available()) {
      Serial.write(mySerial.read());
    }
  } else {
    Serial.println("No response from GSM module during call attempt.");
  }

  delay(30000); // Call duration (30 seconds)
  
  Serial.println("Hanging up the call...");
  mySerial.println("ATH"); // Hang up the call
  delay(1000);
}

void makeCallsAndSendSMS() {
  sendSMS(phoneNumber1, defaultMessage); // Send default SMS to the number
  delay(3000); // Delay to ensure SMS is sent before making a call
  
  // Make the first call
  makeCall(phoneNumber1);
}
```

## Usage
1. **Upload the Code**: Upload the provided code to your Arduino board.
2. **Power the GSM Module**: Ensure your GSM module is powered on and has network access.
3. **Press the Button**: When the button is pressed, the system sends an SMS alert and makes a call to the predefined phone number, while the buzzer sounds as a notification.
4. **Monitor Output**: Use the Serial Monitor to view status messages and debug information.

## Notes
- **Adjust Delay**: The delay values for sending SMS and making calls can be adjusted as needed.
- **Modify Message and Number**: Update the `phoneNumber1` and `defaultMessage` variables as per your requirements.

--- 

This README provides a comprehensive guide for setting up and using your emergency alert system. Let me know if there are any other details you’d like to include.
