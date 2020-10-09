---
title: Schémata sledování X12 pro zprávy B2B
description: Vytvoření sledovacích schémat pro monitorování zpráv X12 pro Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76905300"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Vytváření schémat pro sledování zpráv X12 v Azure Logic Apps

Abyste vám pomohli monitorovat úspěšné, chyby a vlastnosti zpráv pro transakce B2B (Business-to-Business), můžete tato schémata sledování X12 použít ve svém účtu integrace:

* Schéma sledování sady transakcí X12
* Schéma sledování potvrzení sady transakcí X12
* Schéma sledování X12 Interchange
* Schéma sledování potvrzení X12 Interchange
* Schéma sledování skupin funkcí X12
* Schéma sledování potvrzení X12 funkční skupiny

## <a name="x12-transaction-set-tracking-schema"></a>Schéma sledování sady transakcí X12

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
      "needAk2LoopForValidMessages": "",
      "segmentsCount": ""
   }
}
```

| Vlastnost | Požaduje se | Typ | Popis |
|----------|----------|------|-------------|
| senderPartnerName | No | Řetězec | Název partnera pro odesílatele zprávy X12 |
| receiverPartnerName | No | Řetězec | Název partnera pro zprávu X12 |
| senderQualifier | Ano | Řetězec | Poslat kvalifikátor partnerů |
| senderIdentifier | Ano | Řetězec | Odeslat identifikátor partnera |
| receiverQualifier | Ano | Řetězec | Získat kvalifikátor partnerů |
| receiverIdentifier | Ano | Řetězec | Získat identifikátor partnera |
| smlouva | No | Řetězec | Název smlouvy X12, na kterou jsou zprávy vyřešeny |
| směr | Ano | Výčet | Směr toku zpráv, který je buď `receive` nebo `send` |
| interchangeControlNumber | No | Řetězec | Řídicí číslo výměny |
| functionalGroupControlNumber | No | Řetězec | Kontrolní číslo funkce |
| transactionSetControlNumber | No | Řetězec | Kontrolní číslo sady transakcí |
| CorrelationMessageId | No | Řetězec | ID zprávy korelace, která je kombinací {Agreement} {*GroupControlNumber*} {TransactionSetControlNumber} |
| messageType | No | Řetězec | Sada transakcí nebo typ dokumentu |
| isMessageFailed | Ano | Logická hodnota | Zda se nezdařila zpráva X12 |
| isTechnicalAcknowledgmentExpected | Ano | Logická hodnota | Jestli je technické potvrzení nakonfigurované v rámci smlouvy X12 |
| isFunctionalAcknowledgmentExpected | Ano | Logická hodnota | Jestli je funkční potvrzení nakonfigurované v X12 smlouvě |
| needAk2LoopForValidMessages | Ano | Logická hodnota | Určuje, zda je pro platnou zprávu vyžadován cyklus AK2 |
| segmentsCount | No | Integer | Počet segmentů v sadě transakcí X12 |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>Schéma sledování potvrzení sady transakcí X12

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

| Vlastnost | Požaduje se | Typ | Popis |
|----------|----------|------|-------------|
| senderPartnerName | No | Řetězec | Název partnera pro odesílatele zprávy X12 |
| receiverPartnerName | No | Řetězec | Název partnera pro zprávu X12 |
| senderQualifier | Ano | Řetězec | Poslat kvalifikátor partnerů |
| senderIdentifier | Ano | Řetězec | Odeslat identifikátor partnera |
| receiverQualifier | Ano | Řetězec | Získat kvalifikátor partnerů |
| receiverIdentifier | Ano | Řetězec | Získat identifikátor partnera |
| smlouva | No | Řetězec | Název smlouvy X12, na kterou jsou zprávy vyřešeny |
| směr | Ano | Výčet | Směr toku zpráv, který je buď `receive` nebo `send` |
| interchangeControlNumber | No | Řetězec | Kontrolní číslo výměny funkčního potvrzení. Hodnota se naplní jenom pro odesílající stranu, kde se pro zprávy odesílané partnerovi obdrží funkční potvrzení. |
| functionalGroupControlNumber | No | Řetězec | Řídicí skupina funkčního počtu funkčního potvrzení. Hodnota se naplní jenom pro odesílající stranu, kde se pro zprávy odesílané partnerovi obdrží funkční potvrzení. |
| isaSegment | No | Řetězec | Segment ISA zprávy. Hodnota se naplní jenom pro odesílající stranu, kde se pro zprávy odesílané partnerovi obdrží funkční potvrzení. |
| gsSegment | No | Řetězec | Segment GS zprávy Hodnota se naplní jenom pro odesílající stranu, kde se pro zprávy odesílané partnerovi obdrží funkční potvrzení. |
| respondingfunctionalGroupControlNumber | No | Řetězec | Odpovídající kontrolní číslo výměny |
| respondingFunctionalGroupId | No | Řetězec | ID funkční skupiny odpovědi, která se mapuje na AK101 v potvrzení |
| respondingtransactionSetControlNumber | No | Řetězec | Číslo kontrolního čísla sady transakcí |
| respondingTransactionSetId | No | Řetězec | ID sady transakcí s odpovědí, které se v potvrzení namapuje na AK201 |
| statusCode | Ano | Logická hodnota | Stavový kód potvrzení sady transakcí |
| segmentsCount | Ano | Výčet | Stavový kód potvrzení s těmito povolenými hodnotami: `Accepted` , `Rejected` a `AcceptedWithErrors` |
| processingStatus | Ano | Výčet | Stav zpracování potvrzení s těmito povolenými hodnotami: `Received` , `Generated` a `Sent` |
| CorrelationMessageId | No | Řetězec | ID zprávy korelace, která je kombinací {Agreement} {*GroupControlNumber*} {TransactionSetControlNumber} |
| isMessageFailed | Ano | Logická hodnota | Zda se nezdařila zpráva X12 |
| ak2Segment | No | Řetězec | Potvrzení pro sadu transakcí v rámci přijaté funkční skupiny |
| ak3Segment | No | Řetězec | Oznamuje chyby v datovém segmentu. |
| ak5Segment | No | Řetězec | Oznamuje, zda je sada transakcí identifikovaná v segmentu AK2 přijata nebo zamítnuta a proč |
|||||

## <a name="x12-interchange-tracking-schema"></a>Schéma sledování X12 Interchange

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

| Vlastnost | Požaduje se | Typ | Popis |
|----------|----------|------|-------------|
| senderPartnerName | No | Řetězec | Název partnera pro odesílatele zprávy X12 |
| receiverPartnerName | No | Řetězec | Název partnera pro zprávu X12 |
| senderQualifier | Ano | Řetězec | Poslat kvalifikátor partnerů |
| senderIdentifier | Ano | Řetězec | Odeslat identifikátor partnera |
| receiverQualifier | Ano | Řetězec | Získat kvalifikátor partnerů |
| receiverIdentifier | Ano | Řetězec | Získat identifikátor partnera |
| smlouva | No | Řetězec | Název smlouvy X12, na kterou jsou zprávy vyřešeny |
| směr | Ano | Výčet | Směr toku zpráv, který je buď `receive` nebo `send` |
| interchangeControlNumber | No | Řetězec | Řídicí číslo výměny |
| isaSegment | No | Řetězec | Segment zprávy ISA |
| isTechnicalAcknowledgmentExpected | Logická hodnota | Jestli je technické potvrzení nakonfigurované v rámci smlouvy X12  |
| isMessageFailed | Ano | Logická hodnota | Zda se nezdařila zpráva X12 |
| isa09 | No | Řetězec | Datum výměny dokumentu X12 |
| isa10 | No | Řetězec | Doba zaX12 dokumentu pro výměnu |
| isa11 | No | Řetězec | Identifikátor standardů X12 Interchange Control |
| ISA12 | No | Řetězec | Číslo verze ovládacího prvku výměny X12 |
| isa14 | No | Řetězec | Požaduje se potvrzení X12. |
| isa15 | No | Řetězec | Indikátor pro test nebo produkci |
| isa16 | No | Řetězec | Oddělovač prvků |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>Schéma sledování potvrzení X12 Interchange

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

| Vlastnost | Požaduje se | Typ | Popis |
|----------|----------|------|-------------|
| senderPartnerName | No | Řetězec | Název partnera pro odesílatele zprávy X12 |
| receiverPartnerName | No | Řetězec | Název partnera pro zprávu X12 |
| senderQualifier | Ano | Řetězec | Poslat kvalifikátor partnerů |
| senderIdentifier | Ano | Řetězec | Odeslat identifikátor partnera |
| receiverQualifier | Ano | Řetězec | Získat kvalifikátor partnerů |
| receiverIdentifier | Ano | Řetězec | Získat identifikátor partnera |
| smlouva | No | Řetězec | Název smlouvy X12, na kterou jsou zprávy vyřešeny |
| směr | Ano | Výčet | Směr toku zpráv, který je buď `receive` nebo `send` |
| interchangeControlNumber | No | Řetězec | Kontrolní číslo výměny technického potvrzení přijatého od partnerů |
| isaSegment | No | Řetězec | Segment ISA pro technické potvrzení přijaté od partnerů |
| respondingInterchangeControlNumber | No | Řetězec | Kontrolní číslo výměny pro technické potvrzení přijaté od partnerů |
| isMessageFailed | Ano | Logická hodnota | Zda se nezdařila zpráva X12 |
| statusCode | Ano | Výčet | Stavový kód potvrzení výměny s těmito povolenými hodnotami: `Accepted` , `Rejected` a `AcceptedWithErrors` |
| processingStatus | Ano | Výčet | Stav potvrzení s těmito povolenými hodnotami: `Received` , `Generated` a `Sent` |
| ta102 | No | Řetězec | Datum výměny |
| ta103 | No | Řetězec | Doba výměny |
| ta105 | No | Řetězec | Výměna kódu poznámky |
|||||

## <a name="x12-functional-group-tracking-schema"></a>Schéma sledování skupin funkcí X12

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

| Vlastnost | Požaduje se | Typ | Popis |
|----------|----------|------|-------------|
| senderPartnerName | No | Řetězec | Název partnera pro odesílatele zprávy X12 |
| receiverPartnerName | No | Řetězec | Název partnera pro zprávu X12 |
| senderQualifier | Ano | Řetězec | Poslat kvalifikátor partnerů |
| senderIdentifier | Ano | Řetězec | Odeslat identifikátor partnera |
| receiverQualifier | Ano | Řetězec | Získat kvalifikátor partnerů |
| receiverIdentifier | Ano | Řetězec | Získat identifikátor partnera |
| smlouva | No | Řetězec | Název smlouvy X12, na kterou jsou zprávy vyřešeny |
| směr | Ano | Výčet | Směr toku zpráv, buď přijmout, nebo odeslat |
| interchangeControlNumber | No | Řetězec | Řídicí číslo výměny |
| functionalGroupControlNumber | No | Řetězec | Kontrolní číslo funkce |
| gsSegment | No | Řetězec | Segment GS zprávy |
| isTechnicalAcknowledgmentExpected | Ano | Logická hodnota | Jestli je technické potvrzení nakonfigurované v rámci smlouvy X12 |
| isFunctionalAcknowledgmentExpected | Ano | Logická hodnota | Jestli je funkční potvrzení nakonfigurované v X12 smlouvě |
| isMessageFailed | Ano | Logická hodnota | Zda se nezdařila zpráva X12 |
| gs01 | No | Řetězec | Kód funkčního identifikátoru |
| gs02 | No | Řetězec | Kód odesílatele aplikace |
| gs03 | No | Řetězec | Kód příjemce aplikace |
| gs04 | No | Řetězec | Datum funkční skupiny |
| gs05 | No | Řetězec | Čas funkční skupiny |
| gs07 | No | Řetězec | Kód zodpovědné agentury |
| gs08 | No | Řetězec | Kód identifikátoru verze, verze nebo odvětví |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>Schéma sledování potvrzení X12 funkční skupiny

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

| Vlastnost | Požaduje se | Typ | Popis |
|----------|----------|------|-------------|
| senderPartnerName | No | Řetězec | Název partnera pro odesílatele zprávy X12 |
| receiverPartnerName | No | Řetězec | Název partnera pro zprávu X12 |
| senderQualifier | Ano | Řetězec | Poslat kvalifikátor partnerů |
| senderIdentifier | Ano | Řetězec | Odeslat identifikátor partnera |
| receiverQualifier | Ano | Řetězec | Získat kvalifikátor partnerů |
| receiverIdentifier | Ano | Řetězec | Získat identifikátor partnera |
| smlouva | No | Řetězec | Název smlouvy X12, na kterou jsou zprávy vyřešeny |
| směr | Ano | Výčet | Směr toku zpráv, který je buď `receive` nebo `send` |
| interchangeControlNumber | No | Řetězec | Řídicí číslo výměny, které se naplní pro odeslání, když se od partnerů obdrží technické potvrzení. |
| functionalGroupControlNumber | No | Řetězec | Kontrolní skupina – kontrolní číslo technického potvrzení, které se naplní při přijetí technického potvrzení od partnerů |
| isaSegment | No | Řetězec | Stejné jako řídicí číslo výměny, ale vyplněné jenom v určitých případech |
| gsSegment | No | Řetězec | Stejné jako kontrolní číslo skupiny funkcí, ale vyplní se pouze v určitých případech. |
| respondingfunctionalGroupControlNumber | No | Řetězec | Kontrolní číslo původní funkční skupiny |
| respondingFunctionalGroupId | No | Řetězec | Mapuje se na AK101 v ID skupiny funkcí potvrzení. |
| isMessageFailed | Logická hodnota | Zda se nezdařila zpráva X12 |
| statusCode | Ano | Výčet | Stavový kód potvrzení s těmito povolenými hodnotami: `Accepted` , `Rejected` a `AcceptedWithErrors` |
| processingStatus | Ano | Výčet | Stav zpracování potvrzení s těmito povolenými hodnotami: `Received` , `Generated` a `Sent` |
| ak903 | No | Řetězec | Počet přijatých sad transakcí |
| ak904 | No | Řetězec | Počet sad transakcí přijatých v identifikované funkční skupině |
| ak9Segment | No | Řetězec | Zda je funkční skupina identifikovaná v segmentu AK1 přijata nebo zamítnuta a proč |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schémata sledování protokolu B2B

Informace o schématech sledování protokolu B2B najdete v těchto tématech:

* [Schémata sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Vlastní schémata sledování B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Další kroky

* [Monitorování zpráv B2B s využitím protokolů Azure Monitoru](../logic-apps/monitor-b2b-messages-log-analytics.md)