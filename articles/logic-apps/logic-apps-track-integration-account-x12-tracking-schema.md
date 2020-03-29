---
title: Schémata sledování X12 pro zprávy B2B
description: Vytvoření schémat sledování pro monitorování zpráv X12 pro aplikace Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905300"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Vytvoření schémat pro sledování zpráv X12 v Aplikacích Logika Azure

Chcete-li sledovat úspěch, chyby a vlastnosti zpráv pro transakce Mezi podniky (B2B), můžete použít tyto schémata sledování X12 ve vašem účtu integrace:

* Schéma sledování sady transakcí X12
* Schéma sledování potvrzení sady transakcí X12
* Schéma sledování výměny X12
* Schéma sledování potvrzení výměny X12
* Schéma sledování funkční skupiny X12
* Schéma sledování potvrzení funkční skupiny X12

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
| senderPartnerName | Ne | Řetězec | Název partnera odesílatele zprávy X12 |
| název_partnera příjemce | Ne | Řetězec | Název partnera příjemce zprávy X12 |
| kvalifikace odesílatele | Ano | Řetězec | Odeslat kvalifikátor partnera |
| senderIdentifier | Ano | Řetězec | Odeslat identifikátor partnera |
| přijímačKvalifikace | Ano | Řetězec | Přijmout kvalifikátor partnera |
| receiverIdentifier | Ano | Řetězec | Přijmout identifikátor partnera |
| název_smlouvy | Ne | Řetězec | Název smlouvy X12, ke které jsou zprávy vyřešeny |
| směr | Ano | Výčet | Směr toku zprávy, který `receive` je buď nebo`send` |
| interchangeControlNumber | Ne | Řetězec | Číslo řízení výměny |
| functionalGroupControlNumber | Ne | Řetězec | Funkční kontrolní číslo |
| transactionSetControlNumber | Ne | Řetězec | Kontrolní číslo sady transakcí |
| Korelační čísloId | Ne | Řetězec | ID zprávy o korelaci, což je kombinace {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} |
| Messagetype | Ne | Řetězec | Sada transakcí nebo typ dokladu |
| isMessageFailed | Ano | Logická hodnota | Zda se zpráva X12 nezdařila |
| isTechnicalAcknowledgementExpected | Ano | Logická hodnota | Zda je technické potvrzení nakonfigurováno ve smlouvě X12 |
| isFunctionalAcknowledgementExpected | Ano | Logická hodnota | Zda je funkční potvrzení nakonfigurováno ve smlouvě X12 |
| needAk2LoopForValidMessages | Ano | Logická hodnota | Zda je smyčka AK2 vyžadována pro platnou zprávu |
| segmentyPočet | Ne | Integer | Počet segmentů v sadě transakcí X12 |
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
| senderPartnerName | Ne | Řetězec | Název partnera odesílatele zprávy X12 |
| název_partnera příjemce | Ne | Řetězec | Název partnera příjemce zprávy X12 |
| kvalifikace odesílatele | Ano | Řetězec | Odeslat kvalifikátor partnera |
| senderIdentifier | Ano | Řetězec | Odeslat identifikátor partnera |
| přijímačKvalifikace | Ano | Řetězec | Přijmout kvalifikátor partnera |
| receiverIdentifier | Ano | Řetězec | Přijmout identifikátor partnera |
| název_smlouvy | Ne | Řetězec | Název smlouvy X12, ke které jsou zprávy vyřešeny |
| směr | Ano | Výčet | Směr toku zprávy, který `receive` je buď nebo`send` |
| interchangeControlNumber | Ne | Řetězec | Číslo ovládacího prvku výměny funkčního potvrzení. Hodnota se vyplní pouze pro stranu odeslání, kde je přijato funkční potvrzení pro zprávy odeslané partnerovi. |
| functionalGroupControlNumber | Ne | Řetězec | Kontrolní číslo funkční skupiny funkčního potvrzení. Hodnota se naplní pouze pro stranu odeslání, kde je přijato funkční potvrzení pro zprávy odeslané partnerovi |
| isaSegment | Ne | Řetězec | ISA segment zprávy. Hodnota se naplní pouze pro stranu odeslání, kde je přijato funkční potvrzení pro zprávy odeslané partnerovi |
| gsSegment | Ne | Řetězec | Segment GS zprávy. Hodnota se naplní pouze pro stranu odeslání, kde je přijato funkční potvrzení pro zprávy odeslané partnerovi |
| respondingfunctionalGroupControlNumber | Ne | Řetězec | Odpovídající číslo řízení výměny |
| respondingFunctionalGroupId | Ne | Řetězec | Odpovídající Funkční ID skupiny, které se mapuje na AK101 v potvrzení |
| respondingTransactionSetControlNumber | Ne | Řetězec | Odpovídající číslo ovládacího prvku pro transakci |
| respondingTransactionSetId | Ne | Řetězec | ID odpovídající sady transakcí, které se mapuje na AK201 v potvrzení |
| Statuscode | Ano | Logická hodnota | Stavový kód potvrzení sady transakcí |
| segmentyPočet | Ano | Výčet | Stavový kód potvrzení s těmito povolenými hodnotami: `Accepted`, `Rejected`, a`AcceptedWithErrors` |
| processingStatus | Ano | Výčet | Stav zpracování potvrzení s těmito povolenými hodnotami: `Received`, `Generated`, a`Sent` |
| Korelační čísloId | Ne | Řetězec | ID zprávy o korelaci, což je kombinace {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} |
| isMessageFailed | Ano | Logická hodnota | Zda se zpráva X12 nezdařila |
| ak2Segment | Ne | Řetězec | Potvrzení pro sadu transakcí v rámci přijaté funkční skupiny |
| ak3Segment | Ne | Řetězec | Hlásí chyby v datovém segmentu |
| ak5Segment | Ne | Řetězec | Hlásí, zda je sada transakcí identifikovaná v segmentu AK2 přijata nebo odmítnuta a proč |
|||||

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

