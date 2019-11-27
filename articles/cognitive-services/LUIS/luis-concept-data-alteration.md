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
ms.openlocfilehash: a199821c4db7fd8131ec54700b8c999dfe604a6e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74222023"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Příkaz ALTER utterance dat před nebo během predikcí
Služba LUIS poskytuje možnosti pro manipulaci s utterance před nebo během do predikce. Mezi ně patří [Oprava pravopisu](luis-tutorial-bing-spellcheck.md)a řešení problémů s pásmem pro předem sestavené [datetimeV2](luis-reference-prebuilt-datetimev2.md). 

## <a name="correct-spelling-errors-in-utterance"></a>Psaní opravovat pravopisné chyby v utterance

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


LUIS používá [rozhraní API Bingu pro kontrolu pravopisu v7](../Bing-Spell-Check/overview.md) k opravě pravopisných chyb v utterance. Služba LUIS, musí klíč spojený s touto službou. Vytvořte klíč a pak na [koncový bod](https://go.microsoft.com/fwlink/?linkid=2092356)přidejte klíč jako parametr QueryString. 

<!--
You can also correct spelling errors in the **Test** panel by [entering the key](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key is kept as a session variable in the browser for the Test panel. Add the key to the Test panel in each browser session you want spelling corrected. 

Usage of the key in the test panel and at the endpoint count toward the [key usage](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) quota. LUIS implements Bing Spell Check limits for text length. 

-->

Koncový bod vyžaduje dva parametry pro opravy pravopisu pracovat:

|Param|Hodnota|
|--|--|
|`spellCheck`|Datový typ Boolean|
|`bing-spell-check-subscription-key`|Klíč koncového bodu [rozhraní API Bingu pro kontrolu pravopisu v7](https://azure.microsoft.com/services/cognitive-services/spell-check/)|

Když [rozhraní API Bingu pro kontrolu pravopisu v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) detekuje chybu, původní utterance a opravené utterance se vrátí společně s předpovědi z koncového bodu.

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
Časové pásmo se opravuje přidáním časového pásma uživatele do [koncového bodu](https://go.microsoft.com/fwlink/?linkid=2092356) pomocí param `timezoneOffset`. Hodnota `timezoneOffset` by měla být kladné nebo záporné číslo v minutách, aby se změnil čas.  

|Param|Hodnota|
|--|--|
|`timezoneOffset`|kladné nebo záporné číslo, během několika minut|

### <a name="daylight-savings-example"></a>Příklad úspory letního času
Pokud potřebujete vrátit předem sestavené datetimeV2 pro úpravu pro letní čas, měli byste použít parametr `timezoneOffset` QueryString s hodnotou +/-v minutách pro dotaz na [koncový bod](https://go.microsoft.com/fwlink/?linkid=2092356) .

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[Hodnota koncového bodu předpovědi v2](#tab/V2)

Přidáte 60 minut: 

https://{region}. API. vnímání. Microsoft. com/Luis/v 2.0/Apps/{appId}? q = zapnout světla? **timezoneOffset = 60**& verbose = {boolean} & kontroly pravopisu = {boolean} & fázování = {boolean} & Bing-check-Subscription-Subscription-Key = {string} & log = {Boolean}

Odebrání 60 minut: 

https://{region}. API. vnímání. Microsoft. com/Luis/v 2.0/Apps/{appId}? q = zapnout světla? **timezoneOffset =-60**& verbose = {boolean} & kontrola pravopisu = {boolean} & fázování = {boolean} & Bing-check-check-Subscription-Key = {string} & log = {Boolean}

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[Požadavek na koncový bod verze V3](#tab/V3)

Přidáte 60 minut:

https://{region}. API. vnímání. Microsoft. com/Luis/v 3.0-Preview/Apps/{appId}/sloty/produkce/předpověď? dotaz = zapnout světla? **timezoneOffset = 60**& kontroly pravopisu = {boolean} & Bing-check-Subscription-Subscription-Key = {string} & log = {Boolean}

Odebrání 60 minut: 

https://{region}. API. vnímání. Microsoft. com/Luis/v 3.0-Preview/Apps/{appId}/sloty/produkce/předpověď? dotaz = zapnout světla? **timezoneOffset =-60**& kontrola pravopisu = {boolean} & Bing-check-Subscription-Subscription-Key = {string} & log = {Boolean}

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

* * * 

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>Kód jazyka C# určuje správnou hodnotu timezoneOffset
Následující C# kód používá metodu [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) třídy [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) k určení správného `timezoneOffset` na základě systémového času:

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Opravení pravopisných chyb pomocí tohoto kurzu](luis-tutorial-bing-spellcheck.md)
