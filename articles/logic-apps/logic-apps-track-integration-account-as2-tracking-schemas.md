---
title: Schémata sledování AS2 pro zprávy B2B
description: Vytvoření schémat sledování AS2, která monitorují zprávy B2B v integračních účtech pro Azure Logic Apps s Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 515d7cfc985ee9929f70de2c862170ff79ae4d60
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792806"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>Vytváření schémat pro sledování zpráv AS2 a MDNs v integračních účtech pro Azure Logic Apps

Abyste vám pomohli monitorovat úspěšné, chyby a vlastnosti zpráv pro transakce B2B (Business-to-Business), můžete tato schémata sledování AS2 použít ve svém účtu integrace:

* Schéma sledování zpráv AS2
* Schéma sledování AS2 MDN

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
      "incomingHeaders": {
       },
      "outgoingHeaders": {
       },
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| senderPartnerName | Řetězec | Název partnera pro odesílatele zprávy AS2. Volitelné |
| receiverPartnerName | Řetězec | Název partnera pro zprávu AS2. Volitelné |
| as2To | Řetězec | Název příjemce zprávy AS2, od záhlaví zprávy AS2. Závaznou |
| as2From | Řetězec | AS2 jméno odesílatele zprávy, a to z hlaviček zprávy AS2. Závaznou |
| smlouva | Řetězec | Název smlouvy AS2, na kterou jsou zprávy vyřešeny Volitelné |
| směr | Řetězec | Směr toku zpráv, přijetí nebo odeslání. Závaznou |
| Parametr | Řetězec | ID zprávy AS2, z hlaviček zprávy AS2 (volitelné) |
| dispositionType |Řetězec | Hodnota typu dispozice oznámení MDN (zpráva) Volitelné |
| fileName | Řetězec | Název souboru, který se nachází v hlavičce zprávy AS2. Volitelné |
| isMessageFailed |Logická hodnota | Zda zpráva AS2 selhala. Závaznou |
| isMessageSigned | Logická hodnota | Určuje, zda byla zpráva AS2 podepsána. Závaznou |
| isMessageEncrypted | Logická hodnota | Určuje, zda byla zpráva AS2 zašifrována. Závaznou |
| isMessageCompressed |Logická hodnota | Zda byla zpráva AS2 komprimovaná. Závaznou |
| CorrelationMessageId | Řetězec | AS2 ID zprávy, aby bylo možné korelovat zprávy pomocí MDNs. Volitelné |
| incomingHeaders |Slovník JToken | Podrobnosti hlavičky zprávy příchozího AS2 Volitelné |
| outgoingHeaders |Slovník JToken | Podrobnosti hlavičky zprávy odchozího AS2 Volitelné |
| isNrrEnabled | Logická hodnota | Pokud není hodnota známá, použijte výchozí hodnotu. Závaznou |
| isMdnExpected | Logická hodnota | Pokud není hodnota známá, použijte výchozí hodnotu. Závaznou |
| mdnType | Výčet | Povolené hodnoty jsou **notconfigured**, **Sync**a **Async**. Závaznou |
||||

## <a name="as2-mdn-tracking-schema"></a>Schéma sledování AS2 MDN

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": "g"
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

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| senderPartnerName | Řetězec | Název partnera pro odesílatele zprávy AS2. Volitelné |
| receiverPartnerName | Řetězec | Název partnera pro zprávu AS2. Volitelné |
| as2To | Řetězec | Název partnera, který obdrží zprávu AS2. Závaznou |
| as2From | Řetězec | Název partnera, který odesílá zprávu AS2. Závaznou |
| smlouva | Řetězec | Název smlouvy AS2, na kterou jsou zprávy vyřešeny Volitelné |
| směr |Řetězec | Směr toku zpráv, přijetí nebo odeslání. Závaznou |
| Parametr | Řetězec | ID zprávy AS2 Volitelné |
| originalMessageId |Řetězec | AS2 původní ID zprávy. Volitelné |
| dispositionType | Řetězec | MDN hodnota typu dispozice. Volitelné |
| isMessageFailed |Logická hodnota | Zda zpráva AS2 selhala. Závaznou |
| isMessageSigned |Logická hodnota | Určuje, zda byla zpráva AS2 podepsána. Závaznou |
| isNrrEnabled | Logická hodnota | Pokud není hodnota známá, použijte výchozí hodnotu. Závaznou |
| StatusCode | Výčet | Povolené hodnoty jsou **přijaté**, **zamítnuté**a **AcceptedWithErrors**. Závaznou |
| micVerificationStatus | Výčet | Povolené hodnoty jsou **NotApplicable**, **Succeeded**a **Failed**. Závaznou |
| CorrelationMessageId | Řetězec | ID korelace. Původní ID zprávy (ID zprávy, pro kterou je nakonfigurované MDN). Volitelné |
| incomingHeaders | Slovník JToken | Označuje podrobnosti hlavičky příchozích zpráv. Volitelné |
| outgoingHeaders |Slovník JToken | Označuje podrobnosti záhlaví odchozí zprávy. Volitelné |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Schémata sledování protokolu B2B

Informace o schématech sledování protokolu B2B najdete v těchto tématech:

* [Schémata sledování X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Vlastní schémata sledování B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Další kroky

* Další informace o [sledování zpráv B2B](logic-apps-monitor-b2b-message.md)
* Další informace o [sledování zpráv B2B v protokolech Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)