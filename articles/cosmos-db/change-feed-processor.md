---
title: Procesor kanálu změn ve službě Azure Cosmos DB
description: Naučte se používat modul Azure Cosmos DB změnového kanálu ke čtení kanálu změn, součástí procesoru Change feed.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 377165c94303a4a44d481009700cdef9169b3d78
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92475800"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Procesor kanálu změn ve službě Azure Cosmos DB

Procesor změn kanálu je součástí sady [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3). Zjednodušuje proces čtení kanálu změn a umožňuje distribuci zpracování událostí mezi více příjemců efektivně.

Hlavní výhodou knihovny Change feed Processor je chování odolné proti chybám, které zajišťuje "nejméně jednou" doručení všech událostí v kanálu změn.

## <a name="components-of-the-change-feed-processor"></a>Součásti procesoru změny kanálu

Implementace procesoru kanálu změn zahrnuje čtyři hlavní komponenty:

1. **Monitorovaný kontejner:** Monitorovaný kontejner obsahuje data, ze kterých se kanál změn generuje. Jakékoli vložení nebo aktualizace v monitorovaném kontejneru se projeví v kanálu změn kontejneru.

1. **Kontejner zapůjčení:** Kontejner zapůjčení funguje jako úložiště stavu a koordinuje zpracování kanálu změn mezi několika pracovními procesy. Kontejner zapůjčení může být uložený ve stejném účtu jako monitorovaný kontejner nebo v samostatném účtu.

1. **Hostitel:** Hostitel je instance aplikace, která pomocí procesoru kanálu změn naslouchá změnám. Paralelně může být spuštěných více instancí se stejnou konfigurací zapůjčení, ale každá instance musí mít jiný **název instance**.

1. **Delegát:** Delegát je kód, který definuje, co jako vývojář chcete udělat s jednotlivými dávkami změn, které procesor kanálu změn načte. 

Abychom lépe porozuměli tomu, jak tyto čtyři prvky procesoru Change feed fungují společně, Podívejme se na příklad v následujícím diagramu. Monitorovaný kontejner ukládá dokumenty a používá jako klíč oddílu "City". Zjistili jsme, že hodnoty klíčů oddílu jsou distribuované v oblastech, které obsahují položky. Existují dvě instance hostitele a procesor změn kanálu přiřazuje každé instanci různé rozsahy hodnot klíče oddílu, aby bylo možné maximalizovat výpočetní rozdělení. Každý rozsah je čten paralelně a jeho průběh se udržuje odděleně od jiných rozsahů v kontejneru zapůjčení.

:::image type="content" source="./media/change-feed-processor/changefeedprocessor.png" alt-text="Ukázka změny procesoru kanálu" border="false":::

## <a name="implementing-the-change-feed-processor"></a>Implementace procesoru změny kanálu

Bod vstupu je vždy monitorovaný kontejner, z `Container` instance, kterou voláte `GetChangeFeedProcessorBuilder` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Kde první parametr je jedinečný název, který popisuje cíl tohoto procesoru a druhý název je implementace delegáta, která bude zpracovávat změny. 

Příkladem delegáta může být:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Nakonec definujete název této instance procesoru s a, `WithInstanceName` který je kontejnerem pro udržení stavu zapůjčení `WithLeaseContainer` .

Volání vám `Build` poskytne instanci procesoru, kterou můžete spustit voláním `StartAsync` .

## <a name="processing-life-cycle"></a>Životní cyklus zpracování

Normální životní cyklus instance hostitele je následující:

1. Přečtěte si kanál změn.
1. Pokud nedošlo k žádným změnám, přejdete do režimu spánku v předdefinovaném čase (dá se přizpůsobit `WithPollInterval` v Tvůrci) a přejdete na #1.
1. Pokud dojde ke změnám, odešlete je **delegátovi**.
1. Když delegát dokončí zpracování změn **úspěšně**, aktualizujte úložiště zapůjčení s nejnovějším zpracovávaným bodem v čase a přejděte na #1.

## <a name="error-handling"></a>Zpracování chyb

Procesor změn kanálu je odolný proti chybám uživatelského kódu. To znamená, že pokud vaše implementace delegáta má neošetřenou výjimku (krok #4), zpracování vlákna, které konkrétní dávku změn dojde, se zastaví a vytvoří se nové vlákno. Nové vlákno zkontroluje, který byl nejnovějším bodem v čase, který má úložiště zapůjčení pro daný rozsah hodnot klíče oddílu, a pak se z něj restartuje a efektivně posílá stejnou dávku změn delegáta. Toto chování bude pokračovat, dokud váš delegát správně nezpracuje změny a jedná se o důvod, proč má procesor změn "alespoň jednou" jistotu, protože pokud kód delegáta vyvolá výjimku, bude opakovat tuto dávku.

Chcete-li zabránit tomu, aby procesor změn v kanálu se neustále znovu pokusil o stejnou dávku změn, měli byste do kódu delegáta přidat logiku pro zápis dokumentů na frontu nedoručených zpráv. Tento návrh zajišťuje, že budete moci sledovat nezpracované změny a pořád nadále zpracovávat budoucí změny. Fronta nedoručených zpráv může být jiný kontejner Cosmos. Přesné úložiště dat nezáleží na tom, že nezpracované změny jsou trvalé.

Kromě toho můžete pomocí [estimatoru Change feed](how-to-use-change-feed-estimator.md) monitorovat průběh instancí procesoru změn kanálu při čtení kanálu změn. Tento odhad můžete použít k pochopení, zda je procesor změn v kanálu "zablokovaný" nebo zpožděný na základě dostupných prostředků, jako jsou například CPU, paměť a šířka pásma sítě.

## <a name="deployment-unit"></a>Jednotka nasazení

Jednotka nasazení s jedinou změnou kanálu se skládá z jedné nebo několika instancí se stejnou `processorName` konfigurací kontejneru zapůjčení. Můžete mít mnoho jednotek nasazení, kde má každý z nich jiný obchodní tok pro změny a každou jednotku nasazení, která se skládá z jedné nebo několika instancí. 

Například můžete mít jednu jednotku nasazení, která spustí externí rozhraní API, kdykoli dojde ke změně v kontejneru. Jiná jednotka nasazení může přesunout data v reálném čase, pokaždé, když dojde ke změně. Když dojde ke změně v monitorovaném kontejneru, všechny vaše jednotky nasazení budou dostávat oznámení.

## <a name="dynamic-scaling"></a>Dynamické škálování

Jak je uvedeno dříve, v rámci jednotky nasazení můžete mít jednu nebo více instancí. Aby bylo možné využít výhod výpočtů v rámci jednotky nasazení, jsou k dispozici pouze klíčové požadavky:

1. Všechny instance musí mít stejnou konfiguraci kontejneru zapůjčení.
1. Všechny instance by měly být stejné `processorName` .
1. Každá instance musí mít jiný název instance (`WithInstanceName`).

Pokud platí tyto tři podmínky, bude procesor změn s použitím stejného distribučního algoritmu distribuovat všechna zapůjčení do kontejneru zapůjčení ve všech spuštěných instancích této jednotky nasazení a paralelizovat Compute. Jednu zapůjčenou adresu může vlastnit jenom jedna instance v daném okamžiku, takže maximální počet instancí se rovná počtu zapůjčení.

Počet instancí se může zvětšovat a zmenšovat a procesor změn bude dynamicky upravovat zatížení tím, že je odpovídajícím způsobem distribuován.

Procesor změn kanálu se navíc může dynamicky upravovat na kontejnery škálované z důvodu zvýšení propustnosti nebo úložiště. Když se Váš kontejner rozroste, procesor Change feed transparentně tyto scénáře zpracovává tím, že dynamicky zvyšuje zapůjčení a distribuuje nové zapůjčení mezi stávajícími instancemi.

## <a name="change-feed-and-provisioned-throughput"></a>Změnit kanál a zřízenou propustnost

Operace čtení kanálu změny u monitorovaného kontejneru budou spotřebovávat ru. 

Operace na kontejneru zapůjčení využívají ru. Čím vyšší počet instancí používá stejný kontejner zapůjčení, tím vyšší bude možné využití s možností RU. Pokud se rozhodnete škálovat a zvyšovat počet instancí, nezapomeňte monitorovat spotřebu RU na kontejneru zapůjčení.

## <a name="starting-time"></a>Čas spuštění

Ve výchozím nastavení se při prvním spuštění procesoru Change feed inicializuje kontejner zapůjčení a spustí se jeho [životní cyklus zpracování](#processing-life-cycle). Všechny změny, ke kterým došlo v monitorovaném kontejneru předtím, než byl procesor pro změnu kanálu při prvním spuštění inicializován, nebudou zjišťovány.

### <a name="reading-from-a-previous-date-and-time"></a>Čtení z předchozího data a času

Je možné inicializovat procesor změn, aby bylo možné číst změny od **určitého data a času**, a to předáním instance do `DateTime` `WithStartTime` rozšíření tvůrce:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

Procesor změnového kanálu se inicializuje pro konkrétní datum a čas a začne číst změny, ke kterým došlo po.

### <a name="reading-from-the-beginning"></a>Čtení od začátku

V jiných scénářích, jako jsou migrace dat nebo analýza celé historie kontejneru, musíme načíst kanál změn od **začátku životnosti tohoto kontejneru**. K tomu můžeme použít `WithStartTime` rozšíření tvůrce, ale předáním `DateTime.MinValue.ToUniversalTime()` , což vygeneruje reprezentaci UTC minimální `DateTime` hodnoty, například:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

Procesor změny kanálu bude inicializován a začne číst změny od začátku životnosti kontejneru.

> [!NOTE]
> Tyto možnosti přizpůsobení fungují pouze při nastavení počátečního bodu v čase procesoru změny kanálu. Po prvním spuštění kontejneru zapůjčení, změna nemá žádný vliv.

## <a name="where-to-host-the-change-feed-processor"></a>Místo hostování procesoru změny kanálu

Procesor změnového kanálu lze hostovat na libovolné platformě, která podporuje dlouho běžící procesy nebo úlohy:

* Nepřetržitě běžící [Webová úloha Azure](/learn/modules/run-web-app-background-task-with-webjobs/)
* Proces na [virtuálním počítači Azure](/azure/architecture/best-practices/background-jobs#azure-virtual-machines).
* Úloha na pozadí ve [službě Azure Kubernetes](/azure/architecture/best-practices/background-jobs#azure-kubernetes-service).
* [Hostovaná služba ASP.NET](/aspnet/core/fundamentals/host/hosted-services)

I když se procesor Change feed může spouštět v krátkodobých prostředích, protože kontejner zapůjčení udržuje stav, cyklus spuštění těchto prostředí přidá zpoždění pro příjem oznámení (kvůli režii při spouštění procesoru při každém spuštění prostředí).

## <a name="additional-resources"></a>Další materiály

* [Sada Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Kompletní ukázková aplikace na GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)
* [Další ukázky použití na GitHubu](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Cosmos DB Workshop Labs pro procesor změn kanálu](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html#consume-cosmos-db-change-feed-via-the-change-feed-processor)

## <a name="next-steps"></a>Další kroky

Teď můžete pokračovat a získat další informace o procesoru Change feed v následujících článcích:

* [Přehled kanálu změn](change-feed.md)
* [Model vyžádání kanálu změn](change-feed-pull-model.md)
* [Postup migrace z knihovny Change feed Processor](how-to-migrate-from-change-feed-library.md)
* [Použití estimátoru pro kanálu změn](how-to-use-change-feed-estimator.md)
* [Počáteční čas procesoru kanálu změn](#starting-time)