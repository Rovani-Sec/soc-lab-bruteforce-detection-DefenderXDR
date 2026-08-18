# RovaniSec — Windows Authentication Brute Force Detection Lab

> Laboratório prático de Blue Team / SOC desenvolvido para simular, detectar e investigar tentativas de força bruta contra autenticação Windows utilizando SMB e Microsoft Defender XDR.

---

## 📌 Sobre o Projeto

Este projeto apresenta a construção de um laboratório de segurança defensiva focado na detecção e investigação de tentativas de força bruta contra um endpoint Windows.

A simulação foi realizada em um ambiente virtualizado e isolado, utilizando um host Kali Linux como origem da atividade e um endpoint Windows 10 como alvo.

O objetivo principal foi reproduzir um cenário próximo ao fluxo operacional de um SOC, passando pelas etapas de:

- Reconhecimento;
- Enumeração de serviços;
- Simulação de autenticação por força bruta;
- Geração de telemetria no endpoint;
- Hunting utilizando Advanced Hunting;
- Criação de Custom Detection;
- Geração de alerta;
- Criação e investigação do incidente;
- Classificação;
- Tomada de decisão;
- Encerramento do incidente.

---

# 🏗️ Arquitetura

O laboratório utiliza uma rede virtual isolada para a comunicação entre os hosts.

### Arquitetura do Laboratório

```text
                     LABORATÓRIO ISOLADO
                            
                    Host-Only Network
                           │
             ┌─────────────┴─────────────┐
             │                           │
             ▼                           ▼
       Kali Linux                   Windows 10
       Attack Source               Target Endpoint
       192.168.56.103              192.168.56.104
             │                           │
             │      SMB / TCP 445        │
             └──────────────────────────►│
                                         │
                                         ▼
                              Microsoft Defender
                              for Endpoint
                                         │
                                         ▼
                              Microsoft Defender XDR
                                         │
                              ┌──────────┴──────────┐
                              │                     │
                       Advanced Hunting      Custom Detection
                              │                     │
                              └──────────┬──────────┘
                                         │
                                         ▼
                                      Alert
                                         │
                                         ▼
                                    Incident
                                         │
                                         ▼
                                SOC Investigation
```

---

| Componente                      | Função                                       |
| ------------------------------- | -------------------------------------------- |
| Kali Linux                      | Origem da simulação de ataque                |
| Windows 10                      | Endpoint alvo                                |
| SMB                             | Serviço utilizado na simulação               |
| Microsoft Defender for Endpoint | Coleta de telemetria do endpoint             |
| Microsoft Defender XDR          | Detecção, correlação e investigação          |
| Advanced Hunting                | Consulta e análise da telemetria             |
| Custom Detection                | Detecção de múltiplas falhas de autenticação |
| KQL                             | Linguagem utilizada na consulta              |

---

⚔️ Cenário de Ataque

O cenário começa com o reconhecimento do endpoint Windows a partir do Kali Linux.

## 1. Reconhecimento

**Enumeração SMB**

A identificação do serviço SMB permitiu prosseguir para a etapa de simulação de autenticação

<img width="952" height="314" alt="001-smb-enumeration-kali" src="https://github.com/user-attachments/assets/7c84d492-6ffc-41a4-b16e-f626eac1716a" />

O serviço SMB foi identificado através da porta:
```text
TCP/445
```
---

## 3. Tentativas de Autenticação

Foram realizadas múltiplas tentativas de autenticação contra o endpoint Windows utilizando credenciais incorretas.

<img width="1582" height="432" alt="002-Attack-Kali-SMB" src="https://github.com/user-attachments/assets/8f4dd4d7-043c-4d65-b980-7eebba4838ad" />

A atividade gerou eventos de falha de autenticação de rede.

🔎 Detecção

A detecção foi criada utilizando uma Custom Detection no Microsoft Defender XDR.

<img width="1820" height="632" alt="005-KQL-consulta-evento-LogonFailed" src="https://github.com/user-attachments/assets/202fcb8f-8418-4d4d-9a9c-298aa29678b8" />


A consulta utiliza a tabela:

```text
DeviceLogonEvents
```

e identifica eventos com:

```text
ActionType = LogonFailed
```

A atividade é agrupada por:

```text
Dispositivo;
Conta;
IP remoto;
Tipo de logon.
```
## Custom Detection

A regra foi criada no Microsoft Defender XDR utilizando a consulta desenvolvida para o laboratório.

<img width="1868" height="690" alt="008-rule-custom-windows-bruteforce" src="https://github.com/user-attachments/assets/ffb0fd3e-9a51-47b7-8849-59e4900d8d96" />


A regra foi configurada para identificar:

5 ou mais falhas de autenticação

Quando o limite é atingido, o Defender XDR gera o alerta:

```text
Windows Authentication Brute Force Detected
```

## Configuração do Alerta

A Custom Detection foi configurada para gerar um alerta específico para o comportamento identificado.

<img width="1868" height="690" alt="008-rule-custom-windows-bruteforce" src="https://github.com/user-attachments/assets/6b205c4e-c222-433a-b96c-9140a8667690" />

O alerta utiliza o título:

```text
Windows Authentication Brute Force Detected
```
---


## Evidência da Detecção

Após a execução da simulação, o Microsoft Defender XDR gerou o alerta correspondente.

##<img width="1842" height="743" alt="006-KQL-contagem-eventos" src="https://github.com/user-attachments/assets/93d19062-376f-4007-b394-25355406f8fc" />

A investigação identificou:


| Campo             | Valor            |
| ----------------- | ---------------- |
| Endpoint          | `windows10`      |
| Conta alvo        | `joao`           |
| IP de origem      | `192.168.56.103` |
| Tipo de Logon     | `Network`        |
| Falhas detectadas | `5`              |
| Serviço           | SMB              |
| Porta             | TCP/445          |

---

## Resposta ao Incidente

O incidente foi classificado como:

```text
True Positive
```
<img width="1864" height="444" alt="009-resolved-incident" src="https://github.com/user-attachments/assets/f08ccadc-c63d-47aa-8227-f8033c7a2eff" />

A classificação como True Positive significa que o comportamento detectado realmente ocorreu e correspondeu ao comportamento que a regra foi desenvolvida para identificar.

Entretanto:

True Positive não significa comprometimento confirmado.

Neste cenário, a atividade foi intencionalmente gerada para validar a capacidade de detecção.

Decisão do SOC

```text
Detecção
   ↓
Investigação
   ↓
Atividade validada
   ↓
Sem evidência de comprometimento
   ↓
Contenção não necessária
   ↓
True Positive
   ↓
Resolved
```

