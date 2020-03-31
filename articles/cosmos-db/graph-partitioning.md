---
title: Dělení dat v rozhraní AZURE Cosmos DB Gremlin API
description: Zjistěte, jak můžete použít rozdělený graf v Azure Cosmos DB. Tento článek také popisuje požadavky a osvědčené postupy pro dělený graf.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 44d3b7c2b9e23b90f696162747d9728b18fb7d3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623370"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Použití děleného grafu ve službě Azure Cosmos DB

Jednou z klíčových funkcí rozhraní Gremlin API v Azure Cosmos DB je schopnost zpracovávat rozsáhlé grafy prostřednictvím horizontálníškálování. Kontejnery lze škálovat nezávisle z hlediska úložiště a propustnost. Můžete vytvořit kontejnery v Azure Cosmos DB, které lze automaticky škálovat pro ukládání dat grafu. Data jsou automaticky vyvážena na základě zadaného **klíče oddílu**.

**Dělení je vyžadováno,** pokud se očekává, že kontejner uloží více než 20 GB nebo pokud chcete přidělit více než 10 000 jednotek požadavků za sekundu (RU). Stejné obecné zásady z [mechanismu oddílů Azure Cosmos DB](partition-data.md) platí s několika optimalizacemi specifickými pro graf popsaných níže.

![Dělení grafu.](./media/graph-partitioning/graph-partitioning.png)

## <a name="graph-partitioning-mechanism"></a>Mechanismus dělení grafů

Následující pokyny popisují, jak funguje strategie dělení v Azure Cosmos DB:

- **Oba vrcholy a hrany jsou uloženy jako dokumenty JSON**.

- **Vrcholy vyžadují klíč oddílu**. Tento klíč určí, ve kterém oddílu bude vrchol uložen prostřednictvím algoritmu hash. Název vlastnosti klíče oddílu je definován při vytváření `/partitioning-key-name`nového kontejneru a má formát: .

- **Hrany budou uloženy s jejich zdrojovým vrcholem**. Jinými slovy, pro každý vrchol jeho klíč oddílu definuje, kde jsou uloženy spolu s jeho odchozí hrany. Tato optimalizace se provádí, aby se `out()` zabránilo dotazy mezi oddíly při použití mohutnost v grafech.

- **Hrany obsahují odkazy na vrcholy, na které odkazují**. Všechny hrany jsou uloženy s klíči oddílu a ID vrcholů, na které směřují. Tento výpočetní provede `out()` všechny směrové dotazy vždy rozdělený dotaz s vymezeným oborem a nikoli slepý dotaz mezi oddíly. 

- **Dotazy na graf je třeba zadat klíč oddílu**. Chcete-li plně využít horizontální dělení v Azure Cosmos DB, klíč oddílu by měl být určen při výběru jednoho vrcholu, kdykoli je to možné. Následují dotazy pro výběr jednoho nebo více vrcholů v dělený graf:

    - `/id`a `/label` nejsou podporovány jako klíče oddílu pro kontejner v rozhraní GREMLIN API.


    - Výběr vrcholu podle ID a **potom `.has()` pomocí kroku určit vlastnost klíče oddílu**: 
    
        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Výběr vrcholu **zadáním řazené kolekce členů včetně hodnoty klíče oddílu a ID**: 
    
        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Určení **pole řazených kolekcí členů hodnot klíče oddílu a ID**klíče oddílu :
    
        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Výběr sady vrcholů s jejich ID a **určení seznamu hodnot klíče oddílu**: 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Použití **strategie oddílu** na začátku dotazu a určení oddílu pro rozsah zbytku dotazu Gremlin: 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Doporučené postupy při použití rozděleného grafu

Pomocí následujících pokynů můžete zajistit výkon a škálovatelnost při použití dělených grafů s neomezeným počtem kontejnerů:

- **Při dotazování na vrchol vždy zadejte hodnotu klíče oddílu**. Získání vrcholu ze známého oddílu je způsob, jak dosáhnout výkonu. Všechny následné operace sousedství budou vždy vymezeny na oddíl, protože hrany obsahují ID odkazu a klíč oddílu k cílovým vrcholům.

- **Odchozí směr použijte při dotazování hran, kdykoli je to možné**. Jak bylo uvedeno výše, hrany jsou uloženy s jejich zdrojovými vrcholy v odchozím směru. Takže šance na uchýlení se k dotazům mezi oddíly jsou minimalizovány při data a dotazy jsou navrženy s ohledem na tento vzor. Naopak dotaz `in()` bude vždy nákladný množení dotaz.

- **Zvolte klíč oddílu, který bude rovnoměrně distribuovat data mezi oddíly**. Toto rozhodnutí do značné míry závisí na datovém modelu řešení. Přečtěte si další informace o vytvoření vhodného klíče oddílu v [oddílování a škálování v Azure Cosmos DB](partition-data.md).

- **Optimalizujte dotazy pro získání dat v rámci hranic oddílu**. Optimální dělení strategie by být zarovnány s dotazování vzory. Dotazy, které získávají data z jednoho oddílu poskytují nejlepší možný výkon.

## <a name="next-steps"></a>Další kroky

Dále můžete pokračovat v čtení následujících článků:

* Další informace o [oddílu a škálování v Azure Cosmos DB](partition-data.md).
* Další informace o [podpoře Gremlin v Gremlin API](gremlin-support.md).
* Další informace o [úvodu do gremlin API](graph-introduction.md).
