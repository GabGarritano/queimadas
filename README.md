---

# Sensor de Queimada com ESP32 e ThingSpeak

## 🚀 **BAIXE O CÓDIGO AQUI:** [codigo_final.ino](https://github.com/GabGarritano/queimadas/blob/main/codigo_final.ino)

---

Este projeto oferece uma solução para o monitoramento de **queimadas** utilizando um conjunto de sensores conectados a um **ESP32 WEMOS D1 R32**. Os dados de temperatura, umidade, pressão, CO2 e CH4 são coletados e enviados para a plataforma **ThingSpeak**, permitindo a visualização e análise em tempo real.

---

## 1. Objetivos do Projeto

Nosso principal objetivo é criar um sistema de **monitoramento de queimadas** eficaz e de baixo custo. Para isso, utilizamos diversos sensores para coletar dados cruciais do ambiente:

* **Temperatura e Umidade:** Essenciais para avaliar as condições climáticas e o risco de propagação do fogo.
* **Pressão Atmosférica:** Pode indicar frentes de vento que influenciam a direção e velocidade de uma queimada.
* **Gases (CO2 e CH4):** A presença e o aumento desses gases são fortes indicadores de combustão e de um incêndio em andamento, mesmo em estágios iniciais.

Todos esses dados são enviados para o **ThingSpeak**, uma plataforma de IoT que permite a visualização em gráficos, a criação de alertas e a análise temporal dos parâmetros monitorados. Isso possibilita uma **detecção precoce de focos de incêndio** e o acompanhamento das condições ambientais, auxiliando na prevenção e no combate a queimadas.

---

## 2. Materiais Necessários

Para montar este projeto, você precisará dos seguintes componentes:

* **Processador:** ESP32 WEMOS D1 R32
* **Sensor de Temperatura e Umidade:** DHT11 ou DHT22
* **Sensor de Gás Metano (CH4):** MQ-4
* **Sensor de Qualidade do Ar (CO2/VOCs):** MQ-135
* **Sensor de Pressão e Temperatura:** BMP180 ou BMP280
* **Display:** Liquid Crystal Display (LCD) com interface I2C (ex: 16x2 com módulo PCF8574)
* **Jumpers:** Fios macho-macho e macho-fêmea
* **Resistor:** 4.7kΩ ou 10kΩ (para o pino de DATA do DHT e 3V)
* **Protoboard:** Para montagem dos circuitos
* **Cabo Micro USB:** Para alimentar e programar o ESP32

---

## 3. Montagem do Circuito

A montagem do circuito envolve a conexão de cada sensor e do display ao **ESP32 WEMOS D1 R32**. Siga as instruções e o diagrama de pinos cuidadosamente.

### 3.1. Conexões Comuns (Alimentação)

* Conecte o pino **GND** de todos os sensores e do display ao pino **GND** do ESP32.
* Conecte o pino **5V** dos sensores MQ-4, MQ-135 e do Display ao pino **5V** do ESP32.
* Conecte o pino **3V** dos sensores DHT e BMP ao pino **3V3** do ESP32.

### 3.2. Conexões Específicas dos Sensores e Display

* **Display Liquid Crystal (I2C):**
    * **SDA** (Display) $\rightarrow$ Pino **SDA** (GPIO21) do ESP32
    * **SCL** (Display) $\rightarrow$ Pino **SCL** (GPIO22) do ESP32

* **Sensor MQ-4 (Gás Metano):**
    * **AO** (Saída Analógica) $\rightarrow$ Pino **IO12** (GPIO12) do ESP32

* **Sensor MQ-135 (Qualidade do Ar/CO2):**
    * **AO** (Saída Analógica) $\rightarrow$ Pino **IO13** (GPIO13) do ESP32

* **Sensor DHT (Temperatura e Umidade):**
    * **DATA** (Saída Digital) $\rightarrow$ Pino **GPIO18** do ESP32
        * **Obs:** O código fornecido usa `#define DHTPIN 18`. Para evitar conflito com os pinos I2C (GPIO21/SDA e GPIO22/SCL), é **crucial** que o pino de dados do DHT seja o **GPIO18** conforme definido no código. Conectar o DHT a outro pino I2C pode causar falhas na comunicação.
    * **Resistor:** Conecte um resistor de 4.7kΩ ou 10kΩ entre o pino **DATA** do DHT e o pino **3V3** do ESP32 (ou a linha de 3V na protoboard).

* **Sensor BMP180/BMP280 (Pressão e Temperatura):**
    * **SDA** (BMP) $\rightarrow$ Pino **SDA** (GPIO21) do ESP32
    * **SCL** (BMP) $\rightarrow$ Pino **SCL** (GPIO22) do ESP32

---

## 4. Como Utilizar o Código (Arduino IDE)

### 4.1. Preparação da Arduino IDE

1.  **Instalar o Core ESP32:**
    * Vá em `Arquivo > Preferências`.
    * No campo "URLs Adicionais para Gerenciadores de Placas", adicione a seguinte URL:
        ```
        [https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json](https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json)
        ```
    * Clique em "OK".
    * Vá em `Ferramentas > Placa > Gerenciador de Placas...`.
    * Procure por "esp32" e instale o pacote "**esp32 by Espressif Systems**".

2.  **Instalar Bibliotecas Necessárias:**
    * Vá em `Sketch > Incluir Biblioteca > Gerenciar Bibliotecas...`.
    * Pesquise e instale as seguintes bibliotecas:
        * **`DHT sensor library by Adafruit`**
        * **`Adafruit Unified Sensor`**
        * **`Adafruit BMP085 Unified`** (se estiver usando o BMP180) **OU** `Adafruit BMP280 Library` (se estiver usando o BMP280)
        * **`LiquidCrystal I2C by Frank de Brabander`**
        * **`ThingSpeak by MathWorks`**
        * **`WiFi`** (já faz parte do core ESP32, mas é bom verificar se está habilitado)

### 4.2. Configuração do Código

1.  **Abra o Código:** Carregue o arquivo `.ino
