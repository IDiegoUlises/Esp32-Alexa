# Esp32-Alexa

### Librerias
<img src="https://github.com/IDiegoUlises/Esp32-Alexa/blob/main/Imagenes/fauxmo.png" />

* Fauxmo

<img src="https://github.com/IDiegoUlises/Esp32-Alexa/blob/main/Imagenes/AsyncTCP.png" />

* AsyncTCP

  


### Codigo nuevo experimental que funciona y falta poco para que sea produccion
```c++
#include <WiFi.h>
#include <fauxmoESP.h>

// Wifi Credenciales
const char *WIFI_SSID =  "Wifi Home";
const char *WIFI_PASSWORD  = "S4m4sw3n0s";

//Led
int led = 2;

//Nombre para interactuar con la voz
const char *lampara = "lampara";

//Objeto para despues inicializar
fauxmoESP fauxmo;

void setup()
{
  //Incializa el puerto serial
  Serial.begin(115200);

  //Declaracion del led
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

  //Agrega el dispositivo
  fauxmo.addDevice(lampara); //Si existen mas comandos de voz agregar mas dispositivos

  fauxmo.setPort(80); //Requerido para los dispositivos de generacion 3
  fauxmo.enable(true);

  fauxmo.onSetState([](unsigned char device_id, const char * device_name, bool state, unsigned char value)
  {
    Serial.printf("[MAIN] Device #%d (%s) state: %s value: %d\n", device_id, device_name, state ? "ON" : "OFF", value);

    //Strcmp hace una comparacion de dos const char* ya que es la unica manera de comparar el contenido que almacena y ademas luego compara el estado
    if (strcmp(device_name, lampara) == 0  and state == 1)
    {
      digitalWrite(led, HIGH);
      Serial.println("Lampara ENCENDIDA");
    }

    //Hace una comparacion de dos const char* y para agregar mas dispositivos agregar mas else if
    else if (strcmp(device_name, lampara) == 0  and state == 0)
    {
      digitalWrite(led, LOW);
      Serial.println("Lampara APAGADA");
    }

  });

}

void loop()
{
  fauxmo.handle();
}
```
