---
title: Schémata sledování AS2 na zprávy B2B – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvoření schémata sledování AS2, která monitorování zpráv B2B v integračních účtů pro Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.date: 01/27/2017
ms.openlocfilehash: 180d90450497b38f107f3601944385a003f50282
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845779"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>Vytvoření schémat pro sledování zpráv AS2 a něho v integračních účtů pro Azure Logic Apps

Ke sledování úspěšnosti, chyby a vlastnosti zprávy pro transakce business-to-business (B2B), můžete použít tato schémata sledování AS2 v účtu pro integraci:

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
| senderPartnerName | String | Jméno partnera odesílatele zprávy AS2. (Volitelné) |
| receiverPartnerName | String | Jméno partnera příjemce zprávy AS2. (Volitelné) |
| as2To | String | Příjemce zprávy AS2 název ze záhlaví zprávy AS2. (Povinné) |
| as2From | String | Název odesílatele zprávy AS2 od záhlaví zprávy AS2. (Povinné) |
| agreementName | String | Název smlouvy AS2, ke které se řeší zprávy. (Volitelné) |
| direction | String | Směr toku zprávy příjmu nebo odesílání. (Povinné) |
| messageId | String | ID zprávy AS2 od záhlaví zprávy AS2 (volitelné) |
| dispositionType |String | Hodnota typu dispozice zpráva dispozice upozornění (zprávy MDN.). (Volitelné) |
| fileName | String | Název souboru z hlavičky zprávy AS2. (Volitelné) |
| isMessageFailed |Boolean | Určuje, zda zpráva AS2 se nepodařilo. (Povinné) |
| isMessageSigned | Boolean | Určuje, zda byl podepsán zpráva AS2. (Povinné) |
| isMessageEncrypted | Boolean | Určuje, zda byl zašifrován zpráva AS2. (Povinné) |
| isMessageCompressed |Boolean | Určuje, zda byl komprimované zprávy AS2. (Povinné) |
| correlationMessageId | String | ID zprávy AS2 ke korelaci zprávy pomocí něho. (Volitelné) |
| incomingHeaders |Slovník JToken | Příchozí podrobnosti záhlaví zprávy AS2. (Volitelné) |
| outgoingHeaders |Slovník JToken | Odchozí podrobnosti záhlaví zprávy AS2. (Volitelné) |
| isNrrEnabled | Boolean | Používejte výchozí hodnotu, pokud hodnota není známá. (Povinné) |
| isMdnExpected | Boolean | Používejte výchozí hodnotu, pokud hodnota není známá. (Povinné) |
| mdnType | Výčet | Povolené hodnoty jsou **NotConfigured**, **synchronizace**, a **asynchronní**. (Povinné) |
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
| senderPartnerName | String | Jméno partnera odesílatele zprávy AS2. (Volitelné) |
| receiverPartnerName | String | Jméno partnera příjemce zprávy AS2. (Volitelné) |
| as2To | String | Jméno partnera, který obdrží zprávu AS2. (Povinné) |
| as2From | String | Jméno partnera, který odešle zprávu AS2. (Povinné) |
| agreementName | String | Název smlouvy AS2, ke které se řeší zprávy. (Volitelné) |
| direction |String | Směr toku zprávy příjmu nebo odesílání. (Povinné) |
| messageId | String | ID zprávy AS2 (Volitelné) |
| originalMessageId |String | ID původní zprávy AS2 (Volitelné) |
| dispositionType | String | Hodnota disposition typu zprávy MDN. (Volitelné) |
| isMessageFailed |Boolean | Určuje, zda zpráva AS2 se nepodařilo. (Povinné) |
| isMessageSigned |Boolean | Určuje, zda byl podepsán zpráva AS2. (Povinné) |
| isNrrEnabled | Boolean | Používejte výchozí hodnotu, pokud hodnota není známá. (Povinné) |
| statusCode | Výčet | Povolené hodnoty jsou **přijato**, **Odmítnuto**, a **AcceptedWithErrors**. (Povinné) |
| micVerificationStatus | Výčet | Povolené hodnoty jsou **NotApplicable**, **Succeeded**, a **neúspěšné**. (Povinné) |
| correlationMessageId | String | ID korelace. Původní messaged ID (ID zprávy, pro který je nakonfigurovaný zprávy MDN. zprávy). (Volitelné) |
| incomingHeaders | Slovník JToken | Určuje podrobnosti záhlaví příchozí zprávy. (Volitelné) |
| outgoingHeaders |Slovník JToken | Určuje podrobnosti odchozí záhlaví zprávy. (Volitelné) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Schémata sledování B2B protokolu

Informace o protokolu B2B schémata sledování najdete tady:

* [Schémata sledování X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Vlastní schémata sledování B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Další postup

* Další informace o [sledování zpráv B2B](logic-apps-monitor-b2b-message.md)
* Další informace o [sledování zpráv B2B v protokoly Azure monitoru](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)