# GS 2025 - Edge

## Integrantes 1ESPS
- Gabrielly Candido RM: 560916
- Luiza Ribeiro RM: 560200

## Descrição do Problema
Enchentes em áreas urbanas causam prejuízos graves e colocam vidas em risco. A falta de monitoramento em tempo real dificulta a reação preventiva por parte da Defesa Civil e da população.

## Solução Proposta - ChuvaSegura
Desenvolvemos um sistema IoT usando ESP32 e sensores (ultrassônico, DHT22 e chuva) que monitora o ambiente em tempo real. Os dados são enviados para a plataforma TagoIO, onde são visualizados em dashboards e utilizados para acionar alertas automáticos por e-mail/sms para a Defesa Civil.

## Arquitetura em Camadas

> O sistema está organizado em três camadas principais: IoT, Back-End e Aplicação.
> Portal Web: 

## Código do Microcontrolador

O código completo está no arquivo CODIGO e é possível acessar pelo link do Wokwi: https://wokwi.com/projects/432057679999717377
![image](https://github.com/user-attachments/assets/43b8c203-db97-4d3d-8614-31ee80ca5a6c)

## Simulação
Você pode simular no Wokwi ou fisicamente com os sensores. 
O LED e o LCD fornecem feedback local, já os dados são enviados à nuvem e alertas por e-mail/sms são disparados automaticamente.

## Estrutura do projeto de Arduino -  Especificações Técnicas

### Instruções de Execução
1. Carregue o código no Wokwi ou na IDE Arduino.
2. Certifique-se que o Wi-Fi esteja correto.
3. Configure o Token da TagoIO no código.
4. No TagoIO:
   - Crie um device.
   - Configure um dashboard para visualizar os dados.
   - Crie uma Action para enviar e-mail se `nivel_agua` less than `10`. (De a acordo com a regra estabelecida)

### Diagrama da arquitetura e fluxo do projeto
![SP4-Challenge2025-ArduinoGS2025 drawio (1)](https://github.com/user-attachments/assets/2024dac3-cb60-4b7c-8e39-52590d721ab3)

### Bibliotecas Utilizadas
- #include <Wire.h>: Comunicação I2C.
- #include <LiquidCrystal_I2C.h>: Comunicação com o display LCD.
- #include <WiFi.h>: Conexão do ESP32 com rede Wi-Fi.
- #include <HTTPClient.h>: Realiza requisições HTTP.
- #include <WiFiClientSecure.h>: Permite enviar dados com HTTPS.

### Camada IoT - Componentes Utilizados
- ESP32 DevKit
- Sensor ultrassônico (nível da água)
- Sensor DHT22 (temperatura/umidade)
- Sensor de chuva (simulado no Wokwi)
- Display LCD I2C 16x2
- LED indicador

### Camada Back-End (Plataforma de IoT)
- Plataforma TagoIO (dashboard + trigger de e-mail/sms)
  - Recebe os dados via protocolo HTTPS com autenticação via token.
  - Armazena os dados em buckets.
  - Permite criar dashboards interativos.
  - Envia notificações automáticas via Actions.

## Explicação das Funções Principais

O código do projeto é dividido em duas funções principais da programação embarcada: `setup()` e `loop()`. Abaixo está a explicação detalhada de cada uma:

---

### void setup()

A função `setup()` é executada **uma única vez** no início da execução. Sua principal função é **inicializar os componentes e configurar os pinos**. Veja o que acontece nela:

- `Serial.begin(115200)`: Inicia a comunicação serial para debug.
- `pinMode(...)`: Configura os pinos digitais usados (trigger/echo do sensor ultrassônico, sensor de chuva, LED).
- `dht.begin()`: Inicializa o sensor de temperatura e umidade (DHT22).
- `Wire.begin(SDA, SCL)`: Inicializa a comunicação I2C com o display LCD.
- `lcd.init()` e `lcd.backlight()`: Liga e ativa o backlight do display.
- `lcd.print(...)`: Mostra mensagens iniciais no LCD (ex: "Conectando...").
- `WiFi.begin(...)`: Conecta-se à rede Wi-Fi.
- Laço `while(WiFi.status() != WL_CONNECTED)`: Aguarda conexão com o Wi-Fi antes de seguir.

---

### void loop()

A função `loop()` roda **continuamente** enquanto o dispositivo estiver ligado. Ela realiza a lógica principal do sistema. As etapas são:

1. **Leitura dos Sensores:**
   - Ultrassônico: mede a distância da água.
   - DHT22: mede temperatura e umidade.
   - Chuva: detecta se está chovendo via pino digital.

2. **Avaliação de Alertas:**
   - Se a água estiver acima do limite → alerta de enchente.
   - Se a temperatura for muito alta → alerta de calor extremo.
   - Se a umidade for muito alta → alerta de saturação do ar.
   - Se estiver chovendo e a água estiver alta → alerta **crítico** (situação perigosa).

3. **Exibição no LCD:**
   - Exibe alertas ou dados em tempo real, como:
     - Nível da água (em cm)
     - Chuva: C (chovendo) ou S (seco)
     - Temperatura e umidade

4. **LED de Alerta:**
   - O LED acende automaticamente se **qualquer alerta** estiver ativo.

5. **Envio para TagoIO:**
   - Os dados de `nivel_agua`, `temperatura`, `umidade` e `chuva` são enviados em formato JSON para a API da TagoIO via protocolo HTTP POST.
   - Se houver erro, é possível ver pelo Serial Monitor.

6. **Delay:**
   - O loop espera 20 segundos (`delay(20000)`) antes de repetir o ciclo.

---

Essas funções garantem que o sistema funcione de forma contínua, inteligente e em tempo real — essencial em cenários de risco como enchentes.


##  Lógica de Alerta

O sistema opera com base em quatro condições críticas:

- **Alerta de água**: ativado quando `distancia_cm < 10`
- **Alerta de temperatura**: ativado quando `temperatura > 40.0°C`
- **Alerta de umidade**: ativado quando `umidade > 95.0%`
- **Alerta crítico**: ativado quando `chuva == 1` **e** `distancia_cm < 15`  
  *(situação potencialmente grave com risco de enchente durante chuva)*

As condições acima também acendem o **LED de alerta** e atualizam o display LCD com mensagens visuais.

---

## Integração com a Plataforma TagoIO

Os dados são enviados para a **TagoIO** via requisição HTTP `POST` com o seguinte header: Device-Token {SEU_TOKEN}
As variáveis enviadas e monitoradas:

- `nivel_agua` (distância em cm)
- `temperatura` (°C)
- `umidade` (%)
- `chuva` (0 ou 1)

### Dashboard

Essas variáveis podem ser visualizadas em **dashboards interativos** na TagoIO, com gráficos, indicadores e controles.

### Ações Automatizadas

É possível configurar **ações automáticas** como:

- **Envio de e-mail** quando `nivel_agua` indicar risco.
- Disparo de **webhooks** para outros sistemas.
- Integração com notificações móveis ou sirenes.

---

## Anexos
INSERIR ANEXOS
![image](https://github.com/user-attachments/assets/0bf9a948-8033-4a92-bdbc-51e11ecb20ec)
![image](https://github.com/user-attachments/assets/a6ac9896-2bb8-458b-af3c-e8d82f1f036b)
![image](https://github.com/user-attachments/assets/fb658b5c-7095-4fba-bab9-5d466d32afe5)