| Vlastnost | Požaduje se | Typ | Popis |
|----------|----------|------|-------------|
| senderPartnerName | Ne | Řetězec | Název partnera odesílatele zprávy X12 |
| název_partnera příjemce | Ne | Řetězec | Název partnera příjemce zprávy X12 |
| kvalifikace odesílatele | Ano | Řetězec | Odeslat kvalifikátor partnera |
| senderIdentifier | Ano | Řetězec | Odeslat identifikátor partnera |
| přijímačKvalifikace | Ano | Řetězec | Přijmout kvalifikátor partnera |
| receiverIdentifier | Ano | Řetězec | Přijmout identifikátor partnera |
| název_smlouvy | Ne | Řetězec | Název smlouvy X12, ke které jsou zprávy vyřešeny |
| směr | Ano | Výčet | Směr toku zprávy, který `receive` je buď nebo`send` |
| interchangeControlNumber | Ne | Řetězec | Číslo řízení výměny |
| isaSegment | Ne | Řetězec | Segment ISA zprávy |
| isTechnicalAcknowledgementExpected | Logická hodnota | Zda je technické potvrzení nakonfigurováno ve smlouvě X12  |
| isMessageFailed | Ano | Logická hodnota | Zda se zpráva X12 nezdařila |
| isa09 | Ne | Řetězec | X12 datum výměny dokumentu |
| isa10 | Ne | Řetězec | X12 doba výměny dokumentů |
| isa11 | Ne | Řetězec | Identifikátor standardů výměny x12 |
| isa12 | Ne | Řetězec | Číslo verze ovládacího prvku výměny X12 |
| isa14 | Ne | Řetězec | Je požadováno potvrzení X12 |
| isa15 | Ne | Řetězec | Ukazatel pro zkoušku nebo výrobu |
| isa16 | Ne | Řetězec | Oddělovač prvků |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>Schéma sledování potvrzení výměny X12

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
| senderPartnerName | Ne | Řetězec | Název partnera odesílatele zprávy X12 |
| název_partnera příjemce | Ne | Řetězec | Název partnera příjemce zprávy X12 |
| kvalifikace odesílatele | Ano | Řetězec | Odeslat kvalifikátor partnera |
| senderIdentifier | Ano | Řetězec | Odeslat identifikátor partnera |
| přijímačKvalifikace | Ano | Řetězec | Přijmout kvalifikátor partnera |
| receiverIdentifier | Ano | Řetězec | Přijmout identifikátor partnera |
| název_smlouvy | Ne | Řetězec | Název smlouvy X12, ke které jsou zprávy vyřešeny |
| směr | Ano | Výčet | Směr toku zprávy, který `receive` je buď nebo`send` |
| interchangeControlNumber | Ne | Řetězec | Kontrolní číslo výměny technického potvrzení, které bylo přijato od partnerů |
| isaSegment | Ne | Řetězec | Segment ISA pro technické potvrzení, které bylo přijato od partnerů |
| respondingInterchangeControlNumber | Ne | Řetězec | Kontrolní číslo pro výměnu technického potvrzení, které bylo přijato od partnerů |
| isMessageFailed | Ano | Logická hodnota | Zda se zpráva X12 nezdařila |
| Statuscode | Ano | Výčet | Stavový kód potvrzení výměny s `Accepted`těmito povolenými hodnotami: , `Rejected`, a`AcceptedWithErrors` |
| processingStatus | Ano | Výčet | Stav potvrzení s těmito `Received`povolenými hodnotami: , `Generated`, a`Sent` |
| ta102 | Ne | Řetězec | Datum výměny |
| ta103 | Ne | Řetězec | Čas výměny |
| ta105 řekl: | Ne | Řetězec | Kód výměnné poznámky |
|||||

