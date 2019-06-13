---
title: X12 sledování schémata na zprávy B2B – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvoření X12 sledování schémat, které monitorování zpráv B2B v integračních účtů pro Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.date: 01/27/2017
ms.openlocfilehash: 1db324006e1e6332b5fdd8afd28ebed8a32ac707
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845762"
---
# <a name="create-schemas-for-tracking-x12-messages-in-integration-accounts-for-azure-logic-apps"></a>Vytvoření schémat pro sledování X12 zprávy v integračních účtů pro Azure Logic Apps

Při monitorování úspěch, chyby a vlastnosti zprávy pro transakce business-to-business (B2B), můžete použít tyto X12 schémata v účtu integrace sledování:

* X12 schéma sledování sady transakcí
* X12 schéma sledování potvrzení sady transakcí
* Schéma sledování výměny X12
* Schéma sledování potvrzení výměny X12
* X12 funkční skupině schéma sledování
* X12 funkční skupině potvrzení schéma sledování

## <a name="x12-transaction-set-tracking-schema"></a>X12 schéma sledování sady transakcí

```json
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
```

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| senderPartnerName | String | X12 zpráv odesílatele název partnera. (Volitelné) |
| receiverPartnerName | String | X12 zprávy jméno od přijímajícího partnera. (Volitelné) |
| senderQualifier | String | Odešlete kvalifikátor partnera. (Povinné) |
| senderIdentifier | String | Odešlete identifikátor partnera. (Povinné) |
| receiverQualifier | String | Přijímat kvalifikátor partnera. (Povinné) |
| receiverIdentifier | String | Zobrazí identifikátor partnera. (Povinné) |
| agreementName | String | Název X12 smlouvy, ke které se řeší zprávy. (Volitelné) |
| direction | Enum | Směr toku zprávy příjmu nebo odesílání. (Povinné) |
| interchangeControlNumber | String | Kontrolní číslo výměny. (Volitelné) |
| functionalGroupControlNumber | String | Funkční kontrolní číslo. (Volitelné) |
| transactionSetControlNumber | String | Kontrolní číslo sady transakcí. (Volitelné) |
| CorrelationMessageId | String | ID korelace zprávy. Kombinace {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}. (Volitelné) |
| messageType | String | Transakce nastavit nebo typ dokumentu. (Volitelné) |
| isMessageFailed | Boolean | Zda X12 zprávy se nezdařilo. (Povinné) |
| isTechnicalAcknowledgmentExpected | Boolean | Určuje, zda je technický potvrzení nakonfigurovaný v X12 smlouvy. (Povinné) |
| isFunctionalAcknowledgmentExpected | Boolean | Určuje, zda je funkční potvrzení gurovaný X12 smlouvy. (Povinné) |
| needAk2LoopForValidMessages | Boolean | Určuje, zda je smyčka smyčku AK2 vyžaduje platnou zprávu. (Povinné) |
| segmentsCount | Integer | Počet segmentů X12 sada transakcí. (Volitelné) |
||||

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 schéma sledování potvrzení sady transakcí

