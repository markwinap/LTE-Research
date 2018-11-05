# ESP32 SEQUANS Monarch LR5.1.1.0 

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

LTE-M or LTE Cat-M1, narrow band needs to be supported by the carrier and cell tower in order to take advage of low power
 - PSM (power saving mode) - Not able to send or receive message on schedule
 - eDRX - (Extended Discontinuous Reception) - receive messages time to time 

### Usefull Links

Links related to the modem usage:

* [ATCommands] - Sequans Monarch modem
* [PycomSource] - Pycom LTE Source Code
* [PycomDoc] - Pycom LTE Documentation
* [PHY] - LTE Physical Layer Overview
* [PSMeDRX] - PSM vs eDRX
* [3GPPTS] - 3GPP Release 13

### LTE In Mexico

Current LTE providers and bands available in Mexico

| Provider | Band | Uplink | Downlink |
| ------ | ------ | ------ | ------ |
| Telcel | 4 | 1710 MHz – 1755 MHz  | 2110 MHz – 2155 MHz |
| AT&T | 4 | 1710 MHz – 1755 MHz  | 2110 MHz – 2155 MHz |


# Pycom Test

Basic Connection

```py
from network import LTE

def pretty(cmd):
    response = lte.send_at_cmd(cmd).split('\r\n')
    for line in response:
        print(line)
        
lte = LTE()#lte = LTE(carrier="at&t")
lte.attach()#lte.attach(band=4)
lte.isattached()
lte.connect()
lte.isconnected()
lte.disconnect()
lte.dettach()
```


### Usefull AT Commands

SYSTEM FSM
```sh
AT!="fsm"

    +--------------------------+--------------------+
    |            FSM           |        STATE       |
    +--------------------------+--------------------+
    | RRC TOP FSM              |CAMPED              |
    | RRC SEARCH FSM           |CAMPED_ANY          |
    | RRC ACTIVE FSM           |IDLE                |
    | PMM PLMN FSM             |ANY_CAMPED          |
    | EMM MAIN FSM             |NULL                |
    | EMM AUTH FSM             |NULL                |
    | EMM CONN FSM             |NULL                |
    | EMM TAU FSM              |NULL                |
    | EMM TEST FSM             |NULL                |
    | ESM BEARER FSM           |BEARER_NULL         |
    | SMS MT FSM               |IDLE                |
    | SMS MO FSM               |IDLE                |
    | HP MAIN FSM              |IDLE                |
    | HP USIM FSM              |ABSENT              |
    | HP SMS MO FSM            |IDLE                |
    | HP SMS MT FSM            |IDLE                |
    | HP CAT FSM               |NULL                |
    +--------------------------+--------------------+
```

PHY - Physical Layer Overview
```sh
AT!="showphy"

DL SYNCHRO STATISTICS
=====================
    Synchro state                         : DRX_SLEEP
    PPU SIB1 ACQ watchdog                 : 0
    Frequency Hypothesis RF  (Hz)         : 0
    RSRP (dBm)                            : -98.70
    RSRQ  (dB)                            : -13.89
    Channel estimation state (Cell-spec.) : LOW CINR
    Channel estimation state (UE-spec.)   : LOW CINR
    Channel estimation state (MBSFN)      : LOW CINR
    Channel estimation CINR               : 1.77
    Channel length                        : SHORT
  AGC
    AGC RX gain (dB)                      : 55.16
    RX PSD BO (dBFs)                      : -20.15
    RX PSD (dBm)                          : -101.62
    Noise level RS (dBm)                  : -101.98
    Digital gain (dB)                     : 9.87
    CINR RS (dB)                          : 3.28
  NARROWBANDS
    Last DL NB                            : Central
    Last UL NB                            : 0
  AFC
    Frequency offset RF  (Hz)             : -1323
    Frequency offset BB  (Hz)             : 0
  PBCH
    MIB received quantity                 : 14
    MIB timeout quantity                  : 0
```

