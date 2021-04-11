---
title: Použití estimatoru Change feed-Azure Cosmos DB
description: Naučte se používat estimatoru Change feed k analýze průběhu procesoru změny kanálu.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/01/2021
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: 5d4e461b25a25ecdf0d4d89ee7f1c82b9d4a0737
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220160"
---
# <a name="use-the-change-feed-estimator"></a>Použít Estimator kanálu změn
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek popisuje, jak můžete monitorovat průběh instancí [procesoru změn kanálu](./change-feed-processor.md) při čtení kanálu změn.

## <a name="why-is-monitoring-progress-important"></a>Proč je monitorování důležité?

Procesor změn kanálu funguje jako ukazatel, který přechází mezi [kanálem změn](./change-feed.md) a přináší změny implementace delegáta.

Nasazení procesoru změny kanálu může zpracovávat změny za určitou míru na základě dostupných prostředků, jako jsou například CPU, paměť, síť a tak dále.

Pokud je tato rychlost pomalejší než frekvence, s jakou se vaše změny vyskytují v rámci vašeho kontejneru Azure Cosmos, váš procesor se zahájí na zpoždění.

Identifikace tohoto scénáře vám pomůže pochopit, jestli potřebujeme škálovat naše nasazení procesoru Change feed.

## <a name="implement-the-change-feed-estimator"></a>Implementace Estimator kanálu změn

### <a name="as-a-push-model-for-automatic-notifications"></a>Jako model nabízených oznámení pro automatická oznámení

Podobně jako u [procesoru Change feed](./change-feed-processor.md)může Estimator Change feed fungovat jako model nabízených oznámení. Estimator vyhodnotí rozdíl mezi poslední zpracovávanou položkou (definovanou stavem kontejneru zapůjčení) a poslední změnou v kontejneru a nasdílením této hodnoty do delegáta. Interval, ve kterém je měření prováděno, lze také přizpůsobit výchozí hodnotu 5 sekund.

Pokud je například procesor pro změnu kanálu definován takto:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

Správný způsob, jak inicializovat Estimator k měření toho, jak by procesor používal, `GetChangeFeedEstimatorBuilder` například:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

Kde jak procesor, tak i Estimator mají stejný `leaseContainer` název.

Další dva parametry jsou delegát, který obdrží číslo, které představuje, **kolik změn čeká na jejich čtení** procesorem, a časový interval, ve kterém chcete toto měření provést.

Příklad delegáta, který obdrží odhad, je:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

Tento odhad můžete odeslat řešení monitorování a použít ho k pochopení toho, jak se v průběhu času chová průběh.

### <a name="as-an-on-demand-detailed-estimation"></a>Jako podrobný odhad na vyžádání

Na rozdíl od modelu nabízených oznámení existuje alternativa, která vám umožní získat odhad na vyžádání. Tento model poskytuje také podrobnější informace:

* Odhadované prodlevy na zapůjčení
* Instance vlastnící a zpracovávající jednotlivá zapůjčení, abyste mohli zjistit, jestli došlo k potížím s instancí.

Pokud je procesor změny kanálu definovaný takto:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimatorDetailed)]

Estimator můžete vytvořit se stejnou konfigurací zapůjčení:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimatorDetailed)]

A kdykoli budete chtít, s frekvencí, kterou požadujete, můžete získat podrobný odhad:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=GetIteratorEstimatorDetailed)]

Každá `ChangeFeedProcessorState` z nich bude obsahovat informace o zapůjčení a prodlevě a také o tom, kdo je aktuální instancí vlastnící. 

> [!NOTE]
> Estimator kanálu změn není nutné nasazovat jako součást procesoru změny kanálu ani být součástí stejného projektu. Může být nezávislý a spuštěný v zcela jiné instanci, která se doporučuje. Stačí použít stejný název a konfiguraci zapůjčení.

## <a name="additional-resources"></a>Další zdroje informací

* [Sada Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Ukázky použití na GitHubu](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Další ukázky na GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Další kroky

Teď můžete pokračovat a získat další informace o procesoru Change feed v následujících článcích:

* [Přehled procesoru Change feed](change-feed-processor.md)
* [Počáteční čas procesoru kanálu změn](./change-feed-processor.md#starting-time)