```json
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
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| senderPartnerName | String | X12 zpráv odesílatele název partnera. (Volitelné) |
| receiverPartnerName | String | X12 zprávy jméno od přijímajícího partnera. (Volitelné) |
| senderQualifier | String | Odešlete kvalifikátor partnera. (Povinné) |
| senderIdentifier | String | Odešlete identifikátor partnera. (Povinné) |
| receiverQualifier | String | Přijímat kvalifikátor partnera. (Povinné) |
| receiverIdentifier | String | Zobrazí identifikátor partnera. (Povinné) |
| agreementName | String | Název X12 smlouvy, ke které se řeší zprávy. (Volitelné) |
| direction | Enum | Směr toku zprávy příjmu nebo odesílání. (Povinné) |
| interchangeControlNumber | String | Kontrolní číslo funkční potvrzení výměny. Hodnota naplní pouze pro straně odesílání, kde je přijato funkční potvrzení pro zprávy odeslané na partnera. (Volitelné) |
| functionalGroupControlNumber | String | Kontrolní číslo skupiny funkční funkční potvrzení. Hodnota naplní pouze pro straně odesílání, kde je přijato funkční potvrzení pro zprávy odeslané na partnera. (Volitelné) |
| isaSegment | String | Segment ISA zprávy. Hodnota naplní pouze pro straně odesílání, kde je přijato funkční potvrzení pro zprávy odeslané na partnera. (Volitelné) |
| gsSegment | String | Segment GS zprávy. Hodnota naplní pouze pro straně odesílání, kde je přijato funkční potvrzení pro zprávy odeslané na partnera. (Volitelné) |
| respondingfunctionalGroupControlNumber | String | Kontrolní číslo výměny reagovat. (Volitelné) |
| respondingFunctionalGroupId | String | Neodpovídá ID funkční skupiny, který mapuje na AK101 v potvrzení. (Volitelné) |
| respondingtransactionSetControlNumber | String | Kontrolní číslo sady transakcí reagovat. (Volitelné) |
| respondingTransactionSetId | String | Sady transakcí odpovídá ID, který mapuje na AK201 v potvrzení. (Volitelné) |
| statusCode | Boolean | Stavový kód potvrzení sady transakcí. (Povinné) |
| segmentsCount | Enum | Stavový kód potvrzení. Povolené hodnoty jsou **přijato**, **Odmítnuto**, a **AcceptedWithErrors**. (Povinné) |
| processingStatus | Enum | Stav zpracování potvrzení. Povolené hodnoty jsou **přijaté**, **vygenerované**, a **odeslané**. (Povinné) |
| CorrelationMessageId | String | ID korelace zprávy. Kombinace {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}. (Volitelné) |
| isMessageFailed | Boolean | Zda X12 zprávy se nezdařilo. (Povinné) |
| ak2Segment | String | Potvrzení transakce v rámci přijatý funkční skupiny. (Volitelné) |
| ak3Segment | String | Sestavy chyb v datový segment. (Volitelné) |
| ak5Segment | String | Uvádí, zda je sada identifikované v segmentu smyčku AK2 transakcí přijímat nebo odmítat a proč. (Volitelné) |
||||

## <a name="x12-interchange-tracking-schema"></a>Schéma sledování výměny X12

```json
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
```

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| senderPartnerName | String | X12 zpráv odesílatele název partnera. (Volitelné) |
| receiverPartnerName | String | X12 zprávy jméno od přijímajícího partnera. (Volitelné) |
| senderQualifier | String | Odešlete kvalifikátor partnera. (Povinné) |
| senderIdentifier | String | Odešlete identifikátor partnera. (Povinné) |
| receiverQualifier | String | Přijímat kvalifikátor partnera. (Povinné) |
| receiverIdentifier | String | Zobrazí identifikátor partnera. (Povinné) |
| agreementName | String | Název X12 smlouvy, ke které se řeší zprávy. (Volitelné) |
| direction | Enum | Směr toku zprávy příjmu nebo odesílání. (Povinné) |
| interchangeControlNumber | String | Kontrolní číslo výměny. (Volitelné) |
| isaSegment | String | Segment ISA zprávy. (Volitelné) |
| isTechnicalAcknowledgmentExpected | Boolean | Určuje, zda je technický potvrzení nakonfigurovaný v X12 smlouvy. (Povinné) |
| isMessageFailed | Boolean | Zda X12 zprávy se nezdařilo. (Povinné) |
| isa09 | String | X12 dokumentu výměny datum. (Volitelné) |
| isa10 | String | X12 dokumentu výměny čas. (Volitelné) |
| isa11 | String | Ovládací prvek interchange X12 identifikátor standardy. (Volitelné) |
| isa12 | String | Ovládací prvek interchange X12 číslo verze. (Volitelné) |
| isa14 | String | X12 vyžádání potvrzení. (Volitelné) |
| isa15 | String | Indikátor pro testovací nebo produkční prostředí. (Volitelné) |
| isa16 | String | Prvek oddělovače. (Volitelné) |
||||

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>Schéma sledování potvrzení výměny X12

```json
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
```

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| senderPartnerName | String | X12 zpráv odesílatele název partnera. (Volitelné) |
| receiverPartnerName | String | X12 zprávy jméno od přijímajícího partnera. (Volitelné) |
| senderQualifier | String | Odešlete kvalifikátor partnera. (Povinné) |
| senderIdentifier | String | Odešlete identifikátor partnera. (Povinné) |
| receiverQualifier | String | Přijímat kvalifikátor partnera. (Povinné) |
| receiverIdentifier | String | Zobrazí identifikátor partnera. (Povinné) |
| agreementName | String | Název X12 smlouvy, ke které se řeší zprávy. (Volitelné) |
| direction | Enum | Směr toku zprávy příjmu nebo odesílání. (Povinné) |
| interchangeControlNumber | String | Technické potvrzení, které se získaly od partnerů pro kontrolní číslo výměny. (Volitelné) |
| isaSegment | String | Segment ISA pro technické potvrzení, které se získaly od partnerů. (Volitelné) |
| respondingInterchangeControlNumber |String | Technické potvrzení, které se získaly od partnerů pro kontrolní číslo výměny. (Volitelné) |
| isMessageFailed | Boolean | Zda X12 zprávy se nezdařilo. (Povinné) |
| statusCode | Enum | Výměny. stavový kód potvrzení. Povolené hodnoty jsou **přijato**, **Odmítnuto**, a **AcceptedWithErrors**. (Povinné) |
| processingStatus | Enum | Stav potvrzení. Povolené hodnoty jsou **přijaté**, **vygenerované**, a **odeslané**. (Povinné) |
| ta102 | String | Interchange datum. (Volitelné) |
| ta103 | String | Interchange čas. (Volitelné) |
| ta105 | String | Výměny. Poznámka: kód. (Volitelné) |
||||

## <a name="x12-functional-group-tracking-schema"></a>X12 funkční skupině schéma sledování

```json
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
```

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| senderPartnerName | String | X12 zpráv odesílatele název partnera. (Volitelné) |
| receiverPartnerName | String | X12 zprávy jméno od přijímajícího partnera. (Volitelné) |
| senderQualifier | String | Odešlete kvalifikátor partnera. (Povinné) |
| senderIdentifier | String | Odešlete identifikátor partnera. (Povinné) |
| receiverQualifier | String | Přijímat kvalifikátor partnera. (Povinné) |
| receiverIdentifier | String | Zobrazí identifikátor partnera. (Povinné) |
| agreementName | String | Název X12 smlouvy, ke které se řeší zprávy. (Volitelné) |
| direction | Enum | Směr toku zprávy příjmu nebo odesílání. (Povinné) |
| interchangeControlNumber | String | Kontrolní číslo výměny. (Volitelné) |
| functionalGroupControlNumber | String | Funkční kontrolní číslo. (Volitelné) |
| gsSegment | String | Zpráva GS segmentu. (Volitelné) |
| isTechnicalAcknowledgmentExpected | Boolean | Určuje, zda je technický potvrzení nakonfigurovaný v X12 smlouvy. (Povinné) |
| isFunctionalAcknowledgmentExpected | Boolean | Určuje, zda je funkční potvrzení gurovaný X12 smlouvy. (Povinné) |
| isMessageFailed | Boolean | Zda X12 zprávy se nezdařilo. (Povinné)|
| gs01 | String | Funkční kód. (Volitelné) |
| gs02 | String | Kód aplikace odesílatele. (Volitelné) |
| gs03 | String | Kód aplikace příjemce. (Volitelné) |
| gs04 | String | Funkční skupina datum. (Volitelné) |
| gs05 | String | Funkční skupina čas. (Volitelné) |
| gs07 | String | Příslušné agentury kódu. (Volitelné) |
| gs08 | String | Identifikátor verze nebo verze/odvětví kódu. (Volitelné) |
||||

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>X12 funkční skupině potvrzení schéma sledování

```json
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
```

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| senderPartnerName | String | X12 zpráv odesílatele název partnera. (Volitelné) |
| receiverPartnerName | String | X12 zprávy jméno od přijímajícího partnera. (Volitelné) |
| senderQualifier | String | Odešlete kvalifikátor partnera. (Povinné) |
| senderIdentifier | String | Odešlete identifikátor partnera. (Povinné) |
| receiverQualifier | String | Přijímat kvalifikátor partnera. (Povinné) |
| receiverIdentifier | String | Zobrazí identifikátor partnera. (Povinné) |
| agreementName | String | Název X12 smlouvy, ke které se řeší zprávy. (Volitelné) |
| direction | Enum | Směr toku zprávy příjmu nebo odesílání. (Povinné) |
| interchangeControlNumber | String | Kontrolní číslo výměny, která naplní na straně odesílání při přijetí technické potvrzení od partnerů. (Volitelné) |
| functionalGroupControlNumber | String | Kontrolní číslo skupiny funkční technické potvrzení, která naplní na straně odesílání při přijetí technické potvrzení od partnerů. (Volitelné) |
| isaSegment | String | Stejné jako výměny řídit číslo, ale mají údaj vyplněný pouze ve zvláštních případech. (Volitelné) |
| gsSegment | String | Stejné jako funkční skupiny řídit číslo, ale mají údaj vyplněný pouze ve zvláštních případech. (Volitelné) |
| respondingfunctionalGroupControlNumber | String | Kontrolní číslo skupiny původní funkční. (Volitelné) |
| respondingFunctionalGroupId | String | ID mapuje AK101 ve funkční skupině potvrzení. (Volitelné) |
| isMessageFailed | Boolean | Zda X12 zprávy se nezdařilo. (Povinné) |
| statusCode | Enum | Stavový kód potvrzení. Povolené hodnoty jsou **přijato**, **Odmítnuto**, a **AcceptedWithErrors**. (Povinné) |
| processingStatus | Enum | Stav zpracování potvrzení. Povolené hodnoty jsou **přijaté**, **vygenerované**, a **odeslané**. (Povinné) |
| ak903 | String | Počet přijatých sady transakcí. (Volitelné) |
| ak904 | String | Číslo sady transakcí přijaté v identifikované funkční skupiny. (Volitelné) |
| ak9Segment | String | Zda je funkční skupinu určené v segmentu AK1 přijímat nebo odmítat a proč. (Volitelné) |
|||| 

## <a name="b2b-protocol-tracking-schemas"></a>Schémata sledování B2B protokolu

Informace o protokolu B2B schémata sledování najdete tady:

* [Schémata sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Vlastní schémata sledování B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Další postup

* Další informace o [sledování zpráv B2B](logic-apps-monitor-b2b-message.md).
* Další informace o [sledování zpráv B2B v Azure Monitor protokoly](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
