#include <ESP8266WiFi.h>
#include <OneWire.h>
#include <PubSubClient.h>

const char *ssid =  "Wifi_PI";        //Nome do WiFi para acesso a rede
const char *pass =  "123456654321";    //Senha de acesso ao WiFi
const char* serverTS = "api.thingspeak.com";
String apiKey = "AIPCO3EJUY0RP0GP";          //KEY API do canal Thingspeak
int TRIGGER = 5; //Pin D1 = TRIGGER
int ECHO = 4; //Pin D2 = ECHO
void setup() 
{
  pinMode(2,OUTPUT);            //conexão do LED GPIO2
  Serial.begin(115200);         
  pinMode(TRIGGER,OUTPUT);
  pinMode(ECHO,INPUT);
  connectWifi();
  }

void loop() 
{
  /* Estabelecendo variaveis para a duração do PING e a distância resultada em centímentros*/
  long duration, cm;

  /* O PING))) é disparado por um pulso HIGH de 2 ou mais microssegundos.
   Dê um pulso BAIXO de antemão para garantir um pulso HIGH limpo: */
 
  digitalWrite(TRIGGER, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIGGER, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIGGER, LOW);

  /* O mesmo pino é usado para ler o sinal do PING))): um pulso HIGH cuja 
  duração é o tempo (em microssegundos) desde o envio do ping até a 
  recepção do eco de um objeto. */
  duration = pulseIn(ECHO, HIGH);

  // converter o tempo em uma distância
  cm = microsecondsToCentimeters(duration);

  Serial.print(cm);
  Serial.print("cm");
  Serial.println();

  delay(100);

  digitalWrite(2, HIGH);   // liga o LED (HIGH é o nível de voltagem)
  delay(1000);              // espere por um segundo
  digitalWrite(2, LOW);    // desligue o LED fazendo a tensão LOW
  delay(1000);              // espere por um segundo

  sendHeight(cm);
}
void connectWifi()
{ 
  Serial.print("Connecting to "+*ssid);
  WiFi.begin(ssid, pass);
  while (WiFi.status() != WL_CONNECTED) {
  delay(1000);
  Serial.print(".");
  }
  Serial.println("");
  Serial.println("Connected");
  Serial.println("");  
}

long microsecondsToInches(long microseconds) 
{ 
    /*De acordo com a folha de dados da Parallax para o PING))), existem 
    73.746 microssegundos por polegada (ou seja, o som viaja a 1130 pés por segundo). 
    Isto dá a distância percorrida pelo ping, saída e retorno, por isso dividimos 
    por 2 para obter a distância do obstáculo.*/
  // See: http://www.parallax.com/dl/docs/prod/acc/28015-PING-v1.3.pdf
  return microseconds / 74 / 2;
}
long microsecondsToCentimeters(long microseconds) {
  /*
A velocidade do som é de 340 m / s ou 29 microssegundos por centímetro. 
O ping viaja para fora e para trás, então, para encontrar a distância 
do objeto, tomamos metade da distância percorrida.*/
  return microseconds / 29 / 2;
}

 void sendHeight(float cm)
{  
  WiFiClient tclient;// não deve ser confundido com "client" no PubSub {}, e client for mqtt
   if (tclient.connect(serverTS, 80)) { // Use ip 184.106.153.149 ou api.thingspeak.com
   Serial.println("WiFi Client connected ");
   
   String postStr = apiKey;
   postStr += "&field1=";
   postStr += String(cm);
   postStr += "\r\n\r\n";
   
   tclient.print("POST /update HTTP/1.1\n");
   tclient.print("Host: api.thingspeak.com\n");
   tclient.print("Connection: close\n");
   tclient.print("X-THINGSPEAKAPIKEY: " + apiKey + "\n");
   tclient.print("Content-Type: application/x-www-form-urlencoded\n");
   tclient.print("Content-Length: ");
   tclient.print(postStr.length());
   tclient.print("\n\n");
   tclient.print(postStr);
   delay(60000);
   
   }

 tclient.stop();
}
