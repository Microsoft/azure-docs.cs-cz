---
title: Použití odhadu podavače změn – Azure Cosmos DB
description: Přečtěte si, jak pomocí odhadu zdroje změn analyzovat průběh procesoru zdroje změn
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: maquaran
ms.openlocfilehash: 0023f68400b36b9abd3b9d4a789895e79f67aa03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585284"
---
# <a name="use-the-change-feed-estimator"></a>Použít odhad posuvu změn

Tento článek popisuje, jak můžete sledovat průběh instancí [procesoru kanálu změn](./change-feed-processor.md) při čtení kanálu změn.

## <a name="why-is-monitoring-progress-important"></a>Proč je sledování pokroku důležité?

Procesor kanálu změn funguje jako ukazatel, který se pohybuje vpřed v kanálu [změn](./change-feed.md) a přináší změny do implementace delegáta. 

Nasazení procesoru kanálu změn může zpracovávat změny určitou rychlostí na základě dostupných prostředků, jako je procesor, paměť, síť a tak dále.

Pokud je tato rychlost pomalejší než rychlost, jakou dochází k vašim změnám v kontejneru Azure Cosmos, procesor začne zaostávat.

Identifikace tohoto scénáře pomáhá pochopit, pokud potřebujeme škálovat nasazení našeho procesoru kanálu změn.

## <a name="implement-the-change-feed-estimator"></a>Implementace odhadu posuvu změn

Podobně jako [při posuvu změn](./change-feed-processor.md)funguje odhad posuvu změn jako model push. Odhad změří rozdíl mezi poslední zpracovanou položkou (definovanou stavem kontejneru zapůjčení) a poslední změnou v kontejneru a tuto hodnotu posune delegátovi. Interval, ve kterém je měření přijato, lze také přizpůsobit s výchozí hodnotou 5 sekund.

Jako příklad, pokud je procesor zdroje změn definován takto:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

Správný způsob, jak inicializovat odhad pro měření, že procesor by se pomocí `GetChangeFeedEstimatorBuilder` takto:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

Pokud procesor i odhad sdílejí stejný `leaseContainer` a stejný název.

Další dva parametry jsou delegát, který obdrží číslo, které **představuje, kolik změn čeká na čtení** procesorem a časový interval, ve kterém chcete, aby toto měření bylo přijato.

Příkladem delegáta, který obdrží odhad je:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

Tento odhad můžete odeslat do vašeho řešení monitorování a použít jej k pochopení, jak se váš pokrok chová v průběhu času.

> [!NOTE]
> Odhad kanálu změn nemusí být nasazen jako součást procesoru kanálu změn ani být součástí stejného projektu. Může být nezávislý a spustit ve zcela jiné instanci. Je třeba použít stejný název a konfiguraci zapůjčení.

## <a name="additional-resources"></a>Další zdroje

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Ukázky využití na GitHubu](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Další ukázky na GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Další kroky

Další informace o procesoru kanálu změn můžete získat v následujících článcích:

* [Přehled zpracovatele zdrojů změn](change-feed-processor.md)
* [Počáteční čas procesoru kanálu změn](how-to-configure-change-feed-start-time.md)
