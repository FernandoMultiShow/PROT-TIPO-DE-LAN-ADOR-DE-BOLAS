PROTÓTIPO DE LANÇADOR DE BOLAS DE TÊNIS - UTILIZANDO A PLACA ESP-32

TCC - CURSO DE TECNÓLOGO EM AUTOMAÇÃO INDUSTRIAL - FATEC CATANDUVA-SP - FORMANDO DO 1º SEMESTRE DE 2025.

VÍDEO QUE COMPÕE A APRESENTAÇÃO DO PROTÓTIPO: https://youtu.be/C9-QMZfs53g 


############################################################################################################


CÓDIGO EM C, PARA A PLACA ESP-32 - IDE ARDUINO:

#include "BluetoothSerial.h" // Inclui a biblioteca para comunicação Bluetooth

#if !defined(CONFIG_BT_ENABLED) || !defined(CONFIG_BLUEDROID_ENABLED)
#error Bluetooth is not enabled! Please run `make menuconfig` to enable it.
#endif

BluetoothSerial SerialBT; // Cria um objeto BluetoothSerial

// Definição dos pinos GPIO para os motores e o LED
const int PIN_LANCADOR = 18;  // Motor Lançador (GPIO 18)
const int PIN_SELETOR = 19;   // Motor Seletor (GPIO 19)
const int PIN_ARTICULADOR = 32; // Motor Articulador (GPIO 32)
const int PIN_LED_EMBUTIDO = 2; // LED embutido do ESP32 (GPIO 2)

// Configurações de PWM para controle dos motores
// Canais PWM para o ESP32 (0-15)
const int canalLancador = 0;
const int canalSeletor = 1;
const int canalArticulador = 2;

// Frequência do PWM (Hz)
const int freqPWM = 5000; // 5 kHz é um bom ponto de partida para motores DC
// Resolução do PWM (bits). 8 bits = 0-255
const int resolucaoPWM = 8; // Permite valores de 0 a 255 para a velocidade

void setup() {
  Serial.begin(9600); // Inicia a comunicação serial para depuração
  SerialBT.begin("robo_lancador_2"); // Inicia o Bluetooth com o nome "robo_lancador_2"
  Serial.println("O Bluetooth 'robo_lancador_2' foi iniciado!");

  // Configura os pinos dos motores como saída
  pinMode(PIN_LANCADOR, OUTPUT);
  pinMode(PIN_SELETOR, OUTPUT);
  pinMode(PIN_ARTICULADOR, OUTPUT);
  pinMode(PIN_LED_EMBUTIDO, OUTPUT); // Configura o pino do LED como saída

  // Configura os canais PWM
  // ledcSetup(canal, freq, resolucao)
  ledcSetup(canalLancador, freqPWM, resolucaoPWM);
  ledcSetup(canalSeletor, freqPWM, resolucaoPWM);
  ledcSetup(canalArticulador, freqPWM, resolucaoPWM);

  // Atribui os canais PWM aos pinos dos motores
  // ledcAttachPin(pino, canal)
  ledcAttachPin(PIN_LANCADOR, canalLancador);
  ledcAttachPin(PIN_SELETOR, canalSeletor);
  ledcAttachPin(PIN_ARTICULADOR, canalArticulador);

  // Define o status inicial dos motores como LOW (desligados)
  ledcWrite(canalLancador, 0);   // Velocidade 0 para o lançador
  ledcWrite(canalSeletor, 0);    // Velocidade 0 para o seletor
  ledcWrite(canalArticulador, 0); // Velocidade 0 para o articulador
}

void loop() {
  // Verifica se há dados disponíveis via Bluetooth
  if (SerialBT.available()) {
    String comandoCompleto = SerialBT.readStringUntil('\n'); // Lê o comando completo até a quebra de linha
    comandoCompleto.trim(); // Remove espaços em branco no início/fim

    Serial.print("Comando Recebido: ");
    Serial.println(comandoCompleto); // Imprime o comando completo na serial

    // Faz o LED embutido piscar
    digitalWrite(PIN_LED_EMBUTIDO, HIGH); // Liga o LED
    delay(50); // Mantém ligado por 50ms
    digitalWrite(PIN_LED_EMBUTIDO, LOW);  // Desliga o LED
    delay(50); // Mantém desligado por 50ms (para um piscar rápido)

    // Extrai o caractere de comando (primeira letra)
    char tipoComando = comandoCompleto.charAt(0);

    // Extrai o valor da velocidade (o restante da string)
    int velocidade = comandoCompleto.substring(1).toInt();

    // Garante que a velocidade esteja dentro da faixa de 0 a 255
    velocidade = constrain(velocidade, 0, 255);

    // Processa o comando
    switch (tipoComando) {
      case 'F': // Lançador (F de "Forward" ou Lançador - como você usa "lancador")
        Serial.print("Controlando Lançador com velocidade: ");
        Serial.println(velocidade);
        ledcWrite(canalLancador, velocidade); // Define a velocidade do lançador
        break;

      case 'E': // Seletor
        Serial.print("Controlando Seletor com velocidade: ");
        Serial.println(velocidade);
        ledcWrite(canalSeletor, velocidade); // Define a velocidade do seletor
        break;

      case 'D': // Articulador
        Serial.print("Controlando Articulador com velocidade: ");
        Serial.println(velocidade);
        ledcWrite(canalArticulador, velocidade); // Define a velocidade do articulador
        break;

      // Comandos para DESLIGAR os motores (botões separados)
      case 'X': // Desligar Lançador (usando 'f' minúsculo para diferenciar)
        Serial.println("Desligando Lançador.");
        ledcWrite(canalLancador, 0); // Define a velocidade do lançador para 0
        break;

      case 'Y': // Desligar Seletor
        Serial.println("Desligando Seletor.");
        ledcWrite(canalSeletor, 0); // Define a velocidade do seletor para 0
        break;

      case 'Z': // Desligar Articulador
        Serial.println("Desligando Articulador.");
        ledcWrite(canalArticulador, 0); // Define a velocidade do articulador para 0
        break;

      case 'P': // Desligar Todo o Sistema
        Serial.println("Desligando Todo o Sistema.");
        ledcWrite(canalLancador, 0); // Define a velocidade do articulador para 0
        ledcWrite(canalSeletor, 0); // Define a velocidade do articulador para 0
        ledcWrite(canalArticulador, 0); // Define a velocidade do articulador para 0
        break;


      default:
        Serial.println("Comando inválido!");
        break;
    }
  }
}

##########################################################################################################################



