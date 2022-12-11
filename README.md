## Project name:

VoLoRa is composite acronym **V**oice **O**ver **LoRa**

## Project description:

Project is in PoC stage. The idea is to send SIP messages
from one device to another over radio channel using LoRa modules.

## Hardware:

* Arduino UNO R3 Compatible Board ATmega328P ATmega16U2
<br>_(2 pcs)_
* LLCC68 868MHz 915MHz Ebyte LoRa E220-900T30D DIP
<br>_(2 pcs)_
* 5 dbi Omni Outdoor/Indoor 868MHz 915MHz Antenna N-Male Aerial for LoRa Helium
<br>_(2 pcs)_
* Raspberry Pi 3/4/400
<br>_(2 pcs)_

## Software:

* [Kamailio 5.6.2](https://github.com/kamailio/kamailio/tree/5.6.2)
* [Asterisk 20.0.1](https://www.asterisk.org/downloads/)
* Softphone (UA) e.g. [Zoiper](https://www.zoiper.com/)
* _layer project which will allow kamailio to use UDP transport on "fake" port_ - to be created
* _Arduino UNO sketches_

## Schematic:

```mermaid
sequenceDiagram
    participant Asterisk
    participant Kamailio
    participant Transport layer project
    participant WiFi hotspot on RPI
    participant User Agent
    
    User Agent->>Kamailio: REGISTER
    Kamailio->>User Agent: 401/407
    User Agent->>Kamailio: REGISTER + credentials
    Kamailio->>User Agent: 200
    
    loop
        User Agent->>WiFi hotspot on RPI: Connection
    end
    
    Note right of User Agent: Same goes on <br/>far end <br/>with User Agent 2
```

```mermaid
sequenceDiagram
    participant LoRa
    participant Arduino UNO sketch
    participant Asterisk
    participant Kamailio
    participant Transport layer project
    participant WiFi hotspot on RPI
    participant User Agent
    
    User Agent->>Kamailio: INVITE
    Kamailio->>Asterisk: INVITE
    Asterisk->>Arduino UNO sketch: INVITE
    Arduino UNO sketch->>LoRa: INVITE
    
    LoRa-->>Arduino UNO sketch: 100
    Asterisk-->>Kamailio: 100
    Kamailio-->>User Agent: 100
    Arduino UNO sketch-->>Asterisk: 100
    
    LoRa-->>Arduino UNO sketch: 200
    Asterisk->>Kamailio: 200
    Kamailio->>User Agent: 200
    Arduino UNO sketch-->>Asterisk: 200
    
    loop
        Asterisk->>Arduino UNO sketch: Connection between RPI and Arduino via USB and virtual COM
    end
    
    loop
        User Agent->>WiFi hotspot on RPI: Connection
    end
    
    Note left of LoRa: Same goes bakward <br/>on far end <br/>to User Agent 2
```