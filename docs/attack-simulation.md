# Simulação de Ataque

## 1. Reconhecimento

O Nmap foi utilizado a partir do host Kali Linux para identificar serviços expostos.



## 2. Enumeração SMB

A porta TCP/445 foi identificada como aberta no endpoint Windows.

## 3. Ataque de Força Bruta de Autenticação

Foram geradas múltiplas tentativas de autenticação SMB malsucedidas
contra o endpoint Windows.

## 4. Origem do Ataque

Atacante:
192.168.56.103

Alvo:
192.168.56.104

## 5. Resultado

A atividade gerou eventos de falha na autenticação de rede
que foram posteriormente detectados pelo Microsoft Defender XDR.