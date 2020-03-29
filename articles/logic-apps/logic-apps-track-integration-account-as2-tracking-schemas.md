---
title: AS2 sledovací schémata pro zprávy B2B
description: Vytvoření schémat sledování pro sledování zpráv AS2 v Aplikacích Logika Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906974"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>Vytvoření schémat pro sledování zpráv AS2 v aplikacích Azure Logic Apps

Chcete-li sledovat úspěch, chyby a vlastnosti zpráv pro transakce Mezi podniky (B2B), můžete použít tyto schémata sledování AS2 ve vašem účtu integrace:

* Schéma sledování zpráv AS2
* Schéma sledování sledování dispozice zprávy AS2 (MDN)

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

| Vlastnost | Požaduje se | Typ | Popis |
|----------|----------|------|-------------|
| senderPartnerName | Ne | Řetězec | Název partnera odesílatele zprávy AS2 |
| název_partnera příjemce | Ne | Řetězec | Název partnera příjemce zprávy AS2 |
| as2To | Ano | Řetězec | Jméno příjemce zprávy AS2 z záhlaví zprávy AS2 |
| as2From | Ano | Řetězec | Jméno odesílatele zprávy AS2 ze záhlaví zprávy AS2 |
| název_smlouvy | Ne | Řetězec | Název dohody AS2, ke které jsou zprávy vyřešeny |
| směr | Ano | Řetězec | Směr toku zprávy, který `receive` je buď nebo`send` |
| Messageid | Ne | Řetězec | ID zprávy AS2 z záhlaví zprávy AS2 |
| dispoziceTyp | Ne | Řetězec | Hodnota dispozičního typu dispozice dispozice upozornění na dispoziční oznámení zprávy (MDN) |
| fileName | Ne | Řetězec | Název souboru z hlavičky zprávy AS2 |
| isMessageFailed | Ano | Logická hodnota | Zda se zpráva AS2 nezdařila |
| isMessageSigned | Ano | Logická hodnota | Zda byla zpráva AS2 podepsána |
| isMessageEncrypted | Ano | Logická hodnota | Zda byla zpráva AS2 zašifrována |
| isMessageCompressed | Ano | Logická hodnota | Zda byla zpráva AS2 komprimována |
| correlationMessageId | Ne | Řetězec | ID zprávy AS2 pro korelaci zpráv s mdns |
| příchozízáhlaví | Ne | Slovník JToken | Podrobnosti záhlaví příchozí zprávy AS2 |
| odchozí záhlaví | Ne | Slovník JToken | Podrobnosti záhlaví odchozí zprávy AS2 |
| isNrrEnabled | Ano | Logická hodnota | Jestli použít výchozí hodnotu, pokud není známa |
| isMdnOčekávané | Ano | Logická hodnota | Jestli použít výchozí hodnotu, pokud není známa hodnota |
| mdnTyp | Ano | Výčet | Povolené `NotConfigured`hodnoty: `Sync`, , a`Async` |
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

| Vlastnost | Požaduje se | Typ | Popis |
|----------|----------|------|-------------|
| senderPartnerName | Ne | Řetězec | Název partnera odesílatele zprávy AS2 |
| název_partnera příjemce | Ne | Řetězec | Název partnera příjemce zprávy AS2 |
| as2To | Ano | Řetězec | Jméno partnera, který obdrží zprávu AS2 |
| as2From | Ano | Řetězec | Jméno partnera, který odešle zprávu AS2 |
| název_smlouvy | Ne | Řetězec | Název dohody AS2, ke které jsou zprávy vyřešeny |
| směr | Ano | Řetězec | Směr toku zprávy, který `receive` je buď nebo`send` |
| Messageid | Ne | Řetězec | ID zprávy AS2 |
| originalMessageId | Ne | Řetězec | PŮVODNÍ ID původní zprávy AS2 |
| dispoziceTyp | Ne | Řetězec | Hodnota typu dispozice MDN |
| isMessageFailed | Ano | Logická hodnota | Zda se zpráva AS2 nezdařila |
| isMessageSigned | Ano | Logická hodnota | Zda byla zpráva AS2 podepsána |
| isNrrEnabled | Ano | Logická hodnota | Jestli použít výchozí hodnotu, pokud není známa hodnota |
| Statuscode | Ano | Výčet | Povolené `Accepted`hodnoty: `Rejected`, , a`AcceptedWithErrors` |
| micVerificationStatus | Ano | Výčet | Povolené`NotApplicable`hodnoty: `Succeeded`, , a`Failed` |
| correlationMessageId | Ne | Řetězec | ID korelace, což je ID pro původní zprávu, která má nakonfigurovaný MDN |
| příchozízáhlaví | Ne | Slovník JToken | Podrobnosti záhlaví příchozí zprávy |
| odchozí záhlaví | Ne | Slovník JToken | Podrobnosti záhlaví odchozí zprávy |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schémata sledování protokolu B2B

Informace o schématech sledování protokolu B2B naleznete v tématu:

* [Schémata sledování X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Vlastní sledovací schémata B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Další kroky

* [Monitorování zpráv B2B s využitím protokolů Azure Monitoru](../logic-apps/monitor-b2b-messages-log-analytics.md)