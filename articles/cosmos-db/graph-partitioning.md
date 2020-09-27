---
title: Dělení dat v rozhraní Azure Cosmos DB Gremlin API
description: Naučte se, jak můžete použít rozdělený graf v Azure Cosmos DB. Tento článek také popisuje požadavky a osvědčené postupy pro oddílový graf.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 6a993779bc47f1a9b2be8851fafe628ae4286f4a
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400498"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Použití děleného grafu ve službě Azure Cosmos DB

Jednou z klíčových funkcí rozhraní Gremlin API v Azure Cosmos DB je schopnost zpracovávat rozsáhlá měřítka prostřednictvím horizontálního škálování. Kontejnery se můžou nezávisle škálovat z hlediska úložiště a propustnosti. V Azure Cosmos DB můžete vytvořit kontejnery, které se dají automaticky škálovat, aby se ukládaly data grafu. Data se automaticky vyrovnávají podle zadaného **klíče oddílu**.

**Dělení je vyžadováno** , pokud se očekává, že velikost kontejneru ukládá více než 20 GB nebo pokud chcete přidělit více než 10 000 jednotek žádostí za sekundu (ru). Stejné obecné principy [mechanismu dělení Azure Cosmos DB](partition-data.md) platí pro několik optimalizací specifických pro graf, které jsou popsané níže.

:::image type="content" source="./media/graph-partitioning/graph-partitioning.png" alt-text="Dělení grafů." border="false":::

## <a name="graph-partitioning-mechanism"></a>Mechanizmus dělení grafů

Následující pokyny popisují, jak strategie dělení v Azure Cosmos DB funguje:

- **Vrcholy i hrany se ukládají jako dokumenty JSON**.

- **Vrcholy vyžadují klíč oddílu**. Tento klíč určí, v jakém oddílu bude vrchol uložený pomocí algoritmu hash. Název vlastnosti klíče oddílu je definován při vytváření nového kontejneru a má formát: `/partitioning-key-name` .

- **Okraje budou uloženy spolu s jejich zdrojovými vrcholy**. Jinými slovy, pro každý vrchol svůj klíč oddílu definuje, kde jsou uloženy spolu s odchozími hranami. Tato optimalizace se zabrání v dotazech mezi oddíly při použití `out()` mohutnosti v dotazech grafu.

- **Okraje obsahují odkazy na vrcholy, na které odkazují**. Všechny hrany jsou uloženy s klíči oddílu a identifikátory vrcholů, na které odkazují. Tento výpočet zpřístupňuje všechny `out()` směrové dotazy v oboru, který není v oboru, a ne na neslepém dotazu mezi oddíly.

- **Dotazy grafů musí zadat klíč oddílu**. Pokud chcete plně využít horizontální dělení v Azure Cosmos DB, klíč oddílu by měl být zadaný při výběru jednoho vrcholu, kdykoli je to možné. Níže jsou uvedené dotazy pro výběr jednoho nebo více vrcholů v rozděleném grafu:

    - `/id` a `/label` nejsou podporované jako klíče oddílů pro kontejner v rozhraní Gremlin API.


    - Výběr vrcholu podle ID a následné ** `.has()` zadání vlastnosti klíče oddílu pomocí kroku**:

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

## <a name="best-practices-when-using-a-partitioned-graph"></a>Osvědčené postupy při použití rozděleného grafu

Pro zajištění výkonu a škálovatelnosti při použití grafů s neomezenými kontejnery použijte následující pokyny:

- **Při dotazování vrcholu vždy zadat hodnotu klíče oddílu**. Získání vrcholu ze známého oddílu je způsob, jak dosáhnout výkonu. Všechny následné operace souseda budou vždy vymezeny na oddíl, protože hrany obsahují ID odkazu a klíč oddílu na své cílové vrcholy.

- **Při dotazování na hrany používejte odchozí směr, kdykoli je to možné**. Jak je uvedeno výše, hrany se ukládají se zdrojovými vrcholy v odchozím směru. Takže se při navrhování dat a dotazů s tímto vzorem minimalizuje možnost vytváření dotazů mezi oddíly. V opačném případě `in()` bude dotaz vždy nákladným dotazem na více instancí.

- **Vyberte klíč oddílu, který bude rovnoměrně distribuovat data mezi oddíly**. Toto rozhodnutí je silně závislé na datovém modelu řešení. Přečtěte si další informace o vytvoření vhodného klíče oddílu v tématu [dělení a škálování v Azure Cosmos DB](partition-data.md).

- **Optimalizuje dotazy pro získání dat v rámci hranic oddílu**. Optimální strategie dělení by byla zarovnaná na vzory dotazů. Dotazy, které získávají data z jednoho oddílu, poskytují nejlepší možný výkon.

## <a name="next-steps"></a>Další kroky

Dál si můžete přečíst následující články:

* Přečtěte si o [oddílu a škálování v Azure Cosmos DB](partition-data.md).
* Přečtěte si o [podpoře Gremlin v rozhraní Gremlin API](gremlin-support.md).
* Přečtěte si o [úvodu k rozhraní Gremlin API](graph-introduction.md).
