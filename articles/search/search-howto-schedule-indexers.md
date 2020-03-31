---
title: Naplánovat spuštění indexeru
titleSuffix: Azure Cognitive Search
description: Naplánujte indexery Azure Cognitive Search tak, aby indexovaly obsah pravidelně nebo v určitou dobu.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72326413d463d449d339b1f3fd241ba2c27b4b6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112940"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Jak naplánovat indexery v Azure Cognitive Search

Indexer obvykle spustí jednou, ihned po jeho vytvoření. Můžete jej spustit znovu na vyžádání pomocí portálu, rozhraní REST API nebo sady .NET SDK. Můžete také nakonfigurovat indexer pravidelně spouštět podle plánu.

Některé situace, kdy je užitečné plánování indexeru:

* Zdrojová data se budou v průběhu času měnit a chcete, aby indexery Azure Cognitive Search automaticky zpracovávaly změněná data.
* Index bude naplněn z více zdrojů dat a chcete se ujistit, že indexery běží v různých časech snížit konflikty.
* Zdrojová data jsou velmi velká a chcete rozšířit zpracování indexeru v průběhu času. Další informace o indexování velkých objemů dat najdete v tématu [Jak indexovat velké datové sady v Azure Cognitive Search](search-howto-large-index.md).

Plánovač je integrovaná funkce Azure Cognitive Search. Externí plánovač nelze použít k řízení indexerů vyhledávání.

## <a name="define-schedule-properties"></a>Definování vlastností plánu

Plán indexeru má dvě vlastnosti:
* **Interval**, který definuje dobu mezi plánovanými spuštěními indexeru. Nejmenší povolený interval je 5 minut a největší je 24 hodin.
* **Počáteční čas (UTC)**, což znamená, že poprvé, kdy by měl být indexer spuštěn.

