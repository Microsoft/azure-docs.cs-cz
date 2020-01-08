---
title: Změna dat – LUIS
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak lze změnit data před predikce v Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 1bde70dadbe1e5b8ba9bf90bd9ca2f48a4c65491
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75381796"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Příkaz ALTER utterance dat před nebo během predikcí
Služba LUIS poskytuje možnosti pro manipulaci s utterance před nebo během do predikce. Mezi ně patří [Oprava pravopisu](luis-tutorial-bing-spellcheck.md)a řešení problémů s pásmem pro předem sestavené [datetimeV2](luis-reference-prebuilt-datetimev2.md). 

## <a name="correct-spelling-errors-in-utterance"></a>Psaní opravovat pravopisné chyby v utterance

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


Služba LUIS používá [API V7 kontrola pravopisu Bingu](../Bing-Spell-Check/overview.md) opravte pravopisné chyby utterance. Služba LUIS, musí klíč spojený s touto službou. Vytvoření klíče a pak přidejte klíč jako parametr řetězce dotazu na [koncový bod](https://go.microsoft.com/fwlink/?linkid=2092356). 

<!--
You can also correct spelling errors in the **Test** panel by [entering the key](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key is kept as a session variable in the browser for the Test panel. Add the key to the Test panel in each browser session you want spelling corrected. 

Usage of the key in the test panel and at the endpoint count toward the [key usage](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) quota. LUIS implements Bing Spell Check limits for text length. 

-->

Koncový bod vyžaduje dva parametry pro opravy pravopisu pracovat:

|Param|Hodnota|
|--|--|
|`spellCheck`|Boolean|
|`bing-spell-check-subscription-key`|[API V7 kontrola pravopisu Bingu](https://azure.microsoft.com/services/cognitive-services/spell-check/) klíče koncového bodu|

Když [API V7 kontrola pravopisu Bingu](https://azure.microsoft.com/services/cognitive-services/spell-check/) zjistí chybu, původní utterance a opravený utterance jsou vráceny spolu s předpovědí z koncového bodu.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)
 
```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * * 

### <a name="list-of-allowed-words"></a>Seznam povolených slov
Rozhraní API pro kontrolu pravopisu Bingu používané v LUIS nepodporuje seznam slov, která se mají ignorovat během úprav kontroly pravopisu. Pokud potřebujete povolený seznam slov nebo akronymů, před odesláním utterance do LUIS pro předpověď záměru zpracujte utterance v klientské aplikaci.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Změna časového pásma datetimeV2 předem připravených entit
Když aplikace LUIS používá předem vytvořenou entitu [datetimeV2](luis-reference-prebuilt-datetimev2.md) , hodnota DateTime může být vrácena v odpovědi předpovědi. Časové pásmo požadavku se používá k určení správné datum a čas k vrácení. Pokud požadavek pochází z robota nebo jiné centralizované aplikace před získáním LUIS, opravte časové pásmo, které používá služba LUIS. 

### <a name="endpoint-querystring-parameter"></a>Parametr řetězce dotazu koncového bodu
Časové pásmo je opravit tak, že přidáte uživatele časové pásmo pro [koncový bod](https://go.microsoft.com/fwlink/?linkid=2092356) pomocí `timezoneOffset` param. Hodnota `timezoneOffset` během několika minut, chcete-li změnit čas by měl být kladné nebo záporné číslo.  

|Param|Hodnota|
|--|--|
|`timezoneOffset`|kladné nebo záporné číslo, během několika minut|

### <a name="daylight-savings-example"></a>Příklad úspory letního času
Pokud potřebujete vrácené předem připravených datetimeV2 upravit pro letní čas, měli byste použít `timezoneOffset` parametr řetězce dotazu s +/-hodnotu v minutách pro [koncový bod](https://go.microsoft.com/fwlink/?linkid=2092356) dotazu.

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[Hodnota koncového bodu předpovědi v2](#tab/V2)

Přidáte 60 minut: 

https://{region}.API.cognitive.microsoft.com/luis/v2.0/Apps/{appId}?q=Turn světla na? **timezoneOffset = 60**& podrobné = {datový typ boolean} & Kontrola pravopisu = {datový typ boolean} & pracovní = {datový typ boolean} & Bingu – pravopisu – kontrola subscription-key = {řetězec} protoko & l = {datový typ boolean}

Odebrání 60 minut: 

https://{region}.API.cognitive.microsoft.com/luis/v2.0/Apps/{appId}?q=Turn světla na? **timezoneOffset = 60**& podrobné = {datový typ boolean} & Kontrola pravopisu = {datový typ boolean} & pracovní = {datový typ boolean} & Bingu – pravopisu – kontrola subscription-key = {řetězec} protoko & l = {datový typ boolean}

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[Požadavek na koncový bod verze V3](#tab/V3)

Přidáte 60 minut:

https://{region}. API. vnímání. Microsoft. com/Luis/v 3.0-Preview/Apps/{appId}/sloty/produkce/předpověď? dotaz = zapnout světla? **timezoneOffset = 60**& kontroly pravopisu = {boolean} & Bing-check-Subscription-Subscription-Key = {string} & log = {Boolean}

Odebrání 60 minut: 

https://{region}. API. vnímání. Microsoft. com/Luis/v 3.0-Preview/Apps/{appId}/sloty/produkce/předpověď? dotaz = zapnout světla? **timezoneOffset =-60**& kontrola pravopisu = {boolean} & Bing-check-Subscription-Subscription-Key = {string} & log = {Boolean}

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

* * * 

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>Kód jazyka C# určuje správnou hodnotu timezoneOffset
Následující kód jazyka C# používá [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) třídy [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) metodou ke zjištění správné `timezoneOffset` podle systémový čas:

```csharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset
int timezoneOffset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Opravovat pravopisné chyby v tomto kurzu](luis-tutorial-bing-spellcheck.md)
