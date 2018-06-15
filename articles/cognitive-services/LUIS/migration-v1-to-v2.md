---
title: Rozhraní API migrovat průvodce z v1 v2 | Microsoft Docs
titleSuffix: Azure
description: Zjistěte, jak nastavit migrace nejnovější rozhraní API.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/01/2018
ms.author: v-geberr
ms.openlocfilehash: 07c26753ed7198a2d98f4b7d5afb4cc7845fbcbe
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "35343834"
---
# <a name="api-v2-migration-guide"></a>Příručka k migraci v2 rozhraní API
Verze 1 [koncový bod](https://aka.ms/v1-endpoint-api-docs) a [vytváření](https://aka.ms/v1-authoring-api-docs) přestanou rozhraní API. Tento průvodce vám pomůže porozumět způsob migrace do verze 2 [koncový bod](https://aka.ms/luis-endpoint-apis) a [vytváření](https://aka.ms/luis-authoring-apis) rozhraní API. 

## <a name="new-azure-regions"></a>Nové oblastí Azure
LEOŠ má nové [oblasti](https://aka.ms/LUIS-regions) zadaná pro rozhraní API LEOŠ. LEOŠ poskytuje na jiný web pro oblast skupiny. Aplikace musí být vytvořené ve stejné oblasti, které chcete dotaz. Aplikace se nemigrují automaticky oblasti. Aplikace je exportovat z jedné oblasti pak importovat do jiné pro něj k dispozici v nové oblasti.

## <a name="authoring-route-changes"></a>Vytváření změny směrování
Vytváření rozhraní API trasy změnit pomocí **programové** trasy k použití **rozhraní api** trasy.


| verze | trasa |
|--|--|
|1|/luis/V1.0/**programové**/apps|
|2|/luis/**rozhraní api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>Koncový bod změny směrování
Koncový bod rozhraní API je nové parametry řetězce dotazu, jakož i jiné odpovědi. Pokud podrobné příznak hodnotu true, vrátí se všechny záměry, bez ohledu na to skóre, v pole s názvem záměry kromě topScoringIntent.

| verze | ZÍSKAT trasy |
|--|--|
|1|/luis/V1/Application? ID = {appId} & q = {q}|
|2|/ luis/v2.0/apps/{appId}?q={q} [& timezoneOffset] [& podrobné] [& Kontrola pravopisu] [& pracovní] [& bing pravopisu – kontrola subscription-key] [& protokolu]|


Úspěšná odpověď V1 koncový bod:
```JSON
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

Úspěšná odpověď v2 koncový bod:
```JSON
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
Klíč předplatného rozhraní API jsou zastaralé, vrácení 410 GONE.

| verze | trasa |
|--|--|
|1|/luis/V1.0/Prog/subscriptions|
|1|/ luis/v1.0/prog/subscriptions/{subscriptionKey}|

Azure [předplatné klíče](luis-how-to-azure-subscription.md) jsou generovány na portálu Azure. Přiřazení klíče do aplikace LEOŠ na **[publikovat](manage-keys.md)** stránky. Není nutné vědět, skutečná hodnota klíče. LEOŠ přiřazení používá název odběru. 

## <a name="new-versioning-route"></a>Novou trasu Správa verzí
Je nyní součástí modelu v2 [verze](luis-how-to-manage-versions.md). Název verze je 10 znaků trasy. Výchozí verze je "0,1".

| verze | trasa |
|--|--|
|1|/luis/V1.0/**programové**/apps/ {appId} / entity|
|2|/luis/**rozhraní api**/v2.0/apps/{appId}/**verze**/ {versionId} / entity|

## <a name="metadata-renamed"></a>Metadata přejmenovat
Několik rozhraní API, které vracejí metadata LEOŠ mít nové názvy.

| název trasy V1 | název trasy v2 |
|--|--|
|PersonalAssistantApps |Pomocníci|
|applicationcultures|jazykové verze|
|applicationdomains|domény|
|applicationusagescenarios|usagescenarios|


## <a name="sample-renamed-to-suggest"></a>"Ukázkový" přejmenovat "navrhovat"
LEOŠ navrhuje utterances z existujícího [utterances koncový bod](label-suggested-utterances.md) , může zvýšit modelu. V předchozí verzi, to nazýval **ukázka**. V nové verzi název změnil z vzorku, který se **navrhnout**. To se označuje jako **[zkontrolujte koncový bod utterances](https://docs.microsoft.com/azure/cognitive-services/LUIS/label-suggested-utterances)** LEOŠ webu.

| verze | trasa |
|--|--|
|1|/luis/V1.0/**programové**/entities/ /apps/ {appId} {entityId} /**vzorku**|
|1|/luis/V1.0/**programové**/intents/ /apps/ {appId} {intentId} /**vzorku**|
|2|/luis/**rozhraní api**/v2.0/apps/{appId}/**verze**/ /entities/ {versionId} {entityId} /**navrhnout**|
|2|/luis/**rozhraní api**/v2.0/apps/{appId}/**verze**/ /intents/ {versionId} {intentId} /**navrhnout**|


## <a name="create-app-from-prebuilt-domains"></a>Vytvoření aplikace z předem domén
[Předkompilované domén](luis-how-to-use-prebuilt-domains.md) poskytnout model předdefinované domény. Předkompilované domén umožňují rychle vyvíjet aplikaci LEOŠ pro běžné domény. Toto rozhraní API umožňuje vytvořit novou aplikaci na základě přednastavených domény. Odpověď se nové ID aplikace.

|trasy v2|Příkaz|
|--|--|
|/luis/API/v2.0/Apps/customprebuiltdomains  |GET, post|
|/ luis/api/v2.0/apps/customprebuiltdomains/{culture}  |GET|

## <a name="importing-1x-app-into-2x"></a>Importování aplikace 1.x do 2.x
Exportovaný 1.x aplikace JSON obsahuje některé oblasti, které budete muset změnit před importem do [LEOŠ] [ LUIS] 2.0. 

### <a name="prebuilt-entities"></a>Předkompilované entity 
[Předem entity](Pre-builtEntities.md) změnily. Zajistěte, aby používáte V2 předem entity. To zahrnuje pomocí [datetimeV2](pre-builtentities.md?#use-a-prebuilt-datetimev2-entity), místo data a času. 

### <a name="actions"></a>Akce
Vlastnost akce již není platný. Měla by být prázdná 

### <a name="labeled-utterances"></a>S popiskem utterances
V1 povoleno s popiskem utterances k obsahovat mezery na začátku nebo na konci slovo nebo frázi. Odebrat prostory. 

## <a name="common-reasons-for-http-response-status-codes"></a>Běžnými důvody kódy stavu odpovědi HTTP
V tématu [kódy odpovědí LEOŠ API](luis-reference-response-codes.md).

## <a name="next-steps"></a>Další postup

Použití v dokumentaci rozhraní API v2 k aktualizaci existující REST volání LIUS [koncový bod](https://aka.ms/luis-endpoint-apis) a [vytváření](https://aka.ms/luis-authoring-apis) rozhraní API. 

[LUIS]: luis-reference-regions.md