---
title: Naplánovat provádění indexeru
titleSuffix: Azure Cognitive Search
description: Naplánování pravidelného nebo v určitých časech indexerům služby Azure Kognitivní hledání indexování
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: dffa8393dcfebf1cb73e3ab72890999cfa633b80
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91532563"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Postup plánování indexerů v Azure Kognitivní hledání

Indexer se normálně spouští jednou ihned po jeho vytvoření. Můžete ji znovu spustit na vyžádání pomocí portálu, REST API nebo sady .NET SDK. Můžete také nakonfigurovat indexer, aby pravidelně běžel podle plánu.

V některých situacích, kdy je plánování indexeru užitečné:

* Zdrojová data se v průběhu času mění a chcete, aby indexery služby Azure Kognitivní hledání automaticky zpracovaly změněná data.
* Index bude vyplněn z více zdrojů dat a chcete zajistit, aby se indexery spouštěly v různých časech, aby se snížily konflikty.
* Zdrojová data jsou velmi velká a je třeba rozdělit zpracování indexerem v čase. Další informace o indexování velkých objemů dat najdete v tématu [postup indexování velkých datových sad v Azure kognitivní hledání](search-howto-large-index.md).

Plánovač je integrovaná funkce Azure Kognitivní hledání. Nemůžete použít externí plánovač k řízení vyhledávacích indexerů.

## <a name="define-schedule-properties"></a>Definovat vlastnosti plánu

Plán indexeru má dvě vlastnosti:
* **Interval**, který definuje dobu mezi plánovanými provádění indexerů. Nejmenší povolený interval je 5 minut a největší je 24 hodin.
* **Čas spuštění (UTC)**, který označuje, že se má indexer spustit poprvé.

