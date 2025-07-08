#include <Wire.h>
#include <MPU6050.h>
#include <WiFi.h>
#include <BlynkSimpleEsp32.h>

#define BLYNK_TEMPLATE_ID "Your_Template_ID"
#define BLYNK_DEVICE_NAME "SmartPen"
#define BLYNK_AUTH_TOKEN "Your_Blynk_Auth_Token"

// WiFi Credentials
char ssid[] = "Your_WiFi_SSID";
char pass[] = "Your_WiFi_Password";

MPU6050 mpu;
const int pressureSensorPin = 34;  // Adjust based on your ESP32 board

void setup() {
    Serial.begin(115200);
    WiFi.begin(ssid, pass);
    Blynk.begin(BLYNK_AUTH_TOKEN, ssid, pass);
    
    Wire.begin();
    mpu.initialize();
    
    if (!mpu.testConnection()) {
        Serial.println("MPU6050 connection failed!");
        while (1);
    }
}

void loop() {
    int16_t ax, ay, az, gx, gy, gz;
    mpu.getMotion6(&ax, &ay, &az, &gx, &gy, &gz);
    int pressureValue = analogRead(pressureSensorPin);

    Serial.print("Accel: ");
    Serial.print(ax); Serial.print(",");
    Serial.print(ay); Serial.print(",");
    Serial.print(az); Serial.print(" | Gyro: ");
    Serial.print(gx); Serial.print(",");
    Serial.print(gy); Serial.print(",");
    Serial.print(gz); Serial.print(" | Pressure: ");
    Serial.println(pressureValue);

    // Detect concentration lapse (adjust thresholds as needed)
    if (abs(ax) < 3000 && abs(ay) < 3000 && abs(az) < 3000 && pressureValue < 300) {
        Serial.println("⚠️ Concentration lapse detected!");
        Blynk.logEvent("concentration_lapse", "Concentration lapse detected!");
        Blynk.virtualWrite(V1, "⚠️ Concentration lapse detected!");  // Update Blynk UI
    }

    Blynk.run();
    delay(500);
}
