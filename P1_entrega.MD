# P_1: 

### Objetivo:
- El objetivo de la practica es producir el parpadeo periodico de un led. Se utilizara la salida serie para depurar el programa.

### Materiales: 
- ESP32-S1 
- LED
### Procedimiento:  
**Codigo:**
```cpp
    #define LED_BUILTIN 2
    #define DELAY 500

    void setup()
    {
        Serial.begin(115200); 
        pinMode(LED_BUILTIN, OUTPUT);
    }
    void loop()
    {
        digitalWrite(LED_BUILTIN, HIGH); 
        Serial.println("ON"); 
        delay(500); 
        digitalWrite(LED_BUILTIN, LOW);
        Serial.println("OFF");  
        delay(500);
    }
```
### Código con delay

**Descripcion:** <br>
Este código enciende y apaga el LED integrado en un intervalo de medio segundo, mientras envía mensajes "ON" y "OFF" a través del puerto serial para monitorear el estado del LED.


### Sin delay: 
**Código:**
```cpp
#include <Arduino.h>

#define LED_BUILTIN 2
#define DELAY 1000 //ms

void setup() {
  Serial.begin(115200); // Inicialitza la comunicació sèrie entre l'Arduino i un altre dispositiu.
  pinMode(LED_BUILTIN, OUTPUT);
}

void loop() {
  digitalWrite(LED_BUILTIN, HIGH);
  Serial.println("ON");
  delay(DELAY);
  digitalWrite(LED_BUILTIN, LOW);
  Serial.println("OFF");
  delay(DELAY);
}
```
### Código sin delay:

**Descripcion:** <br>
En este código, la LED se encenderá y se apagará repetidamente sin ninún tipo de separación temporal porque hemos quitado los delay del código anterior.

### Modificar la salida a uno cualquiera de los que estan disponibles y medir con el osciloscopio la máxima frecuencia de apagado y encendido que permite el microcontrolador:
### 4 códigos: 
- **Envio por el puerto série del mensaje i utilizando funciones de Arduino**
```cpp
   #include <Arduino.h>
   #define LED_BUILTIN 23
   void setup() {                
      pinMode(LED_BUILTIN, OUTPUT);   
      Serial.begin(115200);
   }
   void loop() {
      Serial.println("ON");
      digitalWrite(LED_BUILTIN, HIGH);
      Serial.println("OFF");      
      digitalWrite(LED_BUILTIN, LOW);
   }
   ```
Frecuencias: 33,8us o 29,6khz .

- **Envio por el puerto série y accediendo directamente a los registros:**
```cpp
    #include <Arduino.h>
    #define LED_BUILTIN 23
    uint32_t *gpio_out = (uint32_t *)GPIO_OUT_REG;
    void setup() {                
        pinMode(LED_BUILTIN, OUTPUT);   
        Serial.begin(115200);
    }
    void loop() {
        Serial.println("ON");
        *gpio_out |= (1 << LED_BUILTIN);
        Serial.println("OFF");      
        *gpio_out ^= (1 << LED_BUILTIN);
    }
```
Frecuencias: 34us o 29.1khz

 - **Sin envio por el puerto série del mensaje i utilizando funciones de Arduino:**
```cpp
    #include <Arduino.h>
    #define LED_BUILTIN 23

    void setup() {                
    pinMode(LED_BUILTIN, OUTPUT);   
    }

    void loop() {
    digitalWrite(LED_BUILTIN, HIGH);
    digitalWrite(LED_BUILTIN, LOW);
    }
   ```
Frecuencias: 571ns o 1.80Mhz

 - **Sin envio por el puerto série y accediendo directamente a los registros:**
```cpp
    #include <Arduino.h>

    #define LED_BUILTIN 23
    uint32_t *gpio_out = (uint32_t *)GPIO_OUT_REG;

    void setup() {                
    pinMode(LED_BUILTIN, OUTPUT);   
    }

    void loop() {
    *gpio_out |= (1 << LED_BUILTIN);
    *gpio_out ^= (1 << LED_BUILTIN);
    }
```
Frecuencia: 206ns o 4.69Mhz.

### Diagramas del código con delay:

**Diagrama de flujos:**
``` mermaid 
graph LR
    A[Setup] --> B[Loop]
    B --> C[Enciende el LED]
    C --> D[Mensaje ON]
    D --> E[Delay medio segundo]
    E --> F[Apaga el LED]
    F --> G[Mensaje OFF]
    G --> H[Delay medio segundo]
    H --> B
```

**Diagrama de tiempo:**
``` mermaid
sequenceDiagram
    participant Arduino as ESP-32 Board
    participant Serial as Serial Monitor
    participant LED as Built-in LED
    Arduino->>LED: digitalWrite(LED_BUILTIN, HIGH)
    LED->>Arduino: State = ON
    Arduino->>Serial: Serial.println("ON")
    Serial->>Arduino: Receive "ON"
    Arduino->>LED: delay(500)
    LED->>Arduino: State = ON
    Arduino->>LED: digitalWrite(LED_BUILTIN, LOW)
    LED->>Arduino: State = OFF
    Arduino->>Serial: Serial.println("OFF")
    Serial->>Arduino: Receive "OFF"
    Arduino->>LED: delay(500)
    LED->>Arduino: State = OFF
```

### Diagramas del código sin delay:

**Diagrama de flujos:**
``` mermaid 
graph LR
    A[Setup] --> B[Loop]
    B --> C[Enciende el LED]
    C --> D[Mensaje ON]
    D --> E[Apaga el LED]
    E --> F[Mensaje OFF]
    F --> G[Loop]
    G --> B
 ```

 **Diagrama de tiempo:**
 ``` mermaid
 sequenceDiagram
    participant Arduino as ESP-32 Board
    participant Serial as Serial Monitor
    participant LED as Built-in LED
    Arduino->>LED: digitalWrite(LED_BUILTIN, HIGH)
    LED->>Arduino: State = ON
    Arduino->>Serial: Serial.println("ON")
    Serial->>Arduino: Receive "ON"
    Arduino->>LED: digitalWrite(LED_BUILTIN, LOW)
    LED->>Arduino: State = OFF
    Arduino->>Serial: Serial.println("OFF")
    Serial->>Arduino: Receive "OFF"
    Arduino->>LED: digitalWrite(LED_BUILTIN, HIGH)
    LED->>Arduino: State = ON
    Arduino->>Serial: Serial.println("ON")
    Serial->>Arduino: Receive "ON"
    Arduino->>LED: digitalWrite(LED_BUILTIN, LOW)
    LED->>Arduino: State = OFF
    Arduino->>Serial: Serial.println("OFF")
    Serial->>Arduino: Receive "OFF"
 ```

### Pregunta:
**¿En el programa que se ha realizado cual es el tiempo libre que tiene el procesador?**
El tiempo libre de un programa es el tiempo en el que el programa no está activamente ejecutando una tarea específica y puede realizar otras tareas o esperar eventos. Al definir un delay de 500ms podemos decir que el programa tenia ese tiempo libre.


### Conclusión:
En esta práctica hemos empezado a utilizar una nueva forma de programación con una placa arduino. Hemos visto los conceptos básicos y hemos podido ver un ejemplo de ello utilizando una LED y desarrollando un código para que esta haga algún tipo de funcionalidad.


