const int PIN_SENSOR = A0;   // Señal del sensor
const int PIN_LED    = 13;   // LED integrado (alerta de riego)

// ===== CALIBRACIÓN (Valores típicos para Arduino UNO R3) =====
// 1. Lectura al aire / tierra seca  -> VALOR_SECO (suele ser alto, ej: 800-1023)
// 2. Lectura en tierra muy mojada   -> VALOR_MOJADO (suele ser bajo, ej: 200-400)
const int VALOR_SECO   = 1020;
const int VALOR_MOJADO = 400;

// Porcentaje mínimo de humedad para considerar la tierra húmeda
const int UMBRAL_PORCENTAJE = 40;

void setup() {
  Serial.begin(9600);

  pinMode(PIN_LED, OUTPUT);

  Serial.println("=== Monitor de humedad del suelo - UNO R3 ===");
}

void loop() {
  int lectura = analogRead(PIN_SENSOR);

  // Invertimos los límites en map() si VALOR_SECO > VALOR_MOJADO
  int porcentaje = map(lectura, VALOR_SECO, VALOR_MOJADO, 0, 100);
  porcentaje = constrain(porcentaje, 0, 100);

  Serial.print("Lectura: ");
  Serial.print(lectura);
  Serial.print("  |  Humedad: ");
  Serial.print(porcentaje);
  Serial.print("%  |  Estado: ");

  if (porcentaje < UMBRAL_PORCENTAJE) {
    Serial.println("TIERRA SECA -> Se recomienda regar");
    digitalWrite(PIN_LED, HIGH);   // Alerta encendida
  } else {
    Serial.println("TIERRA HUMEDA -> No necesita riego");
    digitalWrite(PIN_LED, LOW);    // Alerta apagada
  }

  delay(1000);  // Una lectura por segundo
}
