
# Análise de Detecção

## 1. Resumo do Incidente

Foi realizada uma simulação controlada de ataque de força bruta contra autenticação SMB em um ambiente de laboratório isolado.

A atividade teve origem no host Kali Linux e foi direcionada ao endpoint Windows 10 por meio do protocolo SMB. Durante a simulação, foram realizadas múltiplas tentativas de autenticação utilizando
credenciais incorretas contra a conta `joao`.

O Microsoft Defender XDR identificou a atividade por meio de uma regra de **Custom Detection** e gerou o alerta:

Evidência:

<img width="1868" height="690" alt="008-rule-custom-windows-bruteforce" src="https://github.com/user-attachments/assets/93803d9a-4642-4346-b4c0-efbaa7c189bd" />

**Windows Authentication Brute Force Detected**

---

O incidente foi classificado como **True Positive** e posteriormente resolvido após a investigação.

<img width="1864" height="444" alt="009-resolved-incident" src="https://github.com/user-attachments/assets/c2218067-d474-46d7-a491-d808228d908d" />


Não foram identificadas autenticações bem-sucedidas nem evidências de comprometimento do endpoint durante a simulação.

---

## 2. Lógica da Detecção

A detecção foi implementada utilizando uma regra de **Custom Detection** no Microsoft Defender XDR, baseada na tabela `DeviceLogonEvents` do Advanced Hunting.

<img width="1827" height="713" alt="007-detection-defenderXDR" src="https://github.com/user-attachments/assets/6c030acf-b0b7-44ae-bdaa-359b8d76e3de" />

A consulta analisa eventos de falha de autenticação e utiliza os seguintes critérios:

- Endpoint Windows identificado como `windows10`;
- Conta alvo identificada como `joao`;
- `ActionType` igual a `LogonFailed`;
- Atividade de autenticação de rede;
- Agrupamento das tentativas pelo dispositivo, conta, endereço IP remoto e tipo de logon;
- Contagem das falhas de autenticação.

<img width="1842" height="743" alt="006-KQL-contagem-eventos" src="https://github.com/user-attachments/assets/1b88ce82-ce44-4b09-b7be-cba011d7fd07" />


Quando a quantidade de tentativas atinge o limite definido pela regra, o Microsoft Defender XDR gera o alerta correspondente.

### Campos utilizados

| Campo | Finalidade |
|---|---|
| `DeviceName` | Identifica o endpoint afetado |
| `AccountName` | Identifica a conta alvo |
| `RemoteIP` | Identifica a origem das tentativas |
| `LogonType` | Identifica o tipo de autenticação |
| `FailedAttempts` | Quantidade de tentativas de autenticação malsucedidas |
| `FirstSeen` | Primeiro registro observado na agregação |
| `LastSeen` | Último registro observado na agregação |

---

## 3. Limite de Detecção

A regra foi configurada para identificar:

**5 ou mais tentativas de autenticação malsucedidas**

dentro da janela de tempo definida na consulta.

Durante a simulação, o limite foi atingido:

```text
FailedAttempts: 5
```

