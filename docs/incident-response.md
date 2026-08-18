# Resposta ao Incidente

## 1. Objetivo

Este documento descreve as ações realizadas após a geração do incidente de segurança no Microsoft Defender XDR.

O objetivo da resposta foi demonstrar o processo de tomada de decisão de um analista SOC diante de uma detecção de força bruta, desde a triagem inicial até o encerramento do incidente.

---

## 2. Estado Inicial

Após a geração do incidente, o analista SOC iniciou o processo de triagem para determinar:

- Se a atividade era legítima ou maliciosa;
- Se o comportamento havia ocorrido de forma isolada ou repetitiva;
- Qual ativo estava envolvido;
- Se havia evidências de comprometimento;
- Se alguma ação de contenção era necessária.

O incidente foi analisado dentro do contexto do ambiente de laboratório controlado.

---

## 3. Triagem do Alerta

A primeira etapa consistiu em validar se o alerta correspondia ao comportamento esperado pela regra de detecção.

A investigação confirmou que:

- O alerta foi gerado pela Custom Detection criada para identificar múltiplas falhas de autenticação;
- A atividade observada correspondia ao comportamento esperado da regra;
- Os eventos estavam relacionados entre si;
- A atividade tinha origem no host utilizado para a simulação.

Com base nessa validação, o alerta foi considerado uma detecção válida.

---

## 4. Validação da Atividade

Após confirmar a validade do alerta, foi realizada a análise contextual da atividade.

Foi verificado que o tráfego fazia parte de uma simulação controlada realizada no próprio laboratório.

O cenário conhecido era:

```text
Kali Linux
      ↓
Simulação de ataque
      ↓
Windows 10
      ↓
Microsoft Defender XDR
```
## 5. Avaliação do Endpoint

O endpoint envolvido foi avaliado para determinar se havia indícios de comprometimento além das falhas de autenticação observadas.

Durante a análise, não foram identificados:

```text
. Autenticação bem-sucedida decorrente da atividade;
. Evidências de execução de código no endpoint;
. Evidências de persistência;
. Evidências de movimentação lateral;
. Outros indicadores que justificassem o isolamento imediato do dispositivo.
```
Com base nas evidências disponíveis, não foi identificada necessidade de contenção do endpoint.

## 6. Avaliação da Necessidade de Contenção

A decisão de contenção foi baseada no impacto e no nível de risco identificado.

Contenção do endpoint

Não realizada.

Justificativa

O isolamento do Windows não foi executado porque:
```text
1. A atividade fazia parte de uma simulação controlada;
2. A origem da atividade era conhecida;
3. Não foi identificada autenticação bem-sucedida;
4. Não foram observadas evidências de comprometimento do endpoint;
5. A contenção não acrescentaria valor ao cenário investigado.
````
Essa decisão representa uma etapa importante do processo de resposta: nem todo alerta verdadeiro exige isolamento do dispositivo.

## 7. Ações de Resposta

As ações realizadas durante o tratamento do incidente foram:

| Ação                         | Resultado      |
| ---------------------------- | -------------- |
| Triagem do alerta            | Concluída      |
| Validação da atividade       | Concluída      |
| Análise do endpoint          | Concluída      |
| Avaliação de comprometimento | Sem evidências |
| Contenção                    | Não necessária |
| Remediação                   | Não realizada  |
| Classificação                | True Positive  |
| Encerramento                 | Resolved       |


Nenhuma ação automática de remediação foi executada.

## 8. Classificação

Após a investigação, o incidente foi classificado como:

```text
True Positive
```

A classificação indica que o comportamento detectado realmente ocorreu e correspondia à atividade que a regra de detecção foi criada para identificar.

Entretanto, a classificação não representa confirmação de comprometimento do endpoint.

O evento foi um True Positive de detecção, originado por uma atividade intencionalmente simulada no laboratório.

## 9. Encerramento

Após a conclusão da investigação e da avaliação de impacto, o incidente foi marcado como:

```text
Resolved
```

O encerramento ocorreu porque:

```text
A atividade foi validada;
A origem foi identificada;
O contexto da atividade foi confirmado;
Não foram encontradas evidências de comprometimento;
Não havia necessidade de contenção;
Não havia ações adicionais de remediação a executar.
``` 
## 10. Resultado da Resposta

O tratamento do incidente demonstrou o seguinte processo operacional:

```text
Alerta recebido
      ↓
Triagem
      ↓
Validação
      ↓
Avaliação do endpoint
      ↓
Avaliação de impacto
      ↓
Decisão de contenção
      ↓
Classificação
      ↓
Encerramento
```
## Resultado final:
```text
Classification:  True Positive
Containment:     Not Required
Remediation:     Not Performed
Status:          Resolved
```

O incidente foi encerrado após a confirmação de que a atividade correspondia a uma simulação controlada e não apresentava evidências de comprometimento do endpoint.