Můžete určit plán při prvním vytvoření indexeru nebo pozdější aktualizací vlastností indexeru. Plány indexeru lze nastavit pomocí [portálu](#portal), [rozhraní REST API](#restApi)nebo sady [.NET SDK](#dotNetSdk).

Současně lze spustit pouze jedno spuštění indexeru. Pokud indexer je již spuštěna při jeho další spuštění je naplánováno, toto spuštění je odloženo na další naplánovaný čas.

Podívejme se na příklad, aby to konkrétnější. Předpokládejme, že nakonfigurujeme plán indexeru s **intervalem** hodin a **časem zahájení** 1. Co se může stát, když spuštění indexeru trvá déle než hodinu:

* První spuštění indexeru začíná na nebo kolem června 1, 2019 v 8:00 AM UTC. Předpokládejme, že toto spuštění trvá 20 minut (nebo kdykoli méně než 1 hodinu).
* Druhé spuštění začíná na nebo kolem června 1, 2019 9:00 AM UTC. Předpokládejme, že toto spuštění trvá 70 minut – více než hodinu – a nebude dokončena až do 10:10 UTC.
* Třetí spuštění je naplánováno na začátek v 10:00 UTC, ale v té době je stále spuštěno předchozí spuštění. Toto naplánované spuštění je pak přeskočeno. Další spuštění indexeru se nespustí až do 11:00 UTC.

> [!NOTE]
> Pokud je indexer nastaven na určitý plán, ale opakovaně selže ve stejném dokumentu znovu a znovu při každém spuštění, indexer začne běžet v méně častém intervalu (až do maximálního počtu alespoň jednou za 24 hodin), dokud úspěšně neprovede pokrok Znovu.  Pokud se domníváte, že jste opravili jakýkoli problém, který způsoboval, že indexer byl v určitém okamžiku zablokovaný, můžete provést spuštění indexeru na vyžádání, a pokud to úspěšně provede pokrok, indexer se znovu vrátí do nastaveného intervalu plánu.

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>Plán na portálu

Průvodce importem dat na portálu umožňuje definovat plán indexeru v době vytvoření. Výchozí nastavení plánu je **Hodinová**, což znamená, že indexer se spustí jednou po jeho vytvoření a spustí se znovu každou hodinu poté.

Pokud nechcete, aby se indexer spouštěl znovu automaticky, nebo na **Denní,** aby se spouštěl jednou denně, můžete změnit nastavení Plán na **Jednou.** Nastavte ji na **vlastní,** pokud chcete zadat jiný interval nebo konkrétní budoucí čas zahájení.

Když nastavíte plán na **Vlastní**, zobrazí se pole, která umožňují určit **interval** a **čas zahájení (UTC).** Nejkratší povolený časový interval je 5 minut a nejdelší je 1440 minut (24 hodin).

   ![Nastavení plánu indexeru v Průvodci importem dat](media/search-howto-schedule-indexers/schedule-import-data.png "Nastavení plánu indexeru v Průvodci importem dat")

Po vytvoření indexeru můžete změnit nastavení plánu pomocí panelu Úpravy indexeru. Pole Plán jsou stejná jako v Průvodci importem dat.

   ![Nastavení plánu v panelu Úpravy indexeru](media/search-howto-schedule-indexers/schedule-edit.png "Nastavení plánu v panelu Úpravy indexeru")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>Plánování pomocí rest API

Můžete definovat plán pro indexer pomocí rozhraní REST API. Chcete-li to provést, zahrnout **vlastnost plánu** při vytváření nebo aktualizaci indexeru. Následující příklad ukazuje požadavek PUT na aktualizaci existujícího indexeru:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

Je vyžadován parametr **intervalu.** Interval odkazuje na čas mezi začátkem dvou po sobě jdoucích indexeru spuštění. Nejmenší povolený interval je 5 minut; nejdelší je jeden den. Musí být formátován jako hodnota XSD "dayTimeDuration" (omezená podmnožina hodnoty [trvání ISO 8601).](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) Vzor pro toto `P(nD)(T(nH)(nM))`je: . Příklady: `PT15M` za každých `PT2H` 15 minut, za každé 2 hodiny.

Volitelné **startTime** označuje, kdy by měla začít plánovaná spuštění. Pokud je vynechán, použije se aktuální čas UTC. Tato doba může být v minulosti, v takovém případě je naplánováno první spuštění, jako kdyby indexer byl spuštěn nepřetržitě od původního **startTime**.

Indexer na vyžádání můžete také kdykoli spustit pomocí volání Spustit indexer. Další informace o spuštění indexerů a nastavení indexeru plány, naleznete v [tématu Spuštění Indexer](https://docs.microsoft.com/rest/api/searchservice/run-indexer), [Získat Indexer](https://docs.microsoft.com/rest/api/searchservice/get-indexer)a [Aktualizovat Indexer](https://docs.microsoft.com/rest/api/searchservice/update-indexer) v odkazu rozhraní REST API.

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>Naplánovat použití sady .NET SDK

Můžete definovat plán pro indexer pomocí Azure Cognitive Search .NET SDK. Chcete-li to provést, zahrnout **vlastnost plánu** při vytváření nebo aktualizaci indexeru.

Následující příklad jazyka C# vytvoří indexer pomocí předdefinovaného zdroje dat a indexu a nastaví jeho plán tak, aby se spouštěl jednou denně od nynějška za 30 minut:

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
Pokud je parametr **plánu** vynechán, indexer bude spuštěn pouze jednou bezprostředně po jeho vytvoření.

Parametr **startTime** lze nastavit na čas v minulosti. V takovém případě je naplánováno první spuštění, jako by indexer byl spuštěn nepřetržitě od dané **startTime**.

Plán je definován pomocí třídy [IndexingSchedule.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) Konstruktor **IndexingSchedule** vyžaduje parametr **intervalu** určený pomocí objektu **TimeSpan.** Nejmenší povolená hodnota intervalu je 5 minut a největší je 24 hodin. Druhý parametr **startTime** zadaný jako objekt **DateTimeOffset** je volitelný.

Sada .NET SDK umožňuje řídit operace indexeru pomocí třídy [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) a její [vlastnosti Indexery,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) která implementuje metody z rozhraní **IIndexersOperations.** 

Indexer na vyžádání můžete kdykoli spustit pomocí jedné z metod [Run](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)nebo [RunWithHttpMessagesAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync)

Další informace o vytváření, aktualizaci a spuštění indexerů naleznete v [tématu IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).
