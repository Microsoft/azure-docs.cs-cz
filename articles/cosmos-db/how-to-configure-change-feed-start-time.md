---
title: Jak nakonfigurovat čas zahájení procesoru kanálu změn – Azure Cosmos DB
description: Přečtěte si, jak nakonfigurovat procesor kanálu změn tak, aby začal číst od určitého data a času
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: 600556a06d3f58c4d2ec79a49fdee5e8e04d4036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586270"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>Jak nakonfigurovat čas zahájení procesoru kanálu změn

Tento článek popisuje, jak můžete nakonfigurovat [kanál změny procesor](./change-feed-processor.md) začít číst od určitého data a času.

## <a name="default-behavior"></a>Výchozí chování

Při prvním spuštění procesoru kanálu změn inicializuje kontejner zapůjčení a spustí [jeho životní cyklus zpracování](./change-feed-processor.md#processing-life-cycle). Nebudou zjištěny žádné změny, ke kterým došlo v kontejneru před první inicializaí procesoru kanálu změn.

## <a name="reading-from-a-previous-date-and-time"></a>Čtení z předchozího data a času

Je možné inicializovat procesor kanálu změn číst změny počínaje **určité datum a čas** `DateTime` , `WithStartTime` předáním instance a do rozšíření tvůrce:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

Procesor kanálu změn bude inicializován pro dané konkrétní datum a čas a začne číst změny, ke kterým došlo po.

## <a name="reading-from-the-beginning"></a>Čtení od začátku

V jiných scénářích, jako je migrace dat nebo analýza celé historie kontejneru, musíme číst kanál změn od **začátku životnosti tohoto kontejneru**. Chcete-li to provést, můžeme použít `WithStartTime` na `DateTime.MinValue.ToUniversalTime()`rozšíření tvůrce, ale předávání , `DateTime` které by generovat UTC reprezentace minimální hodnoty, jako je takto:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

Procesor kanálu změn bude inicializován a začne číst změny od začátku životnosti kontejneru.

> [!NOTE]
> Tyto možnosti vlastního nastavení fungují pouze k nastavení počátečního bodu v čase procesoru kanálu změn. Jakmile zapůjčení kontejneru je inicializován a poprvé, jejich změna nemá žádný vliv.

> [!NOTE]
> Při zadávání bodu v čase budou přečteny pouze změny pro položky, které aktuálně existují v kontejneru. Pokud byla položka odstraněna, odebere se také její historie v kanálu změn.

## <a name="additional-resources"></a>Další zdroje

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Ukázky využití na GitHubu](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Další ukázky na GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Další kroky

Další informace o procesoru kanálu změn můžete získat v následujících článcích:

* [Přehled zpracovatele zdrojů změn](change-feed-processor.md)
* [Použití estimátoru pro kanálu změn](how-to-use-change-feed-estimator.md)
