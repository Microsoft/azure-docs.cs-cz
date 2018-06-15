---
title: Azure událostí mřížky schéma pro IoT Hub | Microsoft Docs
description: Odkaz na stránku vlastností služby IoT Hub a formát schématu událostí
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 812ca3ba546112f54a76319fda853d441ce34f1b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34303534"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Azure schématu události událostí mřížky pro IoT Hub

Tento článek poskytuje vlastnosti a schématu pro události Azure IoT Hub. Úvod do schémata událostí, naleznete v části [schématu události mřížky událostí Azure](event-schema.md). 

## <a name="available-event-types"></a>Typy událostí k dispozici

Azure IoT Hub vysílá následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publikuje, když je zařízení registrováno do služby IoT hub. |
| Microsoft.Devices.DeviceDeleted | Publikuje, když zařízení se odstraní ze služby IoT hub. | 

## <a name="example-event"></a>Příklad událostí

Schéma pro události DeviceCreated a DeviceDeleted mají stejnou strukturu. Tato ukázka událost znázorňuje schéma událost se vyvolá, když je zařízení registrováno do služby IoT hub:

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

Všechny události obsahovat stejné dat nejvyšší úrovně: 

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| id | řetězec | Jedinečný identifikátor pro událost. |
| Téma | řetězec | Úplné prostředků cesta ke zdroji událostí. Toto pole není možné zapisovat. Událost mřížky poskytuje tuto hodnotu. |
| Předmět | řetězec | Cesta definované vydavatele události předmět. |
| Typ události | řetězec | Jeden z typů událostí registrovaných pro tento zdroj událostí. |
| eventTime | řetězec | Čas, který se vygeneruje událost založené na čas UTC poskytovatele. |
| data | objekt | Data událostí služby IoT Hub.  |
| dataVersion | řetězec | Verze schématu datového objektu Vydavatel definuje verze schématu. |
| metadataVersion | řetězec | Verze schématu metadat události Událost mřížky definuje schéma vlastnosti nejvyšší úrovně. Událost mřížky poskytuje tuto hodnotu. |

Obsah objektu dat se liší pro každý zdroj události. Datový objekt pro události služby IoT Hub obsahuje následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| hubName | řetězec | Název centra IoT, kde byla zařízení vytvořené nebo odstraněné. |
| deviceId | řetězec | Jedinečný identifikátor zařízení. Tento řetězec malá a velká písmena může být maximálně 128 znaků dlouhé a podporuje alfanumerických znaků ASCII 7 bitů a následující speciální znaky: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| operationTimestamp | řetězec | Časové razítko ISO8601 operace. |
| opType | řetězec | Typ události, který je určený pro tuto operaci službou IoT Hub: buď `DeviceCreated` nebo `DeviceDeleted`.
| Twin | objekt | Informace o dvojče zařízení, což je represenation cloudové aplikace zařízení metadat. | 
| ID zařízení | řetězec | Jedinečný identifikátor dvojče zařízení. | 
| Značka Etag | řetězec | Část informace, který popisuje obsah dvojče zařízení. Každý značka etag se musí být jedinečný za dvojče zařízení. | 
| status | řetězec | Jestli dvojče zařízení je povolený nebo zakázaný. | 
| statusUpdateTime | řetězec | Aktualizovat ISO8601 časové razítko poslední twin stav zařízení. |
| Hodnota connectionState | řetězec | Jestli je zařízení připojené nebo odpojeno. | 
| lastActivityTime | řetězec | ISO8601 časové razítko poslední aktivity. | 
| cloudToDeviceMessageCount | integer | Počet cloudu na zařízení zprávy odeslané do tohoto zařízení. | 
| authenticationType. | řetězec | Typ ověřování používaný pro toto zařízení: buď `SAS`, `SelfSigned`, nebo `CertificateAuthority`. |
| X509Thumbprint | řetězec | Jedinečnou hodnotu x509 je kryptografický otisk certifikátu, běžně použít k vyhledání konkrétní certifikát v úložišti certifikátů. Kryptografický otisk se dynamicky vygeneruje pomocí algoritmu SHA1 a fyzicky neexistuje v certifikátu. | 
| primaryThumbprint | řetězec | Primární kryptografický otisk pro x509 certifikátu. |
| secondaryThumbprint | řetězec | Sekundární kryptografický otisk pro x509 certifikátu. | 
| verze | integer | Celé číslo, které se zvýší při každém čas zařízení twin se aktualizuje. |
| požadované | objekt | Část vlastnosti, které lze zapsat pouze pomocí back-end aplikace a číst zařízení. | 
| hlášené | objekt | Část vlastnosti, které lze zapsat pouze pomocí zařízení a číst back-end aplikace. |
| lastUpdated | řetězec | Aktualizovat ISO8601 časové razítko poslední twin vlastnosti zařízení. | 

## <a name="next-steps"></a>Další postup

* Úvod do Azure událostí mřížky, najdete v části [co je mřížky událostí?](overview.md)
* Další informace o jak spolupracují IoT Hub a mřížky událostí najdete v tématu [reagování na události služby IoT Hub pomocí mřížky události pro aktivační událost akce](../iot-hub/iot-hub-event-grid.md).