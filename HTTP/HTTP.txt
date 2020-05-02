#include <BearSSLHelpers.h>
#include <CertStoreBearSSL.h>
#include <ESP8266WiFi.h>
#include <ESP8266WiFiAP.h>
#include <ESP8266WiFiGeneric.h>
#include <ESP8266WiFiMulti.h>
#include <ESP8266WiFiScan.h>
#include <ESP8266WiFiSTA.h>
#include <ESP8266WiFiType.h>
#include <WiFiClient.h>
#include <WiFiClientSecure.h>
#include <WiFiClientSecureAxTLS.h>
#include <WiFiClientSecureBearSSL.h>
#include <WiFiServer.h>
#include <WiFiServerSecure.h>
#include <WiFiServerSecureAxTLS.h>
#include <WiFiServerSecureBearSSL.h>
#include <WiFiUdp.h>

#include <Adafruit_Sensor.h>

const char* ssid = "Anil Wadhwa";
const char* password = "twix@123";
//int ledPin1 = D4;
int switch1 = HIGH;

WiFiServer server(80);

void setup() {
  // put your setup code here, to run once:
  pinMode(LED_BUILTIN, OUTPUT);
  digitalWrite(LED_BUILTIN, switch1);

  Serial.begin(9600);
  Serial.print("Connecting to: ");
  Serial.println(ssid);

  WiFi.begin(ssid, password);

  while(WiFi.status() != WL_CONNECTED){
    delay(100);
    Serial.print(".");
  }

  Serial.println("");
  Serial.println("WiFi Connected");

  server.begin();
  Serial.println("Server connected");

  Serial.print("Use this URL to connect:");
  Serial.print("http://");
  Serial.print(WiFi.localIP());
  Serial.print("/");
}

void loop() {
  // put your main code here, to run repeatedly:
  WiFiClient client = server.available();
  if(!client){
    return;
  }

  Serial.println("New Client");
  while(!client.available()){
    delay(1);
  }

  String request = client.readStringUntil('\r');
  Serial.println(request);
  client.flush();

  if(request.indexOf("/LED=ON") != -1){
    digitalWrite(LED_BUILTIN, LOW);
    switch1 = LOW;
  }

  if(request.indexOf("/LED=OFF") != -1){
    digitalWrite(LED_BUILTIN, HIGH);
    switch1 = HIGH;
  }

  client.println("<html><body><h1>Button:");
  if (switch1 == LOW){
    client.print("ON");
  }
  if(switch1 == HIGH){
    client.print("OFF");
  }
  client.print("</h1>");
  client.println("<br/><a href='/LED=ON'><button>ON</button></a> ");
  client.println("<a href='/LED=OFF'><button>OFF</button></a> ");
  client.print("<br>");
  delay(1);
  Serial.println("Client disconnected");
  Serial.println("");
}
