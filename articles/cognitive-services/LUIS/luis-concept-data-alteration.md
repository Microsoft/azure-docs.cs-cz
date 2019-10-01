---
title: Změna dat – LUIS
titleSuffix: Azure Cognitive Services
description: Informace o tom, jak je možné změnit data před předpovědi v Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: diberry
ms.openlocfilehash: 734389c92ede88d336df60a1a79a738d2abcfa92
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703161"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>ALTER data utterance před nebo během předpovědi
LUIS poskytuje možnosti pro manipulaci s utterance před nebo během předpovědi. Mezi ně patří [Oprava pravopisu](luis-tutorial-bing-spellcheck.md)a řešení problémů s pásmem pro předem sestavené [datetimeV2](luis-reference-prebuilt-datetimev2.md). 

## <a name="correct-spelling-errors-in-utterance"></a>Opravy pravopisných chyb v utterance
LUIS používá [rozhraní API Bingu pro kontrolu pravopisu v7](../Bing-Spell-Check/overview.md) k opravě pravopisných chyb v utterance. LUIS potřebuje klíč přidružený k této službě. Vytvořte klíč a pak na [koncový bod](https://go.microsoft.com/fwlink/?linkid=2092356)přidejte klíč jako parametr QueryString. 

Můžete také opravit pravopisné chyby na panelu **test** [zadáním klíče](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Klíč se uchovává jako proměnná relace v prohlížeči pro testovací panel. Přidejte klíč do testovacího panelu v každé relaci prohlížeče, u které chcete pravopis opravit. 

Použití klíče na panelu Test a v počtu koncových bodů směrem k kvótě [použití klíče](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) . LUIS implementuje limity Kontrola pravopisu Bingu pro délku textu. 

Koncový bod vyžaduje dva parametry pro správné opravy pravopisu:

|Bajty|Hodnota|
|--|--|
|`spellCheck`|Logická hodnota|
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

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Změna časového pásma předem sestavené entity datetimeV2
Když aplikace LUIS používá předem vytvořenou entitu [datetimeV2](luis-reference-prebuilt-datetimev2.md) , hodnota DateTime může být vrácena v odpovědi předpovědi. Časové pásmo požadavku slouží k určení správného typu DateTime, který se má vrátit. Pokud požadavek pochází z robota nebo jiné centralizované aplikace před tím, než se LUIS, opravte časové pásmo LUIS používá. 

### <a name="endpoint-querystring-parameter"></a>Parametr koncového bodu QueryString
Časové pásmo se opravuje přidáním časového pásma uživatele do [koncového bodu](https://go.microsoft.com/fwlink/?linkid=2092356) pomocí parametru `timezoneOffset`. Hodnota `timezoneOffset` by měla být kladné nebo záporné číslo v minutách, aby se změnil čas.  

|Bajty|Hodnota|
|--|--|
|`timezoneOffset`|kladné nebo záporné číslo v minutách|

### <a name="daylight-savings-example"></a>Příklad letního úspory
Pokud potřebujete vrátit předem sestavené datetimeV2 pro úpravu pro letní čas, měli byste použít parametr řetězce dotazu `timezoneOffset` s hodnotou +/-v minutách pro dotaz na [koncový bod](https://go.microsoft.com/fwlink/?linkid=2092356) .

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[Hodnota koncového bodu předpovědi v2](#tab/V2)

Přidejte 60 minut: 

https://{region}. API. vnímání. Microsoft. com/Luis/v 2.0/Apps/{appId}? q = zapnout světla? **timezoneOffset = 60**& verbose = {boolean} & kontroly pravopisu = {boolean} & fázování = {boolean} & Bing-check-Subscription-Subscription-Key = {string} & log = {Boolean}

Odebrat 60 minut: 

https://{region}. API. vnímání. Microsoft. com/Luis/v 2.0/Apps/{appId}? q = zapnout světla? **timezoneOffset =-60**& verbose = {boolean} & kontrola pravopisu = {boolean} & fázování = {boolean} & Bing-check-check-Subscription-Key = {string} & log = {Boolean}

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[Požadavek na koncový bod verze V3](#tab/V3)

Přidejte 60 minut:

https://{region}. API. vnímání. Microsoft. com/Luis/v 3.0-Preview/Apps/{appId}/sloty/produkce/předpověď? dotaz = zapnout světla? **timezoneOffset = 60**& kontroly pravopisu = {boolean} & Bing-check-Subscription-Subscription-Key = {string} & log = {Boolean}

Odebrat 60 minut: 

https://{region}. API. vnímání. Microsoft. com/Luis/v 3.0-Preview/Apps/{appId}/sloty/produkce/předpověď? dotaz = zapnout světla? **timezoneOffset =-60**& kontrola pravopisu = {boolean} & Bing-check-Subscription-Subscription-Key = {string} & log = {Boolean}

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

* * * 

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C#kód určuje správnou hodnotu timezoneOffset.
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
