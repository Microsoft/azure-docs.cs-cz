---
title: Základními pojmy změnou data v LEOŠ - Azure | Microsoft Docs
description: Zjistěte, jak lze změnit data před předpovědi znalosti jazyka (LEOŠ)
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/26/2018
ms.author: v-geberr
ms.openlocfilehash: ab9b9fe5c34c334fd65971f9040c8e43467aa242
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112146"
---
# <a name="data-alterations"></a>Změny dat
LEOŠ poskytuje způsoby, jak pracovat s utterance před nebo během předpovědi. 

## <a name="correct-spelling-errors-in-utterance"></a>Správné pravopisné chyby v utterance
Používá LEOŠ [API V7 kontrola pravopisu Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) ke kontrole pravopisu v utterance. LEOŠ musí klíč spojený s touto službou. Vytvořte klíč a pak přidejte klíč jako parametr řetězce dotazu v [koncový bod](https://aka.ms/luis-endpoint-apis). 

Můžete také vyřešit pravopisné chyby v **Test** panelu podle [zadávání klíče](interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Klíč je ponechat ve formě proměnné relace v prohlížeči pro panel testu. Přidejte klíč do panelu testu v každé relaci prohlížeče Chcete pravopis opravena. 

Použití klíče v panelu test a počet koncový bod směrem k [použití klíče](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) kvóty. LEOŠ implementuje kontrola pravopisu Bing limity pro délka textu. 

Koncový bod vyžaduje dva parametry z kontroly pravopisu postup:

|Param|Hodnota|
|--|--|
|`spellCheck`|Boolean|
|`bing-spell-check-subscription-key`|[Zkontrolujte V7 rozhraní API aplikace Bing pravopisu](https://azure.microsoft.com/services/cognitive-services/spell-check/) klíč koncového bodu|

Když [API V7 kontrola pravopisu Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) zjistí chybu původní utterance, opravené utterance jsou vráceny a společně s předpovědi z koncového bodu.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```
 
## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Změnit časové pásmo předem datetimeV2 entity
Pokud aplikace LEOŠ používá předem datetimeV2 entity, mohou být vráceny hodnotu data a času v odpovědi předpovědi. Časové pásmo požadavku slouží k určení správné datum a čas vrátit. Pokud požadavek pochází z robotu nebo jiné centralizované aplikace před získáním k LEOŠ, opravte časové pásmo, které používá LEOŠ. 

### <a name="endpoint-querystring-parameter"></a>Parametr řetězce dotazu koncového bodu
Přidání uživatele časové pásmo pro časové pásmo je vyřešen [koncový bod](https://aka.ms/luis-endpoint-apis) pomocí `timezoneOffset` param. Hodnota `timezoneOffset` musí být kladné a záporné číslo, v minutách, k příkazu alter čas.  

|Param|Hodnota|
|--|--|
|`timezoneOffset`|kladné a záporné číslo, v minutách|

### <a name="daylight-savings-example"></a>Příklad úspory letního času
Pokud potřebujete vrácený předem datetimeV2 upravit pro letní čas, měli byste použít `timezoneOffset` parametr řetězce dotazu s +/-hodnotu v minutách pro [koncový bod](https://aka.ms/luis-endpoint-apis) dotazu.

Přidejte 60 minut: 

https://{region}.API.cognitive.microsoft.com/luis/v2.0/Apps/{appId}?q=Turn indikátory na? **timezoneOffset = 60**& podrobné = {logická hodnota} & Kontrola pravopisu = {logická hodnota} & pracovní = {logická hodnota} & bing pravopisu – kontrola subscription-key = {řetězec} & protokolu = {logická hodnota}

Odebrání 60 minut: 

https://{region}.API.cognitive.microsoft.com/luis/v2.0/Apps/{appId}?q=Turn indikátory na? **timezoneOffset = 60**& podrobné = {logická hodnota} & Kontrola pravopisu = {logická hodnota} & pracovní = {logická hodnota} & bing pravopisu – kontrola subscription-key = {řetězec} & protokolu = {logická hodnota}

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>Určuje správnou hodnotu timezoneOffset kód C#
Následující kód C# používá [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo?view=netframework-4.7.1) třídy [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid?view=netframework-4.7.1#examples) metoda k určení správného `timezoneOffset` podle času systému:

```CSharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset
int timezoneOffset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Správné pravopisné chyby v tomto kurzu](luis-tutorial-bing-spellcheck.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions