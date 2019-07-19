---
title: Práce s knihovnou Change feed Processor v Azure Cosmos DB
description: Pomocí knihovny Azure Cosmos DB Change feed Processor.
author: rimman
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 42b7cd8a60e70ab75afc30910c46eb49f1f6d62a
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68000951"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Změnit procesor kanálu v Azure Cosmos DB 

Procesor změn kanálu je součástí sady [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3). Zjednodušuje proces čtení kanálu změn a umožňuje distribuci zpracování událostí mezi více příjemců efektivně.

Hlavní výhodou knihovny Change feed Processor je chování odolné proti chybám, které zajišťuje "nejméně jednou" doručení všech událostí v kanálu změn.

## <a name="components-of-the-change-feed-processor"></a>Součásti procesoru změny kanálu

Existují čtyři hlavní součásti implementace procesoru Change feed: 

1. **Monitorovaný kontejner:** Monitorovaný kontejner obsahuje data, ze kterých se generuje kanál změn. Jakékoli vložení a aktualizace monitorovaného kontejneru se projeví v kanálu změn kontejneru.

1. **Kontejner zapůjčení:** Kontejner zapůjčení funguje jako úložiště stavu a koordinuje zpracování kanálu změn napříč několika procesy. Kontejner zapůjčení může být uložený ve stejném účtu jako monitorovaný kontejner nebo v samostatném účtu. 

1. **Hostitel:** Hostitel je instance aplikace, která používá procesor změn kanálu k naslouchání změnám. Víc instancí se stejnou konfigurací zapůjčení můžete spustit paralelně, ale každá instance by měla mít jiný **název instance**. 

1. **Delegát:** Delegát je kód, který definuje, co vy, vývojář, chcete dělat s každou dávkou změn, které má procesor Change feed načíst. 

Abychom lépe porozuměli tomu, jak tyto čtyři prvky procesoru Change feed fungují společně, Podívejme se na příklad v následujícím diagramu. Monitorovaný kontejner ukládá dokumenty a používá jako klíč oddílu "City". Zjistili jsme, že hodnoty klíčů oddílu jsou distribuované v oblastech, které obsahují položky. Existují dvě instance hostitele a procesor změn kanálu přiřazuje každé instanci různé rozsahy hodnot klíče oddílu, aby bylo možné maximalizovat výpočetní rozdělení. Každý rozsah je čten paralelně a jeho průběh se udržuje odděleně od jiných rozsahů v kontejneru zapůjčení.

![Ukázka změny procesoru kanálu](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>Implementace procesoru změny kanálu

Bod vstupu je vždy monitorovaný kontejner, z `Container` instance, kterou voláte: `GetChangeFeedProcessorBuilder`

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Kde první parametr je jedinečný název, který popisuje cíl tohoto procesoru a druhý název je implementace delegáta, která bude zpracovávat změny. 

Příkladem delegáta může být:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Nakonec definujete název této instance procesoru s `WithInstanceName` a, který je kontejnerem pro udržení `WithLeaseContainer`stavu zapůjčení.

Volání `Build` vám poskytne instanci procesoru, kterou můžete spustit voláním `StartAsync`.

## <a name="processing-life-cycle"></a>Životní cyklus zpracování

Normální životní cyklus instance hostitele je:

1. Přečtěte si kanál změn.
1. Pokud nedošlo k žádným změnám, přejdete do režimu spánku v předdefinovaném `WithPollInterval` čase (dá se přizpůsobit v Tvůrci) a přejdete na #1.
1. Pokud dojde ke změnám, odešlete je **delegátovi**.
1. Když delegát dokončí zpracování změn **úspěšně**, aktualizujte úložiště zapůjčení s nejnovějším zpracovávaným bodem v čase a přejděte na #1.

## <a name="error-handling"></a>Zpracování chyb

Procesor změn kanálu je odolný proti chybám uživatelského kódu. To znamená, že pokud vaše implementace delegáta má neošetřenou výjimku (krok #4), zpracování vlákna, které konkrétní dávku změn dojde, se zastaví a vytvoří se nové vlákno. Nové vlákno zkontroluje, který byl nejnovějším bodem v čase, který má úložiště zapůjčení pro daný rozsah hodnot klíče oddílu, a pak se z něj restartuje a efektivně posílá stejnou dávku změn delegáta. Toto chování bude pokračovat, dokud váš delegát nezpracuje změny správně a jedná se o důvod, proč má procesor změn "alespoň jednou" jistotu, protože pokud kód delegáta vyvolá, bude opakovat tuto dávku.

## <a name="dynamic-scaling"></a>Dynamické škálování

Jak je uvedeno v úvodu, procesor Change feed může distribuovat výpočty napříč několika instancemi automaticky. Můžete nasadit více instancí aplikace pomocí procesoru změn kanálu a využít je k tomu, že jsou k dispozici jenom tyto klíčové požadavky:

1. Všechny instance musí mít stejnou konfiguraci kontejneru zapůjčení.
1. Všechny instance musí mít stejný název pracovního postupu.
1. Každá instance musí mít jiný název instance (`WithInstanceName`).

Pokud platí tyto tři podmínky, bude procesor změn pomocí stejného distribučního algoritmu distribuovat všechna zapůjčení do kontejneru zapůjčení ve všech spuštěných instancích a paralelizovat Compute. Jednu zapůjčenou adresu může vlastnit jenom jedna instance v daném okamžiku, takže maximální počet instancí se rovná počtu zapůjčení.

Instance se můžou zvětšovat a zmenšovat a procesor změn kanálu bude dynamicky upravovat zatížení tím, že je odpovídajícím způsobem znovu distribuován.

## <a name="change-feed-and-provisioned-throughput"></a>Změnit kanál a zřízenou propustnost

Účtují se vám poplatky za ru spotřebované, protože přesun dat do kontejnerů Cosmos a z nich vždycky spotřebovává ru. Účtují se vám poplatky za ru spotřebované kontejnerem zapůjčení.

## <a name="additional-resources"></a>Další zdroje

* [Sada Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Další ukázky na GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Další postup

Teď můžete přejít k další informace o změně v následujících článcích:

* [Přehled kanálu změn](change-feed.md)
* [Způsoby čtení kanálu změn](read-change-feed.md)
* [Změna kanálu s využitím Azure Functions](change-feed-functions.md)
