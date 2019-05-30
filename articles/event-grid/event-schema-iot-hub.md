---
title: Azure Event Grid schéma pro Centrum IoT | Dokumentace Microsoftu
description: Stránka odkazu pro formát schématu událostí a vlastností služby IoT Hub
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: kgremban
ms.openlocfilehash: e770beb0470b54d8e13493bca4790323b2e96ce1
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393188"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Schéma událostí Azure Event Grid pro službu IoT Hub

Tento článek obsahuje vlastnosti a schéma pro události služby Azure IoT Hub. Úvod do schémata událostí, naleznete v tématu [schéma událostí služby Azure Event Grid](event-schema.md). 

Seznam ukázkových skriptů a kurzy, naleznete v tématu [zdroje událostí IoT Hub](event-sources.md#iot-hub).

## <a name="available-event-types"></a>Typy událostí k dispozici

Azure IoT Hub generuje následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publikuje, když je zařízení zaregistrované do služby IoT hub. |
| Microsoft.Devices.DeviceDeleted | Publikuje, když zařízení se odstraní ze služby IoT hub. | 
| Microsoft.Devices.DeviceConnected | Publikuje, když je zařízení připojené do služby IoT hub. |
| Microsoft.Devices.DeviceDisconnected | Publikuje, když zařízení je odpojen od služby IoT hub. | 
| Microsoft.Devices.DeviceTelemetry | Publikovat odeslání telemetrické zprávy do služby IoT hub. |

Všechny události zařízení s výjimkou zařízení telemetrické události jsou obecně dostupné ve všech oblastech podporovaných služby Event Grid. Telemetrické události zařízení je ve verzi public preview a je k dispozici ve všech oblastech kromě USA – východ, USA – Západ, západní Evropa, [Azure Government](/azure-government/documentation-government-welcome.md), [Azure China 21Vianet](/azure/china/china-welcome.md), a [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="example-event"></a>Příklad události

Schéma pro DeviceConnected a DeviceDisconnected události mají stejnou strukturu. Událost vzorku pro tento ukazuje schématu události vyvolané při připojení zařízení do služby IoT hub:

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

DeviceTelemetry událost se vyvolá, když telemetrické události je odeslána do služby IoT Hub. Ukázka schématu pro tuto událost je uveden níže.

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

Schéma pro DeviceCreated a DeviceDeleted události mají stejnou strukturu. Událost vzorku pro tento ukazuje schématu události vyvolané při registraci zařízení do služby IoT hub:

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

Všechny události obsahovat stejná data nejvyšší úrovně: 

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| id | string | Jedinečný identifikátor pro událost. |
| téma | string | Úplné prostředků cesta ke zdroji události. Toto pole není zapisovatelná. Event gridu poskytuje tuto hodnotu. |
| Předmět | string | Vydavatel definované cesta předmět události. |
| eventType | string | Jeden z typů registrované události pro tento zdroj událostí. |
| čas události | string | Vygenerování události podle času UTC poskytovatele. |
| data | objekt | Data událostí služby IoT Hub.  |
| dataVersion | string | Verze schématu datového objektu. Vydavatel Určuje verzi schématu. |
| metadataVersion | string | Verze schématu metadat události. Event Grid definuje schéma vlastnosti nejvyšší úrovně. Event gridu poskytuje tuto hodnotu. |

Pro všechny události služby IoT Hub datový objekt obsahuje následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| hubName | string | Název služby IoT Hub, kde byl vytvořen zařízení nebo je odstranit. |
| deviceId | string | Jedinečný identifikátor zařízení. Tento řetězec malá a velká písmena mohou být dlouhé až 128 znaků a podporuje ASCII 7bitové alfanumerické znaky a následující speciální znaky: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |

Obsah datového objektu se liší pro každý zdroj události. 

Pro **zařízení připojeno** a **odpojení zařízení** události služby IoT Hub, datový objekt obsahuje následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| ID modulu | string | Jedinečný identifikátor modulu. Toto pole je výstup pouze pro zařízení se modul. Tento řetězec malá a velká písmena mohou být dlouhé až 128 znaků a podporuje ASCII 7bitové alfanumerické znaky a následující speciální znaky: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| deviceConnectionStateEventInfo | objekt | Informace o události stavu připojení zařízení
| sequenceNumber | string | Číslo, která pomáhá určit pořadí připojeno zařízení nebo zařízení odpojí události. Nejnovější událost může mít pořadové číslo, které je vyšší než předchozí události. Toto číslo může změnit ve více než 1, ale přísné zvýšení. Zobrazit [použití pořadové číslo](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Pro **Telemetrii zařízení** událostí služby IoT Hub, datový objekt obsahuje zprávu typu zařízení cloud ve [formát zprávy služby IoT hub](../iot-hub/iot-hub-devguide-messages-construct.md) a má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| Text | string | Obsah zprávy ze zařízení. |
| properties | string | Vlastnosti aplikace jsou uživatelem definované řetězce, které lze přidat do zprávy. Tato pole jsou volitelná. |
| Vlastnosti systému | string | [Vlastnosti systému](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) identifikaci obsahu a zdrojové zpráv. Zařízení telemetrické zprávy musí být v platném formátu JSON s contentType nastavit do formátu JSON a contentEncoding nastavena na UTF-8 v systémových vlastnostech zprávy. Pokud to není nastavena, pak služby IoT Hub bude zapisovat zprávy v zakódovaném formátu base 64.  |

Pro **zařízení vytvořit** a **zařízení odstraní** události služby IoT Hub, datový objekt obsahuje následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| dvojče | objekt | Informace o dvojčeti zařízení, což je cloudové reprezentace metadat zařízení aplikace. | 
| deviceID | string | Jedinečný identifikátor dvojčeti zařízení. | 
| Značka Etag | string | Validátor pro zajištění konzistence aktualizace dvojčete zařízení. Každá značka etag je musí být jedinečný na dvojče zařízení. |  
| deviceEtag| string | Validátor pro zajištění konzistence aktualizací do registru zařízení. Každý deviceEtag je musí být jedinečný na registr zařízení. |
| status | string | Dvojče zařízení určuje, zda je povoleno nebo zakázáno. | 
| statusUpdateTime | string | Aktualizovat ISO8601 časové razítko poslední stav dvojčete zařízení. |
| Vlastnost connectionState | string | Určuje, zda je zařízení připojeno nebo odpojeno. | 
| lastActivityTime | string | ISO8601 časové razítko poslední aktivity. | 
| cloudToDeviceMessageCount | integer | Počet cloudu na zařízení zprávy odeslané k tomuto zařízení. | 
| authenticationType | string | Typ ověřování používaný pro toto zařízení: buď `SAS`, `SelfSigned`, nebo `CertificateAuthority`. |
| X509Thumbprint | string | Jedinečná hodnota pro x509 je kryptografický otisk certifikátu, běžně používají k vyhledání konkrétního certifikátu v úložišti certifikátů. Kryptografický otisk generuje dynamicky pomocí algoritmu SHA1 a fyzicky neexistuje v certifikátu. | 
| primaryThumbprint | string | Primární kryptografický otisk pro x509 certifikátu. |
| secondaryThumbprint | string | Sekundární kryptografický otisk pro x509 certifikátu. | 
| version | integer | Celé číslo, které je zvýšen o každý čas zařízení dvojčete se aktualizuje. |
| požadované | objekt | Část vlastnosti, které může zapisovat pouze pomocí back endové aplikace a číst zařízení. | 
| Hlášená | objekt | Část vlastnosti, které může zapisovat pouze podle zařízení a číst back endové aplikace. |
| lastUpdated | string | Aktualizovat ISO8601 časové razítko poslední vlastnosti dvojčete zařízení. | 

## <a name="next-steps"></a>Další postup

* Úvod do služby Azure Event Grid najdete v tématu [novinky služby Event Grid?](overview.md)
* Další informace o jak spolu fungují služby IoT Hub a Event Grid najdete v tématu [reagovat na události služby IoT Hub s využitím služby Event Grid pro aktivaci akcí](../iot-hub/iot-hub-event-grid.md).