---
title: Migrace z knihovny procesoru kanálu změn do sady Azure Cosmos DB .NET V3 SDK
description: Zjistěte, jak migrovat aplikaci z použití knihovny procesorů kanálu změn do sady Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: 9570a8512e3437b12ecce2ef0c708a74a8806482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588879"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Migrace z knihovny procesoru kanálu změn do sady Azure Cosmos DB .NET V3 SDK

Tento článek popisuje požadované kroky k migraci kódu existující aplikace, který používá [knihovnu procesoru kanálu změn](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) na funkci [kanálu změn](change-feed.md) v nejnovější verzi sady .NET SDK (označované také jako .NET V3 SDK).

## <a name="required-code-changes"></a>Požadované změny kódu

Sada SDK .NET V3 má několik narušujících změn, následující jsou klíčové kroky pro migraci aplikace:

1. Převést `DocumentCollectionInfo` instance `Container` na odkazy pro monitorované a zapůjčené kontejnery.
1. Použití vlastního `WithProcessorOptions` nastavení by měla `WithLeaseConfiguration` `WithPollInterval` být aktualizována `WithStartTime` tak, aby `WithMaxItems` se používala a pro intervaly, čas [zahájení](how-to-configure-change-feed-start-time.md)a definování maximálního počtu položek.
1. Nastavte `processorName` zapnuto `GetChangeFeedProcessorBuilder` tak, aby `ChangeFeedProcessorOptions.LeasePrefix`odpovídalo `string.Empty` hodnotě nakonfigurované v písmenu a), nebo použijte jinak.
1. Změny již nejsou doručovány jako `IReadOnlyList<Document>`, `IReadOnlyCollection<T>` místo `T` toho je to místo, kde je typ, který je třeba definovat, již neexistuje žádná základní třída položky.
1. Chcete-li zpracovat změny, již nepotřebujete implementaci, místo toho je třeba [definovat delegáta](change-feed-processor.md#implementing-the-change-feed-processor). Delegát může být statické funkce nebo pokud potřebujete udržovat stav napříč spuštění, můžete vytvořit vlastní třídu a předat metodu instance jako delegát.

Například pokud původní kód pro sestavení procesoru kanálu změn vypadá takto:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

Migrovaný kód bude vypadat takto:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

A delegát, může být statická metoda:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>Státní a nájemní kontejner

Podobně jako knihovna procesoru kanálu změn funkce kanálu změn v .NET V3 SDK používá [kontejner zapůjčení](change-feed-processor.md#components-of-the-change-feed-processor) k uložení stavu. Schémata jsou však různé.

Procesor kanálu změn Sady SDK V3 detekuje jakýkoli starý stav knihovny a automaticky jej migruje do nového schématu při prvním spuštění kódu přenesené aplikace. 

Můžete bezpečně zastavit aplikaci pomocí starého kódu, migrovat kód do nové verze, spustit migrou a všechny změny, ke kterým došlo, když byla aplikace zastavena, budou vyzvednuty a zpracovány novou verzí.

> [!NOTE]
> Migrace z aplikací používajících knihovnu do sady .NET V3 SDK jsou jednosměrné, protože stav (zapůjčení) bude migrován do nového schématu. Migrace není zpětně kompatibilní.


## <a name="additional-resources"></a>Další zdroje

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Ukázky využití na GitHubu](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Další ukázky na GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Další kroky

Další informace o procesoru kanálu změn můžete získat v následujících článcích:

* [Přehled zpracovatele zdrojů změn](change-feed-processor.md)
* [Použití estimátoru pro kanálu změn](how-to-use-change-feed-estimator.md)
* [Počáteční čas procesoru kanálu změn](how-to-configure-change-feed-start-time.md)
