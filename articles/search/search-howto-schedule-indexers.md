---
title: Naplánovat provádění indexeru
titleSuffix: Azure Cognitive Search
description: Naplánování pravidelného nebo v určitých časech indexerům služby Azure Kognitivní hledání indexování
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 8ae9a89ddba2010603ae5a5f6b812e3aa1e1e3a6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100097972"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Postup plánování indexerů v Azure Kognitivní hledání

Indexer se normálně spouští jednou ihned po jeho vytvoření. Následně ho můžete znovu spustit na vyžádání pomocí Azure Portal, [indexeru (REST)](/rest/api/searchservice/run-indexer)nebo sady Azure SDK. Případně můžete také nakonfigurovat indexer, který se má spustit podle plánu. V některých situacích, kdy je plánování indexeru užitečné, patří:

* Zdrojová data se v průběhu času mění a chcete, aby indexer vyhledávání automaticky zpracovával rozdíl.

* Zdrojová data jsou velmi velká a je třeba rozdělit zpracování indexerem v čase. Úlohy indexeru jsou v souladu s maximální dobou spuštění 24 hodin pro běžné zdroje dat a 2 hodiny pro indexery pomocí dovednosti. Pokud indexování nelze dokončit v rámci maximálního intervalu, můžete nakonfigurovat plán, který se spouští každé 2 hodiny. Indexery se mohou automaticky vyznačit tam, kde byly položeno, jako důkaz interní horní značky, která označuje, kde bylo indexování naposledy ukončeno. Spuštění indexeru s opakovaným 2 hodinovým plánem umožňuje IT zpracovat velmi velkou datovou sadu (mnoho milionů dokumentů) nad rámec povolený pro jednu úlohu. Další informace o indexování velkých objemů dat najdete v tématu [postup indexování velkých datových sad v Azure kognitivní hledání](search-howto-large-index.md).

* Index vyhledávání bude vyplněn z více zdrojů dat a chcete, aby indexery běžely v různou dobu, aby se snížily konflikty.

Vizuální plán může vypadat takto: od 1. ledna se spouští každých 50 minut.

```json
{
    "dataSourceName" : "myazuresqldatasource",
    "targetIndexName" : "target index name",
    "schedule" : { "interval" : "PT50M", "startTime" : "2021-01-01T00:00:00Z" }
}
```

> [!NOTE]
> Plánovač je integrovaná funkce Azure Kognitivní hledání. Pro externí plánovače není podporována žádná podpora.

## <a name="schedule-property"></a>Vlastnost plánu

Plán je součástí definice indexeru. Pokud je vlastnost **Schedule** vynechána, indexer se spustí pouze jednou ihned po vytvoření. Pokud přidáte vlastnost **plánu** , zadáte dvě části.

| Vlastnost | Popis |
|----------|-------------|
|**Interval** | požadovanou Množství času mezi začátkem dvou po sobě jdoucích spuštění indexeru. Nejmenší povolený interval je 5 minut a nejdelší je 1440 minut (24 hodin). Musí být formátován jako hodnota XSD "dayTimeDuration" (omezená podmnožina hodnoty [Duration ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Vzor pro tuto hodnotu je: `P(nD)(T(nH)(nM))` . <br/><br/>Příklady: `PT15M` každých 15 minut každé `PT2H` 2 hodiny.|
| **Čas spuštění (UTC)** | volitelné Určuje, kdy by mělo začít naplánované provádění. Je-li tento parametr vynechán, bude použit aktuální čas UTC. Tato doba může být v minulosti. v takovém případě je první spuštění naplánováno, jako kdyby indexer běžel nepřetržitě od původního **StartTime**.<br/><br/>Příklady: `2021-01-01T00:00:00Z` od 1. ledna od 1. ledna `2021-01-05T22:28:00Z` po 10:28. odp. 5. ledna.|

## <a name="scheduling-behavior"></a>Plánování chování

V jednom okamžiku může být spuštěno pouze jedno spuštění indexeru. Pokud je indexer již spuštěn, když je naplánováno jeho další spuštění, je spuštění odloženo až do dalšího naplánovaného času.

Pojďme tento příklad využít k tomu, aby to bylo konkrétnější. Předpokládejme, že nakonfigurujeme plán indexeru s **intervalem** hodin a **časem zahájení** od 1. června 2019 v 8:00:00 UTC. K tomu může dojít, když indexer trvá déle než hodinu:

* První spuštění indexeru začíná od 1. června 2019 v 8:00 UTC. Předpokládat, že provedení trvá 20 minut (nebo kdykoli méně než 1 hodinu).
* Druhé spuštění začíná od 1. června 2019 9:00 UTC. Předpokládejme, že toto spuštění trvá 70 minut – více než hodinu – a nebude dokončeno až do 10:10 UTC.
* Třetí spuštění je naplánováno na začátek v 10:00 UTC, ale v tuto chvíli je předchozí spuštění stále spuštěno. Toto plánované provedení je pak vynecháno. Další spuštění indexeru nebude zahájeno až do 11:00 UTC.

> [!NOTE]
> Pokud indexer nastavíte na určitý plán, ale opakovaně selže na stejném dokumentu, indexer začne běžet v méně častých intervalech (až do maximálního intervalu nejméně jednou za 24 hodin), dokud se znovu nestane. Pokud si myslíte, že jste si jisti, že jste nastavili některý z těchto potíží, můžete indexer spustit ručně a pokud je indexování úspěšné, indexer se vrátí do svého pravidelného plánu.

## <a name="schedule-using-rest"></a>Naplánování pomocí REST

Při vytváření nebo aktualizaci indexeru zadejte vlastnost **Schedule** .

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2021-01-01T00:00:00Z" }
    }
```

## <a name="schedule-using-net"></a>Plánování pomocí .NET

Následující příklad jazyka C# vytvoří indexer Azure SQL Database s použitím předdefinovaného zdroje dat a indexu a nastaví plán, který se spustí jednou denně, počínaje teď:

```csharp
var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
    StartTime = DateTimeOffset.Now
};

var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "Data indexer",
    Schedule = schedule
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

Plán je definován pomocí třídy [IndexingSchedule](/dotnet/api/azure.search.documents.indexes.models.indexingschedule) při vytváření nebo aktualizaci indexeru pomocí [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient). Konstruktor **IndexingSchedule** vyžaduje parametr **intervalu** zadaný pomocí objektu **TimeSpan** . Jak už bylo uvedeno výše, nejmenší povolená hodnota intervalu je 5 minut a největší je 24 hodin. Druhý parametr **StartTime** , zadaný jako objekt **DateTimeOffset** , je volitelný.

## <a name="next-steps"></a>Další kroky

U indexerů, které běží podle plánu, můžete sledovat operace načtením stavu ze služby vyhledávání nebo získat podrobné informace povolením diagnostického protokolování.

* [Sledovat stav indexeru hledání](search-howto-monitor-indexers.md)
* [Shromažďování a analýza dat protokolu](search-monitor-logs.md)