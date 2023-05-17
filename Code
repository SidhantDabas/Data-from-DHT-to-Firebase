#include <Firebase_ESP_Client.h>
#include <addons/TokenHelper.h>
#include <addons/RTDBHelper.h>

#include <ESP8266WiFi.h>
//#include <FirebaseESP8266.h>
#include <DHT.h>

#define WIFI_SSID "Dabas 2"
#define WIFI_PASSWORD "future123"

#define API_KEY "AIzaSyALJGCuW_NuYrox6yXcdmAUlNq-t7LoGEE"
#define DATABASE_URL "https://predict-temp-and-humidity-default-rtdb.asia-southeast1.firebasedatabase.app/"
#define USER_EMAIL "sidhant.dabas177@gmail.com"
#define USER_PASSWORD "Club@123"

#define DHTPIN D4
#define DHTTYPE DHT11

DHT dht(DHTPIN, DHTTYPE);
FirebaseData fbdo;
FirebaseAuth auth;
FirebaseConfig config;

unsigned long sendDataPrevMillis = 0;
unsigned long count = 0;
const long interval = 10000;  // Send data every 10 seconds

void setup() {
  Serial.begin(115200);//setup nodemcu

  dht.begin();//setup dht

  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);//setup wifi
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.println("Connecting to WiFi...");
  }
  Serial.print("Connected with IP: ");
  Serial.println(WiFi.localIP());
  Serial.println();

  Serial.printf("Firebase Client v%s\n\n", FIREBASE_CLIENT_VERSION);//setup firebase
  config.api_key = API_KEY;
  auth.user.email = USER_EMAIL;
  auth.user.password = USER_PASSWORD;
  config.database_url = DATABASE_URL;
  config.token_status_callback = tokenStatusCallback; // see addons/TokenHelper.h
  fbdo.setBSSLBufferSize(2048 /* Rx buffer size in bytes from 512 - 16384 */, 2048 /* Tx buffer size in bytes from 512 - 16384 */);
  Firebase.begin(&config, &auth);
}

void loop() {
  unsigned long currentMillis = millis();
  if (millis() - sendDataPrevMillis > 15000 || sendDataPrevMillis == 0) {
    sendDataPrevMillis = millis();
    count++;
    float temperature = dht.readTemperature(); //read temperature
    float humidity = dht.readHumidity();// read humidity

    if (isnan(temperature) || isnan(humidity)) { //to check whether sensor is working
      Serial.println("Failed to read from DHT sensor!");
      return;
    }
    String path1 = "5/18/0200/Rain/Humidity/"+String(count);
    String path2 = "5/18/0200/Rain/Temperature/"+String(count);

    if (Firebase.RTDB.setFloat(&fbdo, path1, humidity)) {//send humidity
      Serial.println("Humidity: ");
      Serial.println(humidity);
      Serial.println("Data sent to Firebase at "+ fbdo.dataPath());//send value to firebase
      Serial.println("(" + fbdo.dataType() + ")");
    } else {
      Serial.println("Error sending humidity data to Firebase." + fbdo.errorReason());
    }

    if (Firebase.RTDB.setFloat(&fbdo, path2, temperature)) {//send temperature
      Serial.println("Temperature: ");
      Serial.println(temperature);
      Serial.println("Data sent to Firebase at "+ fbdo.dataPath());//send value to firebase
      Serial.println("(" + fbdo.dataType() + ")");
    } else {
      Serial.println("Error sending temperature data to Firebase." + fbdo.errorReason());
    }
  }
  delay(32000);
}