## <a name="x12-functional-group-tracking-schema"></a>Schéma sledování funkční skupiny X12

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
| senderPartnerName | Ne | Řetězec | Název partnera odesílatele zprávy X12 |
| název_partnera příjemce | Ne | Řetězec | Název partnera příjemce zprávy X12 |
| kvalifikace odesílatele | Ano | Řetězec | Odeslat kvalifikátor partnera |
| senderIdentifier | Ano | Řetězec | Odeslat identifikátor partnera |
| přijímačKvalifikace | Ano | Řetězec | Přijmout kvalifikátor partnera |
| receiverIdentifier | Ano | Řetězec | Přijmout identifikátor partnera |
| název_smlouvy | Ne | Řetězec | Název smlouvy X12, ke které jsou zprávy vyřešeny |
| směr | Ano | Výčet | Směr toku zprávy, buď přijímat nebo odesílat |
| interchangeControlNumber | Ne | Řetězec | Číslo řízení výměny |
| functionalGroupControlNumber | Ne | Řetězec | Funkční kontrolní číslo |
| gsSegment | Ne | Řetězec | Segment Zprávy GS |
| isTechnicalAcknowledgementExpected | Ano | Logická hodnota | Zda je technické potvrzení nakonfigurováno ve smlouvě X12 |
| isFunctionalAcknowledgementExpected | Ano | Logická hodnota | Zda je funkční potvrzení nakonfigurováno ve smlouvě X12 |
| isMessageFailed | Ano | Logická hodnota | Zda se zpráva X12 nezdařila |
| gs01 | Ne | Řetězec | Kód funkčního identifikátoru |
| gs02 | Ne | Řetězec | Kód odesílatele aplikace |
| gs03 | Ne | Řetězec | Kód přijímače aplikace |
| gs04 | Ne | Řetězec | Datum funkční skupiny |
| gs05 | Ne | Řetězec | Funkční skupinový čas |
| gs07 | Ne | Řetězec | Kód odpovědné agentury |
| gs08 | Ne | Řetězec | Identifikační kód pro verzi, vydání nebo odvětví |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>Schéma sledování potvrzení funkční skupiny X12

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
| senderPartnerName | Ne | Řetězec | Název partnera odesílatele zprávy X12 |
| název_partnera příjemce | Ne | Řetězec | Název partnera příjemce zprávy X12 |
| kvalifikace odesílatele | Ano | Řetězec | Odeslat kvalifikátor partnera |
| senderIdentifier | Ano | Řetězec | Odeslat identifikátor partnera |
| přijímačKvalifikace | Ano | Řetězec | Přijmout kvalifikátor partnera |
| receiverIdentifier | Ano | Řetězec | Přijmout identifikátor partnera |
| název_smlouvy | Ne | Řetězec | Název smlouvy X12, ke které jsou zprávy vyřešeny |
| směr | Ano | Výčet | Směr toku zprávy, který `receive` je buď nebo`send` |
| interchangeControlNumber | Ne | Řetězec | Kontrolní číslo výměny, které se naplní pro stranu odeslání, když je od partnerů přijato technické potvrzení |
| functionalGroupControlNumber | Ne | Řetězec | Kontrolní číslo funkční skupiny technického potvrzení, které se naplní pro stranu odeslání, když je od partnerů přijato technické potvrzení |
| isaSegment | Ne | Řetězec | Stejné jako číslo řízení výměny, ale vyplněné pouze v určitých případech |
| gsSegment | Ne | Řetězec | Stejné jako kontrolní číslo funkční skupiny, ale vyplněno pouze v určitých případech |
| respondingfunctionalGroupControlNumber | Ne | Řetězec | Kontrolní číslo původní funkční skupiny |
| respondingFunctionalGroupId | Ne | Řetězec | Mapy na AK101 v potvrzení funkční skupiny ID |
| isMessageFailed | Logická hodnota | Zda se zpráva X12 nezdařila |
| Statuscode | Ano | Výčet | Stavový kód potvrzení s těmito povolenými hodnotami: `Accepted`, `Rejected`, a`AcceptedWithErrors` |
| processingStatus | Ano | Výčet | Stav zpracování potvrzení s těmito povolenými hodnotami: `Received`, `Generated`, a`Sent` |
| ak903 | Ne | Řetězec | Počet přijatých sad transakcí |
| ak904 | Ne | Řetězec | Počet sad transakcí přijatých v identifikované funkční skupině |
| ak9Segment | Ne | Řetězec | Zda je funkční skupina identifikovaná v segmentu AK1 přijata nebo odmítnuta a proč |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schémata sledování protokolu B2B

Informace o schématech sledování protokolu B2B naleznete v tématu:

* [Schémata sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Vlastní sledovací schémata B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Další kroky

* [Monitorování zpráv B2B s využitím protokolů Azure Monitoru](../logic-apps/monitor-b2b-messages-log-analytics.md)