Network Registration
Mode= Operator=334020(MobileCountryCodes(MCC)+MobileNetworkCodes(MNC) 334-Mexico 020-TelCel/America Movil),AccessTechnology=7(E-UTRA LTE)
```sh
AT+COPS?
    +COPS: 0,2,"334020",7    
```

Check eDRX (Extended Discontinuous Reception)
ACt Type, Pagging
```sh
AT+CEDRXRDP
    +CEDRXRDP: 4,"1101","1101","0000"
```

eDRX Setting
AcT Type, Pagging Type (page 598 of 3GPP TS 24.008 V13.5.0)
```sh
AT+CEDRXS?
    +CEDRXS: 4,"1101"
#Set
AT+CEDRXS=1

```

Signal quality
rssi,ber(bit error rate%) 99=Not Detectable
rss1 0 = -113 dBm,  31 = -51 dBm (range [0:31] = [-113:-51]increments of 2 dBm)
```sh
AT+CSQ
    +CSQ: 15,99
```

PDP Context
```sh
AT+CGDCONT?
    +CGDCONT: 1,"IP","web.iusacellgsm.mx",,,,0,0,0,0,0,0
    +CGDCONT: 1,"IPV4V6","",,,,0,0,0,0,0,0
```

Network Registration
```sh
AT+CREG?
    +CREG: 0,5#Roaming
```

Software Version
```sh
ATI1
    UE5.0.0.0d
    LR5.1.1.0-38638
```

ATC Cell Monitoring
```sh
AT+SQNMONI=?
    Available Commands
AT+SQNMONI=7
    +SQNMONI: TELCEL Cc:334 Nc:020 RSRP:-1 CINR:-1 RSRQ:-1 TAC:23330 Id:235 EARFCN:2050 PWR:-1 PAGING:128
```

ICCID - card identification number on the SIM card.
```sh
AT+SQNCCID
    +SQNCCID: "8901260852290270222",""
```

IMSI - International Mobile Subscriber Identity on the SIM card.
```sh
AT+CIMI
    310260859027022
```

Conformance Test Mode
(Standard, vasrizon, att)
```sh
AT+SQNCTM?
    +SQNCTM: standard
```

Network Regfistration Status
EPSRegistrationStatus=5(Roaming),TrackingAreaCode="5B22",AccessTechnology=E-UTRA(LTE)
```sh
AT+CEREG?
    +CEREG: 2,5,"5B22","0362CA01",7
```

PS Attach or Detach (Packet-switched DATA)
0 detacched 1 attached
```sh
AT+CGATT?
    +CGATT: 1
```


### Todos

 - A lot


**42**

[//]: # (These are reference links used in the body of this note and get stripped out when the markdown processor does its job. There is no need to format nicely because it shouldn't be seen. Thanks SO - http://stackoverflow.com/questions/4823468/store-comments-in-markdown-syntax)

   [3GPPTS]: <https://www.arib.or.jp/english/html/overview/doc/STD-T63V12_00/5_Appendix/Rel13/24/24008-d50.pdf>  
   [PSMeDRX]: <https://www.link-labs.com/blog/lte-e-drx-psm-explained-for-lte-m1>  
   [PHY]: <http://rfmw.em.keysight.com/wireless/helpfiles/89600b/webhelp/subsystems/lte/content/lte_overview.htm>
   [PycomSource]: <https://github.com/pycom/pycom-micropython-sigfox/tree/master/esp32/lte>
   [PycomDoc]: <https://github.com/pycom/pycom-documentation/blob/master/firmwareapi/pycom/network/lte.md>
   [ATCommands]: <https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LIL0iGdl11z7Jos_jpX%2F-LKN7scqKKkb6TqB3uYO%2F-LKN82sqi_sHewyinxC5%2Fmonarch_4g-ez_lr5110_atcommands_referencemanual_rev3_noconfidential%20(2).pdf?generation=1534782114581269&alt=media>
