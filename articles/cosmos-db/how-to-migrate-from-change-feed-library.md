---
title: Migrace z knihovny Change feed Processor do sady Azure Cosmos DB .NET V3 SDK
description: Přečtěte si, jak migrovat aplikaci z použití knihovny Change feed Processor na sadu Azure Cosmos DB SDK v3.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: 9570a8512e3437b12ecce2ef0c708a74a8806482
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077552"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Migrace z knihovny Change feed Processor do sady Azure Cosmos DB .NET V3 SDK

Tento článek popisuje nezbytné kroky pro migraci kódu existující aplikace, který používá [knihovnu Change feed Processor](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) , na funkci [Change feed](change-feed.md) v nejnovější verzi sady .NET SDK (označované také jako sada .NET V3 SDK).

## <a name="required-code-changes"></a>Požadované změny kódu

Sada .NET V3 SDK obsahuje několik zásadních změn, následující jsou klíčové kroky pro migraci aplikace:

1. Převeďte `DocumentCollectionInfo` `Container` instance na odkazy pro kontejnery monitorované a zapůjčení.
1. Vlastní nastavení, která `WithProcessorOptions` by se měla použít, `WithLeaseConfiguration` by `WithPollInterval` se měla aktualizovat `WithStartTime` , aby se používaly `WithMaxItems` intervaly [pro čas spuštění](how-to-configure-change-feed-start-time.md)a definovaly maximální počet položek.
1. `ChangeFeedProcessorOptions.LeasePrefix` `string.Empty` Nastavte on tak`GetChangeFeedProcessorBuilder` , aby odpovídal hodnotě nakonfigurované na, nebo použijte jinak. `processorName`
1. Změny již nejsou dodávány jako `IReadOnlyList<Document>`místo `IReadOnlyCollection<T>` , kde `T` je typ, který je třeba definovat, již neexistuje žádná základní třída Item.
1. Pro zpracování změn už nebudete potřebovat implementaci, místo toho musíte [definovat delegáta](change-feed-processor.md#implementing-the-change-feed-processor). Delegát může být statická funkce nebo, pokud potřebujete zachovat stav napříč prováděním, můžete vytvořit vlastní třídu a předat metodu instance jako delegáta.

Například pokud původní kód pro sestavení procesoru Change feed, vypadá takto:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

Migrovaný kód bude vypadat takto:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

A delegát může být statická metoda:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>Stav a kontejner zapůjčení

Podobně jako knihovna Change feed Processor, funkce Change feed v sadě .NET V3 SDK používá [kontejner zapůjčení](change-feed-processor.md#components-of-the-change-feed-processor) pro uložení stavu. Schémata se ale liší.

Procesor změn sady SDK V3 detekuje libovolný původní stav knihovny a automaticky ho migruje do nového schématu při prvním spuštění migrovaného kódu aplikace. 

Aplikaci můžete bezpečně zastavit pomocí starého kódu, migrovat kód na novou verzi, spustit migrovaná aplikace a všechny změny, ke kterým došlo v době, kdy byla aplikace zastavena, budou vyzvednuty a zpracovávány novou verzí.

> [!NOTE]
> Migrace z aplikací, které používají knihovnu do sady .NET V3 SDK, je jednosměrná, protože stav (zapůjčení) bude migrován do nového schématu. Migrace není zpětně kompatibilní.


## <a name="additional-resources"></a>Další zdroje

* [Sada Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Ukázky použití na GitHubu](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Další ukázky na GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Další postup

Teď můžete pokračovat a získat další informace o procesoru Change feed v následujících článcích:

* [Přehled procesoru Change feed](change-feed-processor.md)
* [Použití Estimator kanálu změn](how-to-use-change-feed-estimator.md)
* [Změna počátečního času procesoru kanálu](how-to-configure-change-feed-start-time.md)
