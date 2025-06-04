# queimadas

Objetivos do Projeto
O principal objetivo deste projeto é criar um sistema de monitoramento de queimadas eficaz e de baixo custo. Para isso, são utilizados diversos sensores para coletar dados cruciais do ambiente:

Temperatura e Umidade: Essenciais para avaliar as condições climáticas e o risco de propagação do fogo.
Pressão Atmosférica: Pode indicar frentes de vento que influenciam a direção e velocidade de uma queimada.
Gases (CO2 e CH4): A presença e o aumento desses gases são fortes indicadores de combustão e de um incêndio em andamento, mesmo em estágios iniciais.
Todos esses dados são enviados para o ThingSpeak, uma plataforma de IoT que permite a visualização em gráficos, a criação de alertas e a análise temporal dos parâmetros monitorados. Isso possibilita uma detecção precoce de focos de incêndio e o acompanhamento das condições ambientais, auxiliando na prevenção e no combate a queimadas.

Materiais Necessários
Para montar este projeto, você precisará dos seguintes componentes:

Processador: ESP32 WEMOS D1 R32
Sensor de Temperatura e Umidade: DHT11 ou DHT22
Sensor de Gás Metano (CH4): MQ-4
Sensor de Qualidade do Ar (CO2/VOCs): MQ-135
Sensor de Pressão e Temperatura: BMP180 ou BMP280
Display: Liquid Crystal Display (LCD) com interface I2C (ex: 16x2 com módulo PCF8574)
Jumpers: Fios macho-macho e macho-fêmea
Resistor: 4.7kΩ ou 10kΩ (para o pino de DATA SCL e 3V do DHT)
Protoboard: Para montagem dos circuitos
Cabo Micro USB: Para alimentar e programar o ESP32
Montagem do Circuito
A montagem do circuito envolve a conexão de cada sensor e do display ao ESP32 WEMOS D1 R32. Siga as instruções e o diagrama de pinos cuidadosamente.

