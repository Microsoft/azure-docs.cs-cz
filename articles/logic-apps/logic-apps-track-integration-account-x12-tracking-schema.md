---
title: X12 sledování schémata B2B monitorování - Azure Logic Apps | Microsoft Docs
description: Ke sledování zpráv B2B z transakcí v účtu Azure integrace použít X12 sledování schémat.
author: padmavc
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 385ff73d780f62f19af2face591cd0b2291ef396
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300969"
---
# <a name="start-or-enable-tracking-of-x12-messages-to-monitor-success-errors-and-message-properties"></a>Počáteční nebo povolit sledování X12 zprávy k úspěchu monitorování, chyb a vlastnosti zprávy
Můžete použít tyto X12 sledování schémata ve vašem účtu integrace se službou Azure vám pomohou monitorovat transakce business-to-business (B2B):

* X12 transakce nastavení sledování schématu
* X12 transakce nastavit potvrzení sledování schématu
* X12 interchange sledování schématu
* X12 interchange potvrzení sledování schématu
* X12 funkční skupiny sledování schématu
* X12 funkční skupiny potvrzení sledování schématu

## <a name="x12-transaction-set-tracking-schema"></a>X12 transakce nastavení sledování schématu
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "transactionSetControlNumber": "",
                "CorrelationMessageId": "",
                "messageType": "",
                "isMessageFailed": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "needAk2LoopForValidMessages":  "",
                "segmentsCount": ""
            }
    }
````

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| senderPartnerName | Řetězec | X12 zpráva partnera název odesílatele. (Volitelné) |
| receiverPartnerName | Řetězec | X12 zpráva Název partnera společnosti příjemce. (Volitelné) |
| senderQualifier | Řetězec | Odešlete kvalifikátor partnera. (Povinné) |
| senderIdentifier | Řetězec | Odešlete identifikátor partnera. (Povinné) |
| receiverQualifier | Řetězec | Zobrazí kvalifikátor partnera. (Povinné) |
| receiverIdentifier | Řetězec | Zobrazí identifikátor partnera. (Povinné) |
| agreementName | Řetězec | Název X12 smlouvy, ke kterému jsou vyřešeny zprávy. (Volitelné) |
| směr | výčet | Směr toku zpráv přijímat nebo odesílat. (Povinné) |
| interchangeControlNumber | Řetězec | Interchange číslo ovládacího prvku. (Volitelné) |
| functionalGroupControlNumber | Řetězec | Funkční řízení číslo. (Volitelné) |
| transactionSetControlNumber | Řetězec | Transakce nastavit počet ovládacího prvku. (Volitelné) |
| correlationMessageId | Řetězec | ID korelace zprávy. Kombinace {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}. (Volitelné) |
| Typ zprávy | Řetězec | Transakce nastavit nebo typ dokumentu. (Volitelné) |
| isMessageFailed | Logická hodnota | Jestli X12 zpráv se nezdařilo. (Povinné) |
| isTechnicalAcknowledgmentExpected | Logická hodnota | Jestli je v X12 nakonfigurovaný technické potvrzení smlouvy. (Povinné) |
| isFunctionalAcknowledgmentExpected | Logická hodnota | Zda je funkční potvrzení nakonfigurovaný v X12 smlouvy. (Povinné) |
| needAk2LoopForValidMessages | Logická hodnota | Zda je požadovaná platné zprávy AK2 smyčky. (Povinné) |
| segmentsCount | Integer | Počet segmentů X12 sadu transakce. (Volitelné) |

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 transakce nastavit potvrzení sledování schématu
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "respondingtransactionSetControlNumber": "",
                "respondingTransactionSetId": "",
                "statusCode": "",
                "processingStatus": "",
                "CorrelationMessageId": ""
                "isMessageFailed": "",
                "ak2Segment": "",
                "ak3Segment": "",
                "ak5Segment": ""
            }
    }
