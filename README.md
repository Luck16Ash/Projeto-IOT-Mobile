# 📱 Controle IoT – App Android + ESP32 via MQTT (HiveMQ)

> Projeto acadêmico — **Kotlin/Java Android** que controla e monitora um dispositivo IoT real (ESP32) pela internet usando o protocolo MQTT com broker HiveMQ Cloud.

---

## 📋 Descrição

Aplicativo Android nativo (Java) que se comunica com um **ESP32** conectado ao broker **HiveMQ Cloud** via MQTT/WebSocket com TLS. O app e o ESP32 nunca se falam diretamente: ambos trocam mensagens pelo broker, publicando e assinando tópicos.

### O app permite:

| Função | Descrição |
|--------|-----------|
| 💡 **Controle de LED** | Liga e desliga o LED do ESP32 em tempo real |
| 🌡️ **Leitura de sensor** | Exibe temperatura (°C) e umidade (%) atualizados a cada 5 s |
| 📺 **Envio de mensagem** | Envia texto que aparece no display do ESP32 por 10 s |
| 🔌 **Status do ESP32** | Indica se o dispositivo está online ou offline |
| 🔗 **Conexão configurável** | Host, porta, usuário, senha e prefixo de tópico editáveis |

---

## 🛠️ Tecnologia

