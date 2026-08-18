
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

| Campo            | Finalidade                                            |
|------------------|-------------------------------------------------------|
| `DeviceName`     | Identifica o endpoint afetado                         |
| `AccountName`    | Identifica a conta alvo                               |
| `RemoteIP`       | Identifica a origem das tentativas                    |
| `LogonType`      | Identifica o tipo de autenticação                     |
| `FailedAttempts` | Quantidade de tentativas de autenticação malsucedidas |
| `FirstSeen`      | Primeiro registro observado na agregação              |
| `LastSeen`       | Último registro observado na agregação                |

---

## 3. Limite de Detecção

A regra foi configurada para identificar:

**5 ou mais tentativas de autenticação malsucedidas**

dentro da janela de tempo definida na consulta.

Durante a simulação, o limite foi atingido:

```text
FailedAttempts: 5
```

4. Fonte de Dados

A principal fonte de telemetria utilizada na detecção foi o Microsoft Defender for Endpoint, por meio dos dados disponibilizados no Microsoft Defender XDR Advanced Hunting.

A consulta utiliza a tabela:
```text
DeviceLogonEvents
``` 
Os principais eventos analisados apresentaram:

```text
ActionType: LogonFailed
LogonType: Network
```
A telemetria foi gerada durante as tentativas de autenticação realizadas contra o serviço SMB do endpoint Windows.

5. Evidências

Durante a investigação, foram identificadas as seguintes evidências:
```text
| Evidência               | Valor                                         |
| ----------------------- | --------------------------------------------- |
| Endpoint afetado        | `windows10`                                   |
| Conta alvo              | `joao`                                        |
| IP remoto               | `192.168.56.103`                              |
| Tipo de Logon           | `Network`                                     |
| Tentativas malsucedidas | `5`                                           |
| Alerta                  | `Windows Authentication Brute Force Detected` |
| Severidade              | `Medium`                                      |
| Classificação           | `True Positive`                               |
| Status                  | `Resolved`                                    |
```

O Microsoft Defender XDR também classificou a atividade na categoria Credential Access e realizou o mapeamento para técnicas relacionadas à força bruta no MITRE ATT&CK.

6. Indicadores

6.1 IP de Origem
```text
192.168.56.103
```

Durante a etapa de reconhecimento, o Kali Linux identificou a porta TCP/445 aberta no endpoint Windows, confirmando a disponibilidade do serviço SMB utilizado posteriormente na simulação de autenticação.

7. MITRE ATT&CK

A atividade identificada foi associada às seguintes técnicas do MITRE ATT&CK:

[T1110 — Brute Force](https://attack.mitre.org/techniques/T1110/)

A atividade apresentou múltiplas tentativas de autenticação contra o endpoint Windows utilizando credenciais incorretas.

A simulação envolveu tentativas repetidas de autenticação utilizando senhas incorretas contra a conta alvo.

As técnicas estão relacionadas à tática:
```text
Credential Access
```
O mapeamento para MITRE ATT&CK permite contextualizar tecnicamente o comportamento identificado e relacioná-lo a uma técnica utilizada em ataques reais.


8. Investigação

A investigação foi realizada seguindo uma abordagem de triagem compatível com um processo de SOC N1.

8.1 Identificação do Alerta

O Microsoft Defender XDR gerou o alerta:

```text
Windows Authentication Brute Force Detected
```
O alerta foi associado ao endpoint Windows afetado.

8.2 Identificação da Origem

O campo RemoteIP identificou:

```text
192.168.56.103
```
como origem das tentativas de autenticação.

Esse endereço corresponde ao host Kali Linux utilizado como origem da simulação no ambiente de laboratório.

8.3 Identificação do Alvo

O endpoint afetado foi identificado como:
```text
windows10
```
A conta utilizada como alvo das tentativas foi:
```text
joao
```

8.4 Análise da Autenticação

Os eventos analisados apresentaram:

```text
ActionType: LogonFailed
LogonType: Network
```
A detecção identificou cinco falhas de autenticação associadas à mesma origem, dispositivo, conta e tipo de logon.

8.5 Correlação do Incidente

O Microsoft Defender XDR correlacionou os eventos e classificou o incidente de acordo com a regra que criamos:
```text
Windows Authentication Brute Force Detected
```
<img width="1868" height="690" alt="008-rule-custom-windows-bruteforce" src="https://github.com/user-attachments/assets/c48f020e-8c46-4f7a-81e5-e07082f73187" />


O incidente foi classificado como:

```text
Credential Access
```

8.5 Impacto

Durante a investigação, não foram identificadas evidências de autenticação bem-sucedida ou comprometimento do endpoint Windows.

A atividade foi intencionalmente gerada como parte de uma simulação controlada de segurança.

Dessa forma, não foi necessária uma ação de contenção contra o endpoint neste cenário.

9. Resultados da Investigação

A investigação confirmou que a regra de Custom Detection funcionou conforme o esperado.

Foram identificados os seguintes elementos:

```text
Múltiplas tentativas de autenticação malsucedidas;
Autenticação do tipo Network;
Mesma origem de rede;
Mesma conta alvo;
Cinco tentativas identificadas;
Endpoint Windows afetado;
Geração do alerta pelo Microsoft Defender XDR;
Criação e correlação do incidente;
Mapeamento para MITRE ATT&CK;
Classificação como True Positive.
```
O alerta foi considerado um True Positive, pois o comportamento detectado correspondeu exatamente ao comportamento para o qual a regra foi desenvolvida.

Entretanto, True Positive não significa comprometimento confirmado.

Neste laboratório, o comportamento foi intencionalmente gerado para validar a capacidade de detecção e não houve evidência de autenticação bem-sucedida.

Nenhuma ação de remediação automática foi executada.


10. Conclusão

O laboratório demonstrou com sucesso o ciclo de detecção e investigação de uma tentativa de força bruta contra autenticação Windows utilizando SMB.

A atividade teve origem no Kali Linux e foi direcionada ao endpoint Windows 10. Após a identificação do serviço SMB, foram realizadas múltiplas tentativas de autenticação malsucedidas.

A telemetria gerada pelo endpoint foi processada pelo Microsoft Defender XDR, permitindo a execução de uma regra de Custom Detection baseada na tabela DeviceLogonEvents.

A regra identificou cinco falhas de autenticação de rede provenientes do mesmo endereço IP remoto e gerou o alerta:

Windows Authentication Brute Force Detected

O alerta foi posteriormente correlacionado em um incidente, investigado e classificado como:

```text
True Positive
```

Após a confirmação de que a atividade fazia parte de uma simulação controlada e que não havia evidências de autenticação bem-sucedida ou comprometimento do endpoint, o incidente foi marcado como:
```text
Resolved
```
<img width="1864" height="444" alt="009-resolved-incident" src="https://github.com/user-attachments/assets/11eb5d3f-b2cb-4416-9524-c594918fa4fe" />

Fluxo do incidente
```text
Reconhecimento
      ↓
Enumeração SMB
      ↓
Tentativas de autenticação
      ↓
Falhas de autenticação Windows
      ↓
Telemetria do Defender
      ↓
Custom Detection
      ↓
Alerta
      ↓
Incidente
      ↓
Investigação SOC N1
      ↓
Mapeamento MITRE ATT&CK
      ↓
True Positive
      ↓
Sem evidência de comprometimento
      ↓
Resolved
```

O laboratório demonstra conhecimentos práticos em Blue Team, SOC, Microsoft Defender XDR, Advanced Hunting, Custom Detection, análise de autenticação Windows, investigação de incidentes e MITRE ATT&CK.

