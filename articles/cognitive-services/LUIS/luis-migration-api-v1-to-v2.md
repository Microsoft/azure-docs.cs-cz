---
title: migrace rozhraní API v1 až v2
titleSuffix: Azure Cognitive Services
description: Koncový bod verze 1 a vytváření rozhraní API pro porozumění jazykům jsou zastaralé. V této příručce můžete pochopit, jak migrovat do koncového bodu verze 2 a vytvářet úložiště API.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 2f67bf0951ef8928297c71e8fc9f924cf05c63f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68932691"
---
# <a name="api-v1-to-v2-migration-guide-for-luis-apps"></a>Průvodce migrací rozhraní API v1 až v2 pro aplikace LUIS
[Koncový bod](https://aka.ms/v1-endpoint-api-docs) verze 1 a [vytváření](https://aka.ms/v1-authoring-api-docs) api jsou zastaralé. V této příručce můžete pochopit, jak migrovat do [koncového bodu](https://go.microsoft.com/fwlink/?linkid=2092356) verze 2 a [vytvářet](https://go.microsoft.com/fwlink/?linkid=2092087) úložiště API. 

## <a name="new-azure-regions"></a>Nové oblasti Azure
Služba LUIS má nové [oblasti](https://aka.ms/LUIS-regions) pro lana API LUIS. Služba LUIS poskytuje jiný portál pro skupiny oblastí. Aplikace musí být vytvořena ve stejné oblasti, ve které očekáváte dotaz. Aplikace nemigrují oblasti automaticky. Exportujete aplikaci z jedné oblasti a importujete ji do jiné, aby byla dostupná v nové oblasti.

## <a name="authoring-route-changes"></a>Vytváření změn trasy
Vývojová trasa rozhraní API se změnila z použití **prog** trasy na pomocí trasy **rozhraní API.**


| version | trasa |
|--|--|
|1|/luis/v1.0/**prog**/apps|
|2|/luis/**api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>Změny trasy koncového bodu
Rozhraní API koncového bodu má nové parametry řetězce dotazu, stejně jako jinou odpověď. Pokud je podrobný příznak true, všechny záměry, bez ohledu na skóre, jsou vráceny v poli s názvem záměry, kromě topScoringIntent.

| version | Trasa GET |
|--|--|
|1|/luis/v1/application?ID={appId}&q={q}|
|2|/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]|


odpověď na úspěch koncového bodu v1:
```json
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

odpověď na úspěch koncového bodu v2:
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

## <a name="key-management-no-longer-in-api"></a>Správa klíčů již v rozhraní API
Klíčová řešení API koncového bodu předplatného jsou zastaralé, vrací 410 GONE.

| version | trasa |
|--|--|
|1|/luis/v1.0/prog/subscriptions|
|1|/luis/v1.0/prog/subscriptions/{subscriptionKey}|

[Klíče koncového bodu](luis-how-to-azure-subscription.md) Azure se generují na webu Azure Portal. Klíč přiřadíte aplikaci LUIS na stránce **[Publikovat.](luis-how-to-azure-subscription.md)** Není nutné znát skutečnou hodnotu klíče. Služba LUIS používá název předplatného k přiřazení. 

## <a name="new-versioning-route"></a>Nová trasa správy verzí
Model v2 je nyní obsažen ve [verzi](luis-how-to-manage-versions.md). Název verze je 10 znaků v trase. Výchozí verze je "0.1".

| version | trasa |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entities|
|2|/luis/**api**/v2.0/apps/{appId}/**verze**/{versionId}/entities|

## <a name="metadata-renamed"></a>Metadata přejmenována
Několik api, které vracejí metadata LUIS mají nové názvy.

| název trasy v1 | název trasy v2 |
|--|--|
|Aplikace PersonalAssistantApps |Asistenti|
|aplikační kultury|jazykové verze|
|aplikační domény|Domény|
|applicationusagescenarios|scénáře použití|


## <a name="sample-renamed-to-suggest"></a>"Ukázka" přejmenována na "navrhnout"
Služba LUIS navrhuje projevy z existující [projevy koncového bodu,](luis-how-to-review-endpoint-utterances.md) které mohou vylepšit model. V předchozí verzi byla tato verze **pojmenována ukázka**. V nové verzi se název změní z ukázky **navrhnout**. To se nazývá **[revize koncový bod projevy](luis-how-to-review-endpoint-utterances.md)** na webu LUIS.

| version | trasa |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entities/{entityId}/**ukázka**|
|1|/luis/v1.0/**prog**/apps/{appId}/intents/{intentId}/**ukázka**|
|2|/luis/**api**/v2.0/apps/{appId}/ verze /{versionId}/entities/{entityId}/ suggest /v2.0/apps/{appId}/**versions**/{versionId}/entities/{entityId}/ suggest /luis/ api /v2.0/apps/{appId}/ versions /{versionId}/entities/{entityId}/**suggest /luis/ api** /v2|
|2|/luis/**api**/v2.0/apps/{appId}/ verze /{versionId}/intents/{intentId}/ suggest /luis/ api /v2.0/apps/{appId}/**versions**/{versionId}/intents/{intentId}/ suggest /luis/ api /v2.0/apps/{appId}/ versions /{versionId}/intents/{intentId}/**suggest /luis/**|


## <a name="create-app-from-prebuilt-domains"></a>Vytvoření aplikace z předem vytvořených domén
[Předdefinované domény](luis-how-to-use-prebuilt-domains.md) poskytují předdefinovaný model domény. Předem sestavené domény umožňují rychle vyvinout aplikaci LUIS pro běžné domény. Toto rozhraní API umožňuje vytvořit novou aplikaci založenou na předem vytvořené doméně. Odpověď je nové appID.

|v2 trasa|Sloveso|
|--|--|
|/luis/api/v2.0/apps/customprebuiltdomains /luis/api/v2.0/apps/customprebuiltdomains /luis/api/v2.0/apps/customprebuiltdomains /luis  |získat, poštou|
|/luis/api/v2.0/apps/customprebuiltdomains/{culture}  |get|

## <a name="importing-1x-app-into-2x"></a>Import aplikace 1.x do aplikace 2.x
Exportované aplikace 1.x JSON má některé oblasti, které je třeba změnit před importem do [LUIS][LUIS] 2.0. 

### <a name="prebuilt-entities"></a>Předem připravené entity 
Předem vytvořené entity se [změnily.](luis-prebuilt-entities.md) Ujistěte se, že používáte předem sestavené entity V2. To zahrnuje použití [datetimeV2](luis-reference-prebuilt-datetimev2.md), namísto datetime. 

### <a name="actions"></a>Akce
Vlastnost akce již není platná. Mělo by to být prázdné 

### <a name="labeled-utterances"></a>Popisované projevy
V1 povoleno popisované projevy zahrnout mezery na začátku nebo na konci slova nebo fráze. Odstranil mezery. 

## <a name="common-reasons-for-http-response-status-codes"></a>Běžné důvody pro stavové kódy odpovědi HTTP
Viz [Kódy odpovědí rozhraní LUIS API](luis-reference-response-codes.md).

## <a name="next-steps"></a>Další kroky

Pomocí dokumentace rozhraní API v2 aktualizovat existující volání REST na [koncový bod SLUŽBY](https://go.microsoft.com/fwlink/?linkid=2092356) LUIS a [vytváření](https://go.microsoft.com/fwlink/?linkid=2092087) rozhraní API. 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