Conexões Comuns (Alimentação)
Conecte o pino GND de todos os sensores e do display ao pino GND do ESP32.
Conecte o pino 5V dos sensores MQ-4, MQ-135 e do Display ao pino 5V do ESP32.
Conecte o pino 3V dos sensores DHT e BMP ao pino 3V3 do ESP32.
Conexões Específicas dos Sensores e Display
Display Liquid Crystal (I2C):
SDA (Display)
rightarrow Pino SDA (GPIO21) do ESP32
SCL (Display)
rightarrow Pino SCL (GPIO22) do ESP32
Sensor MQ-4 (Gás Metano):
AO (Saída Analógica)
rightarrow Pino IO12 (GPIO12) do ESP32
Sensor MQ-135 (Qualidade do Ar/CO2):
AO (Saída Analógica)
rightarrow Pino IO13 (GPIO13) do ESP32
Sensor DHT (Temperatura e Umidade):
DATA (Saída Digital)
rightarrow Pino SCL (GPIO22) do ESP32 (Obs: O pino SCL do ESP32 é GPIO22. O código fornecido usa DHTPIN 18. Para evitar conflito com o I2C que usa GPIO22/SCL, é crucial que o pino do DHT seja o GPIO18 conforme definido no código (#define DHTPIN 18). Se você conectar o DHT ao GPIO22, o I2C pode falhar. Por favor, conecte o DHT ao GPIO18 do ESP32.)
Resistor: Conecte um resistor de 4.7kΩ ou 10kΩ entre o pino DATA do DHT e o pino 3V3 do ESP32 (ou a linha de 3V na protoboard).
Sensor BMP180/BMP280 (Pressão e Temperatura):
SDA (BMP)
rightarrow Pino SDA (GPIO21) do ESP32
SCL (BMP)
rightarrow Pino SCL (GPIO22) do ESP32
Como Utilizar o Código (Arduino IDE)
1. Preparação da Arduino IDE
Instalar o Core ESP32:

Vá em Arquivo > Preferências.
No campo "URLs Adicionais para Gerenciadores de Placas", adicione a seguinte URL:
https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json
Clique em "OK".
Vá em Ferramentas > Placa > Gerenciador de Placas....
Procure por "esp32" e instale o pacote "esp32 by Espressif Systems".
Instalar Bibliotecas Necessárias:

Vá em Sketch > Incluir Biblioteca > Gerenciar Bibliotecas....
Pesquise e instale as seguintes bibliotecas:
DHT sensor library by Adafruit
Adafruit Unified Sensor
Adafruit BMP085 Unified (se estiver usando o BMP180) OU Adafruit BMP280 Library (se estiver usando o BMP280)
LiquidCrystal I2C by Frank de Brabander
ThingSpeak by MathWorks
WiFi (já faz parte do core ESP32, mas é bom verificar)
2. Configuração do Código
Abra o Código: Carregue o arquivo .ino (o código-fonte do projeto) na Arduino IDE.

Configurações do ThingSpeak:

Crie uma conta no ThingSpeak e um novo "Channel".
Anote o Channel ID e o Write API Key do seu canal.
No código, localize as linhas abaixo e substitua os valores pelos seus próprios:
<!-- end list -->

C++

// Configurações do ThingSpeak
const char* apiKey = "SUA_API_KEY_AQUI"; // <<-- MODIFIQUE AQUI com sua Write API Key do ThingSpeak
const long channelID = SEU_CHANNEL_ID_AQUI;   // <<-- MODIFIQUE AQUI com o ID do seu canal ThingSpeak
Configurações de Rede Wi-Fi:

No código, localize as seguintes linhas e insira o nome da sua rede Wi-Fi (SSID) e a senha (PASSWORD):
<!-- end list -->

C++

const char* ssid = "SEU_NOME_DA_REDE_WIFI";    // <<-- MODIFIQUE AQUI com o nome da sua rede Wi-Fi
const char* password = "SUA_SENHA_WIFI"; // <<-- MODIFIQUE AQUI com a senha da sua rede Wi-Fi
Ajustes de Pinos (se necessário):

Verifique se a definição do pino do DHT está correta: #define DHTPIN 18. Certifique-se de que o fio de dados do seu sensor DHT está realmente conectado ao GPIO18 do ESP32.
Os sensores MQ-4 e MQ-135 estão lendo do analogRead(34). Se você tiver esses sensores conectados a pinos analógicos diferentes, você precisará ajustar as linhas analogRead(34) para os pinos corretos (por exemplo, analogRead(35) para o MQ-135, se ele estiver no GPIO35). No código fornecido, tanto CO2 (CO2_raw) quanto CH4 (CH4_raw) estão lendo do pino 34. Isso geralmente não é o ideal, pois dois sensores de gás distintos devem estar em pinos analógicos diferentes para leituras independentes. Recomenda-se conectar o MQ-4 (CH4) em GPIO34 e o MQ-135 (CO2) em GPIO35 e ajustar o código para ler de ambos os pinos corretamente. Por exemplo:
C++

// NO ARDUINO IDE, MUDE ESTAS LINHAS:
// float CO2_raw = analogRead(34); // Para o MQ-135 (CO2)
// float CH4_raw = analogRead(34); // Para o MQ-4 (CH4)

// SE VOCÊ CONECTOU MQ-135 NO GPIO35 E MQ-4 NO GPIO34:
float CO2_raw = analogRead(35); // Exemplo: MQ-135 conectado ao GPIO35
float CH4_raw = analogRead(34); // Exemplo: MQ-4 conectado ao GPIO34
3. Upload do Código
Selecione a Placa: Vá em Ferramentas > Placa > ESP32 Arduino e selecione "WEMOS D1 R32" (ou a placa ESP32 equivalente que você está usando).
Selecione a Porta: Conecte o ESP32 ao seu computador via cabo USB. Vá em Ferramentas > Porta e selecione a porta serial correspondente ao ESP32.
Fazer Upload: Clique no botão "Upload" (seta para a direita) na Arduino IDE. Aguarde o processo de compilação e upload.
Monitoramento no ThingSpeak
Após o upload do código e a conexão do ESP32 à sua rede Wi-Fi, o dispositivo começará a enviar os dados dos sensores para o seu canal no ThingSpeak. Você poderá:

Visualizar Gráficos: Acesse seu canal no ThingSpeak para ver os dados de temperatura, umidade, pressão, altitude, CO2 e CH4 plotados em gráficos em tempo real.
Configurar Alertas: Crie "Reacts" no ThingSpeak para receber notificações (por e-mail, Twitter, etc.) quando os valores dos sensores excederem determinados limites (por exemplo, alta temperatura ou concentração de gases).
Análise de Dados: Utilize as ferramentas do ThingSpeak para analisar o histórico dos dados e identificar padrões.