````

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| senderPartnerName | Řetězec | X12 zpráva partnera název odesílatele. (Volitelné) |
| receiverPartnerName | Řetězec | X12 zpráva Název partnera společnosti příjemce. (Volitelné) |
| senderQualifier | Řetězec | Odešlete kvalifikátor partnera. (Povinné) |
| senderIdentifier | Řetězec | Odešlete identifikátor partnera. (Povinné) |
| receiverQualifier | Řetězec | Zobrazí kvalifikátor partnera. (Povinné) |
| receiverIdentifier | Řetězec | Zobrazí identifikátor partnera. (Povinné) |
| agreementName | Řetězec | Název X12 smlouvy, ke kterému jsou vyřešeny zprávy. (Volitelné) |
| směr | výčet | Směr toku zpráv přijímat nebo odesílat. (Povinné) |
| interchangeControlNumber | Řetězec | Interchange řízení počet funkční potvrzení. Hodnota naplní pouze pro straně odesílání, kde přijato potvrzení funkční zprávy odeslané na partnera. (Volitelné) |
| functionalGroupControlNumber | Řetězec | Funkční skupiny řízení počet funkční potvrzení. Hodnota naplní pouze pro straně odesílání, kde přijato potvrzení funkční zprávy odeslané na partnera. (Volitelné) |
| isaSegment | Řetězec | Segment ISA zprávy. Hodnota naplní pouze pro straně odesílání, kde přijato potvrzení funkční zprávy odeslané na partnera. (Volitelné) |
| gsSegment | Řetězec | Segment GS zprávy. Hodnota naplní pouze pro straně odesílání, kde přijato potvrzení funkční zprávy odeslané na partnera. (Volitelné) |
| respondingfunctionalGroupControlNumber | Řetězec | Neodpovídá výměnu řízení číslo. (Volitelné) |
| respondingFunctionalGroupId | Řetězec | Neodpovídá ID funkční skupiny, která se mapuje na AK101 na potvrzení. (Volitelné) |
| respondingtransactionSetControlNumber | Řetězec | Odpovídá transakce nastavit počet ovládacího prvku. (Volitelné) |
| respondingTransactionSetId | Řetězec | Odpovídá transakce nastavit ID, která se mapuje na AK201 na potvrzení. (Volitelné) |
| statusCode | Logická hodnota | Transakce nastavit potvrzení stavový kód. (Povinné) |
| segmentsCount | výčet | Potvrzení stavový kód. Povolené hodnoty jsou **platných**, **zamítnutý**, a **AcceptedWithErrors**. (Povinné) |
| StavZpracování | výčet | Stav zpracování potvrzení. Povolené hodnoty jsou **přijaté**, **generované**, a **odeslané**. (Povinné) |
| correlationMessageId | Řetězec | ID korelace zprávy. Kombinace {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}. (Volitelné) |
| isMessageFailed | Logická hodnota | Jestli X12 zpráv se nezdařilo. (Povinné) |
| ak2Segment | Řetězec | Potvrzení transakce nastavit v rámci přijaté funkční skupiny. (Volitelné) |
| ak3Segment | Řetězec | Sestavy chyb v datového segmentu. (Volitelné) |
| ak5Segment | Řetězec | Určí, zda transakce nastavit identifikované v segmentu AK2 přijetí nebo zamítnutí a proto. (Volitelné) |

## <a name="x12-interchange-tracking-schema"></a>X12 interchange sledování schématu
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "isa09": "",
                "isa10": "",
                "isa11": "",
                "isa12": "",
                "isa14": "",
                "isa15": "",
                "isa16": ""
            }
    }
````

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| senderPartnerName | Řetězec | X12 zpráva partnera název odesílatele. (Volitelné) |
| receiverPartnerName | Řetězec | X12 zpráva Název partnera společnosti příjemce. (Volitelné) |
| senderQualifier | Řetězec | Odešlete kvalifikátor partnera. (Povinné) |
| senderIdentifier | Řetězec | Odešlete identifikátor partnera. (Povinné) |
| receiverQualifier | Řetězec | Zobrazí kvalifikátor partnera. (Povinné) |
| receiverIdentifier | Řetězec | Zobrazí identifikátor partnera. (Povinné) |
| agreementName | Řetězec | Název X12 smlouvy, ke kterému jsou vyřešeny zprávy. (Volitelné) |
| směr | výčet | Směr toku zpráv přijímat nebo odesílat. (Povinné) |
| interchangeControlNumber | Řetězec | Interchange číslo ovládacího prvku. (Volitelné) |
| isaSegment | Řetězec | Segment ISA zprávy. (Volitelné) |
| isTechnicalAcknowledgmentExpected | Logická hodnota | Jestli je v X12 nakonfigurovaný technické potvrzení smlouvy. (Povinné) |
| isMessageFailed | Logická hodnota | Jestli X12 zpráv se nezdařilo. (Povinné) |
| isa09 | Řetězec | X12 dokumentu výměnu datum. (Volitelné) |
| isa10 | Řetězec | X12 dokumentů výměnu čas. (Volitelné) |
| isa11 | Řetězec | X12 interchange řízení identifikátor standardů. (Volitelné) |
| isa12 | Řetězec | X12 interchange řízení číslo verze. (Volitelné) |
| isa14 | Řetězec | X12 vyžádání potvrzení. (Volitelné) |
| isa15 | Řetězec | Indikátor pro testovací nebo produkčního prostředí. (Volitelné) |
| isa16 | Řetězec | Element oddělovače. (Volitelné) |

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>X12 interchange potvrzení sledování schématu
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "respondingInterchangeControlNumber": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ta102": "",
                "ta103": "",
                "ta105": ""
            }
    }
