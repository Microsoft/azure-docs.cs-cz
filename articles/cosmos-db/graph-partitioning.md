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
ms.openlocfilehash: 4c8761d82c8a735ac9c4bff2e5ac0107b2a57fe0
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537544"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Použití dělené grafu ve službě Azure Cosmos DB

Jednou z klíčových funkcí rozhraní Gremlin API ve službě Azure Cosmos DB je možnost pro zpracování rozsáhlých grafů prostřednictvím horizontální škálování. Kontejnery můžete nezávisle škálovat z hlediska úložiště a propustnost. Vytvoření kontejnerů ve službě Azure Cosmos DB, který je možné automaticky škálovat pro ukládání dat grafu. Data je automaticky rovnoměrně rozdělen na základě stanoveného **klíč oddílu**.

**Dělení se vyžaduje** Pokud se očekává ukládání více než 10 GB velikosti kontejneru, nebo pokud chcete přidělit víc než 10 000 jednotek žádostí za sekundu (ru). Stejné obecné zásady z [dělení mechanismus služby Azure Cosmos DB](partition-data.md) použít s několika optimalizace konkrétní graf je popsáno níže.

![Dělení grafů.](./media/graph-partitioning/graph-partitioning.png)

## <a name="graph-partitioning-mechanism"></a>Graf mechanismus dělení

Následující pokyny popisují, jak funguje strategie dělení ve službě Azure Cosmos DB:

- **Vrcholů a hran jsou ukládány jako dokumenty JSON**.

- **Klíč oddílu vyžadovat vrcholy**. Tento klíč určí, do kterého oddílu se uloží vrchol pomocí algoritmu hash. Název vlastnosti klíče oddílu je definován při vytváření nového kontejneru a má formát: `/partitioning-key-name`.

- **Okraje se uloží s jejich zdrojový vrchol**. Jinými slovy pro každý vrchol svůj klíč oddílu definuje kde se ukládají společně s jeho odchozí okraje. Tato optimalizace se provádí při použití, aby dotazy napříč oddíly `out()` kardinalitu v dotazy na grafy.

- **Okraje obsahovat odkazy na vrcholy, které ukazují na**. Všechny hrany ukládají pomocí klíče oddílu a ID sady, které odkazují na vrcholy. Tento výpočet zpřístupňuje všechny `out()` směr dotazy vždy být s vymezeným oborem oddílů dotazu a není skrytá dotazu napříč oddíly. 

- **Dotazy na grafy musí určovat klíč oddílu**. Pokud chcete využívat všech výhod horizontální dělení ve službě Azure Cosmos DB, musí být zadaný klíč oddílu, když je vybraný jeden vrchol, kdykoli to bude možné. Tady jsou dotazy pro výběr jednoho nebo více vrcholy v dělené grafu:

    - `/id` a `/label` nejsou podporovány jako klíče oddílu pro kontejner ve službě Gremlin API nevztahují.


    - Výběr vrcholů podle ID, potom **pomocí `.has()` krok pro určení této vlastnosti klíče oddílu**: 
    
        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Výběr vrcholů podle **zadání řazené kolekce členů, včetně hodnoty klíče oddílu a ID**: 
    
        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Určení **pole řazených kolekcí členů hodnoty klíče oddílu a identifikátorů**:
    
        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Vyberte sadu vrcholy s jejich ID a **určení seznamu hodnot klíče oddílu**: 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Použití **oddílu strategie** na začátku dotazu a určení oddílu pro obor zbývající části dotazu Gremlin: 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Osvědčené postupy při používání oddílů grafu

K zajištění výkonu a škálovatelnosti při používání oddílů grafů s neomezené kontejnery pomocí následujících pokynů:

- **Vždycky zadat hodnotu klíče oddílu při dotazování na vrcholu**. Načítání vrcholu ze známých oddílu je způsob, jak dosáhnout výkonu. Všechny následné sousedství operace vždy budou omezené na oddíl od okraje obsahovat odkaz na ID a oddílu klíč pro jejich cílové vrcholy.

- **Při dotazování hrany, kdykoli to bude možné použít odchozí směr**. Jak je uvedeno výše, okraje ukládají s jejich zdroj vrcholy v odchozím směru. Při dat a dotazy jsou navržené v tomto modelu v paměti jsou proto minimalizovány pravděpodobnost nutnosti uchýlit se k dotazy napříč oddíly. Naopak `in()` dotaz bude vždy nákladné větveného dotazu.

- **Zvolit klíč oddílu, který bude rovnoměrně distribuovat data napříč oddíly**. Toto rozhodnutí silně závisí na datovém modelu řešení. Další informace o vytváření klíčem odpovídající typ oddílu v [dělení a škálování ve službě Azure Cosmos DB](partition-data.md).

- **Optimalizace dotazů k získání dat v rámci hranice oddílu**. Optimální strategie dělení by mělo být zarovnáno na dotazování vzory. Dotazy, které získávají data z jednoho oddílu poskytují nejlepší možný výkon.

## <a name="next-steps"></a>Další postup

Potom můžete pokračovat v následujících článcích:

* Další informace o [dělení a škálování ve službě Azure Cosmos DB](partition-data.md).
* Další informace o [podpora Gremlin v rozhraní Gremlin API](gremlin-support.md).
* Další informace o [Úvod do Gremlin API](graph-introduction.md).
