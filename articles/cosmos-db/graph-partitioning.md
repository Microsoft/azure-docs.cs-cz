---
title: Dělení dat v Azure Cosmos DB Gremlin API
description: Zjistěte, jak můžete graf oddílů ve službě Azure Cosmos DB. Tento článek také popisuje požadavky a osvědčené postupy pro dělené grafu.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 44d3b7c2b9e23b90f696162747d9728b18fb7d3f
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623370"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Použití dělené grafu ve službě Azure Cosmos DB

Jednou z klíčových funkcí rozhraní Gremlin API ve službě Azure Cosmos DB je možnost pro zpracování rozsáhlých grafů prostřednictvím horizontální škálování. Kontejnery můžete nezávisle škálovat z hlediska úložiště a propustnost. Vytvoření kontejnerů ve službě Azure Cosmos DB, který je možné automaticky škálovat pro ukládání dat grafu. Data se automaticky vyrovnávají podle zadaného **klíče oddílu**.

**Dělení je vyžadováno** , pokud se očekává, že velikost kontejneru ukládá více než 20 GB nebo pokud chcete přidělit více než 10 000 jednotek žádostí za sekundu (ru). Stejné obecné principy [mechanismu dělení Azure Cosmos DB](partition-data.md) platí pro několik optimalizací specifických pro graf, které jsou popsané níže.

![Dělení grafů.](./media/graph-partitioning/graph-partitioning.png)

## <a name="graph-partitioning-mechanism"></a>Mechanizmus dělení grafů

Následující pokyny popisují, jak strategie dělení v Azure Cosmos DB funguje:

- **Vrcholy i hrany se ukládají jako dokumenty JSON**.

- **Vrcholy vyžadují klíč oddílu**. Tento klíč určí, do kterého oddílu se uloží vrchol pomocí algoritmu hash. Název vlastnosti klíče oddílu je definován při vytváření nového kontejneru a má formát: `/partitioning-key-name`.

- **Okraje budou uloženy spolu s jejich zdrojovými vrcholy**. Jinými slovy pro každý vrchol svůj klíč oddílu definuje kde se ukládají společně s jeho odchozí okraje. Tato optimalizace se zabrání v dotazech mezi oddíly při použití `out()` mohutnosti v dotazech grafu.

- **Okraje obsahují odkazy na vrcholy, na které odkazují**. Všechny hrany jsou uloženy s klíči oddílu a identifikátory vrcholů, na které odkazují. Tento výpočet zpřístupňuje všechny `out()` směrové dotazy vždy oborový dotaz s vymezeným oborem, nikoli neslepý dotaz na více oddílů. 

- **Dotazy grafů musí zadat klíč oddílu**. Pokud chcete využívat všech výhod horizontální dělení ve službě Azure Cosmos DB, musí být zadaný klíč oddílu, když je vybraný jeden vrchol, kdykoli to bude možné. Tady jsou dotazy pro výběr jednoho nebo více vrcholy v dělené grafu:

    - `/id` a `/label` se nepodporují jako klíče oddílů pro kontejner v rozhraní Gremlin API.


    - Vyberte vrchol podle ID a pak **pomocí `.has()` kroku zadejte vlastnost klíče oddílu**: 
    
        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Výběr vrcholu **zadáním řazené kolekce členů, včetně hodnoty klíče oddílu a ID**: 
    
        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Zadání **pole n-tice hodnot klíčů oddílu a ID**:
    
        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Výběr sady vrcholů s jejich ID a **Určení seznamu hodnot klíčů oddílu**: 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Použití **strategie oddílu** na začátku dotazu a určení oddílu pro rozsah zbytku dotazu Gremlin: 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Osvědčené postupy při používání oddílů grafu

K zajištění výkonu a škálovatelnosti při používání oddílů grafů s neomezené kontejnery pomocí následujících pokynů:

- **Při dotazování vrcholu vždy zadat hodnotu klíče oddílu**. Načítání vrcholu ze známých oddílu je způsob, jak dosáhnout výkonu. Všechny následné operace souseda budou vždy vymezeny na oddíl, protože hrany obsahují ID odkazu a klíč oddílu na své cílové vrcholy.

- **Při dotazování na hrany používejte odchozí směr, kdykoli je to možné**. Jak je uvedeno výše, okraje ukládají s jejich zdroj vrcholy v odchozím směru. Při dat a dotazy jsou navržené v tomto modelu v paměti jsou proto minimalizovány pravděpodobnost nutnosti uchýlit se k dotazy napříč oddíly. V opačném případě bude `in()` dotaz vždy nákladným dotazem na více instancí.

- **Vyberte klíč oddílu, který bude rovnoměrně distribuovat data mezi oddíly**. Toto rozhodnutí silně závisí na datovém modelu řešení. Přečtěte si další informace o vytvoření vhodného klíče oddílu v tématu [dělení a škálování v Azure Cosmos DB](partition-data.md).

- **Optimalizuje dotazy pro získání dat v rámci hranic oddílu**. Optimální strategie dělení by mělo být zarovnáno na dotazování vzory. Dotazy, které získávají data z jednoho oddílu poskytují nejlepší možný výkon.

## <a name="next-steps"></a>Další kroky

Potom můžete pokračovat v následujících článcích:

* Přečtěte si o [oddílu a škálování v Azure Cosmos DB](partition-data.md).
* Přečtěte si o [podpoře Gremlin v rozhraní Gremlin API](gremlin-support.md).
* Přečtěte si o [úvodu k rozhraní Gremlin API](graph-introduction.md).
