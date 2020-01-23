---
title: Azure Event Grid schéma pro IoT Hub | Microsoft Docs
description: Tento článek poskytuje vlastnosti a schéma pro události Azure IoT Hub. Obsahuje seznam dostupných typů událostí, příklad události a vlastností události.
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
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513027"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Azure Event Grid schéma událostí pro IoT Hub

Tento článek poskytuje vlastnosti a schéma pro události Azure IoT Hub. Úvod do schémat událostí najdete v tématu [Azure Event Grid schéma událostí](event-schema.md). 

Seznam ukázkových skriptů a kurzů najdete v tématu [IoT Hub zdroj události](event-sources.md#iot-hub).

## <a name="available-event-types"></a>Dostupné typy událostí

Azure IoT Hub emituje následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publikováno, když je zařízení zaregistrované do služby IoT Hub. |
| Microsoft.Devices.DeviceDeleted | Publikováno při odstranění zařízení ze služby IoT Hub. | 
| Microsoft.Devices.DeviceConnected | Publikováno, když je zařízení připojené ke IoT Hub. |
| Microsoft. Devices. DeviceDisconnected | Publikováno, když se zařízení odpojí od služby IoT Hub. | 
| Microsoft.Devices.DeviceTelemetry | Publikováno při odeslání zprávy telemetrie do služby IoT Hub. |

Všechny události zařízení s výjimkou událostí telemetrie zařízení jsou všeobecně dostupné ve všech oblastech podporovaných nástrojem Event Grid. Událost telemetrie zařízení je ve verzi Public Preview a je dostupná ve všech oblastech kromě Východní USA, Západní USA, Západní Evropa, [Azure Government](../azure-government/documentation-government-welcome.md), [Azure Čína 21Vianet](/azure/china/china-welcome)a [Azure Německo](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="example-event"></a>Příklad události

Schéma pro události DeviceConnected a DeviceDisconnected mají stejnou strukturu. Tato ukázková událost ukazuje schéma události vyvolané při připojení zařízení ke službě IoT Hub:

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

Událost DeviceTelemetry se vyvolá při odeslání události telemetrie do IoT Hub. Ukázka schématu pro tuto událost je uvedena níže.

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

Schéma pro události DeviceCreated a DeviceDeleted mají stejnou strukturu. Tato ukázková událost ukazuje schéma události vyvolané při registraci zařízení ve službě IoT Hub:

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
| id | string | Jedinečný identifikátor události |
| téma | string | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| subject | string | Cesta k předmětu události, kterou definuje vydavatel. |
| eventType | string | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | string | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| data | object | IoT Hub data události.  |
| dataVersion | string | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | string | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Pro všechny události IoT Hub datový objekt obsahuje následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| hubName | string | Název IoT Hub, kde se zařízení vytvořilo nebo odstranilo |
| deviceId | string | Jedinečný identifikátor zařízení Tento řetězec, který rozlišuje velká a malá písmena, může být dlouhý až 128 znaků a podporuje i 7 bitů alfanumerických znaků a následující speciální znaky: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |

Obsah datového objektu se u každého vydavatele události liší. 

Pro události **připojené k zařízení** a **odpojení zařízení** IoT Hub datový objekt obsahuje následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| moduleId | string | Jedinečný identifikátor modulu Toto pole je výstupní jenom pro zařízení modulu. Tento řetězec, který rozlišuje velká a malá písmena, může být dlouhý až 128 znaků a podporuje i 7 bitů alfanumerických znaků a následující speciální znaky: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| deviceConnectionStateEventInfo | object | Informace o událostech stavu připojení zařízení
| sequenceNumber | string | Číslo, které pomáhá indikovat pořadí připojených zařízení nebo událostí odpojení zařízení. Poslední událost bude mít pořadové číslo, které je vyšší než předchozí událost. Toto číslo se může změnit o více než 1, ale bude se striktně zvyšovat. Podívejte [se, jak použít pořadové číslo](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

V případě **telemetrie zařízení** IoT Hub událost obsahuje datový objekt zprávu zařízení-Cloud ve [formátu zprávy služby IoT Hub](../iot-hub/iot-hub-devguide-messages-construct.md) a má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| text | string | Obsah zprávy ze zařízení. |
| properties | string | Vlastnosti aplikace jsou uživatelsky definované řetězce, které lze přidat do zprávy. Tato pole jsou volitelná. |
| Vlastnosti systému | string | [Vlastnosti systému](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) vám pomůžou identifikovat obsah a zdroj zpráv. Zpráva telemetrie zařízení musí být v platném formátu JSON se sadou contentType nastavenou na JSON a contentEncoding nastavenou na UTF-8 ve vlastnostech systému zpráv. Pokud tato akce není nastavena, IoT Hub zapíše zprávy ve formátu kódování Base 64.  |

Pro **zařízení vytvořená** a **odstraněná** IoT Hub události obsahuje datový objekt následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| twin | object | Informace o dodaném zařízení, což je cloudová reprezentace metadat aplikačních zařízení. | 
| deviceID | string | Jedinečný identifikátor vlákna zařízení. | 
| etag | string | Validátor pro zajištění konzistence aktualizací v zařízení. U každého ETag je zaručeno, že bude jedinečný pro každé zařízení. |  
| deviceEtag| string | Validátor pro zajištění konzistence aktualizací registru zařízení. Každý deviceEtag je zaručený jako jedinečný v každém registru zařízení. |
| status | string | Zda je zařízení ve dvojím povoleném nebo zakázaném. | 
| statusUpdateTime | string | ISO8601 časové razítko poslední aktualizace stavu vystavení zařízení. |
| connectionState | string | Zda je zařízení připojeno nebo odpojeno. | 
| lastActivityTime | string | ISO8601 časové razítko poslední aktivity. | 
| cloudToDeviceMessageCount | celé číslo | Počet zpráv z cloudu do zařízení odeslaných do tohoto zařízení | 
| authenticationType. | string | Typ ověřování použitý pro toto zařízení: buď `SAS`, `SelfSigned`nebo `CertificateAuthority`. |
| X509Thumbprint | string | Kryptografický otisk je jedinečná hodnota pro certifikát x509, která se běžně používá k vyhledání konkrétního certifikátu v úložišti certifikátů. Kryptografický otisk se dynamicky generuje pomocí algoritmu SHA1 a v certifikátu fyzicky neexistuje. | 
| primaryThumbprint | string | Primární kryptografický otisk pro certifikát x509 |
| secondaryThumbprint | string | Sekundární kryptografický otisk pro certifikát x509 | 
| version | celé číslo | Celé číslo, které je při každé aktualizaci neinteraktivního zařízení zvětšeno o jedno. |
| desired | object | Část vlastností, které lze zapsat pouze back-end aplikací a kterou zařízení čte. | 
| reported | object | Část vlastností, které mohou být zapsány pouze zařízením a které jsou čteny back-end aplikací. |
| lastUpdated | string | ISO8601 časové razítko poslední aktualizace vlastnosti vyzdvojené ze zařízení. | 

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Event Grid najdete v tématu [co je Event Grid?](overview.md)
* Další informace o tom, jak IoT Hub a Event Grid vzájemně spolupracují, najdete v tématu [reakce na IoT Hub události pomocí Event Grid k aktivaci akcí](../iot-hub/iot-hub-event-grid.md).