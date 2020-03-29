---
title: Změna dat - LUIS
description: Zjistěte, jak lze změnit data před předpovědi v jazyce Porozumění (LUIS)
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: b3b36351a64a4e1a0bd13d5785a4e0609a80901d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292055"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Změnit data utterance před nebo během předpovědi
Služba LUIS poskytuje způsoby, jak manipulovat utterance před nebo během předpověď. Patří mezi ně [oprava pravopisu](luis-tutorial-bing-spellcheck.md)a oprava problémů s časovým pásmem pro předem sestavené [datum V2](luis-reference-prebuilt-datetimev2.md).

## <a name="correct-spelling-errors-in-utterance"></a>Oprava pravopisných chyb v utterance


### <a name="v3-runtime"></a>Doba běhu V3

Předzpracování textu pro opravy pravopisu před odesláním utterance luis. Použijte příklad projevy se správným pravopisem zajistit, že dostanete správné předpovědi.

Pomocí [kontroly pravopisu Bingu](../bing-spell-check/overview.md) opravte text před jeho odesláním službě LUIS.

### <a name="prior-to-v3-runtime"></a>Před runtime V3

Služba LUIS používá [rozhraní API pro kontrolu pravopisu Bingu V7](../Bing-Spell-Check/overview.md) k opravě pravopisných chyb v utterance. Služba LUIS potřebuje klíč přidružený k této službě. Vytvořte klíč a přidejte klíč jako parametr querystring v [koncovém bodě](https://go.microsoft.com/fwlink/?linkid=2092356).

Koncový bod vyžaduje dva params pro opravy pravopisu pracovat:

|Param|Hodnota|
|--|--|
|`spellCheck`|Boolean|
|`bing-spell-check-subscription-key`|Klíč koncového bodu rozhraní API pro [kontrolu pravopisu Bingu](https://azure.microsoft.com/services/cognitive-services/spell-check/)|

Při [bing kontrola pravopisu ROZHRANÍ API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) zjistí chybu, původní utterance a opravené utterance jsou vráceny spolu s předpovědi z koncového bodu.

#### <a name="v2-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Odpověď koncového bodu predikce V3](#tab/V3)

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
Rozhraní API kontroly pravopisu Bingu používané v luis nepodporuje seznam slov ignorovat během změny kontroly pravopisu. Pokud potřebujete povolit seznam slov nebo zkratky, zpracovat utterance v klientské aplikaci před odesláním utterance luis pro předpověď záměru.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Změna časového pásma předem sestavené entity datetimeV2
Když aplikace LUIS používá předem sestavené [datetimeV2](luis-reference-prebuilt-datetimev2.md) entity, datetime hodnota může být vrácena v odpovědi předpověď. Časové pásmo požadavku se používá k určení správné ho data čas vrátit. Pokud požadavek pochází z robota nebo jiné centralizované aplikace před získáním luis, opravit časové pásmo LUIS používá.

### <a name="endpoint-querystring-parameter"></a>Parametr řetězce dotazu koncového bodu
Časové pásmo je opraveno přidáním časového pásma uživatele `timezoneOffset` do [koncového bodu](https://go.microsoft.com/fwlink/?linkid=2092356) pomocí param. Hodnota `timezoneOffset` by měla být kladné nebo záporné číslo v minutách, aby se změnil čas.

|Param|Hodnota|
|--|--|
|`timezoneOffset`|kladné nebo záporné číslo v minutách|

### <a name="daylight-savings-example"></a>Příklad letního času
Pokud potřebujete vrácené prebuilt datetimeV2 upravit letní čas, `timezoneOffset` měli byste použít querystring parametr s hodnotou +/- v minutách pro dotaz [koncového bodu.](https://go.microsoft.com/fwlink/?linkid=2092356)

#### <a name="v2-prediction-endpoint-request"></a>[Požadavek koncového bodu predikce V2](#tab/V2)

Přidat 60 minut:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Odstranit 60 minut:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=-60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v3-prediction-endpoint-request"></a>[Požadavek koncového bodu predikce V3](#tab/V3)

Přidat 60 minut:

`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Odstranit 60 minut:

`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=-60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

* * *

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>Kód jazyka C# určuje správnou hodnotu časového pásmaOffset
Následující kód Jazyka C# používá metodu [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) třídy `timezoneOffset` [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) k určení správného nastavení na základě systémového času:

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
> [Oprava pravopisných chyb v tomto kurzu](luis-tutorial-bing-spellcheck.md)
