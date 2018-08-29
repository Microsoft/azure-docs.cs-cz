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
ms.date: 08/17/2018
ms.author: kgremban
ms.openlocfilehash: a86b22b3327b2353dd37a9f9863337d12a009434
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43143569"
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
| id | řetězec | Jedinečný identifikátor pro událost. |
| téma | řetězec | Úplné prostředků cesta ke zdroji události. Toto pole není zapisovatelná. Event gridu poskytuje tuto hodnotu. |
| Předmět | řetězec | Vydavatel definované cesta předmět události. |
| Typ události | řetězec | Jeden z typů registrované události pro tento zdroj událostí. |
| čas události | řetězec | Vygenerování události podle času UTC poskytovatele. |
| data | objekt | Data událostí služby IoT Hub.  |
| dataVersion | řetězec | Verze schématu datového objektu Vydavatel Určuje verzi schématu. |
| verze metadataVersion | řetězec | Verze schématu metadat události Event Grid definuje schéma vlastnosti nejvyšší úrovně. Event gridu poskytuje tuto hodnotu. |

Pro všechny události služby IoT Hub datový objekt obsahuje následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| HubName | řetězec | Název služby IoT Hub, kde byl vytvořen zařízení nebo je odstranit. |
| deviceId | řetězec | Jedinečný identifikátor zařízení. Tento řetězec malá a velká písmena mohou být dlouhé až 128 znaků a podporuje ASCII 7bitové alfanumerické znaky a následující speciální znaky: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |

Obsah datového objektu se liší pro každý zdroj události. Pro **zařízení připojeno** a **odpojení zařízení** události služby IoT Hub, datový objekt obsahuje následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| ID modulu | řetězec | Jedinečný identifikátor modulu. Toto pole je výstup pouze pro zařízení se modul. Tento řetězec malá a velká písmena mohou být dlouhé až 128 znaků a podporuje ASCII 7bitové alfanumerické znaky a následující speciální znaky: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| deviceConnectionStateEventInfo | objekt | Informace o události stavu připojení zařízení
| sequenceNumber | řetězec | Číslo, která pomáhá určit pořadí připojeno zařízení nebo zařízení odpojí události. Nejnovější událost může mít pořadové číslo, které je vyšší než předchozí události. Toto číslo může změnit ve více než 1, ale přísné zvýšení. Zobrazit [použití pořadové číslo](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Obsah datového objektu se liší pro každý zdroj události. Pro **zařízení vytvořit** a **zařízení odstraní** události služby IoT Hub, datový objekt obsahuje následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| dvojče | objekt | Informace o dvojčeti zařízení, což je cloudové represenation metadat zařízení aplikace. | 
| ID zařízení | řetězec | Jedinečný identifikátor dvojčeti zařízení. | 
| Značka Etag | řetězec | Validátor pro zajištění konzistence aktualizace dvojčete zařízení. Každá značka etag je musí být jedinečný na dvojče zařízení. |  
| deviceEtag| řetězec | Validátor pro zajištění konzistence aktualizací do registru zařízení. Každý deviceEtag je musí být jedinečný na registr zařízení. |
| status | řetězec | Dvojče zařízení určuje, zda je povoleno nebo zakázáno. | 
| statusUpdateTime | řetězec | Aktualizovat ISO8601 časové razítko poslední stav dvojčete zařízení. |
| Vlastnost connectionState | řetězec | Určuje, zda je zařízení připojeno nebo odpojeno. | 
| lastActivityTime | řetězec | ISO8601 časové razítko poslední aktivity. | 
| cloudToDeviceMessageCount | integer | Počet cloudu na zařízení zprávy odeslané k tomuto zařízení. | 
| authenticationType. | řetězec | Typ ověřování používaný pro toto zařízení: buď `SAS`, `SelfSigned`, nebo `CertificateAuthority`. |
| X509Thumbprint | řetězec | Jedinečná hodnota pro x509 je kryptografický otisk certifikátu, běžně používají k vyhledání konkrétního certifikátu v úložišti certifikátů. Kryptografický otisk generuje dynamicky pomocí algoritmu SHA1 a fyzicky neexistuje v certifikátu. | 
| primaryThumbprint | řetězec | Primární kryptografický otisk pro x509 certifikátu. |
| secondaryThumbprint | řetězec | Sekundární kryptografický otisk pro x509 certifikátu. | 
| verze | integer | Celé číslo, které je zvýšen o každý čas zařízení dvojčete se aktualizuje. |
| požadované | objekt | Část vlastnosti, které může zapisovat pouze pomocí back endové aplikace a číst zařízení. | 
| Hlášená | objekt | Část vlastnosti, které může zapisovat pouze podle zařízení a číst back endové aplikace. |
| lastUpdated | řetězec | Aktualizovat ISO8601 časové razítko poslední vlastnosti dvojčete zařízení. | 

## <a name="next-steps"></a>Další postup

* Úvod do služby Azure Event Grid najdete v tématu [novinky služby Event Grid?](overview.md)
* Další informace o jak spolu fungují služby IoT Hub a Event Grid najdete v tématu [reagovat na události služby IoT Hub s využitím služby Event Grid pro aktivaci akcí](../iot-hub/iot-hub-event-grid.md).