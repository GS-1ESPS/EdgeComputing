# GS 2025 - Edge

## Integrantes 1ESPS
- Gabrielly Candido RM: 560916
- Luiza Ribeiro RM: 560200

# NOME PROJETO - O QUE FAZ

BREVE DESCRIÇÃO

---

## Objetivo

XXXX

---

## Arquitetura em Camadas

> O sistema está organizado em três camadas principais: IoT, Back-End e Aplicação.

### Camada IoT (Dispositivos e Sensores)

- **Microcontrolador:** ESP32
- **Sensores:**
  - Ultrassônico HC-SR04 (mede a distância para estimar o nível de estoque)
  - Display LCD 16x2 I2C (mostra dados localmente)


### Camada Back-End (Plataforma de IoT)

- **TagoIO**
  - Recebe os dados via protocolo HTTPS com autenticação via token.
  - Armazena os dados em buckets.
  - Permite criar dashboards interativos.
  - Envia notificações automáticas via Actions.

### Camada Aplicação (Interface)

- **Portal Web:** 


## Código do Microcontrolador

O código completo está no arquivo CODIGO e é possível acessar pelo link do Wokwi: INSERIR LINK

INSERIR IMAGEM

## Estrutura do projeto de Arduino -  Especificações Técnicas

### Como Funciona?
1. 

### Bibliotecas Utilizadas
- #include <Wire.h>: Comunicação I2C.
- #include <LiquidCrystal_I2C.h>: Comunicação com o display LCD.
- #include <WiFi.h>: Conexão do ESP32 com rede Wi-Fi.
- #include <HTTPClient.h>: Realiza requisições HTTP.
- #include <WiFiClientSecure.h>: Permite enviar dados com HTTPS.

### Componentes Utilizados
- ESP32: Controlador principal responsável pela leitura dos sensores, controle do LCD e comunicação Wi-Fi.
- Sensor Ultrassônico HC-SR04: Usado para medir a distância entre o sensor e um objeto, com o intuito de simular a quantidade de estoque restante.
- LCD 16x2 I2C: Display utilizado para mostrar informações ao usuário: ID do cartão e a distância medida pelo sensor ultrassônico. (Apenas para teste interno)
- Cabos e Protoboard: Para realizar as conexões físicas entre os componentes.

### Configurações e Definições
- LiquidCrystal_I2C lcd(0x27, 16, 2): Configuração do LCD (16 colunas, 2 linhas). (Apenas para teste interno)

### Funções principais
- sendToTago(...): Envia os dados via HTTP POST com estrutura JSON compatível com TagoIO.
- setup(): Inicializa Wi-Fi, display LCD e sensores.
- loop(): A cada 5 segundos, lê os dados, classifica o status (Cheio, Médio, Vazio), envia à nuvem e atualiza o LCD.

### Configuração Inicial setup()
1. Inicialização do LCD (para teste local).
2. Configuração dos pinos do sensor ultrassônico.
3. Inicialização da comunicação Serial para debug.
4. Conexão com a rede Wi-Fi.
5. Mensagens iniciais no monitor serial e display.

### Configuração no loop()
1. A cada 5 segundos (`SEND_INTERVAL`):
   
   a. Leitura do valor simulado de RFID.  
   b. Leitura da distância via sensor ultrassônico.  
   c. Classificação do status do estoque com base na distância.  
   d. Montagem de objeto JSON com `rfid`, `distancia` e `status`.  
   e. Envio do JSON via HTTPS (POST) para o endpoint do TagoIO.  
   f. Exibição das informações no display LCD (apenas para teste interno).

3. Os dados são armazenados na nuvem e podem ser acessados no dashboard do TagoIO.


## Diagrama da arquitetura e fluxo do projeto
INSERIR IMAGEM

### Explicação do Fluxograma

- Início: O sistema é iniciado.
- Importar Bibliotecas: As bibliotecas necessárias são carregadas.
- Conectar no Wi-Fi: Ao utilizar a rede de Wi-Fi do Wowki ("Wokwi-GUEST"), ele conecta automaticamente.
- Conectar no MQTT: Existe um broker público (test.mosquitto.org), o tópico ("test_topic_challenge") que é publicado pelos dispositivos e uma porta (1883).
- Entrar no loop contínuo: O programa entra no loop(), onde executa de forma contínua a checagem de sensores e o envio de dados.
- Ler sensor ultrassônico: O ESP32 mede a distância usando o sensor HC-SR04 para estimar a quantidade de estoque disponível.
- Configurar LCD: O display LCD é inicializado. (Apenas para teste interno)
- Exibir Dados no LCD: O ID RFID e a distância são mostrados na tela. (Apenas para teste interno)
- Nova leitura de estoque?: O sistema verifica se deve continuar a leitura.
  - Se "Não": O sistema volta no loop para ler novamente os sensores.
  - Se "Sim": 
    - Classificar status do estoque: Com base na distância medida, o sistema classifica o nível de estoque como "Cheio", "Médio" ou "Vazio".
    - Montar JSON com RFID, Distância e Status
    - Publicar JSON via MQTT no tópico "test_topic_challenge", podendo ser recebido por ferramentas como o Node-RED ou a própria plataforma de nuvem TagoIO, que o exibe em um dashboard ou executa ações com base nos dados.
   
OBS: Se estivesse em uma aplicação real conectando á plataforma em nuvem (TagoIO), antes da etapa "Nova leitura de estoque" existiriam outras duas:
- Requisição HTTP (POST) - Irá postar os dados na plataforma de nuvem
- Enviar dados para a plataforma em nuvem (TagoIO): A plataforma escolhida, TagoIO, irá receber os dados de um sensor. Nesse caso, o da distância.
Logo depois, o fluxo seria o mesmo, com exceção de envio para exibição do dashboard no Node-RED, uma vez que o próprio TagoIO poderia fazer isso.

## Anexos
INSERIR ANEXOS

## Links Externos
- Link Vídeo Explicação: INSERIR VIDEO
