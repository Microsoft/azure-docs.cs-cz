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
ms.openlocfilehash: 4bb33eae53d31701b66d13cb4e810b1a0b8a4b0b
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "42056808"
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

## <a name="example-event"></a>Příklad události

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
    "deviceId": "LogicAppTestDevice",
    "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
    "opType": "DeviceCreated"
  },
  "dataVersion": "",
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

Obsah datového objektu se liší pro každý zdroj události. Pro události služby IoT Hub datový objekt obsahuje následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| HubName | řetězec | Název služby IoT Hub, kde byl vytvořen zařízení nebo je odstranit. |
| deviceId | řetězec | Jedinečný identifikátor zařízení. Tento řetězec malá a velká písmena mohou být dlouhé až 128 znaků a podporuje ASCII 7bitové alfanumerické znaky a následující speciální znaky: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| operationTimestamp | řetězec | Časové razítko ISO8601 operace. |
| opType | řetězec | Typ události, který je určený pro tuto operaci ve službě IoT Hub: buď `DeviceCreated` nebo `DeviceDeleted`.
| dvojče | objekt | Informace o dvojčeti zařízení, což je cloudové represenation metadat zařízení aplikace. | 
| ID zařízení | řetězec | Jedinečný identifikátor dvojčeti zařízení. | 
| Značka Etag | řetězec | Určitý údaj, který popisuje obsah dvojčeti zařízení. Každá značka etag je musí být jedinečný na dvojče zařízení. | 
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