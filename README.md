# Sensor de Queimada com ESP32 e ThingSpeak

Este projeto oferece uma solução para o monitoramento de queimadas utilizando um conjunto de sensores conectados a um **ESP32 WEMOS D1 R32**. Os dados de temperatura, umidade, pressão, CO2 e CH4 são coletados e enviados para a plataforma **ThingSpeak**, permitindo a visualização e análise em tempo real.

---

## 1. Objetivos do Projeto

O principal objetivo deste projeto é criar um sistema de **monitoramento de queimadas** eficaz e de baixo custo. Para isso, são utilizados diversos sensores para coletar dados cruciais do ambiente:

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

1.  **Abra o Código:** Carregue o arquivo `.ino` (o código-fonte do projeto) na Arduino IDE.

2.  **Configurações do ThingSpeak:**
    * Crie uma conta no [ThingSpeak](https://thingspeak.com/) e um novo "Channel".
    * Anote o **`Channel ID`** e o **`Write API Key`** do seu canal.
    * No código, localize as linhas abaixo e **substitua os valores pelos seus próprios**:

    ```cpp
    // --- INÍCIO DAS CONFIGURAÇÕES DO USUÁRIO ---

    // Configurações do ThingSpeak
    // ##################################################################################
    // MODIFIQUE AQUI: Substitua "SUA_API_KEY_AQUI" pela sua Write API Key do ThingSpeak
    const char* apiKey = "SUA_API_KEY_AQUI"; 
    // MODIFIQUE AQUI: Substitua "SEU_CHANNEL_ID_AQUI" pelo ID do seu canal ThingSpeak
    const long channelID = SEU_CHANNEL_ID_AQUI;   
    // ##################################################################################
    ```

3.  **Configurações de Rede Wi-Fi:**
    * No código, localize as seguintes linhas e **insira o nome da sua rede Wi-Fi (`SSID`) e a senha (`PASSWORD`)**:

    ```cpp
    // Configurações de Rede Wi-Fi
    // ##################################################################################
    // MODIFIQUE AQUI: Substitua "SEU_NOME_DA_REDE_WIFI" pelo nome da sua rede Wi-Fi
    const char* ssid = "SEU_NOME_DA_REDE_WIFI";    
    // MODIFIQUE AQUI: Substitua "SUA_SENHA_WIFI" pela senha da sua rede Wi-Fi
    const char* password = "SUA_SENHA_WIFI"; 
    // ##################################################################################

    // --- FIM DAS CONFIGURAÇÕES DO USUÁRIO ---
    ```

4.  **Ajustes de Pinos dos Sensores de Gás (Crucial):**
    * No código fornecido, tanto a leitura de CO2 (MQ-135) quanto a de CH4 (MQ-4) estão apontando para `analogRead(34)`. **Isso significa que ambos os sensores estariam lendo do mesmo pino analógico, o que não é o ideal para obter dados independentes.**
    * **Recomendação:** Conecte o sensor **MQ-4 (CH4)** no pino analógico **GPIO34** do ESP32 e o sensor **MQ-135 (CO2)** no pino analógico **GPIO35** do ESP32.
    * Após conectar fisicamente, você precisará ajustar o código nas seções de leitura dos gases:

    ```cpp
    // NO CÓDIGO (localizado dentro da função loop()), MODIFIQUE ESTAS LINHAS:

    // ---------------- Leitura Sensor CO2 (MQ-135) ----------------
    // ATENÇÃO: Ajuste o pino analógico conforme a sua conexão física (ex: GPIO35)
    float CO2_raw = analogRead(35); // Exemplo: MQ-135 conectado ao GPIO35

    // ... (restante do código da média móvel para CO2) ...

    // ---------------- Leitura Sensor MQ4 (CH4) ----------------
    // ATENÇÃO: Ajuste o pino analógico conforme a sua conexão física (ex: GPIO34)
    float CH4_raw = analogRead(34); // Exemplo: MQ-4 conectado ao GPIO34

    // ... (restante do código da média móvel para CH4) ...
    ```

### 4.3. Upload do Código

1.  **Selecione a Placa:** Vá em `Ferramentas > Placa > ESP32 Arduino` e selecione "**WEMOS D1 R32**" (ou a placa ESP32 equivalente que você está usando).
2.  **Selecione a Porta:** Conecte o ESP32 ao seu computador via cabo USB. Vá em `Ferramentas > Porta` e selecione a porta serial correspondente ao ESP32.
3.  **Fazer Upload:** Clique no botão "Upload" (seta para a direita) na Arduino IDE. Aguarde o processo de compilação e upload.

---

## 5. Monitoramento no ThingSpeak

Após o upload do código e a conexão do ESP32 à sua rede Wi-Fi, o dispositivo começará a enviar os dados dos sensores para o seu canal no ThingSpeak. Você poderá:

* **Visualizar Gráficos:** Acesse seu canal no ThingSpeak para ver os dados de temperatura, umidade, pressão, altitude, CO2 e CH4 plotados em gráficos em tempo real.
* **Configurar Alertas:** Crie "Reacts" no ThingSpeak para receber notificações (por e-mail, Twitter, etc.) quando os valores dos sensores excederem determinados limites (por exemplo, alta temperatura ou concentração de gases).
* **Análise de Dados:** Utilize as ferramentas do ThingSpeak para analisar o histórico dos dados e identificar padrões.

---

## Código Fonte (Copie e Cole na Arduino IDE)

```cpp
//#include "BluetoothSerial.h"       // Biblioteca para Bluetooth Serial (comentada no original)
#include <LiquidCrystal_I2C.h>
#include <Wire.h>
#include <Adafruit_Sensor.h>
#include <Adafruit_BMP280.h>
#include "DHT.h"
#include "ThingSpeak.h"
#include <WiFi.h>

//BluetoothSerial SerialBT;            // Objeto para comunicação Bluetooth (comentado no original)
Adafruit_BMP280 bmp;                   // Objeto do sensor BMP280

#define I2C_SDA 21
#define I2C_SCL 22

#define DHTPIN 18                      // Pino para o sensor DHT. Conecte o DATA do DHT aqui.
#define DHTTYPE DHT11                  // Tipo de sensor DHT (DHT11 ou DHT22)

LiquidCrystal_I2C lcd(0x27, 16, 2);    // Endereço I2C do LCD: 0x27, 16 colunas, 2 linhas. Verifique o endereço do seu LCD.

DHT dht(DHTPIN, DHTTYPE);

// Variáveis para média móvel DHT e BMP
const int numReadingsDHT = 10;
float tempReadings[numReadingsDHT] = {0};
float pressReadings[numReadingsDHT] = {0};
float altReadings[numReadingsDHT] = {0};
float umidReadings[numReadingsDHT] = {0};
int readIndexDHT = 0;
float tempTotal = 0, pressTotal = 0, altTotal = 0, umidTotal = 0;
float tempMedia = 0, pressMedia = 0, altMedia = 0, umidMedia = 0;


// Variáveis para controle do tempo de envio ao ThingSpeak
unsigned long lastTime = 0;            // Tempo da última execução do envio
const unsigned long interval = 30000; // Intervalo de 30 segundos (em milissegundos)

// --- INÍCIO DAS CONFIGURAÇÕES DO USUÁRIO ---

// Configurações do ThingSpeak
// ##################################################################################
// MODIFIQUE AQUI: Substitua "SUA_API_KEY_AQUI" pela sua Write API Key do ThingSpeak
const char* apiKey = "SUA_API_KEY_AQUI"; 
// MODIFIQUE AQUI: Substitua "SEU_CHANNEL_ID_AQUI" pelo ID do seu canal ThingSpeak
const long channelID = SEU_CHANNEL_ID_AQUI;   
// ##################################################################################

// Configurações de Rede Wi-Fi
// ##################################################################################
// MODIFIQUE AQUI: Substitua "SEU_NOME_DA_REDE_WIFI" pelo nome da sua rede Wi-Fi
const char* ssid = "SEU_NOME_DA_REDE_WIFI";    
// MODIFIQUE AQUI: Substitua "SUA_SENHA_WIFI" pela senha da sua rede Wi-Fi
const char* password = "SUA_SENHA_WIFI"; 
// ##################################################################################

// --- FIM DAS CONFIGURAÇÕES DO USUÁRIO ---

WiFiClient client; // Objeto WiFiClient

int led = 2; // LED de status (geralmente o LED_BUILTIN do ESP32 ou pino 2)
int cont;                                  // Variável para contagem de tentativas de conexão WiFi
boolean pisca = false;

// Variáveis para média móvel CO2 (MQ-135)
const int numReadingsCO2 = 12;
float CO2Readings[numReadingsCO2] = {0};
int readIndexCO2 = 0;
float CO2Total = 0;
float CO2Media = 0;

// Variáveis para média móvel CH4 (MQ-4)
const int numReadingsCH4 = 13;
float CH4Readings[numReadingsCH4] = {0};
int readIndexCH4 = 0;
float CH4Total = 0;
float CH4Media = 0;

// Protótipo da função email - Implemente-a se for utilizá-la
// void email(); 

void setup() {
    pinMode(led, OUTPUT);
    Serial.begin(115200);
    Serial.println("IMT 2025 - Eng. Computacao - Projeto Final");

    //SerialBT.begin("IMT_Grupo_X"); // Nome do Bluetooth (descomente se for usar Bluetooth)
    //Serial.println("Bluetooth Serial iniciado! Conecte-se ao ESP32.");

    // Inicialização do sensor BMP280. Endereços I2C comuns são 0x76 ou 0x77.
    if (!bmp.begin(0x76)) { 
        Serial.println("Erro ao encontrar BMP280 no endereco 0x76. Tentando 0x77...");
        if (!bmp.begin(0x77)) {
            Serial.println("Tentativa com 0x77 tambem falhou. Sensor BMP280 nao encontrado.");
            while (1); // Trava se não encontrar o sensor
        } else {
            Serial.println("BMP280 encontrado no endereco 0x77.");
        }
    } else {
        Serial.println("BMP280 encontrado no endereco 0x76.");
    }
    
    // Configurações de amostragem do BMP280
    bmp.setSampling(Adafruit_BMP280::MODE_NORMAL,
                    Adafruit_BMP280::SAMPLING_X2,  // Temperatura
                    Adafruit_BMP280::SAMPLING_X16, // Pressão
                    Adafruit_BMP280::FILTER_X16,   // Filtro IIR
                    Adafruit_BMP280::STANDBY_MS_500); // Tempo de standby

    // Inicializa a comunicação I2C com os pinos definidos (GPIO21 para SDA, GPIO22 para SCL)
    Wire.begin(I2C_SDA, I2C_SCL); 
    lcd.init(); // Inicializa o display LCD (use lcd.begin() se lcd.init() não funcionar)
    lcd.backlight(); // Liga a luz de fundo do LCD
    lcd.setCursor(0, 0);
    lcd.print("IMT 2025");
    lcd.setCursor(0, 1);
    lcd.print("Projeto Final");
    delay(2000);
    lcd.clear();
    dht.begin(); // Inicializa o sensor DHT

    // Início da conexão Wi-Fi
    WiFi.mode(WIFI_STA);
    WiFi.begin(ssid, password);

    cont = 0;
    Serial.print("Setup - Iniciando a conexao WiFi");
    lcd.setCursor(0, 0);
    lcd.print("Conectando WiFi");
    while (WiFi.status() != WL_CONNECTED) {
        pisca = !pisca;
        digitalWrite(led, pisca); // Pisca LED azul durante a tentativa

        Serial.print(".");  
        cont++;
        
        lcd.setCursor(0, 1);
        lcd.print("Tentativa: ");
        lcd.print(cont);
        if (cont % 10 == 0) { // Limpa a linha de tentativa para não sobrepor números grandes
            lcd.print("          ");  
            lcd.setCursor(12,1); // Reposiciona para o número
        }
        delay(500); // Reduzido o delay na tentativa de conexão
        if (cont > 60) { // Timeout para tentativa de conexão (e.g., 30 segundos)
            Serial.println("\nFalha ao conectar ao WiFi apos varias tentativas.");
            lcd.clear();
            lcd.setCursor(0,0);
            lcd.print("WiFi Falhou");
            while(1); // Trava o programa se a conexão Wi-Fi falhar após várias tentativas
        }
    }

    digitalWrite(led, LOW); // Apaga o LED azul após conectar
    WiFi.setAutoReconnect(true); // Habilita reconexão automática
    WiFi.persistent(true); // Salva credenciais Wi-Fi na memória não volátil
    Serial.println("\nWiFi conectado!");
    Serial.print("SSID: "); Serial.println(WiFi.SSID());
    Serial.print("IP Address: "); Serial.println(WiFi.localIP());
    Serial.println();

    lcd.clear();
    lcd.setCursor(0, 0);
    lcd.print("WiFi Conectado!");
    lcd.setCursor(0, 1);
    lcd.print(WiFi.localIP());
    delay(3000);
    lcd.clear();

    ThingSpeak.begin(client); // Inicializa a comunicação com ThingSpeak
}


void loop() {
    unsigned long currentTime = millis();

    // Piscar LED de status (pino 'led') como indicador de atividade do loop
    digitalWrite(led, HIGH);
    delay(50);  
    digitalWrite(led, LOW);
    
    // ---------------- Leitura Sensor CO2 (MQ-135) ----------------
    // ATENÇÃO: Ajuste o pino analógico conforme a sua conexão física (ex: GPIO35 para MQ-135)
    float CO2_raw = analogRead(35); // Conecte o MQ-135 (CO2) ao pino analógico GPIO35

    CO2Total -= CO2Readings[readIndexCO2];
    CO2Readings[readIndexCO2] = CO2_raw;
    CO2Total += CO2_raw;
    readIndexCO2 = (readIndexCO2 + 1) % numReadingsCO2;
    CO2Media = CO2Total / numReadingsCO2;

    Serial.print("CO2 Media (raw): ");  
    Serial.println(CO2Media);

    // ---------------- Leitura Sensores BMP280 e DHT11 ----------------
    float temperatura = bmp.readTemperature();
    float pressao = bmp.readPressure() / 100.0F; // Convertendo para hPa
    float altitude = bmp.readAltitude(1013.25);  // Pressão ao nível do mar padrão (ajuste se necessário)
    float umidade = dht.readHumidity();    

    if (isnan(temperatura) || isnan(umidade) || isnan(pressao) || isnan(altitude)) {
        Serial.println("Falha ao ler um dos sensores BMP/DHT!");
        // Não atualizar médias com NaN para evitar dados inválidos.
    } else {
        tempTotal -= tempReadings[readIndexDHT];
        pressTotal -= pressReadings[readIndexDHT];
        altTotal -= altReadings[readIndexDHT];
        umidTotal -= umidReadings[readIndexDHT];
        
        tempReadings[readIndexDHT] = temperatura;
        pressReadings[readIndexDHT] = pressao;
        altReadings[readIndexDHT] = altitude;
        umidReadings[readIndexDHT] = umidade;
        
        tempTotal += temperatura;
        pressTotal += pressao;
        altTotal += altitude;
        umidTotal += umidade;
        
        readIndexDHT = (readIndexDHT + 1) % numReadingsDHT;
        
        // Calcula médias
        tempMedia = tempTotal / numReadingsDHT;
        pressMedia = pressTotal / numReadingsDHT;
        altMedia = altTotal / numReadingsDHT;
        umidMedia = umidTotal / numReadingsDHT;
    }

    Serial.print("Temperatura Media: "); Serial.print(tempMedia); Serial.println(" C");
    Serial.print("Pressao Media: "); Serial.print(pressMedia); Serial.println(" hPa");
    Serial.print("Altitude Media: "); Serial.print(altMedia); Serial.println(" m");
    Serial.print("Umidade Media: "); Serial.print(umidMedia); Serial.println(" %");
    Serial.println(" ");  
    
    // ---------------- Leitura Sensor CH4 (MQ-4) ----------------
    // ATENÇÃO: Ajuste o pino analógico conforme a sua conexão física (ex: GPIO34 para MQ-4)
    float CH4_raw = analogRead(34);  // Conecte o MQ-4 (CH4) ao pino analógico GPIO34
    
    CH4Total -= CH4Readings[readIndexCH4];
    CH4Readings[readIndexCH4] = CH4_raw;
    CH4Total += CH4_raw;
    readIndexCH4 = (readIndexCH4 + 1) % numReadingsCH4;
    CH4Media = CH4Total / numReadingsCH4;

    Serial.print("CH4 Media (raw): ");  
    Serial.println(CH4Media);
    Serial.println(" "); 

    // --- Alerta por Email (MQ4) ---
    if (CH4Media > 2453) { // Valor de limiar exemplo. Ajuste conforme sua calibração.
        Serial.println("Nivel de CH4 alto! (Valor raw > 2453)");
        // email(); // Descomente se a função email() estiver implementada e configurada
    }

    // --- Atualização do LCD com dados (exemplo simples) ---
    lcd.setCursor(0, 0);
    lcd.print("T:"); lcd.print(tempMedia, 1); // Temp com 1 casa decimal
    lcd.print(" U:"); lcd.print(umidMedia, 0); // Umid sem casas decimais
    lcd.print("%");
    lcd.setCursor(0, 1);
    lcd.print("CO2:"); lcd.print(CO2Media,0); // CO2 raw sem casas decimais
    lcd.print(" CH4:");lcd.print(CH4Media,0); // CH4 raw sem casas decimais


    // --- Envio para ThingSpeak ---
    if (currentTime - lastTime >= interval) {
        lastTime = currentTime; 

        Serial.println("Enviando dados para ThingSpeak...");

        ThingSpeak.setField(1, CO2Media);    // Campo 1 para CO2
        ThingSpeak.setField(2, tempMedia);   // Campo 2 para Temperatura
        ThingSpeak.setField(3, pressMedia);  // Campo 3 para Pressão
        ThingSpeak.setField(4, altMedia);    // Campo 4 para Altitude
        ThingSpeak.setField(5, umidMedia);   // Campo 5 para Umidade
        ThingSpeak.setField(6, CH4Media);    // Campo 6 para CH4
        // Adicione mais campos se necessário (ThingSpeak.setField(7, valor7); etc.)

        int statusCode = ThingSpeak.writeFields(channelID, apiKey);
        
        lcd.clear(); 
        if (statusCode == 200) {
            Serial.println("Dados enviados com sucesso para ThingSpeak!");
            lcd.setCursor(0, 0);
            lcd.print("ThingSpeak: OK");
            lcd.setCursor(0, 1);
            lcd.print("Dados Enviados!");
        } else {
            Serial.print("Erro ao enviar dados para ThingSpeak: HTTP ");
            Serial.println(statusCode);
            lcd.setCursor(0, 0);
            lcd.print("ThingSpeak Falha");
            lcd.setCursor(0, 1);
            lcd.print("Erro HTTP: ");
            lcd.print(statusCode);
        }
        delay(2500); // Mostrar mensagem de status no LCD por um tempo
        lcd.clear(); // Limpa para a próxima atualização de dados dos sensores
    }
    delay(200); // Pequeno delay geral no loop para não sobrecarregar leituras/processamento.
                // Ajuste conforme a necessidade de resposta dos sensores e do sistema.
}****
