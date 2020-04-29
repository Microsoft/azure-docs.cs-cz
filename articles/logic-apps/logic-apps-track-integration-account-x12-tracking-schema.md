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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
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
| senderPartnerName | Ne | Řetězec | Název partnera pro odesílatele zprávy X12 |
| receiverPartnerName | Ne | Řetězec | Název partnera pro zprávu X12 |
| senderQualifier | Ano | Řetězec | Poslat kvalifikátor partnerů |
| senderIdentifier | Ano | Řetězec | Odeslat identifikátor partnera |
| receiverQualifier | Ano | Řetězec | Získat kvalifikátor partnerů |
| receiverIdentifier | Ano | Řetězec | Získat identifikátor partnera |
| smlouva | Ne | Řetězec | Název smlouvy X12, na kterou jsou zprávy vyřešeny |
| směr | Ano | Výčet | Směr toku zpráv, který je buď `receive` nebo`send` |
| interchangeControlNumber | Ne | Řetězec | Řídicí číslo výměny |
| functionalGroupControlNumber | Ne | Řetězec | Kontrolní číslo funkce |
| transactionSetControlNumber | Ne | Řetězec | Kontrolní číslo sady transakcí |
| CorrelationMessageId | Ne | Řetězec | ID zprávy korelace, která je kombinací {Agreement} {*GroupControlNumber*} {TransactionSetControlNumber} |
| messageType | Ne | Řetězec | Sada transakcí nebo typ dokumentu |
| isMessageFailed | Ano | Logická hodnota | Zda se nezdařila zpráva X12 |
| isTechnicalAcknowledgmentExpected | Ano | Logická hodnota | Jestli je technické potvrzení nakonfigurované v rámci smlouvy X12 |
| isFunctionalAcknowledgmentExpected | Ano | Logická hodnota | Jestli je funkční potvrzení nakonfigurované v X12 smlouvě |
| needAk2LoopForValidMessages | Ano | Logická hodnota | Určuje, zda je pro platnou zprávu vyžadován cyklus AK2 |
| segmentsCount | Ne | Integer | Počet segmentů v sadě transakcí X12 |
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
| senderPartnerName | Ne | Řetězec | Název partnera pro odesílatele zprávy X12 |
| receiverPartnerName | Ne | Řetězec | Název partnera pro zprávu X12 |
| senderQualifier | Ano | Řetězec | Poslat kvalifikátor partnerů |
| senderIdentifier | Ano | Řetězec | Odeslat identifikátor partnera |
| receiverQualifier | Ano | Řetězec | Získat kvalifikátor partnerů |
| receiverIdentifier | Ano | Řetězec | Získat identifikátor partnera |
| smlouva | Ne | Řetězec | Název smlouvy X12, na kterou jsou zprávy vyřešeny |
| směr | Ano | Výčet | Směr toku zpráv, který je buď `receive` nebo`send` |
| interchangeControlNumber | Ne | Řetězec | Kontrolní číslo výměny funkčního potvrzení. Hodnota se naplní jenom pro odesílající stranu, kde se pro zprávy odesílané partnerovi obdrží funkční potvrzení. |
| functionalGroupControlNumber | Ne | Řetězec | Řídicí skupina funkčního počtu funkčního potvrzení. Hodnota se naplní jenom pro odesílající stranu, kde se pro zprávy odesílané partnerovi obdrží funkční potvrzení. |
| isaSegment | Ne | Řetězec | Segment ISA zprávy. Hodnota se naplní jenom pro odesílající stranu, kde se pro zprávy odesílané partnerovi obdrží funkční potvrzení. |
| gsSegment | Ne | Řetězec | Segment GS zprávy Hodnota se naplní jenom pro odesílající stranu, kde se pro zprávy odesílané partnerovi obdrží funkční potvrzení. |
| respondingfunctionalGroupControlNumber | Ne | Řetězec | Odpovídající kontrolní číslo výměny |
| respondingFunctionalGroupId | Ne | Řetězec | ID funkční skupiny odpovědi, která se mapuje na AK101 v potvrzení |
| respondingtransactionSetControlNumber | Ne | Řetězec | Číslo kontrolního čísla sady transakcí |
| respondingTransactionSetId | Ne | Řetězec | ID sady transakcí s odpovědí, které se v potvrzení namapuje na AK201 |
| statusCode | Ano | Logická hodnota | Stavový kód potvrzení sady transakcí |
| segmentsCount | Ano | Výčet | Stavový kód potvrzení s těmito povolenými `Accepted`hodnotami `Rejected`:, a`AcceptedWithErrors` |
| processingStatus | Ano | Výčet | Stav zpracování potvrzení s těmito povolenými hodnotami: `Received`, a `Generated``Sent` |
| CorrelationMessageId | Ne | Řetězec | ID zprávy korelace, která je kombinací {Agreement} {*GroupControlNumber*} {TransactionSetControlNumber} |
| isMessageFailed | Ano | Logická hodnota | Zda se nezdařila zpráva X12 |
| ak2Segment | Ne | Řetězec | Potvrzení pro sadu transakcí v rámci přijaté funkční skupiny |
| ak3Segment | Ne | Řetězec | Oznamuje chyby v datovém segmentu. |
| ak5Segment | Ne | Řetězec | Oznamuje, zda je sada transakcí identifikovaná v segmentu AK2 přijata nebo zamítnuta a proč |
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
| senderPartnerName | Ne | Řetězec | Název partnera pro odesílatele zprávy X12 |
| receiverPartnerName | Ne | Řetězec | Název partnera pro zprávu X12 |
| senderQualifier | Ano | Řetězec | Poslat kvalifikátor partnerů |
| senderIdentifier | Ano | Řetězec | Odeslat identifikátor partnera |
| receiverQualifier | Ano | Řetězec | Získat kvalifikátor partnerů |
| receiverIdentifier | Ano | Řetězec | Získat identifikátor partnera |
| smlouva | Ne | Řetězec | Název smlouvy X12, na kterou jsou zprávy vyřešeny |
| směr | Ano | Výčet | Směr toku zpráv, který je buď `receive` nebo`send` |
| interchangeControlNumber | Ne | Řetězec | Řídicí číslo výměny |
| isaSegment | Ne | Řetězec | Segment zprávy ISA |
| isTechnicalAcknowledgmentExpected | Logická hodnota | Jestli je technické potvrzení nakonfigurované v rámci smlouvy X12  |
| isMessageFailed | Ano | Logická hodnota | Zda se nezdařila zpráva X12 |
| isa09 | Ne | Řetězec | Datum výměny dokumentu X12 |
| isa10 | Ne | Řetězec | Doba zaX12 dokumentu pro výměnu |
| isa11 | Ne | Řetězec | Identifikátor standardů X12 Interchange Control |
| ISA12 | Ne | Řetězec | Číslo verze ovládacího prvku výměny X12 |
| isa14 | Ne | Řetězec | Požaduje se potvrzení X12. |
| isa15 | Ne | Řetězec | Indikátor pro test nebo produkci |
| isa16 | Ne | Řetězec | Oddělovač prvků |
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
| senderPartnerName | Ne | Řetězec | Název partnera pro odesílatele zprávy X12 |
| receiverPartnerName | Ne | Řetězec | Název partnera pro zprávu X12 |
| senderQualifier | Ano | Řetězec | Poslat kvalifikátor partnerů |
| senderIdentifier | Ano | Řetězec | Odeslat identifikátor partnera |
| receiverQualifier | Ano | Řetězec | Získat kvalifikátor partnerů |
| receiverIdentifier | Ano | Řetězec | Získat identifikátor partnera |
| smlouva | Ne | Řetězec | Název smlouvy X12, na kterou jsou zprávy vyřešeny |
| směr | Ano | Výčet | Směr toku zpráv, který je buď `receive` nebo`send` |
| interchangeControlNumber | Ne | Řetězec | Kontrolní číslo výměny technického potvrzení přijatého od partnerů |
| isaSegment | Ne | Řetězec | Segment ISA pro technické potvrzení přijaté od partnerů |
| respondingInterchangeControlNumber | Ne | Řetězec | Kontrolní číslo výměny pro technické potvrzení přijaté od partnerů |
| isMessageFailed | Ano | Logická hodnota | Zda se nezdařila zpráva X12 |
| statusCode | Ano | Výčet | Stavový kód potvrzení výměny s těmito povolenými `Accepted`hodnotami `Rejected`:, a`AcceptedWithErrors` |
| processingStatus | Ano | Výčet | Stav potvrzení s těmito povolenými hodnotami `Received`: `Generated`, a`Sent` |
| ta102 | Ne | Řetězec | Datum výměny |
| ta103 | Ne | Řetězec | Doba výměny |
| ta105 | Ne | Řetězec | Výměna kódu poznámky |
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
| senderPartnerName | Ne | Řetězec | Název partnera pro odesílatele zprávy X12 |
| receiverPartnerName | Ne | Řetězec | Název partnera pro zprávu X12 |
| senderQualifier | Ano | Řetězec | Poslat kvalifikátor partnerů |
| senderIdentifier | Ano | Řetězec | Odeslat identifikátor partnera |
| receiverQualifier | Ano | Řetězec | Získat kvalifikátor partnerů |
| receiverIdentifier | Ano | Řetězec | Získat identifikátor partnera |
| smlouva | Ne | Řetězec | Název smlouvy X12, na kterou jsou zprávy vyřešeny |
| směr | Ano | Výčet | Směr toku zpráv, buď přijmout, nebo odeslat |
| interchangeControlNumber | Ne | Řetězec | Řídicí číslo výměny |
| functionalGroupControlNumber | Ne | Řetězec | Kontrolní číslo funkce |
| gsSegment | Ne | Řetězec | Segment GS zprávy |
| isTechnicalAcknowledgmentExpected | Ano | Logická hodnota | Jestli je technické potvrzení nakonfigurované v rámci smlouvy X12 |
| isFunctionalAcknowledgmentExpected | Ano | Logická hodnota | Jestli je funkční potvrzení nakonfigurované v X12 smlouvě |
| isMessageFailed | Ano | Logická hodnota | Zda se nezdařila zpráva X12 |
| gs01 | Ne | Řetězec | Kód funkčního identifikátoru |
| gs02 | Ne | Řetězec | Kód odesílatele aplikace |
| gs03 | Ne | Řetězec | Kód příjemce aplikace |
| gs04 | Ne | Řetězec | Datum funkční skupiny |
| gs05 | Ne | Řetězec | Čas funkční skupiny |
| gs07 | Ne | Řetězec | Kód zodpovědné agentury |
| gs08 | Ne | Řetězec | Kód identifikátoru verze, verze nebo odvětví |
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
| senderPartnerName | Ne | Řetězec | Název partnera pro odesílatele zprávy X12 |
| receiverPartnerName | Ne | Řetězec | Název partnera pro zprávu X12 |
| senderQualifier | Ano | Řetězec | Poslat kvalifikátor partnerů |
| senderIdentifier | Ano | Řetězec | Odeslat identifikátor partnera |
| receiverQualifier | Ano | Řetězec | Získat kvalifikátor partnerů |
| receiverIdentifier | Ano | Řetězec | Získat identifikátor partnera |
| smlouva | Ne | Řetězec | Název smlouvy X12, na kterou jsou zprávy vyřešeny |
| směr | Ano | Výčet | Směr toku zpráv, který je buď `receive` nebo`send` |
| interchangeControlNumber | Ne | Řetězec | Řídicí číslo výměny, které se naplní pro odeslání, když se od partnerů obdrží technické potvrzení. |
| functionalGroupControlNumber | Ne | Řetězec | Kontrolní skupina – kontrolní číslo technického potvrzení, které se naplní při přijetí technického potvrzení od partnerů |
| isaSegment | Ne | Řetězec | Stejné jako řídicí číslo výměny, ale vyplněné jenom v určitých případech |
| gsSegment | Ne | Řetězec | Stejné jako kontrolní číslo skupiny funkcí, ale vyplní se pouze v určitých případech. |
| respondingfunctionalGroupControlNumber | Ne | Řetězec | Kontrolní číslo původní funkční skupiny |
| respondingFunctionalGroupId | Ne | Řetězec | Mapuje se na AK101 v ID skupiny funkcí potvrzení. |
| isMessageFailed | Logická hodnota | Zda se nezdařila zpráva X12 |
| statusCode | Ano | Výčet | Stavový kód potvrzení s těmito povolenými `Accepted`hodnotami `Rejected`:, a`AcceptedWithErrors` |
| processingStatus | Ano | Výčet | Stav zpracování potvrzení s těmito povolenými hodnotami: `Received`, a `Generated``Sent` |
| ak903 | Ne | Řetězec | Počet přijatých sad transakcí |
| ak904 | Ne | Řetězec | Počet sad transakcí přijatých v identifikované funkční skupině |
| ak9Segment | Ne | Řetězec | Zda je funkční skupina identifikovaná v segmentu AK1 přijata nebo zamítnuta a proč |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schémata sledování protokolu B2B

Informace o schématech sledování protokolu B2B najdete v těchto tématech:

* [Schémata sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Vlastní schémata sledování B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Další kroky

* [Monitorování zpráv B2B s využitím protokolů Azure Monitoru](../logic-apps/monitor-b2b-messages-log-analytics.md)