# Esp32-Alexa

* Descargar la libreria fauxmo

### Codigo original del creador
```c++
#include <fauxmoESP.h>

fauxmoESP fauxmo;

void setup() {

    Serial.begin(115200);

    ... connect to wifi ...

    fauxmo.addDevice("light one");
    fauxmo.addDevice("light two");
    fauxmo.addDevice("light three");
    fauxmo.addDevice("light four");

    fauxmo.setPort(80); // required for gen3 devices
    fauxmo.enable(true);

    fauxmo.onSetState([](unsigned char device_id, const char * device_name, bool state, unsigned char value) {
        Serial.printf("[MAIN] Device #%d (%s) state: %s value: %d\n", device_id, device_name, state ? "ON" : "OFF", value);
    });

}

void loop() {
    fauxmo.handle();
}
```

### Codigo brevemente modificado QUE FUNCIONA
```c++
#include <fauxmoESP.h>

fauxmoESP fauxmo;

#define lampara "lampara"

// Wifi Credenciales
#define WIFI_SSID "Wifi Home"
#define WIFI_PASSWORD "S4m4sw3n0s"

int led = 2;

void setup() {

  Serial.begin(115200);

  pinMode(led, OUTPUT);

  //Conexion Wifi
  //Se conecta la a red wifi, en caso de no conectarse quedara en un bucle infinito
  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
  while (WiFi.status() != WL_CONNECTED)
  {
    Serial.print(".");
    delay(500);
  }

  //Imprime la direccion IP
  Serial.print("\nWiFi connected. IP address: ");
  Serial.println(WiFi.localIP());
  /////Conexion wifi

  fauxmo.addDevice(lampara);

  fauxmo.setPort(80); // required for gen3 devices
  fauxmo.enable(true);

  fauxmo.onSetState([](unsigned char device_id, const char * device_name, bool state, unsigned char value) {
    Serial.printf("[MAIN] Device #%d (%s) state: %s value: %d\n", device_id, device_name, state ? "ON" : "OFF", value);

    if ( (strcmp(device_name, lampara) == 0) ) 
    {
      Serial.println("lampara 1 switched by Alexa");
    
      if (state) 
      {
        digitalWrite(led, HIGH);
        Serial.println("Lampara Encendida");
        
      } 
      else 
      {
        digitalWrite(led, LOW);
        Serial.println("Lampara APAGADA");
      }
      }//Cerrar
      
    });

  }

  void loop() {
    fauxmo.handle();
  }
```

### Codigo nuevo experimental no probadado
```c++
#include <fauxmoESP.h>

fauxmoESP fauxmo;

#define lampara "lampara"

// Wifi Credenciales
#define WIFI_SSID "Wifi Home"
#define WIFI_PASSWORD "S4m4sw3n0s"

int led = 2;

void setup() {

  Serial.begin(115200);

  pinMode(led, OUTPUT);

  //Conexion Wifi
  //Se conecta la a red wifi, en caso de no conectarse quedara en un bucle infinito
  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
  while (WiFi.status() != WL_CONNECTED)
  {
    Serial.print(".");
    delay(500);
  }

  //Imprime la direccion IP
  Serial.print("\nWiFi connected. IP address: ");
  Serial.println(WiFi.localIP());
  /////Conexion wifi

  fauxmo.addDevice(lampara);

  fauxmo.setPort(80); // required for gen3 devices
  fauxmo.enable(true);

  fauxmo.onSetState([](unsigned char device_id, const char * device_name, bool state, unsigned char value) {
    Serial.printf("[MAIN] Device #%d (%s) state: %s value: %d\n", device_id, device_name, state ? "ON" : "OFF", value);


    if (device_name == lampara and estate == 0)
    {
      digitalWrite(led, LOW);
      Serial.begin("Lampara apagada");
    }

    else if (device_name == lampara and estate == 1)
    {
      digitalWrite(led, HIGH);
      Serial.begin("Lampara Encendida");
    }

    /*
        if ( (strcmp(device_name, lampara) == 0) )
        {
          Serial.println("lampara 1 switched by Alexa");

          if (state)
          {
            digitalWrite(led, HIGH);
            Serial.println("Lampara Encendida");

          }
          else
          {
            digitalWrite(led, LOW);
            Serial.println("Lampara APAGADA");
          }
          }//Cerrar
    */
  });

}

void loop() {
  fauxmo.handle();
}
```