````

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| senderPartnerName | Řetězec | X12 zpráva partnera název odesílatele. (Volitelné) |
| receiverPartnerName | Řetězec | X12 zpráva Název partnera společnosti příjemce. (Volitelné) |
| senderQualifier | Řetězec | Odešlete kvalifikátor partnera. (Povinné) |
| senderIdentifier | Řetězec | Odešlete identifikátor partnera. (Povinné) |
| receiverQualifier | Řetězec | Zobrazí kvalifikátor partnera. (Povinné) |
| receiverIdentifier | Řetězec | Zobrazí identifikátor partnera. (Povinné) |
| agreementName | Řetězec | Název X12 smlouvy, ke kterému jsou vyřešeny zprávy. (Volitelné) |
| směr | výčet | Směr toku zpráv přijímat nebo odesílat. (Povinné) |
| interchangeControlNumber | Řetězec | Interchange řízení počet technické potvrzení, které se získaly od partnerů. (Volitelné) |
| isaSegment | Řetězec | Segment ISA pro technické potvrzení, které se získaly od partnerů. (Volitelné) |
| respondingInterchangeControlNumber |Řetězec | Interchange číslo ovládací prvek pro technické potvrzení, které se získaly od partnerů. (Volitelné) |
| isMessageFailed | Logická hodnota | Jestli X12 zpráv se nezdařilo. (Povinné) |
| statusCode | výčet | Interchange potvrzení stavový kód. Povolené hodnoty jsou **platných**, **zamítnutý**, a **AcceptedWithErrors**. (Povinné) |
| StavZpracování | výčet | Stav potvrzení. Povolené hodnoty jsou **přijaté**, **generované**, a **odeslané**. (Povinné) |
| TA102 | Řetězec | Interchange datum. (Volitelné) |
| ta103 | Řetězec | Interchange čas. (Volitelné) |
| ta105 | Řetězec | Interchange Poznámka kódu. (Volitelné) |

## <a name="x12-functional-group-tracking-schema"></a>X12 funkční skupiny sledování schématu
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "gsSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "gs01": "",
                "gs02": "",
                "gs03": "",
                "gs04": "",
                "gs05": "",
                "gs07": "",
                "gs08": ""
            }
    }
