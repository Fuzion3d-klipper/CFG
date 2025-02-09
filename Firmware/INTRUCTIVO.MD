# Descripción del archivo de firmware para Klipper

El archivo `firmware.bin` o `.hex` es el firmware compilado necesario para que la placa de la impresora 3D pueda ejecutar Klipper. Este archivo contiene las instrucciones y configuraciones esenciales para la comunicación entre la placa controladora y el host donde corre Klipper (generalmente una Raspberry Pi u otro dispositivo con Linux).

El tipo de archivo a flashear depende de la arquitectura de la placa:
- **`.bin`**: Usado en placas con microcontroladores STM32 y algunos otros.
- **`.hex`**: Común en placas con microcontroladores AVR, como los basados en ATmega2560.

# Cómo flashear el firmware en la impresora 3D

## Requisitos previos
1. **Archivo firmware.bin/.hex**: Que se encuentra en la carpeta "Fimware" en este repositorio (renombrar si es necesario).
2. **Una tarjeta microSD (si aplica)** o conexión serial para flasheo directo.
3. **Software de flasheo** (en caso de que no se use una microSD), como `avrdude`, `dfu-util` o `bossac`, dependiendo de la placa.

## Métodos de flasheo según la impresora 3D

### 1. Flasheo mediante microSD (STM32 y algunas placas LPC)
Este método es el más simple y es compatible con muchas placas como la SKR Mini, SKR 1.4, Creality V4.2.7, entre otras.

#### Pasos:
1. Formatea una tarjeta microSD en **FAT32**.
2. Copia el archivo `firmware.bin` en la raíz de la tarjeta.
3. Inserta la microSD en la impresora y enciéndela.
4. La impresora cargará el firmware y renombrará el archivo (por ejemplo, `FIRMWARE.CUR` si todo fue exitoso).
5. Reinicia la impresora y verifica la conexión con Klipper.

### 2. Flasheo mediante DFU (para placas STM32 con USB)
Si la placa soporta DFU (Device Firmware Update), se puede usar `dfu-util`.

#### Pasos:
1. Conecta la placa a la computadora mediante USB.
2. Pon la placa en modo DFU (generalmente presionando un botón de "BOOT" y encendiéndola).
3. En una terminal, usa el siguiente comando:
   ```bash
   dfu-util -a 0 -D firmware.bin -s 0x08000000:leave
   ```
4. Espera a que el proceso termine y reinicia la impresora.

### 3. Flasheo con `avrdude` (para placas AVR como Arduino Mega 2560 Y familia ATMega)
Para placas basadas en ATmega, se usa `avrdude` para flashear el firmware.

#### Pasos:
1. Conecta la impresora a la PC mediante USB.
2. Asegúrate de tener `avrdude` instalado.
3. Ejecuta el siguiente comando (reemplaza `firmware.hex` y el puerto correcto `/dev/ttyUSB0` o `COMx` en Windows):
   ```bash
   avrdude -p atmega2560 -c wiring -P /dev/ttyUSB0 -b 115200 -D -U flash:w:firmware.hex:i
   ```
4. Espera a que termine y reinicia la impresora.

### 4. Flasheo con `bossac` (para placas con procesadores SAMD como la Duet)
Algunas placas como las Duet WiFi usan `bossac` para el flasheo.

#### Pasos:
1. Conecta la impresora por USB.
2. Pon la placa en modo de programación (generalmente presionando "Erase" y luego "Reset").
3. Ejecuta el siguiente comando:
   ```bash
   bossac -e -w -v -b firmware.bin -R
   ```
4. Espera a que termine y reinicia la impresora.

# Verificación del flasheo
Para comprobar que el firmware ha sido flasheado correctamente y que Klipper está funcionando:
1. Conéctate a la Raspberry Pi u host donde corre Klipper.
2. Usa `ls /dev/serial/by-id/` para ver si la impresora es detectada.
3. Verifica la conexión en el log de Klipper con:
   ```bash
   cat /tmp/klippy.log
   ```
4. Si todo está correcto, configura `printer.cfg` y reinicia Klipper.


Nota: En algunas ocasiones, es necesario cambiar el nombre del archivo firmware.bin a un nombre diferente, 
para no crear conflicto con anteriores archivos flasheados.


Con estos pasos, tu impresora 3D debería estar lista para ejecutar Klipper. ¡Disfruta de las mejoras en velocidad y precisión que ofrece este firmware!

