---
title: Změna dat – LUIS
description: Informace o tom, jak je možné změnit data před předpovědi v Language Understanding (LUIS)
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 621a41f743b751a8c24bf6f6ad8497fb5c79775c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "95026006"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>ALTER data utterance před nebo během předpovědi
LUIS poskytuje možnosti pro manipulaci s utterance před nebo během předpovědi. Mezi ně patří [Oprava pravopisu](luis-tutorial-bing-spellcheck.md)a řešení problémů s pásmem pro předem sestavené [datetimeV2](luis-reference-prebuilt-datetimev2.md).

## <a name="correct-spelling-errors-in-utterance"></a>Opravy pravopisných chyb v utterance


### <a name="v3-runtime"></a>Runtime V3

Před odesláním utterance do LUIS je nutné předzpracovat text pro opravy pravopisu. Použijte příklad projevy se správným pravopisem a ujistěte se, že jste získali správnou předpovědi.

Před odesláním do LUIS použijte [Kontrola pravopisu Bingu](../bing-spell-check/overview.md) k opravě textu.

### <a name="prior-to-v3-runtime"></a>Před verzí V3 runtime

LUIS používá [rozhraní API Bingu pro kontrolu pravopisu v7](../Bing-Spell-Check/overview.md) k opravě pravopisných chyb v utterance. LUIS potřebuje klíč přidružený k této službě. Vytvořte klíč a pak na [koncový bod](https://go.microsoft.com/fwlink/?linkid=2092356)přidejte klíč jako parametr QueryString.

Koncový bod vyžaduje dva parametry pro správné opravy pravopisu:

|Param|Hodnota|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|Klíč koncového bodu [rozhraní API Bingu pro kontrolu pravopisu v7](https://azure.microsoft.com/services/cognitive-services/spell-check/)|

Když [rozhraní API Bingu pro kontrolu pravopisu v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) detekuje chybu, původní utterance a opravené utterance se vrátí společně s předpovědi z koncového bodu.

#### <a name="v2-prediction-endpoint-response"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

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

### <a name="v3-prediction-api-to-alter-timezone"></a>Verze v3 rozhraní API pro změnu časového pásma

V `datetimeReference` hodnotě V3 určuje posun časového pásma. Přečtěte si další informace o [předpovědi V3](luis-migration-api-v3.md#v3-post-body).

### <a name="v2-prediction-api-to-alter-timezone"></a>V2 prediktivní rozhraní API pro změnu časového pásma
Časové pásmo se opravuje přidáním časového pásma uživatele do koncového bodu pomocí `timezoneOffset` parametru založeného na verzi rozhraní API. Hodnota parametru by měla být kladné nebo záporné číslo v minutách, aby se změnil čas.

#### <a name="v2-prediction-daylight-savings-example"></a>Příklad "letního úspory" v předpovědi v2
Pokud potřebujete vrátit předem sestavené datetimeV2 pro úpravu pro letní čas, měli byste použít parametr QueryString s hodnotou +/-v minutách pro dotaz na [koncový bod](https://go.microsoft.com/fwlink/?linkid=2092356) .

Přidejte 60 minut:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Odebrat 60 minut:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=-60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v2-prediction-c-code-determines-correct-value-of-parameter"></a>Verze 2 prediktivního C# kódu určuje správnou hodnotu parametru

Následující kód jazyka C# používá metodu [FindSystemTimeZoneById](/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) třídy [TimeZoneInfo](/dotnet/api/system.timezoneinfo) k určení správné hodnoty posunu na základě systémového času:

```csharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset/datetimeReference
int offset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Opravení pravopisných chyb pomocí tohoto kurzu](luis-tutorial-bing-spellcheck.md)