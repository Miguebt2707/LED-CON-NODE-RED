#  LED CON NODE-RED

EN ESTA PRACTICA LO QUE SE HIZO FUE HACER UNA VINCULACION ENTRE UN PULSADOR PARA PODER PRENDER UN FOCO LED PERO DESDE LUEGO TENEMOS QUE MANDARLO AL NODE-RED, ASI QUE SE OCUPO UNA TARJETA ESP32 A LA CUAL SE LE HIZO UNA CONEXION CON UN RELAY MODULE DONDE POSTERIORMENTE EN EL "NODE-RED" SE UTILIZO UN "SWITCH" AL CUAL A ESE SWITCH SE LE AGREGO UN GRUPO PARA QUE EL BOTON FUERA INDEPENDIENTE DE LOS TRABAJOS REALIZADOS ANTERIORMENTE, PARA ESO SE LE HIZO UNA CONFIGURACION LA CUAL TUVO QUE SER NUMERICA EN AMBAS LAS CUALES UNA DEBIA TENER "1" Y "0", EN EL mqtt LE ASIGNAMOS UN NOMBRE PARA PODER IDENTIFICARLO A LO QUE TAMBIEN SE LE AGREGO UNA DIRECCION NUEVA DE UN SERVIDOR POPR QUE LA DIRECCION ANTERIOR ESTABA SATURADA, FINALMENTE EN EL NOTE-RED NOS DIO LA OPCION DE ENCENDER Y APAGAR PERO "OJO" SE DEBEN TENER EL NOTE-RED Y LA SIMULACION DEL LED AMBAS EN CADA ESQUINA PARA PODER APRECIAR LA SIMULACION AI CKMO DEPENDEIENDO DE TU ANCHO DE BANDA LA RESPUESTA ES AL MOMENTO O NO
 
## LOS MATERIALES FUERON:
  - ESP32
  - RELAY MODULE

![](https://github.com/Miguebt2707/LED-CON-NODE-RED/blob/main/CONX.png?raw=true)

![](https://github.com/Miguebt2707/LED-CON-NODE-RED/blob/main/NODE-RED.png?raw=true)

![](https://github.com/Miguebt2707/LED-CON-NODE-RED/blob/main/NODE-RED-ENCENDER%20LED.png?raw=true)

![](https://github.com/Miguebt2707/LED-CON-NODE-RED/blob/main/NODE-RED%20MIGUELON.png?raw=true)

![](https://github.com/Miguebt2707/LED-CON-NODE-RED/blob/main/LED-APAGADO-SIMULACION.png?raw=true)

![](https://github.com/Miguebt2707/LED-CON-NODE-RED/blob/main/LED-ENCENDIDO-SIMULACION.png?raw=true)

```
#include <WiFi.h>
#include <PubSubClient.h>

const char* ssid = "Wokwi-GUEST";
const char* password = "";
const char* mqttServer = "52.29.234.128";
const int mqttPort = 1883;
const char* mqttUser = "Migue";
const char* mqttPassword = "6133";
const char* mqttTopic = "Miguelon";

WiFiClient espClient;
PubSubClient client(espClient);

int ledPin = 13; // Pin del LED

void setup() {
  pinMode(ledPin, OUTPUT);
  Serial.begin(115200);
  setup_wifi();
  client.setServer(mqttServer, mqttPort);
  client.setCallback(callback);
}

void loop() {
  if (!client.connected()) {
    reconnect();
  }
  client.loop();
}

void setup_wifi() {
  delay(10);
  Serial.println();
  Serial.print("Conectando a: ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("");
  Serial.println("Conectado a la red WiFi");
}

void reconnect() {
  while (!client.connected()) {
    Serial.print("Intentando conexión MQTT...");
    if (client.connect("ESP32Client", mqttUser, mqttPassword)) {
      Serial.println("Conectado");
      client.subscribe(mqttTopic);
    } else {
      Serial.print("Error de conexión, rc=");
      Serial.print(client.state());
      Serial.println(" Intentando de nuevo en 5 segundos");
      delay(5000);
    }
  }
}

void callback(char* topic, byte* payload, unsigned int length) {
  Serial.print("Mensaje recibido: [");
  Serial.print(topic);
  Serial.print("] ");
  for (int i = 0; i < length; i++) {
    Serial.print((char)payload[i]);
  }
  Serial.println();

  if (strcmp(topic, mqttTopic) == 0) {
    if ((char)payload[0] == '1') {
      digitalWrite(ledPin, HIGH);
    } else {
      digitalWrite(ledPin, LOW);
    }
  }
}
```