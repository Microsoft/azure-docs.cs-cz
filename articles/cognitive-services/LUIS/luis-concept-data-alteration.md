---
title: Koncepce data změnou v LUIS – Azure | Dokumentace Microsoftu
description: Zjistěte, jak lze změnit data před predikce v Language Understanding (LUIS)
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/26/2018
ms.author: v-geberr
ms.openlocfilehash: ab70839b4fa8f2064a3ff929034a6ffcb8fc7788
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887738"
---
# <a name="data-alterations"></a>Změny dat
Služba LUIS poskytuje možnosti pro manipulaci s utterance před nebo během do predikce. 

## <a name="correct-spelling-errors-in-utterance"></a>Psaní opravovat pravopisné chyby v utterance
Služba LUIS používá [API V7 kontrola pravopisu Bingu](https://azure.microsoft.com/services/cognitive-services/spell-check/) opravte pravopisné chyby utterance. Služba LUIS, musí klíč spojený s touto službou. Vytvoření klíče a pak přidejte klíč jako parametr řetězce dotazu na [koncový bod](https://aka.ms/luis-endpoint-apis). 

Můžete také opravte pravopisné chyby v **testovací** panelu podle [zadávání klíče](interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Klíč se ukládají jako proměnné relace v prohlížeči pro panelu Test. Přidáte klíč do panelu testů v každé relaci prohlížeče má kontrolu pravopisu opravit. 

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
 
## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Změna časového pásma datetimeV2 předem připravených entit
Pokud aplikace LUIS používá datetimeV2 předem připravených entit, hodnotu data a času mohou být vráceny v předpověď odpovědi. Časové pásmo požadavku se používá k určení správné datum a čas k vrácení. Pokud požadavek pochází z robota nebo jiné centralizované aplikace před získáním LUIS, opravte časové pásmo, které používá služba LUIS. 

### <a name="endpoint-querystring-parameter"></a>Parametr řetězce dotazu koncového bodu
Časové pásmo je opravit tak, že přidáte uživatele časové pásmo pro [koncový bod](https://aka.ms/luis-endpoint-apis) pomocí `timezoneOffset` param. Hodnota `timezoneOffset` během několika minut, chcete-li změnit čas by měl být kladné nebo záporné číslo.  

|Param|Hodnota|
|--|--|
|`timezoneOffset`|kladné nebo záporné číslo, během několika minut|

### <a name="daylight-savings-example"></a>Příklad úspory letního času
Pokud potřebujete vrácené předem připravených datetimeV2 upravit pro letní čas, měli byste použít `timezoneOffset` parametr řetězce dotazu s +/-hodnotu v minutách pro [koncový bod](https://aka.ms/luis-endpoint-apis) dotazu.

Přidáte 60 minut: 

https://{region}.API.cognitive.microsoft.com/luis/v2.0/Apps/{appId}?q=Turn světla na? **timezoneOffset = 60**& podrobné = {datový typ boolean} & Kontrola pravopisu = {datový typ boolean} & pracovní = {datový typ boolean} & Bingu – pravopisu – kontrola subscription-key = {řetězec} protoko & l = {datový typ boolean}

Odebrání 60 minut: 

https://{region}.API.cognitive.microsoft.com/luis/v2.0/Apps/{appId}?q=Turn světla na? **timezoneOffset = 60**& podrobné = {datový typ boolean} & Kontrola pravopisu = {datový typ boolean} & pracovní = {datový typ boolean} & Bingu – pravopisu – kontrola subscription-key = {řetězec} protoko & l = {datový typ boolean}

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>Kód jazyka C# určuje správnou hodnotu timezoneOffset
Následující kód jazyka C# používá [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo?view=netframework-4.7.1) třídy [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid?view=netframework-4.7.1#examples) metodou ke zjištění správné `timezoneOffset` podle systémový čas:

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