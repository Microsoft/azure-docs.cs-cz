---
title: Použití estimatoru Change feed-Azure Cosmos DB
description: Naučte se používat estimatoru Change feed k analýze průběhu procesoru změny kanálu.
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/15/2019
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: 442e1c4facce076ce9eeacee772df9381fee6f71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88997213"
---
# <a name="use-the-change-feed-estimator"></a>Použít Estimator kanálu změn

Tento článek popisuje, jak můžete monitorovat průběh instancí [procesoru změn kanálu](./change-feed-processor.md) při čtení kanálu změn.

## <a name="why-is-monitoring-progress-important"></a>Proč je monitorování důležité?

Procesor změn kanálu funguje jako ukazatel, který přechází mezi [kanálem změn](./change-feed.md) a přináší změny implementace delegáta. 

Nasazení procesoru změny kanálu může zpracovávat změny za určitou míru na základě dostupných prostředků, jako jsou například CPU, paměť, síť a tak dále.

Pokud je tato rychlost pomalejší než frekvence, s jakou se vaše změny vyskytují v rámci vašeho kontejneru Azure Cosmos, váš procesor se zahájí na zpoždění.

Identifikace tohoto scénáře vám pomůže pochopit, jestli potřebujeme škálovat naše nasazení procesoru Change feed.

## <a name="implement-the-change-feed-estimator"></a>Implementace Estimator kanálu změn

Podobně jako u [procesoru Change feed](./change-feed-processor.md)funguje Estimator kanálu změn jako model nabízených oznámení. Estimator vyhodnotí rozdíl mezi poslední zpracovávanou položkou (definovanou stavem kontejneru zapůjčení) a poslední změnou v kontejneru a nasdílením této hodnoty do delegáta. Interval, ve kterém je měření prováděno, lze také přizpůsobit výchozí hodnotu 5 sekund.

Pokud je například procesor pro změnu kanálu definován takto:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

Správný způsob, jak inicializovat Estimator k měření toho, jak by procesor používal, `GetChangeFeedEstimatorBuilder` například:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

Kde jak procesor, tak i Estimator mají stejný `leaseContainer` název.

Další dva parametry jsou delegát, který obdrží číslo, které představuje, **kolik změn čeká na jejich čtení** procesorem, a časový interval, ve kterém chcete toto měření provést.

Příklad delegáta, který obdrží odhad, je:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

Tento odhad můžete odeslat řešení monitorování a použít ho k pochopení toho, jak se v průběhu času chová průběh.

> [!NOTE]
> Estimator kanálu změn není nutné nasazovat jako součást procesoru změny kanálu ani být součástí stejného projektu. Může být nezávislý a musí běžet v zcela jiné instanci. Stačí použít stejný název a konfiguraci zapůjčení.

## <a name="additional-resources"></a>Další zdroje

* [Sada Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Ukázky použití na GitHubu](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Další ukázky na GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Další kroky

Teď můžete pokračovat a získat další informace o procesoru Change feed v následujících článcích:

* [Přehled procesoru Change feed](change-feed-processor.md)
* [Počáteční čas procesoru kanálu změn](how-to-configure-change-feed-start-time.md)
