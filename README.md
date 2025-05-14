# Practica6:Buses de comunicación II (SPI)
## Objetivo: 
El objetivo de esta practica es comprender el funcionamiento del bus de comunicación SPI, que es ampliamente utilizado en sistemas embebidos debido a su rapidez y sencillez. 
En esta práctica se realizarán dos ejercicios en los cuales un se usará una tarjeta SD y en el otro un lector RFID. Ambos usan el el bus SPI para la comuncacion con el microcontrolador. 
## Apartado 1:
En este apartado usaremos una tarjeta SD para trabajar utilizando el bus SPI para realizar operaciones básicas de lectura y de escritura.
Los materiales usados han sido:
1. ESP32-S3
2. Módulo lector de tarjetas SD
3. Cables
4. Tarjeta SD
   ![image](https://github.com/user-attachments/assets/b6ba99da-8173-4ae3-a094-8b6a3e19b2c1)

   
### Código usado:
```
#include <SPI.h>
#include <SD.h>


File myFile;


void setup() {
    Serial.begin(115200);
    Serial.print("Iniciando SD...");


    if (!SD.begin(10)) {  // CS en GPIO 10
        Serial.println("No se pudo inicializar");
        return;
    }
    Serial.println("Inicialización exitosa");


 File archivo = SD.open("/ejemplo.txt", FILE_WRITE);
    if (archivo) {
        Serial.println("Escribiendo en archivo ejemplo.txt...");
        archivo.println("Hola, este es un archivo creado en la SD con ESP32-S3. ");
        archivo.println( "Puedes modificar este código para escribir más datos");
        archivo.close();
        Serial.println("Archivo escrito correctamente");
    } else {
        Serial.println("Error al crear el archivo");
    }


    myFile = SD.open("/ejemplo.txt");
    if (myFile) {
        Serial.println("/ejemplo.txt:");
        while (myFile.available()) {
            Serial.write(myFile.read());
        }
        myFile.close();
    } else {
        Serial.println("Error al abrir el archivo");
    }
}


void loop() {
}

```
### Funcionamiento del código:
En el caso que la tarjeta se inicialice correctamente, se crea un archivo donde se escribe "Hola, este es un archivo creado en la SD con ESP32-S3.". Luego se cierra el archivo para que no se pierdan los datos. Una vez 
echo los dos pasos se abre el mismo archivo llamdo ejemplo.txt para leer lo que hay en este y se muestra por el monitor serial. 

###Salidas:
En el puerto serie se muesrtra:
```
Iniciando SD ...inicializacion exitosa
Escribiendo en archivo ejemplo.txt...
Archivo escrito correctamente.
/ejemplo.txt:
Hola, este es un archivo creado en la SD con ESP32-S3.
Puedes modificar este código para escribir más datos.
```
![image](https://github.com/user-attachments/assets/409e4eec-e56e-4e3d-a18d-8fc21ac7b29b)

## Apartado 2:
En este apartado queremos leer el identificador único de una RFID usando el módulo MFRC522, que está conectado con el bus SPI. 
Los materiales usados han sido:
1. Placa ESP32
2. Cables
3. Etiquetas RFID
4. Módulo lector RFID RC522
   ![image](https://github.com/user-attachments/assets/c08bb2cb-2c57-417a-b7a6-c541139d68eb)

### Código usado
```
#include <SPI.h>
#include <MFRC522.h>


#define RST_PIN 11   // Pin de reset del RC522
#define SS_PIN  12   // Pin SS (SDA) del RC522


MFRC522 mfrc522(SS_PIN, RST_PIN);  // Crear objeto RFID


void setup() {
    Serial.begin(115200);  // Iniciar la comunicación serial
    SPI.begin();           // Iniciar el bus SPI
    mfrc522.PCD_Init();    // Iniciar el módulo RFID
    Serial.println("Escaneando tarjetas RFID...");
}


void loop() {
    // Verificar si hay una nueva tarjeta presente
    if (mfrc522.PICC_IsNewCardPresent() && mfrc522.PICC_ReadCardSerial()) {
        Serial.print("UID de tarjeta: ");


        // Leer el UID de la tarjeta y mostrarlo en hexadecimal
        for (byte i = 0; i < mfrc522.uid.size; i++) {
            Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " ");
            Serial.print(mfrc522.uid.uidByte[i], HEX);
        }
        Serial.println();


        mfrc522.PICC_HaltA();  // Detener la lectura de la tarjeta
    }
}

```
### Funcionamiento del código:
Este código funciona que primero se inicializa la comunicación serie y se prepara el módulo para escanear tarjetas. En el bucle, se detecta si una tarjeta RFID se acerca a nuestro lector
, si esto ocurre se muestra su UID en formato hexadecimal por el monitor serial. Por último después de todo esto, se envía un orden de parada . 
![image](https://github.com/user-attachments/assets/d4014ab3-f26b-442b-98a3-af09d285e41c)


###Salidas:
```
Escaneando tarjetas...
ID de tarjeta detectada: 132e8f6
ID de tarjeta detectada: 9097b043
ID de tarjeta detectada: 132e8f6
```
![image](https://github.com/user-attachments/assets/d5014f5a-9812-42f9-9b21-1583239fba44)



