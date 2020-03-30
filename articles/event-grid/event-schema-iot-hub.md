---
title: Schéma sítě událostí Azure pro službu IoT Hub | Dokumenty společnosti Microsoft
description: Tento článek obsahuje vlastnosti a schéma pro události služby Azure IoT Hub. Obsahuje seznam dostupných typů událostí, ukázkové události a vlastností události.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/21/2020
ms.author: kgremban
ms.openlocfilehash: cfbd46ad961bd1dc914bae98e761cd83d445ff88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513027"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Schéma událostí služby Azure Event Grid pro službu IoT Hub

Tento článek obsahuje vlastnosti a schéma pro události služby Azure IoT Hub. Úvod do schémat událostí najdete v [tématu schéma událostí služby Azure Event Grid](event-schema.md). 

Seznam ukázkových skriptů a kurzů najdete v tématu [Zdroj událostí služby IoT Hub](event-sources.md#iot-hub).

## <a name="available-event-types"></a>Dostupné typy událostí

Azure IoT Hub vydává následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Devices.DeviceVytvořeno | Publikováno, když je zařízení registrované do centra IoT. |
| Microsoft.Devices.DeviceOdstraněno | Publikováno při odstranění zařízení z centra IoT. | 
| Microsoft.Devices.DevicePřipojeno | Publikováno, když je zařízení připojené k centru IoT hub. |
| Microsoft.Devices.DeviceOdpojeno | Publikováno, když je zařízení odpojeno od centra IoT hub. | 
| Telemetrie Microsoft.Devices.DeviceTelemetrie | Publikováno při odeslání telemetrické zprávy do služby IoT hub. |

Všechny události zařízení s výjimkou událostí telemetrie zařízení jsou obecně dostupné ve všech oblastech podporovaných službou Event Grid. Událost telemetrie zařízení je ve verzi Public Preview a je dostupná ve všech oblastech kromě východních USA, Západní USA, Západní Evropy, [Azure Government](../azure-government/documentation-government-welcome.md), Azure [China 21Vianet](/azure/china/china-welcome)a [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="example-event"></a>Příklad události

Schéma pro DeviceConnected a DeviceDisconnected události mají stejnou strukturu. Tato ukázková událost ukazuje schéma události vyvolané při připojení zařízení k centru IoT:

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

DeviceTelemetry událost je aktivována při telemetrie událost je odeslána do služby IoT Hub. Ukázkové schéma pro tuto událost je uvedeno níže.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Schéma pro DeviceCreated a DeviceDeleted události mají stejnou strukturu. Tato ukázková událost ukazuje schéma události vyvolané při registraci zařízení do centra IoT:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Vlastnosti události

Všechny události obsahují stejná data nejvyšší úrovně: 

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| id | řetězec | Jedinečný identifikátor události |
| téma | řetězec | Úplná cesta k prostředku ke zdroji události. Toto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| Předmět | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| Eventtype | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy je událost generována na základě času UTC zprostředkovatele. |
| data | objekt | Data událostí ioT hubu.  |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Pro všechny události služby IoT Hub obsahuje datový objekt následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| název hubu | řetězec | Název centra IoT Hub, kde bylo zařízení vytvořeno nebo odstraněno. |
| deviceId | řetězec | Jedinečný identifikátor zařízení. Tento řetězec rozlišující malá a velká písmena může mít až 128 znaků a `- : . + % _ # * ? ! ( ) , = @ ; $ '`podporuje 7bitové alfanumerické znaky ASCII a následující speciální znaky: . |

Obsah datového objektu se liší pro každého vydavatele události. 

Pro události služby IoT Hub **připojeného k zařízení** a **odpojeného od zařízení** obsahuje datový objekt následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| modulId | řetězec | Jedinečný identifikátor modulu. Toto pole je výstupní pouze pro modulová zařízení. Tento řetězec rozlišující malá a velká písmena může mít až 128 znaků a `- : . + % _ # * ? ! ( ) , = @ ; $ '`podporuje 7bitové alfanumerické znaky ASCII a následující speciální znaky: . |
| deviceConnectionStateEventInfo | objekt | Informace o stavu připojení zařízení
| sequenceNumber | řetězec | Číslo, které pomáhá indikovat pořadí událostí připojených k zařízení nebo odpojených zařízení. Poslední událost bude mít pořadové číslo, které je vyšší než předchozí událost. Toto číslo se může změnit o více než 1, ale striktně se zvyšuje. Podívejte [se, jak používat pořadové číslo](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Pro událost Služby IoT Hub **telemetrie zařízení** obsahuje datový objekt zprávu zařízení cloud ve [formátu zpráv služby IoT hub](../iot-hub/iot-hub-devguide-messages-construct.md) a má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| text | řetězec | Obsah zprávy ze zařízení. |
| properties | řetězec | Vlastnosti aplikace jsou uživatelem definované řetězce, které lze přidat do zprávy. Tato pole jsou nepovinná. |
| vlastnosti systému | řetězec | [Vlastnosti systému](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) pomáhají identifikovat obsah a zdroj zpráv. Zpráva telemetrie zařízení musí být v platném formátu JSON s contentType nastavena na JSON a contentEncoding nastavena na UTF-8 ve vlastnostech systému zpráv. Pokud to není nastaveno, pak IoT Hub bude psát zprávy v základním formátu 64 kódované.  |

Pro události **služby** IoT Hub vytvořené zařízení a **odstraněné zařízení** obsahuje datový objekt následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| Twin | objekt | Informace o dvojčeti zařízení, což je cloudová reprezentace metadat aplikačního zařízení. | 
| Deviceid | řetězec | Jedinečný identifikátor dvojčete zařízení. | 
| Etag | řetězec | Validátor pro zajištění konzistence aktualizací dvojčete zařízení. Každý etag je zaručeno, že je jedinečný pro každé dvojče zařízení. |  
| deviceEtag| řetězec | Validátor pro zajištění konzistence aktualizací registru zařízení. Každé deviceEtag je zaručeno, že je jedinečný registr na zařízení. |
| status | řetězec | Zda je dvojče zařízení povoleno nebo zakázáno. | 
| stavUpdateTime | řetězec | Časové razítko ISO8601 poslední aktualizace stavu dvojčete zařízení. |
| connectionState | řetězec | Zda je zařízení připojeno nebo odpojeno. | 
| lastActivityTime | řetězec | Časové razítko poslední aktivity ISO8601. | 
| cloudToDeviceMessageCount | celé číslo | Počet zpráv z cloudu do zařízení odeslaných do tohoto zařízení. | 
| authenticationType | řetězec | Typ ověřování používaný pro `SAS`toto zařízení: buď , `SelfSigned`nebo `CertificateAuthority`. |
| x509Otisk palce | řetězec | Kryptografický otisk je jedinečná hodnota certifikátu x509, který se běžně používá k vyhledání konkrétního certifikátu v úložišti certifikátů. Kryptografický otisk je dynamicky generován pomocí algoritmu SHA1 a fyzicky neexistuje v certifikátu. | 
| primární otisk palce | řetězec | Primární kryptografický otisk pro certifikát x509. |
| sekundárníPalec | řetězec | Sekundární kryptografický otisk pro certifikát x509. | 
| version | celé číslo | Celé číslo, které se po každém aktualizaci dvojčete zařízení zintátážmá o jednu. |
| Požadované | objekt | Část vlastností, které mohou být zapsány pouze back-endaplikace a číst zařízení. | 
| Hlášeny | objekt | Část vlastností, které mohou být zapsány pouze zařízení a číst back-end aplikace. |
| lastUpdated | řetězec | Časové razítko ISO8601 poslední aktualizace vlastnosti dvojčete zařízení. | 

## <a name="next-steps"></a>Další kroky

* Úvod do Služby Azure Event Grid najdete v tématu [Co je event grid?](overview.md)
* Informace o tom, jak služby IoT Hub a Event Grid spolupracují, najdete v tématu [Reakce na události služby IoT Hub pomocí služby Event Grid k aktivaci akcí](../iot-hub/iot-hub-event-grid.md).