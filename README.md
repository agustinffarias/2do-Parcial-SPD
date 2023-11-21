# Alumno:
FARIAS TOMAS AGUSTIN
DNI: 41.259.721

# Link del proyecto: 
https://www.tinkercad.com/things/eIogHyeTggL-parcial-n2

# Mapa del circuito:
![Tinkercad](/imagenes/Parcial_2.jpg)
![Tinkercad](/imagenes/Parcial_2.jpg)
# Objetivo:
El objetivo de este proyecto es diseñar un sistema de incendio utilizando Arduino que pueda
detectar cambios de temperatura y activar un servo motor en caso de detectar un incendio.
Además, se mostrará la temperatura actual y la estación del año en un display LCD.

# COMPONENTES: 
Arduino UNO
Sensor de temperatura
Control remoto IR (Infrarrojo)
Display LCD (16x2 caracteres)
Servo motor
Cables y resistencias según sea necesario
Protoboard para realizar las conexiones
Dos leds.

# CODIGO:
# DEFINIMOS PINES:
#include <IRremote.hpp>
#include <LiquidCrystal.h>
#include <Servo.h>
#define apagado 8
#define prendido 9
#define TEMPERATURA_PIN A0

Servo miServo;  // Declara el objeto Servo.
int pinServo = 6;  // Define el pin al que está conectado el servo.
bool incendio = false; // Definime un valor bool para incendio
float temperatura_celcius;  // Variable global para almacenar la temperatura convertida a grados Celsius.

// IR REMOTE SETUP
const int rcvPin=10;
IRrecv irrecv(rcvPin);
decode_results results;
bool sistema_estado = false;


LiquidCrystal lcd_1(12, 11, 5, 4, 3, 2); // Setea los pines de entrada que usa el lcd
# FUNCION SET:
void setup()
{
  lcd_1.begin(16, 2); // Configura el número de columnas y filas en el LCD. 
  irrecv.enableIRIn();  // Se inicializa el receptor infrarojo
}
# FUNCION PARA LEER LA TEMPERATURA 
void leer_temp()
{
  int lectura = analogRead(TEMPERATURA_PIN);  // Lee el valor analógico del sensor de temperatura.
  temperatura_celcius = map(lectura, 20, 358, -40, 125); //Con la funcion map, lee los valores de TEMPERATURA_PIN
}
# FUNCION INCENDIO ACTIVADO:
void incendio_activado()
//Esta funcion define el incendio activado y prende el led correspondiente junto con el servomotor.
{
  incendio = true; //Define una bandera en True
  if (incendio)
  {
    analogWrite(prendido, 255); // Defuine el led rojo en PRENDIDO
    digitalWrite(apagado, LOW); // Define el led verde en APAGADO
    miServo.attach(pinServo); // Prende el servo
    miServo.write(100); // Representa una posicion del servo en 100°
  }
}
# FUNCION INCENDIO APAGADO: 
void sin_incendio()
{// Cuando el circuito no esta incendiado:
  digitalWrite(prendido, LOW); // Definimos el led rojo apagado
  analogWrite(apagado, 255); // Definimos el led verde prendido
  
  miServo.detach(); // Apaga el servo
}
# FUNCION ESTACIONES DEL AÑO:
void estaciones()
{
  leer_temp();  // Llama a leer_temp() para obtener la temperatura actualizada.

  lcd_1.clear();  // Limpia la pantalla del LCD.

  lcd_1.setCursor(0, 0);  // Establece el cursor en la primera línea.
  
  lcd_1.clear(); // Limpia en cada iteracion lo que se escribe en el lcd.
  if (temperatura_celcius <= 11) //Declaramos condiciones para cuando sea Invierno
  {
    lcd_1.print("Invierno");
    lcd_1.setCursor(0,2);
    lcd_1.print(temperatura_celcius);
    sin_incendio();
  }
  else if (temperatura_celcius <= 17)//Declaramos condiciones para cuando sea Otonio
  {
    lcd_1.print("Otonio");
    lcd_1.setCursor(0,2);
    lcd_1.print(temperatura_celcius);
    sin_incendio();
  }
  else if (temperatura_celcius <= 26) //Declaramos condiciones para cuando sea Primavera
  {
    lcd_1.print("Primavera");
    lcd_1.setCursor(0,2);
    lcd_1.print(temperatura_celcius);
    sin_incendio();
  }
  else if (temperatura_celcius <= 39)//Declaramos condiciones para cuando sea Verano
  {
    lcd_1.print("Verano");
    lcd_1.setCursor(0,2);
    lcd_1.print(temperatura_celcius);
    sin_incendio();
  }
  else //Declaramos condiciones para cuando sea Incendio 
  {
    lcd_1.print("Incendio");
    lcd_1.setCursor(0,2);
    lcd_1.print(temperatura_celcius);
    incendio_activado();
  }
   
}

# FUNCION LECTURA CONTROL REMOTO:
void lectura_CR(){
  if(IrReceiver.decode()) {
    auto value= IrReceiver.decodedIRData.decodedRawData; 	

      switch(value)
      {        
        case 4278238976:
            Serial.println("1");  // Button 1
        	cambiar_estado_sistema();
          	break;

        default: Serial.println(value);     
      }  
    IrReceiver.resume(); // Receive the next value
  }
  
}

# FUNCION CAMBIO DE ESTADO:
void cambiar_estado_sistema(){
   sistema_estado = !sistema_estado;
}

# FUNCION LOOP:
void loop()
{ 
  lectura_CR();
  if (sistema_estado == true){
    estaciones(); //llama a la funcion estaciones dentro del bucle principal.
    delay(1000); // Generamos un delay de 1000(1seg) por cada iteracion del bucle
  }
}