# Funcionamento do protocolo `OCPP`


## 1. Camada Física

A camada física define o meio físico de transmissão de dados entre o carregador (EVSE - Electric Vehicle Supply Equipment) e o sistema central de gerenciamento (Central System).

> Meio físico:

O OCPP não especifica diretamente o meio físico, pois opera em camadas superiores. No entanto, na prática, a comunicação ocorre geralmente sobre redes `IP` (Internet Protocol), utilizando:

- Ethernet: Conexões cabeadas usando cabos Ethernet (ex.: Cat5e, Cat6) são comuns em carregadores instalados em locais fixos, como estações de carregamento públicas ou garagens.
- Wi-Fi: Muitos carregadores utilizam redes Wi-Fi (IEEE 802.11) para comunicação, especialmente em locais onde cabeamento Ethernet não é viável.
- Redes Celulares: Em áreas remotas ou para carregadores móveis, é comum o uso de redes celulares (3G, 4G, 5G) com modems integrados no EVSE.
- PLC (Power Line Communication): Embora menos comum, alguns carregadores podem usar comunicação por linha de energia, especialmente em cenários onde a infraestrutura elétrica já está instalada.

> Conexão Física: 

- No caso de Ethernet, o EVSE possui porta RJ45 para conexão a um switch ou roteador.
- Para Wi-Fi, o EVSE inclui um módulo Wi-FI que se conecta a um ponto de acesso.
- Para redes celulares, o EVSE possui um modem com um cartão SIM para acesso à rede.

## 2. Camada de Enlace de Dados

A camada de enlace de dados gerencia a comunicação entre dispositivos na mesma rede local e garante a entrega confiável de frames.

> Protocolos Utilizados:

- Ethernet (IEEE 802.3): Para conexões cabeadas, o protocolo Ethernet é usado, com endereçamento MAC para identificar dispositivos na rede local.
- Wi-Fi (IEEE 802.11): Para conexões sem fio, o protocolo Wi-Fi gerencia o acesso ao meio, incluindo autenticação (ex.: WPA2/WPA3) e controle de acesso ao meio (CSMA/CA).
- PPP (Point-to-Point Protocol): Em redes celulares, o PPP pode ser usado para estabelecer uma conexão de dados confiável sobre a camada física.

## 3. Camada de Rede

Responsável pelo roteamento de pacotes através de redes, o OCPP utiliza o Protocolo Internet (IP), geralmente IPv4 ou IPv6:

- Cada estação de carregamento e sistema central possui um endereço IP, permitindo comunicação através de redes locais (LAN) ou internet (WAN).
- O roteamento é gerenciado por dispositivos como roteadores, usando tabelas de roteamento baseadas em protocolos como OSPF ou BGP.
- A camada 3 assegura que os pacotes cheguem ao destino correto, independentemente da 
topologia de rede.

## 4. Camada de Transporte


O OCPP depende do TCP (Transmission Control Protocol) para garantir entrega confiavel:

- TCP fornece entrega ordenada, retransmissão de pacotes perdidos, e controle de congestionamento, usando um handshake de três vias para estabelecer conexão.


- Características técnicas incluem números de sequência, janelas deslizantes para controle de fluxo, e ACKs para confirmação de recebimento.


- Para segurança, pode-se usar TCP com SSL/TLS, especialmente em implementações modernas como OCPP 2.0.1, que suporta Secure Web Sockets (WSS).


## 5. Camada de Sessão


A camada de sessão gerencia a comunicação entre aplicações, e no OCPP, utiliza WebSockets:

- Web Sockets, definidos pelo RFC 6455, fornecem uma conexão bidirecional persistente sobre TCP, ideal para comunicação em tempo real.


- A conexão é iniciada com um handshake HTTP, seguido por uma conexão Web Socket que permite troca contínua de mensagens.


- Em implementações seguras, usa-se WSS, que adiciona SSL/TLS para criptografia, protegendo contra ataques como man-in-the-middle.


## 6. Camada de Apresentação 

Esta camada lida com a formatação e codificação de dados, e no OCPP, utiliza JSON-RPC:


- JSON (JavaScript Object Notation) é um formato leve, baseado em texto, usado para serializar mensagens OCPP, como comandos de início/fim de sessão ou relatórios de status.


- Exemplo de mensagem JSON: {"messageType": "CALL", "messageId": "123", "action": "BootNotification", "payload": {"chargePointVendor": "VendorX", "chargePointModel": "ModelY"}}.


## 7. Camada de Aplicação


A camada de aplicação é onde o OCPP opera como protocolo, definindo mensagens e semântica:

- OCPP suporta funcionalidades como autenticação (ex.: via RFID ou app), controle de carregamento (ex.: iniciar/parar sessão), e monitoramento (ex.: relatórios de energia).


- Mensagens são categorizadas em CALL, CALLRESULT, e CALLERROR, usando um esquema de solicitação-resposta.


- Versões como OCPP 2.1, lançada em 2025, adicionam recursos como QR codes dinâmicos para pagamento e melhorias em smart charging, conforme [Open charge point protocol - Open Charge Alliance](https://openchargealliance.org/protocols/open-charge-point-protocol/)


> Variação por versões:

É importante notar que versões anteriores, como OCPP 1.6, usavam HTTP/JSON em vez de Web Sockets, com comunicação menos eficiente para tempo real. A transição para Web Sockets em OCPP 2.0 e posteriores melhorou a latência e escalabilidade, mas não é retrocompatível, conforme [The OCPP Handbook (2025) - AMPECO](https://www.ampeco.com/guides/complete-ocpp-guide/)

> Aspectos de Segurança:

A segurança é crítica, especialmente em OCPP 2.0.1 e superiores, que suportam WSS com SSL/TLS para criptografia de transporte. Além disso, há perfis de segurança, como autenticação mútua via TLS (mTLS) e gerenciamento de certificados PKI, conforme OCPP Security and Security Profiles | Ampcontrol. OCPP 1.6 também tem diretrizes de segurança, como configuração de usuário/senha, conforme Security in OCPP 1.6 (Central System) | by AKM Ahsanuzzaman (Ahsan Aasim).

![compare-table](/docs/compare-table.png)