PROTÓTIPO DE LANÇADOR DE BOLAS DE TÊNIS - UTILIZANDO A PLACA ESP-32

TCC - CURSO DE TECNÓLOGO EM AUTOMAÇÃO INDUSTRIAL - FATEC CATANDUVA-SP - FORMANDO DO 1º SEMESTRE DE 2025.

VÍDEO QUE COMPÕE A APRESENTAÇÃO DO PROTÓTIPO: https://youtu.be/C9-QMZfs53g 


############################################################################################################


CÓDIGO EM C, PARA A PLACA ESP-32 - IDE ARDUINO:

//exemplo 1: Robô com ajuste de velocidade via PWM

//inclusão de bibliotecas:
#include <BluetoothSerial.h>  //biblioteca de bluetooth do ESP32

//definições nomes para pinos (GPIOs):
#define IN1 18  //LANÇADOR
#define IN2 19  //SELETOR
#define IN3 32  //ARTICULADOR

//declaração de instâncias para biblioteca:
BluetoothSerial bluetooth;

//declaração de variáveis:
char comando = 'P';         //variável tipo caracter
int valor_pwm_lanc = 255;   //variável tipo inteira (255 => 100% PWM => 100% de velocidade)
int valor_pwm_selet = 200;  //variável tipo inteira (255 => 100% PWM => 100% de velocidade)
int valor_pwm_artic = 180;  //variável tipo inteira (255 => 100% PWM => 100% de velocidade)


void setup() {  //função de inicialização (configurações)
  //Configuração das GPIOS como saída digital:

  // pinMode(IN1,OUTPUT);
  pinMode(IN1, OUTPUT);  //LANÇADOR
  pinMode(IN2, OUTPUT);  //SELETOR
  pinMode(IN3, OUTPUT);  //ARTICULADOR


  //Configurações de PWM para o ESP32:
  ledcAttachPin(IN1, 0);  //canal 0 PWM em IN1 (GPIO 18)
  ledcSetup(0, 500, 8);   //(CANAL,FREQ PWM, No. BITS PWM) => Canal 0 com 500HZ e 8 bits

  ledcAttachPin(IN2, 1);  //canal 1 PWM em IN2 (GPIO 19)
  ledcSetup(1, 500, 8);   //(CANAL,FREQ PWM, No. BITS PWM) => Canal 1 com 500HZ e 8 bits

  ledcAttachPin(IN3, 2);  //canal 2 PWM em IN3 (GPIO 32)
  ledcSetup(2, 500, 8);   //(CANAL,FREQ PWM, No. BITS PWM) => Canal 2 com 500HZ e 8 bits



  //Configuração de bluetooth:
  bluetooth.begin("LANÇADOR");  //inicializa servidor bluetooth com nome "LANÇADOR"

}  //fim setup

void loop() {  //função de laço infinito (processamento)

  //se algum caracter disponível via bluetooth => comando = caracter lido
  if (bluetooth.available() > 0) comando = bluetooth.read();

  if (comando == 'P') parar();        //se comando igual ao caracter zero executa função de usário chamada "parar"
  if (comando == 'F') lancador();     //se comando igual ao caracter um executa função de usário chamada "lancador"
  if (comando == 'E') seletor();      //se comando igual ao caracter cinco executa função de usário chamada "seletor"
  if (comando == 'D') articulador();  //se comando igual ao caracter três executa função de usário chamada "articulador"

  if (comando == 'X') lancador2();     // parar motor lancador
  if (comando == 'Y') seletor2();      // parar motor seletor
  if (comando == 'Z') articulador2();  // parar motor articulador
}  //fim loop

//Funções de usuário:
void parar() {
  //Parar todos os Motores
  ledcWrite(0, 0);
  ledcWrite(1, 0);
  ledcWrite(2, 0);
}  //fim parar


void lancador() {
  //PMW com valor_pwm => (valor_pwm=127 => vel 50%  ; valor_pwm=255 => vel 100%)
  ledcWrite(0, valor_pwm_lanc);  //(CANAL PWM, VALOR PWM) => canal 2 com valor_pwm de PWM
}  //fim parado

void lancador2() {  //Parar o Motor Lançador
  ledcWrite(0, 0);  //(CANAL PWM, VALOR PWM) => canal 0 com 0 de PWM
}  //fim lançador2


void seletor() {
  //PMW com valor_pwm => (valor_pwm=127 => vel 50%  ; valor_pwm=255 => vel 100%)
  ledcWrite(1, valor_pwm_selet);  //(CANAL PWM, VALOR PWM) => canal 0 com valor_pwm de PWM
}  //fim parado

void seletor2() {   //Parar o Motor Lançador
  ledcWrite(1, 0);  //(CANAL PWM, VALOR PWM) => canal 0 com 0 de PWM
}  //fim seletor2


void articulador() {
  //PMW com valor_pwm => (valor_pwm=127 => vel 50%  ; valor_pwm=255 => vel 100%)
  ledcWrite(2, valor_pwm_artic);  //(CANAL PWM, VALOR PWM) => canal 0 com valor_pwm de PWM
}  //fim parado

void articulador2() {  //Parar o Motor Lançador
  ledcWrite(2, 0);     //(CANAL PWM, VALOR PWM) => canal 0 com 0 de PWM
}  //fim articulador2


##########################################################################################################################