````

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| senderPartnerName | Řetězec | X12 zpráva partnera název odesílatele. (Volitelné) |
| receiverPartnerName | Řetězec | X12 zpráva Název partnera společnosti příjemce. (Volitelné) |
| senderQualifier | Řetězec | Odešlete kvalifikátor partnera. (Povinné) |
| senderIdentifier | Řetězec | Odešlete identifikátor partnera. (Povinné) |
| receiverQualifier | Řetězec | Zobrazí kvalifikátor partnera. (Povinné) |
| receiverIdentifier | Řetězec | Zobrazí identifikátor partnera. (Povinné) |
| agreementName | Řetězec | Název X12 smlouvy, ke kterému jsou vyřešeny zprávy. (Volitelné) |
| směr | výčet | Směr toku zpráv přijímat nebo odesílat. (Povinné) |
| interchangeControlNumber | Řetězec | Interchange číslo ovládacího prvku. (Volitelné) |
| functionalGroupControlNumber | Řetězec | Funkční řízení číslo. (Volitelné) |
| gsSegment | Řetězec | Zpráva GS segmentu. (Volitelné) |
| isTechnicalAcknowledgmentExpected | Logická hodnota | Jestli je v X12 nakonfigurovaný technické potvrzení smlouvy. (Povinné) |
| isFunctionalAcknowledgmentExpected | Logická hodnota | Zda je funkční potvrzení nakonfigurovaný v X12 smlouvy. (Povinné) |
| isMessageFailed | Logická hodnota | Jestli X12 zpráv se nezdařilo. (Povinné)|
| gs01 | Řetězec | Funkční identifikačního kódu. (Volitelné) |
| gs02 | Řetězec | Kód aplikace odesílatele. (Volitelné) |
| gs03 | Řetězec | Kód aplikace příjemce. (Volitelné) |
| gs04 | Řetězec | Datum funkční skupiny. (Volitelné) |
| gs05 | Řetězec | Čas funkční skupiny. (Volitelné) |
| gs07 | Řetězec | Příslušné agentury kód. (Volitelné) |
| gs08 | Řetězec | Identifikátor verze nebo verze nebo odvětví kód. (Volitelné) |

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>X12 funkční skupiny potvrzení sledování schématu
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ak903": "",
                "ak904": "",
                "ak9Segment": ""
            }
    }
````

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| senderPartnerName | Řetězec | X12 zpráva partnera název odesílatele. (Volitelné) |
| receiverPartnerName | Řetězec | X12 zpráva Název partnera společnosti příjemce. (Volitelné) |
| senderQualifier | Řetězec | Odešlete kvalifikátor partnera. (Povinné) |
| senderIdentifier | Řetězec | Odešlete identifikátor partnera. (Povinné) |
| receiverQualifier | Řetězec | Zobrazí kvalifikátor partnera. (Povinné) |
| receiverIdentifier | Řetězec | Zobrazí identifikátor partnera. (Povinné) |
| agreementName | Řetězec | Název X12 smlouvy, ke kterému jsou vyřešeny zprávy. (Volitelné) |
| směr | výčet | Směr toku zpráv přijímat nebo odesílat. (Povinné) |
| interchangeControlNumber | Řetězec | Interchange číslo ovládací prvek, který naplní pro straně odeslání, když je obdržena technické potvrzení od partnerů. (Volitelné) |
| functionalGroupControlNumber | Řetězec | Funkční skupiny řízení počet technické potvrzení, který naplní pro straně odeslání, když je obdržena technické potvrzení od partnerů. (Volitelné) |
| isaSegment | Řetězec | Stejné jako výměnu řídit číslo, ale vyplněná pouze v určitých případech. (Volitelné) |
| gsSegment | Řetězec | Stejná jako skupina funkčnosti řídit číslo, ale vyplněná pouze v určitých případech. (Volitelné) |
| respondingfunctionalGroupControlNumber | Řetězec | Ovládací prvek počet původní funkční skupiny. (Volitelné) |
| respondingFunctionalGroupId | Řetězec | ID mapuje AK101 ve skupině funkčnosti potvrzení. (Volitelné) |
| isMessageFailed | Logická hodnota | Jestli X12 zpráv se nezdařilo. (Povinné) |
| statusCode | výčet | Potvrzení stavový kód. Povolené hodnoty jsou **platných**, **zamítnutý**, a **AcceptedWithErrors**. (Povinné) |
| StavZpracování | výčet | Stav zpracování potvrzení. Povolené hodnoty jsou **přijaté**, **generované**, a **odeslané**. (Povinné) |
| ak903 | Řetězec | Počet přijatých sad transakce. (Volitelné) |
| ak904 | Řetězec | Počet transakcí sad přijaté ve skupině identifikovaných funkčnosti. (Volitelné) |
| ak9Segment | Řetězec | Jestli funkční skupiny identifikovat v segmentu AK1 přijetí nebo zamítnutí a proto. (Volitelné) |

## <a name="next-steps"></a>Další postup
* Další informace o [sledování zpráv B2B](logic-apps-monitor-b2b-message.md).
* Další informace o [schémata sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md).
* Další informace o [B2B vlastní sledování schémata](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md).
* Další informace o [sledování zpráv B2B ve analýzy protokolů](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
* Další informace o [Enterprise integračního balíčku](../logic-apps/logic-apps-enterprise-integration-overview.md).  