Při prvním vytváření indexeru můžete zadat plán nebo aktualizovat vlastnosti indexeru později. Plány indexeru se dají nastavit pomocí [portálu](#portal), [REST API](#restApi)nebo [sady .NET SDK](#dotNetSdk).

V jednom okamžiku může být spuštěno pouze jedno spuštění indexeru. Pokud je indexer již spuštěn, když je naplánováno jeho další spuštění, je spuštění odloženo až do dalšího naplánovaného času.

Pojďme tento příklad využít k tomu, aby to bylo konkrétnější. Předpokládejme, že nakonfigurujeme plán indexeru s **intervalem** hodin a **časem zahájení** od 1. června 2019 v 8:00:00 UTC. K tomu může dojít, když indexer trvá déle než hodinu:

* První spuštění indexeru začíná od 1. června 2019 v 8:00 UTC. Předpokládat, že provedení trvá 20 minut (nebo kdykoli méně než 1 hodinu).
* Druhé spuštění začíná od 1. června 2019 9:00 UTC. Předpokládejme, že toto spuštění trvá 70 minut – více než hodinu – a nebude dokončeno až do 10:10 UTC.
* Třetí spuštění je naplánováno na začátek v 10:00 UTC, ale v tuto chvíli je předchozí spuštění stále spuštěno. Toto plánované provedení je pak vynecháno. Další spuštění indexeru nebude zahájeno až do 11:00 UTC.

> [!NOTE]
> Pokud je indexer nastavený na určitý plán, ale opakovaně selže na stejném dokumentu a znovu se pokaždé, když se spustí, indexer začne běžet v kratším intervalu (až do maximálního počtu alespoň každých 24 hodin), dokud to znovu neudělá.  Pokud se domníváte, že jste se rozhodli, že jste si myslíte, že byl problém, který způsobil, že se indexer zablokoval v určitém bodě, můžete na vyžádání provést indexer a pokud se to úspěšně dokončí, indexer se znovu vrátí do intervalu nastaveného plánu.

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>Plán na portálu

Průvodce importem dat na portálu umožňuje definovat plán pro indexer v okamžiku vytvoření. Výchozí nastavení plánu je **každou hodinu**, což znamená, že indexer se po vytvoření spustí znovu a spustí se znovu každou hodinu.

Nastavení plánu můžete změnit na **jednou** , pokud nechcete, aby indexer znovu běžel automaticky **nebo aby běžel jednou denně.** Nastavte ji na **vlastní** , pokud chcete zadat jiný interval nebo určitý čas spuštění v budoucnosti.

Když nastavíte plán na **vlastní**, zobrazí se pole, která umožňují zadat **interval** a **čas spuštění (UTC)**. Nejkratší časový interval povolený v intervalu 5 minut a nejdelší je 1440 minut (24 hodin).

   ![Nastavení plánu indexeru v Průvodci importem dat](media/search-howto-schedule-indexers/schedule-import-data.png "Nastavení plánu indexeru v Průvodci importem dat")

Po vytvoření indexeru můžete změnit nastavení plánu pomocí panelu úprav indexeru. Pole plánu jsou stejná jako v Průvodci importem dat.

   ![Nastavení plánu v panelu pro úpravy indexeru](media/search-howto-schedule-indexers/schedule-edit.png "Nastavení plánu v panelu pro úpravy indexeru")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>Plánování pomocí rozhraní REST API

Plán pro indexer můžete definovat pomocí REST API. Uděláte to tak, že při vytváření nebo aktualizaci indexeru zadáte vlastnost **Schedule** . Následující příklad ukazuje požadavek PUT k aktualizaci stávajícího indexeru:

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }
```

Parametr **interval** je povinný. Tento interval odkazuje na čas mezi začátkem dvou po sobě jdoucích spuštění indexeru. Nejmenší povolený interval je 5 minut. nejdelší je jeden den. Musí být formátován jako hodnota XSD "dayTimeDuration" (omezená podmnožina hodnoty [Duration ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Vzor pro tuto hodnotu je: `P(nD)(T(nH)(nM))` . Příklady: `PT15M` každých 15 minut každé `PT2H` 2 hodiny.

Nepovinná položka **StartTime** indikuje, kdy by mělo začít naplánované provádění. Je-li tento parametr vynechán, bude použit aktuální čas UTC. Tato doba může být v minulosti. v takovém případě je první spuštění naplánováno, jako kdyby indexer běžel nepřetržitě od původního **StartTime**.

Indexer můžete na vyžádání spustit kdykoli pomocí volání metody Run indexer. Další informace o spouštění indexerů a nastavení plánů indexerů najdete v tématech [spuštění indexeru](/rest/api/searchservice/run-indexer), [získání indexeru](/rest/api/searchservice/get-indexer)a [aktualizace indexeru](/rest/api/searchservice/update-indexer) v odkazu na REST API.

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>Plánování pomocí .NET SDK

Můžete definovat plán pro indexer pomocí sady Azure Kognitivní hledání .NET SDK. Chcete-li to provést, zahrňte při vytváření nebo aktualizaci indexeru vlastnost **Schedule** .

Následující příklad jazyka C# vytvoří indexer pomocí předdefinovaného zdroje dat a indexu a nastaví jeho plán, který se spustí jednou denně od 30 minut od této chvíle:

```
    Indexer indexer = new Indexer(
        name: "azure-sql-indexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        schedule: new IndexingSchedule(
                        TimeSpan.FromDays(1), 
                        new DateTimeOffset(DateTime.UtcNow.AddMinutes(30))
                    )
        );
    await searchService.Indexers.CreateOrUpdateAsync(indexer);
```
Pokud je parametr **Schedule** vynechán, indexer se spustí pouze jednou ihned po jeho vytvoření.

Parametr **StartTime** lze nastavit na čas v minulosti. V takovém případě je naplánováno první spuštění, jako kdyby indexer běžel nepřetržitě od daného **čas_spuštění**.

Plán je definován pomocí třídy [IndexingSchedule](/dotnet/api/microsoft.azure.search.models.indexingschedule) . Konstruktor **IndexingSchedule** vyžaduje parametr **intervalu** zadaný pomocí objektu **TimeSpan** . Minimální povolená hodnota intervalu je 5 minut a největší je 24 hodin. Druhý parametr **StartTime** , zadaný jako objekt **DateTimeOffset** , je volitelný.

Sada .NET SDK umožňuje řídit operace indexeru pomocí třídy [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) a její vlastnosti [indexerů](/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) , která implementuje metody z rozhraní **IIndexersOperations** . 

Indexer můžete na vyžádání spustit kdykoli pomocí jedné z metod [Run](/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)nebo [RunWithHttpMessagesAsync](/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync) .

Další informace o vytváření, aktualizaci a spouštění indexerů najdete v tématu [IIindexersOperations](/dotnet/api/microsoft.azure.search.iindexersoperations).