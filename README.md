# Arduino
Projetos feito em Arduino


https://github.com/user-attachments/assets/9ea21aee-a67b-43c5-9cb0-573a5472a061




#include <Servo.h>

Servo meuServoX;  // Servo para o eixo X (horizontal)
Servo meuServoY;  // Servo para o eixo Y (vertical)
Servo meuServoZ;  // Servo para o botão do joystick (terceiro servo)

int pinoJoystickX = A0; // Pino para o eixo X (VRx)
int pinoJoystickY = A1; // Pino para o eixo Y (VRy)
int pinoBotao = 2;      // Pino para o botão do joystick (SW)

int valorLidoX;
int valorLidoY;

int anguloX;
int anguloY;
int anguloZ = 90;  // Inicializa o terceiro servo em 90 graus

int anguloAnteriorX = 90; // Inicializa com 90 graus (meio do servo)
int anguloAnteriorY = 90; // Inicializa com 90 graus (meio do servo)

void setup() {
  meuServoX.attach(9);    // Servo X no pino 9
  meuServoY.attach(10);   // Servo Y no pino 10
  meuServoZ.attach(11);   // Servo Z no pino 11

  pinMode(pinoBotao, INPUT_PULLUP);  // Define o botão como entrada com resistência de pull-up
  Serial.begin(9600);     // Inicia o Monitor Serial
}

void loop() {
  valorLidoX = analogRead(pinoJoystickX);           // Lê o eixo X do joystick
  valorLidoY = analogRead(pinoJoystickY);           // Lê o eixo Y do joystick

  anguloX = map(valorLidoX, 0, 1023, 0, 180);      // Mapeia o eixo X para 0-180
  anguloY = map(valorLidoY, 0, 1023, 0, 180);      // Mapeia o eixo Y para 0-180

  // Suaviza o movimento do servo X
  if (anguloX != anguloAnteriorX) {
    for (int i = anguloAnteriorX; i != anguloX; i += (anguloX > anguloAnteriorX ? 1 : -1)) {
      meuServoX.write(i); // Move o servo X de um grau por vez
      delay(10); // Atraso para suavizar
    }
    anguloAnteriorX = anguloX; // Atualiza a última posição X
  }

  // Suaviza o movimento do servo Y
  if (anguloY != anguloAnteriorY) {
    for (int i = anguloAnteriorY; i != anguloY; i += (anguloY > anguloAnteriorY ? 1 : -1)) {
      meuServoY.write(i); // Move o servo Y de um grau por vez
      delay(10); // Atraso para suavizar
    }
    anguloAnteriorY = anguloY; // Atualiza a última posição Y
  }

  // Verifica se o botão foi pressionado (SW)
  if (digitalRead(pinoBotao) == LOW) {
    anguloZ = (anguloZ == 90) ? 0 : 90; // Alterna entre 0 e 90 graus ao pressionar
    meuServoZ.write(anguloZ);           // Move o terceiro servo
    delay(500);                         // Delay para evitar múltiplas leituras rápidas do botão
  }

  // Log no monitor serial
  Serial.print("Joystick X: ");
  Serial.print(valorLidoX);
  Serial.print(" -> Servo X: ");
  Serial.print(anguloX);
  Serial.print(" graus");
  Serial.print(" | Joystick Y: ");
  Serial.print(valorLidoY);
  Serial.print(" -> Servo Y: ");
  Serial.print(anguloY);
  Serial.print(" graus");
  Serial.print(" | Botão: ");
  Serial.print(digitalRead(pinoBotao) == LOW ? "Pressionado" : "Liberado");
  Serial.print(" -> Servo Z: ");
  Serial.println(anguloZ);

  delay(50); // Pequeno delay para evitar leituras excessivas
}
