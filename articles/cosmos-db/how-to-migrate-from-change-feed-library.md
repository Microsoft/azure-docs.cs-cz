---
title: Migrace z knihovny Change feed Processor do sady Azure Cosmos DB .NET V3 SDK
description: Přečtěte si, jak migrovat aplikaci z použití knihovny Change feed Processor na sadu Azure Cosmos DB SDK v3.
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2019
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: b1c54c2e486f935b3c3ba1b13207caaa67099459
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490981"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Migrace z knihovny Change feed Processor do sady Azure Cosmos DB .NET V3 SDK

Tento článek popisuje nezbytné kroky pro migraci kódu existující aplikace, který používá [knihovnu Change feed Processor](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) , na funkci [Change feed](change-feed.md) v nejnovější verzi sady .NET SDK (označované také jako sada .NET V3 SDK).

## <a name="required-code-changes"></a>Požadované změny kódu

Sada .NET V3 SDK obsahuje několik zásadních změn, následující jsou klíčové kroky pro migraci aplikace:

1. Převeďte `DocumentCollectionInfo` instance na `Container` odkazy pro kontejnery monitorované a zapůjčení.
1. Vlastní nastavení, která `WithProcessorOptions` by se měla použít, by se měla aktualizovat, aby se používaly `WithLeaseConfiguration` `WithPollInterval` intervaly `WithStartTime` [pro čas spuštění](./change-feed-processor.md#starting-time)a `WithMaxItems` definovaly maximální počet položek.
1. Nastavte `processorName` on `GetChangeFeedProcessorBuilder` tak, aby odpovídal hodnotě nakonfigurované na `ChangeFeedProcessorOptions.LeasePrefix` , nebo použijte `string.Empty` jinak.
1. Změny již nejsou dodávány jako místo, `IReadOnlyList<Document>` `IReadOnlyCollection<T>` kde `T` je typ, který je třeba definovat, již neexistuje žádná základní třída Item.
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

## <a name="additional-resources"></a>Další materiály

* [Sada Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Ukázky použití na GitHubu](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Další ukázky na GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Další kroky

Teď můžete pokračovat a získat další informace o procesoru Change feed v následujících článcích:

* [Přehled procesoru Change feed](change-feed-processor.md)
* [Použití estimátoru pro kanálu změn](how-to-use-change-feed-estimator.md)
* [Počáteční čas procesoru kanálu změn](./change-feed-processor.md#starting-time)