---
title: Migrace V1 na v2 API
titleSuffix: Azure Cognitive Services
description: Koncové body verze 1 a vytváření Language Understanding API jsou zastaralé. Tento průvodce vám pomůže pochopit, jak migrovat na koncový bod verze 2 a vytváření rozhraní API.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 04/02/2019
ms.openlocfilehash: 867ae2cc7567077786bb0840cd11c47b786be423
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018748"
---
# <a name="api-v1-to-v2-migration-guide-for-luis-apps"></a>Průvodce migrací rozhraní API V1 na v2 pro aplikace LUIS
[Koncový bod](https://aka.ms/v1-endpoint-api-docs) verze 1 a rozhraní API pro [vytváření obsahu](https://aka.ms/v1-authoring-api-docs) jsou zastaralé. Tento průvodce vám pomůže pochopit, jak migrovat na [koncový bod](https://go.microsoft.com/fwlink/?linkid=2092356) verze 2 a [vytváření](https://go.microsoft.com/fwlink/?linkid=2092087) rozhraní API.

## <a name="new-azure-regions"></a>Nové oblasti Azure
LUIS má nové [oblasti](./luis-reference-regions.md) , které jsou k dispozici pro rozhraní Luis API. LUIS poskytuje pro skupiny oblastí jiný portál. Aplikace musí být vytvořená ve stejné oblasti, kterou očekáváte dotazovat. Aplikace nemigrují automaticky oblasti. Aplikaci exportujete z jedné oblasti a pak ji naimportujete do jiné, aby byla dostupná v nové oblasti.

## <a name="authoring-route-changes"></a>Vytváření změn tras
Trasa rozhraní API pro vytváření obsahu se změnila z použití **programové** trasy na používání trasy **rozhraní API** .


| verze | route |
|--|--|
|1|/Luis/v1.0/**/Apps**|
|2|**rozhraní/Luis/API**/v2.0/Apps|


## <a name="endpoint-route-changes"></a>Změny směrování koncových bodů
Rozhraní API koncového bodu má nové parametry řetězce dotazu i jinou odpověď. Pokud má příznak verbose hodnotu true, všechny záměry bez ohledu na skóre jsou vráceny v poli s názvem záměry kromě topScoringIntent.

| verze | ZÍSKAT trasu |
|--|--|
|1|/Luis/v1/Application? ID = {appId} &q = {q}|
|2|/luis/v2.0/apps/{appId}? q = {q} [&timezoneOffset] [&verbose] [&kontrola pravopisu] [&fázování] [&Bing-check-Subscription-Subscription-Key] [&log]|


V1 odpověď na úspěšný koncový bod:
```json
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

odpověď na úspěšnost koncového bodu v2:
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
Rozhraní API klíčů koncového bodu předplatného jsou zastaralá a vrátila 410.

| verze | route |
|--|--|
|1|/luis/v1.0/prog/subscriptions|
|1|/luis/v1.0/prog/subscriptions/{subscriptionKey}|

[Klíče koncového bodu](luis-how-to-azure-subscription.md) Azure se generují v Azure Portal. Klíč přiřadíte aplikaci LUIS na stránce **[publikovat](luis-how-to-azure-subscription.md)** . Nemusíte znát skutečnou hodnotu klíče. LUIS pomocí názvu předplatného provádí přiřazení.

## <a name="new-versioning-route"></a>Nová trasa verze
Model v2 je teď obsažený ve [verzi](luis-how-to-manage-versions.md). V trase je název verze 10 znaků. Výchozí verze je "0,1".

| verze | route |
|--|--|
|1|/Luis/v1.0/**/Apps/{appId}/Entities**|
|2|/Luis/**API**/v2.0/Apps/{appId}/**verze**/{versionId}/Entities|

## <a name="metadata-renamed"></a>Metadata byla přejmenována
Několik rozhraní API, které vrací metadata LUIS, mají nové názvy.

| název trasy v1 | název trasy v2 |
|--|--|
|PersonalAssistantApps |asistenti|
|applicationcultures|jazykové verze|
|applicationdomains|doménu|
|applicationusagescenarios|usagescenarios|


## <a name="sample-renamed-to-suggest"></a>"Ukázka" přejmenována na "navrhnout"
LUIS navrhuje projevy z existujícího [koncového bodu projevy](luis-how-to-review-endpoint-utterances.md) , který může model vylepšit. V předchozí verzi se jednalo o název **Sample**. V nové verzi se název změní z ukázky na **navrhnout**. To se označuje jako **[Kontrola projevyového bodu](luis-how-to-review-endpoint-utterances.md)** na webu Luis.

| verze | route |
|--|--|
|1|Ukázka **/Luis/v1.0/** auto/Apps/{appId}/Entities/{EntityId}/|
|1|Ukázka **/Luis/v1.0/** auto/Apps/{appId}/intents/{intentId}/|
|2|/Luis/**API**/v2.0/Apps/{appId}/**verze**/{versionId}/Entities/{EntityId}/–**Návrh**|
|2|/Luis/**API**/v2.0/Apps/{appId}/**verze**/{versionId}/intents/{intentId}/–**Návrh**|


## <a name="create-app-from-prebuilt-domains"></a>Vytvoření aplikace z předem připravených domén
Předem [připravené domény](./howto-add-prebuilt-models.md) poskytují předdefinovaný doménový model. Předem připravené domény umožňují rychle vyvíjet aplikace LUIS pro běžné domény. Toto rozhraní API umožňuje vytvořit novou aplikaci založenou na předem sestavené doméně. Odpověď je nový appID.

|V2 Route|operace|
|--|--|
|/luis/api/v2.0/apps/customprebuiltdomains  |získat, zveřejnit|
|/luis/api/v2.0/apps/customprebuiltdomains/{culture}  |get|

## <a name="importing-1x-app-into-2x"></a>Importování aplikace 1. x do 2. x
Exportovaný formát JSON aplikace 1. x obsahuje některé oblasti, které je třeba před importem do [LUIS][LUIS] 2,0 změnit.

### <a name="prebuilt-entities"></a>Předem připravené entity
[Předem připravené entity](./howto-add-prebuilt-models.md) se změnily. Ujistěte se, že používáte předem připravené Entity v2. To zahrnuje použití [datetimeV2](luis-reference-prebuilt-datetimev2.md)místo data a času.

### <a name="actions"></a>Akce
Vlastnost Actions již není platná. Měla by být prázdná.

### <a name="labeled-utterances"></a>Projevy s popiskem
V1 s názvem projevy, který obsahuje mezery na začátku nebo na konci slova nebo fráze. Odeberou se mezery.

## <a name="common-reasons-for-http-response-status-codes"></a>Běžné důvody pro stavové kódy odpovědi HTTP
Viz [kódy odpovědí rozhraní API pro Luis](luis-reference-response-codes.md).

## <a name="next-steps"></a>Další kroky

Pomocí dokumentace k rozhraní v2 API aktualizujte existující volání REST na [koncový bod](https://go.microsoft.com/fwlink/?linkid=2092356) Luis a [vytváření](https://go.microsoft.com/fwlink/?linkid=2092087) rozhraní API.

[LUIS]: ./luis-reference-regions.md