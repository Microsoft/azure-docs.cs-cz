---
title: Schémata sledování AS2 pro zprávy B2B
description: Vytvoření sledovacích schémat pro monitorování zpráv AS2 v Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "76906974"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>Vytváření schémat pro sledování zpráv AS2 v Azure Logic Apps

Abyste vám pomohli monitorovat úspěšné, chyby a vlastnosti zpráv pro transakce B2B (Business-to-Business), můžete tato schémata sledování AS2 použít ve svém účtu integrace:

* Schéma sledování zpráv AS2
* AS2 schéma sledování oznámení o zařazování zpráv (MDN)

## <a name="as2-message-tracking-schema"></a>Schéma sledování zpráv AS2

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "dispositionType": "",
      "fileName": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isMessageEncrypted": "",
      "isMessageCompressed": "",
      "correlationMessageId": "",
      "incomingHeaders": {},
      "outgoingHeaders": {},
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Vlastnost | Požaduje se | Typ | Description |
|----------|----------|------|-------------|
| senderPartnerName | No | Řetězec | Název partnera pro odesílatele zprávy AS2 |
| receiverPartnerName | No | Řetězec | Název partnera pro zprávu AS2 |
| as2To | Ano | Řetězec | Název příjemce zprávy AS2 z hlaviček zprávy AS2 |
| as2From | Ano | Řetězec | Jméno odesílatele zprávy AS2 z hlaviček zprávy AS2 |
| smlouva | No | Řetězec | Název smlouvy AS2, na kterou jsou zprávy vyřešeny |
| směr | Ano | Řetězec | Směr toku zpráv, který je buď `receive` nebo `send` |
| Parametr | No | Řetězec | AS2 ID zprávy z hlaviček zprávy AS2 |
| dispositionType | No | Řetězec | Hodnota neMDNelné typu oznámení o zařazování zpráv () |
| fileName | No | Řetězec | Název souboru z hlavičky zprávy AS2 |
| isMessageFailed | Yes | Logická hodnota | Zda se nezdařila zpráva AS2 |
| isMessageSigned | Yes | Logická hodnota | Zda byla zpráva AS2 podepsána |
| isMessageEncrypted | Yes | Logická hodnota | Zda byla zpráva AS2 zašifrována |
| isMessageCompressed | Yes | Logická hodnota | Zda byla zpráva AS2 komprimovaná |
| correlationMessageId | No | Řetězec | AS2 ID zprávy pro korelaci zpráv s MDNs |
| incomingHeaders | No | Slovník JToken | Podrobnosti hlavičky zprávy příchozího AS2 |
| outgoingHeaders | No | Slovník JToken | Podrobnosti hlavičky zprávy odchozího AS2 |
| isNrrEnabled | Yes | Logická hodnota | Určuje, zda má být použita výchozí hodnota, pokud není hodnota známa. |
| isMdnExpected | Yes | Logická hodnota | Určuje, zda má být použita výchozí hodnota, pokud není hodnota známa. |
| mdnType | Yes | Výčet | Povolené hodnoty: `NotConfigured` , `Sync` a `Async` |
|||||

## <a name="as2-mdn-tracking-schema"></a>Schéma sledování AS2 MDN

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "originalMessageId": "",
      "dispositionType": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isNrrEnabled": "",
      "statusCode": "",
      "micVerificationStatus": "",
      "correlationMessageId": "",
      "incomingHeaders": {
      },
      "outgoingHeaders": {
      }
   }
}
```

| Vlastnost | Požaduje se | Typ | Description |
|----------|----------|------|-------------|
| senderPartnerName | No | Řetězec | Název partnera pro odesílatele zprávy AS2 |
| receiverPartnerName | No | Řetězec | Název partnera pro zprávu AS2 |
| as2To | Ano | Řetězec | Jméno partnera, který přijímá zprávu AS2 |
| as2From | Ano | Řetězec | Název partnera, který odesílá zprávu AS2 |
| smlouva | No | Řetězec | Název smlouvy AS2, na kterou jsou zprávy vyřešeny |
| směr | Ano | Řetězec | Směr toku zpráv, který je buď `receive` nebo `send` |
| Parametr | No | Řetězec | ID zprávy AS2 |
| originalMessageId | No | Řetězec | ID původní zprávy AS2 |
| dispositionType | No | Řetězec | MDN hodnota typu dispozice |
| isMessageFailed | Yes | Logická hodnota | Zda se nezdařila zpráva AS2 |
| isMessageSigned | Yes | Logická hodnota | Zda byla zpráva AS2 podepsána |
| isNrrEnabled | Yes | Logická hodnota | Určuje, zda má být použita výchozí hodnota, pokud není hodnota známa. |
| statusCode | Yes | Výčet | Povolené hodnoty: `Accepted` , `Rejected` a `AcceptedWithErrors` |
| micVerificationStatus | Yes | Výčet | Povolené hodnoty: `NotApplicable` , `Succeeded` a `Failed` |
| correlationMessageId | No | Řetězec | ID korelace, což je ID původní zprávy s nakonfigurovaným MDN |
| incomingHeaders | No | Slovník JToken | Podrobnosti hlavičky příchozí zprávy |
| outgoingHeaders | No | Slovník JToken | Podrobnosti záhlaví odchozí zprávy |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schémata sledování protokolu B2B

Informace o schématech sledování protokolu B2B najdete v těchto tématech:

* [Schémata sledování X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Vlastní schémata sledování B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Další kroky

* [Monitorování zpráv B2B s využitím protokolů Azure Monitoru](../logic-apps/monitor-b2b-messages-log-analytics.md)