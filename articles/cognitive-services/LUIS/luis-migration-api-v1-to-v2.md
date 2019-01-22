---
title: V1 na v2 migrace rozhraní API
titleSuffix: Azure Cognitive Services
description: Koncový bod verze 1 a pro vytváření rozhraní API jsou zastaralé. Tento průvodce vám pochopit, jak migrovat na koncový bod verze 2 a rozhraní API pro vytváření.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 9542fe14cc262731ca0f2ade65e6e4dfafbc34d7
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427504"
---
# <a name="api-v1-to-v2-migration-guide-for-luis-apps"></a>Rozhraní API v1 na v2 Průvodce migrací pro aplikace LUIS
Verze 1 [koncový bod](https://aka.ms/v1-endpoint-api-docs) a [vytváření](https://aka.ms/v1-authoring-api-docs) jsou zastaralé rozhraní API. Tento průvodce vám pochopit, jak migrovat na verzi 2 [koncový bod](https://aka.ms/luis-endpoint-apis) a [vytváření](https://aka.ms/luis-authoring-apis) rozhraní API. 

## <a name="new-azure-regions"></a>Nové oblasti Azure
Služba LUIS je nový [oblastech](https://aka.ms/LUIS-regions) k rozhraním API LUIS k dispozici. Služba LUIS obsahuje jiný web pro oblasti skupiny. Aplikace musí být vytvořen ve stejné oblasti, které očekáváte, že k dotazování. Aplikace se nemigrují automaticky oblastech. Exportujte aplikace z jedné oblasti a importovat do jiného být k dispozici v nové oblasti.

## <a name="authoring-route-changes"></a>Vytváření změny směrování
Vytváření rozhraní API route změnit pomocí **GID** trasy k použití **api** trasy.


| version | trasa |
|--|--|
|1|/luis/V1.0/**GID**/apps|
|2|/luis/**api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>Koncový bod trasy změny
Koncový bod rozhraní API má nové parametry řetězce dotazu, stejně jako jiné odpověď. Pokud příznak podrobné hodnotu true, všechny příkazy, bez ohledu na to skóre, které jsou vráceny v poli s názvem záměrů, kromě topScoringIntent.

| version | ZÍSKAT trasu |
|--|--|
|1|/luis/V1/Application? ID = {appId} & q = {q}|
|2|/ luis/v2.0/apps/{appId}?q={q} [& timezoneOffset] [& podrobné] [& Kontrola pravopisu] [& pracovní] [& Bingu – pravopisu – kontrola subscription-key] [& protokolu]|


Úspěšná odpověď byla V1 koncový bod:
```json
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

Úspěšná odpověď byla v2 koncový bod:
```json
{
  "query": "forward to frank 30 dollars through HSBC",
  "topScoringIntent": {
    "intent": "give",
    "score": 0.3964121
  },
  "entities": [
    {
      "entity": "30",
      "type": "builtin.number",
      "startIndex": 17,
      "endIndex": 18,
      "resolution": {
        "value": "30"
      }
    },
    {
      "entity": "frank",
      "type": "frank",
      "startIndex": 11,
      "endIndex": 15,
      "score": 0.935219169
    },
    {
      "entity": "30 dollars",
      "type": "builtin.currency",
      "startIndex": 17,
      "endIndex": 26,
      "resolution": {
        "unit": "Dollar",
        "value": "30"
      }
    },
    {
      "entity": "hsbc",
      "type": "Bank",
      "startIndex": 36,
      "endIndex": 39,
      "resolution": {
        "values": [
          "BankeName"
        ]
      }
    }
  ]
}
```

## <a name="key-management-no-longer-in-api"></a>Správa klíčů už v rozhraní API
Klíč předplatného koncový bod rozhraní API jsou zastaralé, vrací 410 GONE.

| version | trasa |
|--|--|
|1|/luis/V1.0/Prog/subscriptions|
|1|/ luis/v1.0/prog/subscriptions/{subscriptionKey}|

Azure [klíče koncového bodu](luis-how-to-azure-subscription.md) se generují na portálu Azure portal. Přiřazení klíče na aplikaci LUIS na **[publikovat](luis-how-to-azure-subscription.md)** stránky. Nepotřebujete vědět, skutečná hodnota klíče. Služba LUIS přiřazení pomocí názvu předplatného. 

## <a name="new-versioning-route"></a>Novou trasu správy verzí
V2 model je teď součástí [verze](luis-how-to-manage-versions.md). Název verze byla 10 znaků v této trase. Výchozí verze je "0.1".

| version | trasa |
|--|--|
|1|/luis/V1.0/**GID**/apps/ {appId} / entity|
|2|/luis/**api**/v2.0/apps/{appId}/**verze**/ {versionId} / entity|

## <a name="metadata-renamed"></a>Metadata přejmenovat
Několik rozhraní API, které vracejí LUIS metadata mají nové názvy.

| název trasy, která V1 | název trasy, která v2 |
|--|--|
|PersonalAssistantApps |Pomocníci|
|applicationcultures|jazykové verze|
|applicationdomains|domény|
|applicationusagescenarios|usagescenarios|


## <a name="sample-renamed-to-suggest"></a>"Ukázkový" přejmenovat "navrhnout"
LUIS navrhuje projevy ze stávajících [koncový bod projevy](luis-how-to-review-endoint-utt.md) modelu, který může vylepšit. V předchozí verzi, to nazýval **ukázka**. V nové verzi, se změní název ze vzorku **navrhnout**. Tento postup se nazývá **[zkontrolujte koncový bod projevy](luis-how-to-review-endoint-utt.md)** LUIS webu.

| version | trasa |
|--|--|
|1|/luis/V1.0/**GID**/entities/ /apps/ {appId} {entityId} /**vzorku**|
|1|/luis/V1.0/**GID**/intents/ /apps/ {appId} {intentId} /**vzorku**|
|2|/luis/**api**/v2.0/apps/{appId}/**verze**/ /entities/ {versionId} {entityId} /**navrhnout**|
|2|/luis/**api**/v2.0/apps/{appId}/**verze**/ /intents/ {versionId} {intentId} /**navrhnout**|


## <a name="create-app-from-prebuilt-domains"></a>Vytvoření aplikace z předem připravených domén
[Předem připravených domén](luis-how-to-use-prebuilt-domains.md) poskytují předdefinované doménový model. Předem připravených domén umožňují rychle vyvíjet aplikace LUIS pro běžné domény. Toto rozhraní API umožňuje vytvořit novou aplikaci z předem připravených domény. Odpověď je nová ID aplikace.

|trasa v2|Příkaz|
|--|--|
|/luis/API/v2.0/Apps/customprebuiltdomains  |GET, post|
|/ luis/api/v2.0/apps/customprebuiltdomains/{culture}  |získat|

## <a name="importing-1x-app-into-2x"></a>Import aplikace 1.x do 2.x
Exportované 1.x aplikace JSON má některé oblasti, které je třeba změnit před importem do [LUIS] [ LUIS] 2.0. 

### <a name="prebuilt-entities"></a>Předem připravených entit 
[Předem připravených entit](luis-prebuilt-entities.md) změnily. Ujistěte se, že používáte V2 předem připravených entit. Jedná se o pomocí [datetimeV2](luis-reference-prebuilt-datetimev2.md), namísto data a času. 

### <a name="actions"></a>Akce
Vlastnost akcí již není platný. To by měla být prázdná. 

### <a name="labeled-utterances"></a>Projevy s popiskem
V1 povolené s popiskem projevy do obsahovat mezery na začátku nebo konci slova nebo fráze. Odebrat mezery. 

## <a name="common-reasons-for-http-response-status-codes"></a>Běžné důvody pro kódy stavu odpovědi HTTP
Zobrazit [kódů odpovědí rozhraní LUIS API](luis-reference-response-codes.md).

## <a name="next-steps"></a>Další postup

Použijte dokumentaci k rozhraní API v2 k aktualizaci existující REST volání LUIS [koncový bod](https://aka.ms/luis-endpoint-apis) a [vytváření](https://aka.ms/luis-authoring-apis) rozhraní API. 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions