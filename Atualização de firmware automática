#include <WiFi.h>
#include <HTTPClient.h>
#include <Update.h>
#include "FS.h"

const char* ssid = "INTELBRAS - JOAO";
const char* password = "jps510123";
const char* githubRepoURL = "https://github.com/PedroKutski/ESP32-IOT/blob/145da62e33e7951e794b26fcc2084dd888b00728/firmware%20(1).bin";
const char* versionURL = "https://github.com/PedroKutski/ESP32-IOT/blob/e353bf770b23400d3bd7219542393a3dc897c137/test.txt";

String currentVersion = "1.0"; // Versão atual do firmware

void checkForUpdates();    // Declaração antecipada
void updateFirmware();    // Declaração antecipada

void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  
  Serial.println("Connected to WiFi");

  checkForUpdates();
}

void loop() {
  // Nada para fazer no loop neste exemplo
}

void checkForUpdates() {
  HTTPClient http;
  String newVersion;
  
  // Verificar a versão mais recente
  http.begin(versionURL);
  int httpCode = http.GET();
  
  if (httpCode == HTTP_CODE_OK) {
    newVersion = http.getString();
  } else {
    Serial.println("Failed to check version.");
    http.end();
    return;
  }
  
  http.end();
  
  if (newVersion != currentVersion) {
    Serial.println("New version available. Updating...");
    updateFirmware();
  } else {
    Serial.println("No update needed.");
  }
}

void updateFirmware() {
  HTTPClient http;
  
  http.begin(githubRepoURL);
  int httpCode = http.GET();
  
  if (httpCode == HTTP_CODE_OK) {
    WiFiClient* stream = http.getStreamPtr();
    if (Update.begin()) {
      size_t written = Update.writeStream(*stream);
      if (written == http.getSize()) {
        if (Update.end(true)) {
          Serial.println("Update successfully completed. Rebooting...");
          ESP.restart();
        } else {
          Update.printError(Serial);
        }
      } else {
        Serial.println("Update not fully written.");
      }
    } else {
      Update.printError(Serial);
    }
  } else {
    Serial.println("Failed to download firmware.");
  }
  
  http.end();
}
