# Simulação de Ataque

## 1. Reconhecimento

O Nmap foi utilizado a partir do host Kali Linux para identificar serviços expostos.

A porta TCP/445 foi identificada como aberta no endpoint Windows.

Evidência:

<img width="952" height="314" alt="001-smb-enumeration-kali" src="https://github.com/user-attachments/assets/4a1b8286-0d5d-44f6-b1ec-64783415256d" />


## 2. Ataque de Força Bruta de Autenticação

Foram geradas múltiplas tentativas de autenticação SMB malsucedidas contra o endpoint Windows.

<img width="1582" height="432" alt="002-Attack-Kali-SMB" src="https://github.com/user-attachments/assets/09e9964d-eb59-4265-86e6-53cc7e2a4bf4" />


## 3. Origem do Ataque

Atacante:
192.168.56.103

Alvo:
192.168.56.104

## 4. Resultado

A atividade gerou eventos de falha na autenticação de rede
que foram posteriormente detectados pelo Microsoft Defender XDR.

<img width="1827" height="713" alt="007-detection-defenderXDR" src="https://github.com/user-attachments/assets/97bd2d5d-0bd3-4f90-916f-aba52788d82a" />