| Item | Detalhe |
|------|---------|
| **Linguagem** | Java (Android nativo) |
| **SDK mínimo** | API 23 (Android 6.0) |
| **SDK alvo** | API 35 (Android 15) |
| **Protocolo** | MQTT 3.1.1 sobre WebSocket com TLS |
| **Broker** | HiveMQ Cloud |
| **Biblioteca MQTT** | [Eclipse Paho MQTT Client v1.2.5](https://github.com/eclipse/paho.mqtt.java) |
| **Build system** | Gradle |

---

## 📦 Dependências

```groovy
// app/build.gradle
dependencies {
    implementation 'androidx.appcompat:appcompat:1.7.0'
    implementation 'com.google.android.material:material:1.12.0'
    implementation 'org.eclipse.paho:org.eclipse.paho.client.mqttv3:1.2.5'
}
```

---

## 🔌 Dados de Conexão do Broker

> [!CAUTION]
> **Nunca publique a senha em repositórios públicos.** Guarde-a em um arquivo de configuração fora do controle de versão (ex.: `local.properties` já está no `.gitignore`).

| Parâmetro | Valor |
|-----------|-------|
| **Broker** | HiveMQ Cloud |
| **Host** | `436ce1fa7ecd416c98446bce794c0d8d.s1.eu.hivemq.cloud` |
| **Porta WebSocket + TLS** | `8884` |
| **Caminho WebSocket** | `/mqtt` |
| **URL completa** | `wss://436ce1fa7ecd416c98446bce794c0d8d.s1.eu.hivemq.cloud:8884/mqtt` |
| **Usuário** | `aula` |
| **Versão do protocolo** | MQTT 3.1.1 |
| **Client ID** | Único por aluno — ex.: `app-alexandre-01` |

> [!IMPORTANT]
> O **Client ID deve ser único** por aluno/sessão. Se dois apps usarem o mesmo Client ID, o broker derruba um deles a cada nova conexão. O app gera automaticamente um sufixo UUID aleatório para garantir unicidade.

---

## 📡 Tópicos MQTT

Todos os tópicos começam com `iotaula/seu-nome/`. Substitua `seu-nome` pelo seu nome ou RA no campo **Prefixo dos tópicos** dentro do app.

| Tópico | Publicado por | Assinado por | Payload | Retido |
|--------|--------------|--------------|---------|--------|
| `iotaula/seu-nome/led/set` | App | ESP32 | `ON` ou `OFF` | ❌ |
| `iotaula/seu-nome/led/state` | ESP32 | App | `ON` ou `OFF` (estado real) | ✅ |
| `iotaula/seu-nome/msg` | App | ESP32 | `{"from":"Ana","text":"Ola turma"}` | ❌ |
| `iotaula/seu-nome/temp` | ESP32 | App | `{"t":24.5,"h":61}` | ✅ |
| `iotaula/seu-nome/status` | ESP32 | App | `online` ou `offline` | ✅ |

> [!NOTE]
> - O display do ESP32 mostra a mensagem por **10 segundos**: campo `from` na linha de cima (máx. 15 chars) e `text` na de baixo (máx. 16 chars).
> - Temperatura e umidade são publicadas a cada **5 segundos**.
> - Como os tópicos `led/state`, `temp` e `status` são retidos, o app recebe o **último valor** assim que assina.

---

## 🚀 Como rodar o app

### Pré-requisitos

- **Android Studio** (versão recomendada: Ladybug ou superior)
- **JDK 17+**
- Dispositivo Android físico ou emulador com **API 23+**
- Acesso à internet

### Passos

```bash
# 1. Clone o repositório
git clone https://github.com/seu-usuario/ControleIoT_HiveMQ.git
cd ControleIoT_HiveMQ

# 2. Abra no Android Studio
# File > Open > selecione a pasta do projeto

# 3. Sincronize as dependências Gradle
# (Android Studio faz isso automaticamente ao abrir)

# 4. Execute o app
# Run > Run 'app'  ou  Shift+F10
```

> [!TIP]
> Para testar **sem o app**, use o [Web Client do HiveMQ Cloud](https://www.hivemq.com/demos/websocket-client/) ou o [MQTT Explorer](https://mqtt-explorer.com/): conecte com os dados acima, assine `iotaula/seu-nome/#` e publique `ON` em `iotaula/seu-nome/led/set`.

---

## 🖼️ Funcionalidades do App

### Tela principal (rolável)

```
┌─────────────────────────────┐
│   Controle IoT - MQTT       │
├─────────────────────────────┤
│ Host MQTT        [campo]    │
│ Porta            [campo]    │
│ Usuário MQTT     [campo]    │
│ Senha MQTT       [campo]    │
│ Prefixo tópicos  [campo]    │
│ [Conectar ao HiveMQ]        │
│ Status: Desconectado        │
│ [Desconectar]               │
├─────────────────────────────┤
│ CONTROLE DO LED             │
│ Estado do LED: ON           │
│ [LIGAR LED]  [DESLIGAR LED] │
├─────────────────────────────┤
│ ENVIAR MENSAGEM             │
│ Seu nome (máx 15)  [campo]  │
│ Mensagem (máx 16)  [campo]  │
│ [Enviar mensagem]           │
├─────────────────────────────┤
│ LEITURA DO SENSOR           │
│ Temperatura: 24.5 °C        │
│ Umidade: 61.0 %             │
│ Última leitura: 16/11 20:10 │
├─────────────────────────────┤
│ STATUS DO ESP32             │
│ ESP32: online               │
└─────────────────────────────┘
```

---

## ⚠️ Problemas Comuns

| Problema | Causa provável | Solução |
|----------|---------------|---------|
| Não conecta | TLS desligado ou porta errada | Use porta `8884` para WebSocket+TLS |
| Conecta e cai em seguida | Client ID duplicado | O app já gera UUID único automaticamente |
| Não recebe temperatura | Tópico errado ou ESP32 offline | Verifique o prefixo e o status do ESP32 |
| Credenciais incorretas | Usuário/senha errados | Confirme `aula` / `12345678` |

---

## 📊 Critérios de Avaliação

| Critério | Peso |
|----------|------|
| Conexão ao broker com TLS e Client ID único | 15% |
| Envio de texto ao display | 20% |
| Controle do LED com estado sincronizado | 20% |
| Leitura da temperatura em tempo real | 20% |
| Tratamento de erros e reconexão | 10% |
| Interface e usabilidade | 15% |
| Opcionais (gráfico, alertas, histórico, salvar config) | até +10% |

---

## 📁 Estrutura do Projeto

```
ControleIoT_HiveMQ/
├── app/
│   ├── src/main/
│   │   ├── java/com/alexandre/controleiot/
│   │   │   └── MainActivity.java      # Lógica principal + UI + MQTT
│   │   ├── res/                       # Recursos (tema, strings)
│   │   └── AndroidManifest.xml        # Permissão de internet
│   └── build.gradle                   # Dependências do módulo
├── build.gradle                       # Configuração raiz
├── settings.gradle
└── README.md
```

---

## 👤 Autores

**Alexandre Gianneff Loureiro Alves**,
**Luca Simões D'Agostino**,
**Max Wesley Reis de Godoy** e
**Maycon Lima Teixeira Cavalcante**

Projeto IoT — Disciplina de Desenvolvimento Mobile  
Prof. Paulo Rogerio da Silva · Nov 2026
