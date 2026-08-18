# Mapeamento MITRE ATT&CK

## 1. Objetivo

Este documento apresenta o mapeamento da atividade observada durante a simulação de ataque para o framework MITRE ATT&CK.

O objetivo é relacionar o comportamento técnico observado no laboratório às técnicas utilizadas por adversários em cenários reais, proporcionando contexto para a análise e detecção da atividade.

---

## 2. Resumo do Mapeamento

| Tática | Técnica | ID | Relação com o LAB |
|---|---|---|---|
| Credential Access | Brute Force | T1110 | Múltiplas tentativas de autenticação contra o endpoint Windows |

A técnica **T1110 — Brute Force** é o principal mapeamento deste cenário.

---

## 3. T1110 — Brute Force

### Tática

**Credential Access**

### Técnica

**T1110 — Brute Force**

### Descrição

A técnica Brute Force representa tentativas repetidas de autenticação com o objetivo de obter acesso a uma conta ou sistema utilizando credenciais que ainda não são conhecidas pelo atacante.

### Aplicação no laboratório

No cenário desenvolvido, o host Kali Linux foi utilizado como origem de uma simulação de força bruta contra o endpoint Windows 10.

Após a identificação do serviço SMB, foram realizadas múltiplas tentativas de autenticação utilizando credenciais incorretas.

As tentativas geraram eventos de falha de autenticação no endpoint Windows.

O comportamento foi posteriormente identificado pelo Microsoft Defender XDR por meio de uma regra de Custom Detection.

### Fluxo da atividade

```text
Kali Linux
     │
     │ Tentativas de autenticação
     ▼
SMB / TCP 445
     │
     ▼
Windows 10
     │
     │ Falhas de autenticação
     ▼
Microsoft Defender
     │
     ▼
Custom Detection
     │
     ▼
Alerta
```
