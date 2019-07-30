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
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 198ce98808c8a62a839d154c365518c9e8263056
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619906"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Příkaz ALTER utterance dat před nebo během predikcí
Služba LUIS poskytuje možnosti pro manipulaci s utterance před nebo během do predikce. Mezi ně patří [Oprava pravopisu](luis-tutorial-bing-spellcheck.md)a řešení problémů s pásmem pro předem sestavené [datetimeV2](luis-reference-prebuilt-datetimev2.md). 

## <a name="correct-spelling-errors-in-utterance"></a>Psaní opravovat pravopisné chyby v utterance
Služba LUIS používá [API V7 kontrola pravopisu Bingu](../Bing-Spell-Check/overview.md) opravte pravopisné chyby utterance. Služba LUIS, musí klíč spojený s touto službou. Vytvoření klíče a pak přidejte klíč jako parametr řetězce dotazu na [koncový bod](https://go.microsoft.com/fwlink/?linkid=2092356). 

Můžete také opravte pravopisné chyby v **testovací** panelu podle [zadávání klíče](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Klíč se ukládají jako proměnné relace v prohlížeči pro panelu Test. Přidáte klíč do panelu testů v každé relaci prohlížeče má kontrolu pravopisu opravit. 

Použití klíče v panelu testu a na koncový bod počítají [použití klíče](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) kvóty. Služba LUIS implementuje kontrolu pravopisu Bingu omezení pro délku textu. 

Koncový bod vyžaduje dva parametry pro opravy pravopisu pracovat:

|Param|Hodnota|
|--|--|
|`spellCheck`|Boolean|
|`bing-spell-check-subscription-key`|[API V7 kontrola pravopisu Bingu](https://azure.microsoft.com/services/cognitive-services/spell-check/) klíče koncového bodu|

Když [API V7 kontrola pravopisu Bingu](https://azure.microsoft.com/services/cognitive-services/spell-check/) zjistí chybu, původní utterance a opravený utterance jsou vráceny spolu s předpovědí z koncového bodu.

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
 
### <a name="list-of-allowed-words"></a>Seznam povolených slov
Rozhraní API pro kontrolu pravopisu Bingu používané v LUIS nepodporuje seznam (označovaný také jako seznam povolených) slov, který se má ignorovat při změnách kontroly pravopisu. Pokud potřebujete povolený seznam slov nebo akronymů, před odesláním utterance do LUIS pro předpověď záměru zpracujte utterance v klientské aplikaci.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Změna časového pásma datetimeV2 předem připravených entit
Když aplikace LUIS používá předem vytvořenou entitu [datetimeV2](luis-reference-prebuilt-datetimev2.md) , hodnota DateTime může být vrácena v odpovědi předpovědi. Časové pásmo požadavku se používá k určení správné datum a čas k vrácení. Pokud požadavek pochází z robota nebo jiné centralizované aplikace před získáním LUIS, opravte časové pásmo, které používá služba LUIS. 

### <a name="endpoint-querystring-parameter"></a>Parametr řetězce dotazu koncového bodu
Časové pásmo je opravit tak, že přidáte uživatele časové pásmo pro [koncový bod](https://go.microsoft.com/fwlink/?linkid=2092356) pomocí `timezoneOffset` param. Hodnota `timezoneOffset` během několika minut, chcete-li změnit čas by měl být kladné nebo záporné číslo.  

|Param|Hodnota|
|--|--|
|`timezoneOffset`|kladné nebo záporné číslo, během několika minut|

### <a name="daylight-savings-example"></a>Příklad úspory letního času
Pokud potřebujete vrácené předem připravených datetimeV2 upravit pro letní čas, měli byste použít `timezoneOffset` parametr řetězce dotazu s +/-hodnotu v minutách pro [koncový bod](https://go.microsoft.com/fwlink/?linkid=2092356) dotazu.

Přidáte 60 minut: 

https://{region}.API.cognitive.microsoft.com/luis/v2.0/Apps/{appId}?q=Turn světla na? **timezoneOffset = 60**& podrobné = {datový typ boolean} & Kontrola pravopisu = {datový typ boolean} & pracovní = {datový typ boolean} & Bingu – pravopisu – kontrola subscription-key = {řetězec} protoko & l = {datový typ boolean}

Odebrání 60 minut: 

https://{region}.API.cognitive.microsoft.com/luis/v2.0/Apps/{appId}?q=Turn světla na? **timezoneOffset = 60**& podrobné = {datový typ boolean} & Kontrola pravopisu = {datový typ boolean} & pracovní = {datový typ boolean} & Bingu – pravopisu – kontrola subscription-key = {řetězec} protoko & l = {datový typ boolean}

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>Kód jazyka C# určuje správnou hodnotu timezoneOffset
Následující kód jazyka C# používá [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) třídy [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) metodou ke zjištění správné `timezoneOffset` podle systémový čas:

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
> [Opravovat pravopisné chyby v tomto kurzu](luis-tutorial-bing-spellcheck.md)
