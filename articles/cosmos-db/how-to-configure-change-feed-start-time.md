---
title: Jak nakonfigurovat čas spuštění procesoru změny kanálu – Azure Cosmos DB
description: Zjistěte, jak nakonfigurovat procesor změn kanálu tak, aby začal číst z konkrétní datum a čas.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: 8a5507d11c9545e4053dde832b7305f9bf35e39e
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586270"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>Jak nakonfigurovat počáteční čas procesoru změny kanálu

Tento článek popisuje, jak můžete nakonfigurovat [procesor změn kanálu](./change-feed-processor.md) tak, aby se spouštěl z konkrétního data a času.

## <a name="default-behavior"></a>Výchozí chování

Když se procesor pro změnu kanálu poprvé spustí, inicializuje kontejner zapůjčení a spustí jeho [životní cyklus zpracování](./change-feed-processor.md#processing-life-cycle). Všechny změny, ke kterým došlo v kontejneru předtím, než byl procesor Change feed inicializován poprvé, se nezjistí.

## <a name="reading-from-a-previous-date-and-time"></a>Čtení z předchozího data a času

Můžete inicializovat procesor změn, aby bylo možné číst změny od **určitého data a času**, a to předáním instance `DateTime` do rozšíření `WithStartTime` Builder:

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="TimeInitialization":::

Procesor změnového kanálu se inicializuje pro konkrétní datum a čas a začne číst změny, ke kterým došlo po.

## <a name="reading-from-the-beginning"></a>Čtení od začátku

V jiných scénářích, jako jsou migrace dat nebo analýza celé historie kontejneru, musíme načíst kanál změn od **začátku životnosti tohoto kontejneru**. K tomu můžeme použít `WithStartTime` v rozšíření tvůrce, ale předávání `DateTime.MinValue.ToUniversalTime()`, které by vygenerovalo reprezentaci UTC minimální hodnoty `DateTime`, například:

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="StartFromBeginningInitialization":::

Procesor změny kanálu bude inicializován a začne číst změny od začátku životnosti kontejneru.

> [!NOTE]
> Tyto možnosti přizpůsobení fungují pouze k nastavení počátečního bodu v čase procesoru Change feed. Po prvním spuštění kontejneru zapůjčení, změna nemá žádný vliv.

> [!NOTE]
> Při zadávání bodu v čase budou čteny pouze změny pro položky, které aktuálně existují v kontejneru. Pokud se položka odstranila, odebere se i její historie kanálu změn.

## <a name="additional-resources"></a>Další zdroje

* [Sada Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Ukázky použití na GitHubu](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Další ukázky na GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Další kroky

Teď můžete pokračovat a získat další informace o procesoru Change feed v následujících článcích:

* [Přehled procesoru Change feed](change-feed-processor.md)
* [Použití Estimator kanálu změn](how-to-use-change-feed-estimator.md)
