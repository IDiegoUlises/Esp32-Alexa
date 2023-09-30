### Librerias
<img src="https://github.com/IDiegoUlises/Esp32-Alexa/blob/main/Imagenes/fauxmo.png" />

* Fauxmo

<img src="https://github.com/IDiegoUlises/Esp32-Alexa/blob/main/Imagenes/AsyncTCP.png" />

* AsyncTCP se debe instalar porque es una dependencia de la libreria anterior

### Paso 1: Ir a dispositivos en la aplicacion de Amazon
<img src="https://github.com/IDiegoUlises/Esp32-Alexa/blob/main/Imagenes/1.jpeg" width="350" height="650" />

### Paso 2: Agregar Dispositivo
<img src="https://github.com/IDiegoUlises/Esp32-Alexa/blob/main/Imagenes/2.jpeg" width="350" height="650" />

### Paso 3: Seleccionar Luz
<img src="https://github.com/IDiegoUlises/Esp32-Alexa/blob/main/Imagenes/3.jpeg" width="350" height="650" />

### Paso 4: Seleccionar marca otros
<img src="https://github.com/IDiegoUlises/Esp32-Alexa/blob/main/Imagenes/4.jpeg" width="350" height="650" />

### Paso 5: Seleccionar el logotipo WiFi
<img src="https://github.com/IDiegoUlises/Esp32-Alexa/blob/main/Imagenes/5.jpeg" width="350" height="650" />

### Paso 6: Seleccionar Detectar Dispositivos
<img src="https://github.com/IDiegoUlises/Esp32-Alexa/blob/main/Imagenes/6.jpeg" width="350" height="650" />

### Paso 7: Esperar
<img src="https://github.com/IDiegoUlises/Esp32-Alexa/blob/main/Imagenes/7.jpeg" width="350" height="650" />

### Paso 8: Seleccionar Configurar Dispositivo
<img src="https://github.com/IDiegoUlises/Esp32-Alexa/blob/main/Imagenes/8.jpeg" width="350" height="650" />

### Paso 9: Finalizar
<img src="https://github.com/IDiegoUlises/Esp32-Alexa/blob/main/Imagenes/9.jpeg" width="350" height="650" />


### Codigo nuevo experimental que funciona y falta poco para que sea produccion
```c++
#include <WiFi.h>
#include <fauxmoESP.h>

// Wifi Credenciales
const char *WIFI_SSID =  "Wifi Home";
const char *WIFI_PASSWORD  = "123456789";

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

  //Funcion que se ejecutara cuando se interactue con la voz, devuelve 3 valores que son; el nombre del dispositivo, el estado y un valor analogico en caso que exista
  fauxmo.onSetState([](unsigned char device_id, const char * device_name, bool state, unsigned char value)
  {
    //Strcmp hace una comparacion de dos const char* ya que es la unica manera de comparar el contenido, y si el usuario dice que se encienda la lampara se ejecutara lo siguiente
    if (strcmp(device_name, lampara) == 0  and state == 1)
    {
      digitalWrite(led, HIGH);
      Serial.println("Lampara Encendida");
    }

    //Hace una comparacion de dos const char*,para agregar mas dispositivos agregar mas else if, si el usuario dice que se apague la lampara se ejecutara lo siguiente
    else if (strcmp(device_name, lampara) == 0  and state == 0)
    {
      digitalWrite(led, LOW);
      Serial.println("Lampara Apagada");
    }

  });

}

void loop()
{
  fauxmo.handle();
}
```
