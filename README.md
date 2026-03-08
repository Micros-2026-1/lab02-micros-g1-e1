[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/KzqfxGd5)
[![Open in Visual Studio Code](https://classroom.github.com/assets/open-in-vscode-2e0aaae1b6195c2367325f4f02e2d04e9abb55f0b24a779b69b11b9e10269abc.svg)](https://classroom.github.com/online_ide?assignment_repo_id=22820198&assignment_repo_type=AssignmentRepo)
# Lab02 - Caracterización de osciladores (externo vs. interno)

## 1. Integrantes

* [Laura Alejandra Fuentes Ubaque](https://github.com/LauraAlejandraFuentes)
* [Juan Sebastian Guerrero Gualteros](https://github.com/juanseguerrerogu07)
* [Pedro Felipe Jimenez Celis](https://github.com/pedrofejimenezce-ship-it)

## 2. Documentación

En esta práctica de laboratorio se configuro el microcontrolador PIC18F45K22 para realizar el control de una salida digital. El programa, escrito en lenguaje de C y compilado con XC8 desde MPLAB, utiliza la libreria (xc.h) para detectar automaticamente que microcontrolador seleccionamos en el proyecto de MPLAB

### 2.1 Descripción del laboratorio

**OBJETIVO DE LA PRACTICA**

Configurar el PIC18F45K22 (o la referencia de PIC seleccionada) para operar con: Oscilador externo basado en cristal de cuarzo.

Oscilador interno (INTOSC). Verificar la frecuencia real de la CPU generando una señal de referencia en un pin de salida y midiendo dicha señal con un osciloscopio.

Comparar ambos modos de operación (cristal vs. INTOSC) en cuanto a:

-Precisión absoluta de la frecuencia.

-Estabilidad temporal de la señal.

-Efectos de la deriva térmica sobre la frecuencia.

**HERRAMIENTAS**

-Microcontrolador PIC18F45K22 (o la referencia de PIC seleccionada) en tarjeta de desarrollo o protoboard.

-Cristal 16 MHz (puede ser 8–20 MHz; se recomienda 16 MHz).

-2× capacitores de 21pF - 15pF.

-LED + resistencia 330Ω – 1kΩ.

-Programador (PICkit 4).

-Entorno de programación MPLAB X IDE con compilador XC8.

-Fuente de alimentación de 5V → El PICkit 4 puede suministrar tensión directamente al circuito 

-Osciloscopio.

-Protoboard y cables de conexión.

**FUNDAMENTO TEORICO**

**Oscilador:**

Un oscilador es necesario porque en casi todos los sistemas electrónicos (microcontroladores, microprocesadores, circuitos de comunicación, etc.) se requiere una señal periódica estable que sirva como referencia de tiempo o reloj. A continuación se enlistan las funciones principales de un oscilador

Sincronización
En un microcontrolador o procesador, el oscilador genera la señal de reloj que marca el “ritmo” de ejecución de las instrucciones.

Sin un reloj, el procesador no sabría cuándo avanzar de una instrucción a la siguiente.

Referencia de tiempo.

Para temporizadores, contadores, protocolos de comunicación (UART, I²C, SPI, CAN, USB, etc.), se necesita una frecuencia precisa para medir intervalos de tiempo o mantener la velocidad de transmisión correcta.

Generación de señales.

**Partes Principales del Oscilador**

**1.Oscilador externo:**

Un oscilador externo es aquel que no se encuentra integrado dentro del microcontrolador, sino que, en el caso del PIC, se conecta a los pines OSC1 y OSC2. Se denomina “externo” porque requiere componentes adicionales para generar y estabilizar la señal de reloj. Estos componentes pueden ser:

-Un cristal de cuarzo

-Un resonador cerámico

-En configuraciones más simples, un circuito RC (resistencia-capacitor).

Por lo tanto, puede funcionar en distintos modos según el cristal o circuito usado:

-LP (Low Power Crystal): cristal de baja frecuencia y bajo consumo.

-XT (Crystal/Resonator): cristal típico de 4 MHz a 10 MHz.

-HS (High-Speed Crystal): cristales de alta frecuencia (hasta decenas de MHz).

**2.Oscilador interno:**
El oscilador interno del PIC18F45K22 está compuesto por dos bloques principales:

HFINTOSC (High-Frequency Internal Oscillator): es un oscilador interno de alta frecuencia calibrado de fábrica, cuyo valor nominal es 8MHz. El microcontrolador puede trabajar directamente con esta frecuencia o con versiones reducidas mediante el divisor de prescalador.

LFINTOSC (Low-Frequency Internal Oscillator): es un oscilador interno de baja frecuencia calibrado a 
31kHz. Suele emplearse para temporizadores internos, como el Timer de encendido (Power-up Timer) y el perro guardián (Watchdog Timer), aunque también puede seleccionarse como fuente de reloj principal para todo el microcontrolador cuando se prioriza bajo consumo.

La elección entre usar un oscilador interno o externo se controla mediante el bit SCS (System Clock Select) en el registro OSCCON, el cual define cuál será la fuente activa de reloj del sistema.

**3.Divisor de frecuencia (Divisor 1/N):**
Permite reducir la frecuencia de HFINTOSC. Los valores posibles son:

8MHz → 4MHz → 2MHz → 1MHz → 500kHz → 250kHz → 125kHz → 31kHz.

La selección se hace con los bits IRCF2:IRCF0 del registro OSCCON.

**4.Registro OSCCON:**
Bits IRCF2, IRCF1, IRCF0: seleccionan la frecuencia del oscilador interno.

Bit SCS (System Clock Select): selecciona la fuente de reloj del sistema (interna o externa).

**5.CPU**
Recibe la señal de reloj seleccionada y la usa para ejecutar instrucciones.

Adicionalmente cuenta con lógica de protección (temporizador de arranque, perro guardián (watchdog), monitor de fallo de reloj) que supervisa el funcionamiento del reloj.

Estabilidad y exactitud
El oscilador interno de un microcontrolador suele ser suficiente para aplicaciones básicas, pero puede ser impreciso (deriva con la temperatura).

Por eso a veces se usa un cristal externo (ej. 16 MHz, 20 MHz) para tener una frecuencia mucho más exacta.

### 2.2 Explicación del código implementado

### 2.3 Análisis y comparación

#### Tabla 1: Medición en frío

| Modo de oscilador | Freq. teórica Fosc | RA6 medible (CLKO)? | Freq. medida RA6 (Hz) | Freq. teórica RC0 (Hz)| Freq. medida RC0 (Hz) | Error RC0 (%) |  
|------------------|------------------|---------------------|---------------|---------------------|---------------|---------------|
| INTOSC (interno) | 16,000,000       | Sí                 |                     |                500                 |               |               | |
| HS (cristal externo 16 MHz) | 16,000,000 | No |     NA      |               500                 |               |               |
| RC externo       | ~16,000,000*     | No                                    |       N/A        | 500                 |               |               | |

#### Tabla 2: Medición con calor

| Modo de oscilador | Freq. teórica Fosc | RA6 medible (CLKO)? | Freq. medida RA6 (Hz) | Freq. teórica RC0 (Hz)| Freq. medida RC0 (Hz) | Error RC0 (%) |  
|------------------|------------------|---------------------|---------------|---------------------|---------------|---------------|
| INTOSC (interno) | 16,000,000       | Sí                 |                     |                500                 |               |               | |
| HS (cristal externo 16 MHz) | 16,000,000 | No |     NA      |               500                 |               |               |
| RC externo       | ~16,000,000*     | No                                    |       N/A        | 500                 |               |               | |

#### Tabla 3: Deriva

| Modo de oscilador |RC0 deriva (Hz) |
|------------------|--------------------|
| INTOSC (interno) |                    |                
| HS (cristal externo 16 MHz) |                |                |
| RC externo       |                 |                


<!-- Agregar tablas para valores usando PLL -->

<!-- Complemente con análisis de lo registrado en tablas -->

## 2.4 Diagramas

![alt text](image-3.png)
Fig 1.Arquitectura de selección del reloj [1]


![alt text](image-4.png)
Fig 2.Oscilador externo (modos LP,XT y HS)[1]


![alt text](image-5.png)
Fig 3.Oscilador externo modo RC [1]


![alt text](image-6.png)
Fig 4. Señal externa


![alt text](image-7.png)
Fig 5. Registro OSCCON [1]

## 2.5 Formas de onda

### INTOSC (interno) 

![alt text](plot_idle_Power_vs_1.png)

### HS

![alt text](image.png)

## RC

![alt text](image-1.png)

![alt text](image-2.png)

## 3. Evidencias de implementación

## 4. Preguntas

* ¿En qué modo se obtuvo la medición más cercana a la frecuencia teórica?

* ¿Fue posible evidenciar el fenómeno de deriva? ¿Qué factores podrían explicar la variación de frecuencia al calentar el PIC?

* ¿Cuál es más preciso en cuanto a frecuencia teórica vs. medida?


* Explique cómo usar RC0 para estimar la frecuencia del oscilador cuando RA6 no está disponible.

* Si se quisiera duplicar la frecuencia del PIC usando PLL, ¿en qué modos se podría aplicar?

* Enliste ventajas y desventajas de cada modo.

## 5. Referencias