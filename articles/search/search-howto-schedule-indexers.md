---
title: Jak plánovat indexery – Azure Search
description: Naplánujte indexerů Azure Search k indexování obsahu, pravidelně nebo v určitých časech.
ms.date: 05/31/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 764fca8d3cb4cd9c40d7880043637f89ef1a8578
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755377"
---
# <a name="how-to-schedule-indexers-for-azure-search"></a>Jak naplánovat indexování pro službu Azure Search
Indexer normálně spouští jednou, ihned po jeho vytvoření. Můžete ho znovu spustit na vyžádání pomocí portálu, rozhraní REST API nebo .NET SDK. Můžete také nakonfigurovat indexer pravidelné spouštění podle plánu.

V některých situacích indexer plánování užitečné:

* Zdrojová data se v průběhu času měnit a chcete indexerů Azure Search a automaticky zpracovávat změněná data.
* Naplní index z různých zdrojů dat a chcete zajistit, aby že indexery spuštění v různých časech, abyste snížili je v konfliktu.
* Zdrojová data jsou velmi velké a chcete rozšířit indexer zpracování v čase. Další informace o indexování velké objemy dat, naleznete v tématu [jak indexovat rozsáhlým datovým sadám ve službě Azure Search](search-howto-large-index.md).

Plánovač je integrovaná funkce služby Azure Search. Externího plánovače nelze použít k řízení hledání indexery.

## <a name="define-schedule-properties"></a>Definovat vlastnosti plánu

Plán indexeru má dvě vlastnosti:
* **Interval**, která definuje časový úsek mezi naplánované spuštění indexeru. Nejmenší povolený interval je 5 minut a největší je 24 hodin.
* **Čas začátku (UTC)** , což znamená poprvé, ve kterém by se měl spustit indexer.

