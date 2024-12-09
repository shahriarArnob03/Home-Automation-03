#include <ESP8266WiFi.h>
#include <ESP8266WebServer.h>

// Wi-Fi credentials
const char* ssid = "T R I N I T Y"; 
const char* password = "Shihab007!2#4%";

// Pin assignments
const int light1Pin = 4;
const int light2Pin = 5;
const int buzzerPin = 14;
const int motionSensorPin = 16;
const int light3Pin = 12;
const int light4Pin = 13;

// States
bool light1State = false;
bool light2State = false;
bool buzzerState = false;
bool motionSensorEnabled = false;
bool light3State = false;
bool light4State = false;

unsigned long lastMotionTime = 0; // Tracks last motion detection
const unsigned long motionTimeout = 15000; // 15 seconds

ESP8266WebServer server(80);

void setup() {
  Serial.begin(115200);

  // Pin setup
  pinMode(light1Pin, OUTPUT);
  pinMode(light2Pin, OUTPUT);
  pinMode(buzzerPin, OUTPUT);
  pinMode(motionSensorPin, INPUT);
  pinMode(light3Pin, OUTPUT);
  pinMode(light4Pin, OUTPUT);

  digitalWrite(light1Pin, LOW);
  digitalWrite(light2Pin, LOW);
  digitalWrite(buzzerPin, LOW);
  digitalWrite(light3Pin, LOW);
  digitalWrite(light4Pin, LOW);

  // Connect to Wi-Fi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nWi-Fi connected.");
  Serial.println(WiFi.localIP());

  // Web server routes
  server.on("/", handleRoot);
  server.on("/toggleLight1", toggleLight1);
  server.on("/toggleLight2", toggleLight2);
  server.on("/toggleBuzzer", toggleBuzzer);
  server.on("/toggleMotionSensor", toggleMotionSensor);
  server.on("/toggleLight3", toggleLight3);
  server.on("/toggleLight4", toggleLight4);

  server.begin();
  Serial.println("Web server started.");
}

void loop() {
  server.handleClient();

  // Motion sensor logic for Room 1
  if (motionSensorEnabled) {
    if (digitalRead(motionSensorPin) == HIGH) {
      Serial.println("Motion High");
      
      // Motion detected
      lastMotionTime = millis();
      digitalWrite(light1Pin, HIGH);
      digitalWrite(light2Pin, HIGH);

      if (!buzzerState) {
         Serial.println("Buzzer High");
        digitalWrite(buzzerPin, HIGH);
        delay(5000);
        digitalWrite(buzzerPin, LOW);
      }
    } else if (millis() - lastMotionTime > motionTimeout) {
      // No motion detected for the timeout duration
       Serial.println("No Motion Turning Lights Off");
      digitalWrite(light1Pin, LOW);
      digitalWrite(light2Pin, LOW);
    }
  }
}

// Web page content
void handleRoot() {
  server.send(200, "text/html", R"rawliteral(
 
    <!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Smart Home Control System</title>
  <style>
  body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    background-color: #070313;
    color: #ffffff;
  }
  header {
    background: #1f1f1f;
    color: #ff7c11;
    text-align: center;
    padding: 1em;
  }
  main {
    padding: 20px;
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  section {
    width: 100%;
    margin: 20px;
    background: #1e1e1e;
    border-radius: 5px;
    padding: 20px;
  }
  .switch-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
    gap: 20px;
  }
  .switch-card {
    background-color: #2a2a2a;
    border-radius: 10px;
    padding: 15px;
    text-align: center;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.2);
    transition: transform 0.3s;
  }
  .switch-card:hover {
    transform: translateY(-5px);
  }
  button {
    padding: 10px;
    background: #09327e;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    margin-top: 10px;
    width: 100%;
  }
  button:hover {
    background: #45a049;
  }
  span {
    display: block;
    margin-top: 10px;
    font-size: 14px;
    color: #aaa;
  }
  </style>
</head>
<body>
  <header>
    <h1>Smart Home Control System</h1>
  </header>
  <main>
    <section>
      <h2>Room 1</h2>
      <div class="switch-grid">
        <div class="switch-card">
          <h3>Light 1</h3>
          <button onclick="toggleLight('light1')">Toggle Light 1</button>
          <span id="light1-status">Off</span>
        </div>
        <div class="switch-card">
          <h3>Light 2</h3>
          <button onclick="toggleLight('light2')">Toggle Light 2</button>
          <span id="light2-status">Off</span>
        </div>
        <div class="switch-card">
          <h3>Buzzer</h3>
          <button onclick="toggleBuzzer()">Toggle Buzzer</button>
          <span id="buzzer-status">Off</span>
        </div>
        <div class="switch-card">
          <h3>Motion Sensor</h3>
          <button onclick="toggleMotionSensor()">Toggle Motion Sensor</button>
          <span id="motion-sensor-status">Inactive</span>
        </div>
      </div>
    </section>

    <section>
      <h2>Room 2</h2>
      <div class="switch-grid">
        <div class="switch-card">
          <h3>Light 1</h3>
          <button onclick="toggleLight('room2-light1')">Toggle Light 1</button>
          <span id="room2-light1-status">Off</span>
        </div>
        <div class="switch-card">
          <h3>Light 2</h3>
          <button onclick="toggleLight('room2-light2')">Toggle Light 2</button>
          <span id="room2-light2-status">Off</span>
        </div>
      </div>
    </section>
  </main>

  <script>
    function toggleLight(lightId) {
      fetch(`/${lightId}`).then(() => {
        const status = document.getElementById(`${lightId}-status`);
        status.textContent = status.textContent === "Off" ? "On" : "Off";
      });
    }

    function toggleBuzzer() {
      fetch("/toggleBuzzer").then(() => {
        const status = document.getElementById("buzzer-status");
        status.textContent = status.textContent === "Off" ? "On" : "Off";
      });
    }

    function toggleMotionSensor() {
      fetch("/toggleMotionSensor").then(() => {
        const status = document.getElementById("motion-sensor-status");
        status.textContent = status.textContent === "Inactive" ? "Active" : "Inactive";
      });
    }
  </script>
</body>
</html>

  )rawliteral");
}



void toggleLight1() {
    light1State = !light1State;
    digitalWrite(light1Pin, light1State ? HIGH : LOW);
     Serial.println("Light1 Toggled");
  
  server.send(200, "text/plain", "OK");
}

void toggleLight2() {
    light2State = !light2State;
    digitalWrite(light2Pin, light2State ? HIGH : LOW);
    Serial.println("Light2 Toggled");
  server.send(200, "text/plain", "OK");
}

void toggleBuzzer() {
  buzzerState = !buzzerState;
  Serial.println("Buzzer Toggled");
  server.send(200, "text/plain", "OK");
}

void toggleMotionSensor() {
  motionSensorEnabled = !motionSensorEnabled;

  if (!motionSensorEnabled) {
    // Reset lights to manual mode
    digitalWrite(light1Pin, light1State ? HIGH : LOW);
    digitalWrite(light2Pin, light2State ? HIGH : LOW);
  }
  server.send(200, "text/plain", "OK");
}

void toggleLight3() {
  light3State = !light3State;
  digitalWrite(light3Pin, light3State ? HIGH : LOW);
  Serial.println("Light3 Toggled");
  server.send(200, "text/plain", "OK");
}

void toggleLight4() {
  light4State = !light4State;
  digitalWrite(light4Pin, light4State ? HIGH : LOW);
  Serial.println("Light4 Toggled");
  server.send(200, "text/plain", "OK");
}
