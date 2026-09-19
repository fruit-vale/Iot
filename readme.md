# Monitor de Temperatura e Umidade (ESP32 + DHT11 + ThingSpeak)

Projeto de IoT que lê temperatura e umidade de um sensor DHT11 conectado a um ESP32 e publica os dados na plataforma [ThingSpeak](https://thingspeak.com/) via protocolo **MQTT**, permitindo visualizar as leituras em tempo real em um dashboard na nuvem.

## Como funciona

1. **Conexão Wi-Fi**: ao ligar, o ESP32 se conecta à rede Wi-Fi configurada (`conectarWiFi()`), tentando repetidamente até obter sucesso.
2. **Conexão MQTT**: em seguida, conecta ao broker MQTT do ThingSpeak (`mqtt3.thingspeak.com`, porta `1883`) usando as credenciais do canal (`conectarMQTT()`).
3. **Leitura do sensor**: a cada ciclo do `loop()`, o código lê temperatura e umidade do sensor DHT11 (pino `4`).
4. **Publicação dos dados**: se a leitura for válida, monta um payload no formato `field1=<temperatura>&field2=<umidade>` e publica no tópico `channels/<ID_DO_CANAL>/publish`, onde:
   - `field1` = temperatura (°C)
   - `field2` = umidade (%)
5. **Repetição**: o processo se repete a cada 16 segundos (`delay(16000)`), respeitando o limite mínimo de intervalo entre atualizações do ThingSpeak (15s).
6. **Reconexão automática**: se a conexão Wi-Fi ou MQTT cair, o código tenta reconectar automaticamente antes de continuar as leituras.

## Hardware necessário

- ESP32
- Sensor de temperatura e umidade DHT11
- Conexão do pino de dados do DHT11 ao **GPIO 4** do ESP32

## Bibliotecas utilizadas (Arduino IDE)

- [`DHT sensor library`](https://github.com/adafruit/DHT-sensor-library) (Adafruit)
- `WiFi.h` (nativa do ESP32)
- [`PubSubClient`](https://github.com/knolleary/pubsubclient) (cliente MQTT)

## Estrutura do projeto

| Arquivo | Descrição |
|---|---|
| `codigo.ino` | Código principal: conexão Wi-Fi/MQTT, leitura do sensor e publicação no ThingSpeak |
| `arduino_secrets.h` | Credenciais sensíveis (Wi-Fi e MQTT/ThingSpeak) — **não deve ser commitado** |

## Configuração

Crie um arquivo `arduino_secrets.h` na mesma pasta do `.ino` com o seguinte formato:

```cpp
#define SECRET_SSID "nome-da-sua-rede-wifi"
#define SECRET_PASS "senha-da-sua-rede-wifi"

#define SECRET_MQTT_CLIENT_ID "seu-client-id-do-thingspeak"
#define SECRET_MQTT_USERNAME  "seu-usuario-mqtt-do-thingspeak"
#define SECRET_MQTT_PASSWORD  "sua-senha-mqtt-do-thingspeak"

#define SECRET_CHANNEL_ID 0000000 // ID do canal no ThingSpeak
```

As credenciais MQTT (`Client ID`, `Username`, `Password`) e o `Channel ID` são obtidos na página do seu canal no ThingSpeak, na aba **API Keys** / **MQTT**.

> ⚠️ **Importante**: `arduino_secrets.h` contém credenciais reais e não deve ser versionado no Git. Adicione-o ao `.gitignore`.

## Uso

1. Configure o `arduino_secrets.h` conforme acima.
2. Abra `codigo.ino` na Arduino IDE (ou PlatformIO) com o board ESP32 selecionado.
3. Instale as bibliotecas listadas acima.
4. Faça o upload para a placa.
5. Abra o Serial Monitor (115200 baud) para acompanhar a conexão e as publicações.
6. Visualize os dados no gráfico do canal do ThingSpeak.
