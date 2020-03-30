---
title: Změna knihovny procesorů informačního kanálu v Azure Cosmos DB
description: Přečtěte si, jak pomocí knihovny procesorů zdrojů změn Azure Cosmos DB číst zdroj změn, součásti procesoru kanálu změn
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: e71b2807595aebeb1f0c8682fde119f4e267e55d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273311"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Procesor kanálu změn ve službě Azure Cosmos DB 

Procesor kanálu změn je součástí [sady Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3). Zjednodušuje proces čtení kanálu změn a efektivně distribuuje zpracování událostí mezi více spotřebitelů.

Hlavní výhodou knihovny procesorů kanálu změn je jeho chování odolné proti chybám, které zajišťuje doručení všech událostí v kanálu změn alespoň jednou.

## <a name="components-of-the-change-feed-processor"></a>Součásti procesoru zdroje změn

Existují čtyři hlavní součásti implementace procesoru zdroje změn: 

1. **Monitorovaný kontejner:** Sledovaný kontejner obsahuje data, ze kterých je generován kanál změn. Všechny vloží a aktualizace monitorovaného kontejneru se projeví v kanálu změn kontejneru.

1. **Kontejner zapůjčení:** Kontejner zapůjčení funguje jako úložiště stavu a koordinuje zpracování kanálu změn mezi více pracovníky. Kontejner zapůjčení lze uložit ve stejném účtu jako monitorovaný kontejner nebo v samostatném účtu. 

1. **Hostitel:** Hostitel je instance aplikace, která používá procesor kanálu změn k naslouchání změnám. Souběžně může běžet více instancí se stejnou konfigurací zapůjčení, ale každá instance by měla mít jiný **název instance**. 

1. **Delegát:** Delegát je kód, který definuje, co vy, vývojář, chcete dělat s každou dávku změn, které čtení kanálu změny procesoru. 

Chcete-li dále pochopit, jak tyto čtyři prvky změny kanálu procesoru spolupracovat, podívejme se na příklad v následujícím diagramu. Monitorovaný kontejner ukládá dokumenty a používá "Město" jako klíč oddílu. Vidíme, že hodnoty klíče oddílu jsou distribuovány v rozsahu, které obsahují položky. Existují dvě instance hostitele a procesor kanálu změn přiřazuje různým rozsahům hodnot klíče oddílů každé instanci, aby maximalizoval rozložení výpočetních prostředků. Každý rozsah je číst paralelně a jeho průběh je udržována odděleně od jiných rozsahů v kontejneru zapůjčení.

![Příklad procesoru kanálu](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>Implementace zpracovatele zdrojů změn

Místem vstupu je vždy sledovaný kontejner `Container` z instance, kterou voláte `GetChangeFeedProcessorBuilder`:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Kde první parametr je odlišný název, který popisuje cíl tohoto procesoru a druhý název je implementace delegáta, který bude zpracovávat změny. 

Příkladem delegáta by bylo:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Nakonec definujete název pro tuto `WithInstanceName` instanci procesoru s a `WithLeaseContainer`který je kontejner udržovat stav zapůjčení s .

Volání `Build` vám poskytne instanci procesoru, `StartAsync`kterou můžete spustit voláním .

## <a name="processing-life-cycle"></a>Životní cyklus zpracování

Normální životní cyklus instance hostitele je:

1. Přečtěte si kanál o změnách.
1. Pokud nedojde k žádným změnám, přejděte na předdefinovanou `WithPollInterval` dobu (přizpůsobitelné v nástroji Builder) a přejděte na #1.
1. Pokud dojde ke změnám, pošlete je **delegátovi**.
1. Po úspěšném zpracování **změn**delegátem aktualizujte úložiště zapůjčení nejnovějším zpracovaným bodem v čase a přejděte na #1.

## <a name="error-handling"></a>Zpracování chyb

Procesor kanálu změn je odolný vůči chybám kódu uživatele. To znamená, že pokud implementace delegáta má neošetřenou výjimku (krok #4), zpracování vlákna, které konkrétní dávka změn bude zastavena a bude vytvořeno nové vlákno. Nové vlákno zkontroluje, který byl poslední bod v čase úložiště zapůjčení má pro tento rozsah hodnot klíče oddílu a restartovat odtud, účinně odesílání stejné dávky změn delegáta. Toto chování bude pokračovat, dokud delegát zpracuje změny správně a je to důvod, proč je procesor kanálu změn "alespoň jednou" záruku, protože pokud je vyvolán kód delegáta, bude opakovat tuto dávku.

## <a name="dynamic-scaling"></a>Dynamické škálování

Jak již bylo zmíněno během úvodu, procesor kanálu změn může automaticky distribuovat výpočetní prostředky mezi více instancí. Můžete nasadit více instancí vaší aplikace pomocí kanálu změn procesoru a využít ji, pouze klíčové požadavky jsou:

1. Všechny instance by měly mít stejnou konfiguraci kontejneru zapůjčení.
1. Všechny instance by měly mít stejný název pracovního postupu.
1. Každá instance musí mít jiný`WithInstanceName`název instance ( ).

Pokud platí tyto tři podmínky, bude procesor kanálu změn pomocí algoritmu stejné distribuce distribuovat všechny zapůjčení v kontejneru zapůjčení mezi všechny spuštěné instance a paralelizovat výpočetní prostředky. Jednu zapůjčení může vlastnit pouze jedna instance v daném okamžiku, takže maximální počet instancí se rovná počtu zapůjčení.

Počet instancí může zvětšovat a zmenšovat a procesor kanálu změn dynamicky upravit zatížení odpovídajícím způsobem redistribuce.

Kromě toho může procesor kanálu změn dynamicky přizpůsobit měřítko kontejnerů z důvodu zvýšení propustnostnebo úložiště. Když váš kontejner roste, procesor kanálu změn transparentně zpracovává tyto scénáře dynamicky zvyšuje zapůjčení a distribuci nové zapůjčení mezi existující instance.

## <a name="change-feed-and-provisioned-throughput"></a>Změna posuvu a zřízená propustnost

Jsou účtovány pro ru spotřebované, protože přesun dat do a z kontejnerů Cosmos vždy spotřebovává RU. Účtuje se vám ru spotřebované kontejnerem zapůjčení.

## <a name="additional-resources"></a>Další zdroje

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Ukázky využití na GitHubu](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Další ukázky na GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Další kroky

Další informace o procesoru kanálu změn můžete získat v následujících článcích:

* [Přehled zdroje změn](change-feed.md)
* [Jak migrovat z knihovny procesorů kanálu změn](how-to-migrate-from-change-feed-library.md)
* [Použití estimátoru pro kanálu změn](how-to-use-change-feed-estimator.md)
* [Počáteční čas procesoru kanálu změn](how-to-configure-change-feed-start-time.md)
