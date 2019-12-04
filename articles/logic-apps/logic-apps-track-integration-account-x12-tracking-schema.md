---
title: Schémata sledování X12 pro zprávy B2B
description: Vytvoření schémat sledování X12, která monitorují zprávy B2B v integračních účtech pro Azure Logic Apps s Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 6f2356600f5b6a637da731c650b26d968092e2f6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791724"
---
# <a name="create-schemas-for-tracking-x12-messages-in-integration-accounts-for-azure-logic-apps"></a>Vytváření schémat pro sledování zpráv X12 v integračních účtech pro Azure Logic Apps

Abyste vám pomohli monitorovat úspěšné, chyby a vlastnosti zpráv pro transakce B2B (Business-to-Business), můžete tato schémata sledování X12 použít ve svém účtu integrace:

* Schéma sledování sady transakcí X12
* Schéma sledování potvrzení X12 sady transakcí
* Schéma sledování X12 Interchange
* Schéma sledování potvrzení X12 výměny
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
      "needAk2LoopForValidMessages":  "",
      "segmentsCount": ""
   }
}
```

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| senderPartnerName | Řetězec | Název partnera pro odesílatele zprávy X12. Volitelné |
| receiverPartnerName | Řetězec | Název partnera pro zprávu X12. Volitelné |
| senderQualifier | Řetězec | Poslat kvalifikátor partnerů Závaznou |
| senderIdentifier | Řetězec | Odeslat identifikátor partnera Závaznou |
| receiverQualifier | Řetězec | Příjem kvalifikátoru partnerů Závaznou |
| receiverIdentifier | Řetězec | Získá identifikátor partnera. Závaznou |
| smlouva | Řetězec | Název smlouvy X12, na kterou jsou zprávy vyřešeny Volitelné |
| směr | Výčet | Směr toku zpráv, přijetí nebo odeslání. Závaznou |
| interchangeControlNumber | Řetězec | Kontrolní číslo výměny Volitelné |
| functionalGroupControlNumber | Řetězec | Číslo funkčního řízení. Volitelné |
| transactionSetControlNumber | Řetězec | Kontrolní číslo sady transakcí Volitelné |
| CorrelationMessageId | Řetězec | ID zprávy korelace. Kombinace {Agreement} {*GroupControlNumber*} {TransactionSetControlNumber} Volitelné |
| messageType | Řetězec | Sada transakcí nebo typ dokumentu. Volitelné |
| isMessageFailed | Logická hodnota | Zda zpráva X12 selhala. Závaznou |
| isTechnicalAcknowledgmentExpected | Logická hodnota | Určuje, jestli je v X12 smlouvě nakonfigurované technické potvrzení. Závaznou |
| isFunctionalAcknowledgmentExpected | Logická hodnota | Určuje, jestli je v X12 smlouvě nakonfigurované funkční potvrzení. Závaznou |
| needAk2LoopForValidMessages | Logická hodnota | Určuje, zda je pro platnou zprávu vyžadován cyklus AK2. Závaznou |
| segmentsCount | Integer | Počet segmentů v sadě transakcí X12 Volitelné |
||||

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>Schéma sledování potvrzení X12 sady transakcí

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
| senderPartnerName | Řetězec | Název partnera pro odesílatele zprávy X12. Volitelné |
| receiverPartnerName | Řetězec | Název partnera pro zprávu X12. Volitelné |
| senderQualifier | Řetězec | Poslat kvalifikátor partnerů Závaznou |
| senderIdentifier | Řetězec | Odeslat identifikátor partnera Závaznou |
| receiverQualifier | Řetězec | Příjem kvalifikátoru partnerů Závaznou |
| receiverIdentifier | Řetězec | Získá identifikátor partnera. Závaznou |
| smlouva | Řetězec | Název smlouvy X12, na kterou jsou zprávy vyřešeny Volitelné |
| směr | Výčet | Směr toku zpráv, přijetí nebo odeslání. Závaznou |
| interchangeControlNumber | Řetězec | Kontrolní číslo výměny funkčního potvrzení. Hodnota se naplní jenom pro odesílající stranu, kde se pro zprávy odesílané partnerovi obdrží funkční potvrzení. Volitelné |
| functionalGroupControlNumber | Řetězec | Kontrolní skupina – počet funkčních potvrzení Hodnota se naplní jenom pro odesílající stranu, kde se pro zprávy odesílané partnerovi obdrží funkční potvrzení. Volitelné |
| isaSegment | Řetězec | Segment ISA zprávy. Hodnota se naplní jenom pro odesílající stranu, kde se pro zprávy odesílané partnerovi obdrží funkční potvrzení. Volitelné |
| gsSegment | Řetězec | Segment GS zprávy Hodnota se naplní jenom pro odesílající stranu, kde se pro zprávy odesílané partnerovi obdrží funkční potvrzení. Volitelné |
| respondingfunctionalGroupControlNumber | Řetězec | Reakce na kontrolní číslo výměny Volitelné |
| respondingFunctionalGroupId | Řetězec | Odpovídá ID skupiny funkcí, která se mapuje na AK101 v potvrzení. Volitelné |
| respondingtransactionSetControlNumber | Řetězec | Reakce na kontrolní číslo sady transakcí Volitelné |
| respondingTransactionSetId | Řetězec | Odpovídá ID sady transakcí, která se mapuje na AK201 v potvrzení. Volitelné |
| StatusCode | Logická hodnota | Stavový kód potvrzení sady transakcí. Závaznou |
| segmentsCount | Výčet | Stavový kód potvrzení. Povolené hodnoty jsou **přijaté**, **zamítnuté**a **AcceptedWithErrors**. Závaznou |
| processingStatus | Výčet | Stav zpracování potvrzení. Povolené hodnoty se **přijímají**, **generují**a **odesílají**. Závaznou |
| CorrelationMessageId | Řetězec | ID zprávy korelace. Kombinace {Agreement} {*GroupControlNumber*} {TransactionSetControlNumber} Volitelné |
| isMessageFailed | Logická hodnota | Zda zpráva X12 selhala. Závaznou |
| ak2Segment | Řetězec | Potvrzení pro sadu transakcí v rámci přijaté funkční skupiny. Volitelné |
| ak3Segment | Řetězec | Oznamuje chyby v datovém segmentu. Volitelné |
| ak5Segment | Řetězec | Oznamuje, zda je sada transakcí identifikovaná v segmentu AK2 přijata nebo zamítnuta a proč. Volitelné |
||||

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

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| senderPartnerName | Řetězec | Název partnera pro odesílatele zprávy X12. Volitelné |
| receiverPartnerName | Řetězec | Název partnera pro zprávu X12. Volitelné |
| senderQualifier | Řetězec | Poslat kvalifikátor partnerů Závaznou |
| senderIdentifier | Řetězec | Odeslat identifikátor partnera Závaznou |
| receiverQualifier | Řetězec | Příjem kvalifikátoru partnerů Závaznou |
| receiverIdentifier | Řetězec | Získá identifikátor partnera. Závaznou |
| smlouva | Řetězec | Název smlouvy X12, na kterou jsou zprávy vyřešeny Volitelné |
| směr | Výčet | Směr toku zpráv, přijetí nebo odeslání. Závaznou |
| interchangeControlNumber | Řetězec | Kontrolní číslo výměny Volitelné |
| isaSegment | Řetězec | Segment zprávy ISA. Volitelné |
| isTechnicalAcknowledgmentExpected | Logická hodnota | Určuje, jestli je v X12 smlouvě nakonfigurované technické potvrzení. Závaznou |
| isMessageFailed | Logická hodnota | Zda zpráva X12 selhala. Závaznou |
| isa09 | Řetězec | Datum výměny dokumentu X12 Volitelné |
| isa10 | Řetězec | Doba pro výměnu dokumentů X12 Volitelné |
| isa11 | Řetězec | Identifikátor standardů řízení výměny X12 Volitelné |
| ISA12 | Řetězec | Číslo verze řídicího prvku výměny X12 Volitelné |
| isa14 | Řetězec | Je požadováno potvrzení X12. Volitelné |
| isa15 | Řetězec | Indikátor pro test nebo produkci Volitelné |
| isa16 | Řetězec | Oddělovač elementu. Volitelné |
||||

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>Schéma sledování potvrzení X12 výměny

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
| senderPartnerName | Řetězec | Název partnera pro odesílatele zprávy X12. Volitelné |
| receiverPartnerName | Řetězec | Název partnera pro zprávu X12. Volitelné |
| senderQualifier | Řetězec | Poslat kvalifikátor partnerů Závaznou |
| senderIdentifier | Řetězec | Odeslat identifikátor partnera Závaznou |
| receiverQualifier | Řetězec | Příjem kvalifikátoru partnerů Závaznou |
| receiverIdentifier | Řetězec | Získá identifikátor partnera. Závaznou |
| smlouva | Řetězec | Název smlouvy X12, na kterou jsou zprávy vyřešeny Volitelné |
| směr | Výčet | Směr toku zpráv, přijetí nebo odeslání. Závaznou |
| interchangeControlNumber | Řetězec | Kontrolní číslo výměny technického potvrzení, které obdrží od partnerů. Volitelné |
| isaSegment | Řetězec | Segment ISA pro technické potvrzení, které obdrželi od partnerů. Volitelné |
| respondingInterchangeControlNumber |Řetězec | Kontrolní číslo výměny pro technické potvrzení, které obdrží od partnerů. Volitelné |
| isMessageFailed | Logická hodnota | Zda zpráva X12 selhala. Závaznou |
| StatusCode | Výčet | Stavový kód potvrzení výměny. Povolené hodnoty jsou **přijaté**, **zamítnuté**a **AcceptedWithErrors**. Závaznou |
| processingStatus | Výčet | Stav potvrzení. Povolené hodnoty se **přijímají**, **generují**a **odesílají**. Závaznou |
| ta102 | Řetězec | Datum výměny Volitelné |
| ta103 | Řetězec | Doba výměny Volitelné |
| ta105 | Řetězec | Výměna kódu poznámky Volitelné |
||||

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

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| senderPartnerName | Řetězec | Název partnera pro odesílatele zprávy X12. Volitelné |
| receiverPartnerName | Řetězec | Název partnera pro zprávu X12. Volitelné |
| senderQualifier | Řetězec | Poslat kvalifikátor partnerů Závaznou |
| senderIdentifier | Řetězec | Odeslat identifikátor partnera Závaznou |
| receiverQualifier | Řetězec | Příjem kvalifikátoru partnerů Závaznou |
| receiverIdentifier | Řetězec | Získá identifikátor partnera. Závaznou |
| smlouva | Řetězec | Název smlouvy X12, na kterou jsou zprávy vyřešeny Volitelné |
| směr | Výčet | Směr toku zpráv, přijetí nebo odeslání. Závaznou |
| interchangeControlNumber | Řetězec | Kontrolní číslo výměny Volitelné |
| functionalGroupControlNumber | Řetězec | Číslo funkčního řízení. Volitelné |
| gsSegment | Řetězec | Segment GS zprávy Volitelné |
| isTechnicalAcknowledgmentExpected | Logická hodnota | Určuje, jestli je v X12 smlouvě nakonfigurované technické potvrzení. Závaznou |
| isFunctionalAcknowledgmentExpected | Logická hodnota | Určuje, jestli je v X12 smlouvě nakonfigurované funkční potvrzení. Závaznou |
| isMessageFailed | Logická hodnota | Zda zpráva X12 selhala. Závaznou|
| gs01 | Řetězec | Kód funkčního identifikátoru Volitelné |
| gs02 | Řetězec | Kód odesílatele aplikace Volitelné |
| gs03 | Řetězec | Kód přijímače aplikace Volitelné |
| gs04 | Řetězec | Datum funkční skupiny Volitelné |
| gs05 | Řetězec | Čas funkční skupiny Volitelné |
| gs07 | Řetězec | Kód zodpovědné agentury Volitelné |
| gs08 | Řetězec | Verze/verze/kód identifikátoru odvětví Volitelné |
||||

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>Schéma sledování potvrzení X12 funkční skupiny

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
| senderPartnerName | Řetězec | Název partnera pro odesílatele zprávy X12. Volitelné |
| receiverPartnerName | Řetězec | Název partnera pro zprávu X12. Volitelné |
| senderQualifier | Řetězec | Poslat kvalifikátor partnerů Závaznou |
| senderIdentifier | Řetězec | Odeslat identifikátor partnera Závaznou |
| receiverQualifier | Řetězec | Příjem kvalifikátoru partnerů Závaznou |
| receiverIdentifier | Řetězec | Získá identifikátor partnera. Závaznou |
| smlouva | Řetězec | Název smlouvy X12, na kterou jsou zprávy vyřešeny Volitelné |
| směr | Výčet | Směr toku zpráv, přijetí nebo odeslání. Závaznou |
| interchangeControlNumber | Řetězec | Řídicí číslo výměny, které se naplní pro odeslání, když se od partnerů obdrží technické potvrzení. Volitelné |
| functionalGroupControlNumber | Řetězec | Kontrolní skupina – kontrolní počet technického potvrzení, které se naplní pro odeslání, když od partnerů obdrží technickou potvrzení. Volitelné |
| isaSegment | Řetězec | Stejné jako řídicí číslo výměny, ale vyplní se pouze v určitých případech. Volitelné |
| gsSegment | Řetězec | Stejné jako kontrolní číslo skupiny funkcí, ale vyplní se pouze v určitých případech. Volitelné |
| respondingfunctionalGroupControlNumber | Řetězec | Kontrolní číslo původní funkční skupiny. Volitelné |
| respondingFunctionalGroupId | Řetězec | Provede mapování na AK101 v ID skupiny funkcí potvrzení. Volitelné |
| isMessageFailed | Logická hodnota | Zda zpráva X12 selhala. Závaznou |
| StatusCode | Výčet | Stavový kód potvrzení. Povolené hodnoty jsou **přijaté**, **zamítnuté**a **AcceptedWithErrors**. Závaznou |
| processingStatus | Výčet | Stav zpracování potvrzení. Povolené hodnoty se **přijímají**, **generují**a **odesílají**. Závaznou |
| ak903 | Řetězec | Počet přijatých sad transakcí. Volitelné |
| ak904 | Řetězec | Počet sad transakcí přijatých v identifikované funkční skupině. Volitelné |
| ak9Segment | Řetězec | Zda je funkční skupina identifikovaná v segmentu AK1 přijata nebo zamítnuta a proč. Volitelné |
|||| 

## <a name="b2b-protocol-tracking-schemas"></a>Schémata sledování protokolu B2B

Informace o schématech sledování protokolu B2B najdete v těchto tématech:

* [Schémata sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Vlastní schémata sledování B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [monitorování zpráv B2B](logic-apps-monitor-b2b-message.md).
* Přečtěte si [, jak sledovat zprávy B2B v protokolech Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
