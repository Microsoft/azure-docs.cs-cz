---
title: Schémata sledování AS2 pro monitorování B2B - Azure Logic Apps | Microsoft Docs
description: Ke sledování zpráv B2B z transakcí v účtu Azure integrace použijte schémata sledování AS2.
author: padmavc
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 48e39fd20716e962c4a3e367fdff18e0b4fba32d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300877"
---
# <a name="start-or-enable-tracking-of-as2-messages-and-mdns-to-monitor-success-errors-and-message-properties"></a>Spuštění nebo povolení sledování AS2 zpráv a MDNs k úspěchu monitorování, chyb a vlastnosti zprávy
Tato schémata sledování AS2 můžete ve vašem účtu integrace se službou Azure vám pomohou monitorovat business-to-business transakce (B2B):

* Schéma sledování AS2 zpráv
* Schéma sledování AS2 MDN

## <a name="as2-message-tracking-schema"></a>Schéma sledování AS2 zpráv
````java

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
````

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| senderPartnerName | Řetězec | Název odesílatele zprávy AS2 partnera. (Volitelné) |
| receiverPartnerName | Řetězec | Příjemce zprávu AS2 název partnera. (Volitelné) |
| as2To | Řetězec | Příjemce zprávu AS2 název ze záhlaví zprávy AS2. (Povinné) |
| as2From | Řetězec | Název odesílatele zprávy AS2 z hlavičky AS2 zprávy. (Povinné) |
| agreementName | Řetězec | Název smlouvy AS2, ke kterému jsou vyřešeny zprávy. (Volitelné) |
| směr | Řetězec | Směr toku zpráv přijímat nebo odesílat. (Povinné) |
| messageId | Řetězec | ID zprávy AS2, z hlavičky AS2 zprávy (volitelné) |
| dispositionType |Řetězec | Hodnota typu dispozice zpráva dispozice oznámení (MDN). (Volitelné) |
| fileName | Řetězec | Název souboru z hlavičky AS2 zprávy. (Volitelné) |
| isMessageFailed |Logická hodnota | Jestli AS2 se nezdařilo. (Povinné) |
| isMessageSigned | Logická hodnota | Jestli byla podepsána zpráva AS2. (Povinné) |
| isMessageEncrypted | Logická hodnota | Zda byla zpráva AS2 šifrována. (Povinné) |
| isMessageCompressed |Logická hodnota | Jestli byla komprimována zpráva AS2. (Povinné) |
| correlationMessageId | Řetězec | ID zprávy AS2 ke korelaci zprávy s MDNs. (Volitelné) |
| incomingHeaders |Slovník JToken | Podrobnosti záhlaví zprávy příchozí AS2. (Volitelné) |
| outgoingHeaders |Slovník JToken | Odchozí AS2 podrobnosti záhlaví zprávy. (Volitelné) |
| isNrrEnabled | Logická hodnota | Použijte výchozí hodnotu, pokud hodnota není znám. (Povinné) |
| isMdnExpected | Logická hodnota | Použijte výchozí hodnotu, pokud hodnota není znám. (Povinné) |
| mdnType | výčet | Povolené hodnoty jsou **NotConfigured**, **synchronizace**, a **asynchronní**. (Povinné) |

## <a name="as2-mdn-tracking-schema"></a>Schéma sledování AS2 MDN
````java

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
````

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| senderPartnerName | Řetězec | Název odesílatele zprávy AS2 partnera. (Volitelné) |
| receiverPartnerName | Řetězec | Příjemce zprávu AS2 název partnera. (Volitelné) |
| as2To | Řetězec | Název partnera, který obdrží zprávu AS2. (Povinné) |
| as2From | Řetězec | Název partnera, který odešle zprávu AS2. (Povinné) |
| agreementName | Řetězec | Název smlouvy AS2, ke kterému jsou vyřešeny zprávy. (Volitelné) |
| směr |Řetězec | Směr toku zpráv přijímat nebo odesílat. (Povinné) |
| messageId | Řetězec | ID AS2 zprávy. (Volitelné) |
| OriginalMessageId |Řetězec | ID AS2 původní zprávy. (Volitelné) |
| dispositionType | Řetězec | Hodnota typu MDN dispozice. (Volitelné) |
| isMessageFailed |Logická hodnota | Jestli AS2 se nezdařilo. (Povinné) |
| isMessageSigned |Logická hodnota | Jestli byla podepsána zpráva AS2. (Povinné) |
| isNrrEnabled | Logická hodnota | Použijte výchozí hodnotu, pokud hodnota není znám. (Povinné) |
| statusCode | výčet | Povolené hodnoty jsou **platných**, **zamítnutý**, a **AcceptedWithErrors**. (Povinné) |
| micVerificationStatus | výčet | Povolené hodnoty jsou **NotApplicable**, **úspěšné**, a **se nezdařilo**. (Povinné) |
| correlationMessageId | Řetězec | ID korelace. Původní messaged ID (ID zprávy zprávy, pro který je nakonfigurovaný MDN). (Volitelné) |
| incomingHeaders | Slovník JToken | Určuje podrobnosti záhlaví příchozí zprávy. (Volitelné) |
| outgoingHeaders |Slovník JToken | Určuje odchozí podrobnosti záhlaví zprávy. (Volitelné) |

## <a name="next-steps"></a>Další postup
* Další informace o [Enterprise integračního balíčku](../logic-apps/logic-apps-enterprise-integration-overview.md).    
* Další informace o [sledování zpráv B2B](logic-apps-monitor-b2b-message.md).   
* Další informace o [B2B vlastní sledování schémata](logic-apps-track-integration-account-custom-tracking-schema.md).   
* Další informace o [X12 sledování schémata](logic-apps-track-integration-account-x12-tracking-schema.md).   
* Další informace o [sledování zpráv B2B ve analýzy protokolů](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