Plán můžete zadat při prvním vytvoření indexeru, nebo prostřednictvím aktualizace indexeru vlastností později. Indexer plány lze nastavit pomocí [portál](#portal), [rozhraní REST API](#restApi), nebo [sady .NET SDK](#dotNetSdk).

Najednou lze spustit pouze jedno provedení indexeru. Pokud indexer je již spuštěn, když jeho dalšímu spuštění je naplánováno, tohoto spuštění je odložit, dokud dalším naplánovaném čase.

Zvažte následující příklad to lze provést konkrétnější. Předpokládejme, že můžeme nakonfigurovat plán indexeru se **Interval** z každou hodinu a **čas zahájení** ze 1 dne. května 2019 v 8:00:00: 00 UTC. Zde je, co může dojít v případě, že trvá déle než hodinu, kterého se spuštění indexeru:

* Dojde k prvnímu spuštění indexeru začíná na nebo okolo 1 dne. května 2019 v 8:00:00 UTC. Předpokládejme, že toto spuštění trvá 20 minut (nebo vždy menší než 1 hodina).
* Spuštění druhého začíná na nebo okolo 1 dne. května 2019 9:00:00 UTC. Předpokládejme, že toto spuštění trvá 70 minut – více než jedna hodina – a to se nikdy nedokončí až do 10:10:00 UTC.
* Třetí spuštění je naplánované spuštění v 10:00:00 UTC, ale v tuto chvíli je stále spuštěna předchozích spuštění. To naplánované spuštění se pak přeskočí. Další spuštění indexeru začnou účtovat až 11:00 AM UTC.

<a name="portal"></a>

## <a name="define-a-schedule-in-the-portal"></a>Definujte plán na portálu

Průvodce importem dat na portálu umožňuje definovat plán pro indexer v okamžiku vytvoření. Ve výchozím nastavení plánu **hodinové**, což znamená, že indexer se spustí jednou po se vytvoří a spustí znovu každou hodinu později.

Můžete změnit nastavení plánu pro **po** Pokud nechcete, aby znovu automatické spuštění indexeru nebo **denní** ke spuštění jednou za den. Nastavte ho na **vlastní** Pokud chcete určit jiný interval nebo konkrétní budoucí čas spuštění.

Když nastavíte plán na **vlastní**, pole se zobrazí na vám umožní zadat **Interval** a **Start čas (UTC)** . Nejkratší povolený interval je 5 minut a nejdelší sekvenci čas je 1440 minut (24 hodin).

   ![Plán indexeru nastavení v Průvodci importem dat](media/search-howto-schedule-indexers/schedule-import-data.png "plán indexeru nastavení v Průvodci importem dat")

Po vytvoření indexeru můžete změnit nastavení plánu pomocí panel úprav indexeru. Pole plánu jsou stejné jako Průvodce importem dat.

   ![Nastavení plánu panelu Upravit indexer](media/search-howto-schedule-indexers/schedule-edit.png "nastavení plánu panelu Upravit indexer")

<a name="restApi"></a>

## <a name="define-a-schedule-using-the-rest-api"></a>Definujte plán pomocí rozhraní REST API

Můžete definovat plán pro indexer pomocí rozhraní REST API. Chcete-li to provést, patří **plán** vlastnost při vytváření nebo aktualizaci indexeru. Následující příklad ukazuje požadavek PUT k aktualizaci existujícího indexeru:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**Interval** parametr je povinný. Interval označuje čas mezi začátkem dvě po sobě jdoucích indexer spuštění. Nejkratší povolený interval je 5 minut; nejdelší sekvenci je za jeden den. Musí být naformátovaná jako hodnotu "dayTimeDuration" XSD (omezená podmnožina [ISO 8601 trvání](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) hodnota). Tento vzor je: `P(nD)(T(nH)(nM))`. Příklady: `PT15M` pro každých 15 minut `PT2H` pro každé dvě hodiny.

Volitelný **startTime** označuje, kdy chcete spustit naplánované spuštění. Pokud ji vynecháte, je použit aktuální čas UTC. Tento čas může být v minulosti, ve kterém případě dojde k prvnímu spuštění plánu jako v případě, že indexer je spuštěné nepřetržitě od původní **startTime**.

Můžete také spustit indexer na vyžádání v každém okamžiku pomocí volání spuštění indexeru. Další informace o provozu indexery a nastavení plánů indexer najdete v tématu [spustit Indexer](https://docs.microsoft.com/rest/api/searchservice/run-indexer), [získat Indexer](https://docs.microsoft.com/rest/api/searchservice/get-indexer), a [aktualizace Indexer](https://docs.microsoft.com/rest/api/searchservice/update-indexer) v referenci rozhraní REST API.

<a name="dotNetSdk"></a>

## <a name="define-a-schedule-using-the-net-sdk"></a>Definujte plán pomocí sady .NET SDK

Můžete definovat plán pro indexer pomocí .NET SDK služby Azure Search. Chcete-li to provést, patří **plán** vlastnost při vytváření nebo aktualizaci indexeru.

Následující C# příklad vytvoří indexer pomocí předdefinované datové zdroje a index a nastaví její plán pro spuštění jednou denně spouští 30 minut od tohoto okamžiku:

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
Pokud **plán** parametr se vynechá, spustí se indexer pouze jednou ihned po jeho vytvoření.

**StartTime** parametr může být nastaven na čas v minulosti. V takovém případě dojde k prvnímu spuštění plánu jako v případě, že indexer je spuštěné nepřetržitě od daný **startTime**.

Plán je definován pomocí [IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) třídy. **IndexingSchedule** vyžaduje konstruktor **interval** zadat pomocí parametru **TimeSpan** objektu. Minimální povolená hodnota interval je 5 minut a největší je 24 hodin. Druhá **startTime** zadán jako parametr **DateTimeOffset** objektu, je volitelný.

Sady .NET SDK vám umožní pomocí operace indexeru ovládacího prvku [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) třídy a jeho [indexery](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) vlastnost, která implementuje metody ze **IIndexersOperations**rozhraní. 

Indexer můžete spouštět na vyžádání v každém okamžiku pomocí jedné z [spustit](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync), nebo [RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync) metody.

Další informace o vytváření, aktualizaci a systémem indexery, najdete v části [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).
