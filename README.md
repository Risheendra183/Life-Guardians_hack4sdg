# code-Life Guardians


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

