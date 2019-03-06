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
ms.openlocfilehash: 5fcd7c10002e7e1ae9683fdd89d3af14a1500050
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449185"
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
| id | string | Jedinečný identifikátor pro událost. |
| téma | string | Úplné prostředků cesta ke zdroji události. Toto pole není zapisovatelná. Event gridu poskytuje tuto hodnotu. |
| předmět | string | Vydavatel definované cesta předmět události. |
| eventType | string | Jeden z typů registrované události pro tento zdroj událostí. |
| čas události | string | Vygenerování události podle času UTC poskytovatele. |
| data | objekt | Data událostí služby IoT Hub.  |
| dataVersion | string | Verze schématu datového objektu Vydavatel Určuje verzi schématu. |
| metadataVersion | string | Verze schématu metadat události Event Grid definuje schéma vlastnosti nejvyšší úrovně. Event gridu poskytuje tuto hodnotu. |

Pro všechny události služby IoT Hub datový objekt obsahuje následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| hubName | string | Název služby IoT Hub, kde byl vytvořen zařízení nebo je odstranit. |
| deviceId | string | Jedinečný identifikátor zařízení. Tento řetězec malá a velká písmena mohou být dlouhé až 128 znaků a podporuje ASCII 7bitové alfanumerické znaky a následující speciální znaky: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |

Obsah datového objektu se liší pro každý zdroj události. Pro **zařízení připojeno** a **odpojení zařízení** události služby IoT Hub, datový objekt obsahuje následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| ID modulu | string | Jedinečný identifikátor modulu. Toto pole je výstup pouze pro zařízení se modul. Tento řetězec malá a velká písmena mohou být dlouhé až 128 znaků a podporuje ASCII 7bitové alfanumerické znaky a následující speciální znaky: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| deviceConnectionStateEventInfo | objekt | Informace o události stavu připojení zařízení
| sequenceNumber | string | Číslo, která pomáhá určit pořadí připojeno zařízení nebo zařízení odpojí události. Nejnovější událost může mít pořadové číslo, které je vyšší než předchozí události. Toto číslo může změnit ve více než 1, ale přísné zvýšení. Zobrazit [použití pořadové číslo](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Obsah datového objektu se liší pro každý zdroj události. Pro **zařízení vytvořit** a **zařízení odstraní** události služby IoT Hub, datový objekt obsahuje následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| dvojče | objekt | Informace o dvojčeti zařízení, což je cloudové reprezentace metadat zařízení aplikace. | 
| deviceID | string | Jedinečný identifikátor dvojčeti zařízení. | 
| etag | string | Validátor pro zajištění konzistence aktualizace dvojčete zařízení. Každá značka etag je musí být jedinečný na dvojče zařízení. |  
| deviceEtag| string | Validátor pro zajištění konzistence aktualizací do registru zařízení. Každý deviceEtag je musí být jedinečný na registr zařízení. |
| status | string | Dvojče zařízení určuje, zda je povoleno nebo zakázáno. | 
| statusUpdateTime | string | Aktualizovat ISO8601 časové razítko poslední stav dvojčete zařízení. |
| Vlastnost connectionState | string | Určuje, zda je zařízení připojeno nebo odpojeno. | 
| lastActivityTime | string | ISO8601 časové razítko poslední aktivity. | 
| cloudToDeviceMessageCount | integer | Počet cloudu na zařízení zprávy odeslané k tomuto zařízení. | 
| authenticationType. | string | Typ ověřování používaný pro toto zařízení: buď `SAS`, `SelfSigned`, nebo `CertificateAuthority`. |
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