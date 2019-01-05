---
title: Dělení dat v Azure Cosmos DB Gremlin API
description: Zjistěte, jak můžete graf oddílů ve službě Azure Cosmos DB. Tento článek také popisuje požadavky a osvědčené postupy pro dělené grafu.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f1e486a302b440d819e15ef86f8d76ea5e50d201
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036320"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Použití dělené grafu ve službě Azure Cosmos DB

Jednou z klíčových funkcí rozhraní Gremlin API ve službě Azure Cosmos DB je možnost pro zpracování rozsáhlých grafů prostřednictvím horizontální škálování. Horizontální škálování můžete dosáhnout [rozdělení funkcí ve službě Azure Cosmos DB](partition-data.md). Kontejnery můžete nezávisle škálovat z hlediska úložiště a propustnost. Vytvoření kontejnerů ve službě Azure Cosmos DB, který je možné automaticky škálovat pro ukládání dat grafu. Data je automaticky rovnoměrně rozdělen na základě stanoveného **klíč oddílu**.

V tomto dokumentu specifika na jak dělí databáze grafů najdete společně s jeho důsledky pro vrcholy (i uzly) a hrany.

## <a name="requirements-for-partitioned-graph"></a>Požadavky pro dělené grafu

Toto jsou podrobnosti, které je třeba porozumět při vytváření oddílů grafu kontejneru:

- **Dělení se vyžaduje** Pokud se očekává ukládání více než 10 GB velikosti kontejneru, nebo pokud chcete přidělit víc než 10 000 jednotek žádostí za sekundu (ru).

- **Vrcholů a hran jsou ukládány jako dokumenty JSON**.

- **Klíč oddílu vyžadovat vrcholy**. Tento klíč určí, do kterého oddílu se uloží vrchol pomocí algoritmu hash. Název tohoto klíče oddílu je řetězec jednoslovnou bez mezery ani speciální znaky. Klíč oddílu je definován při vytváření nového kontejneru a má formát: `/partitioning-key-name`.

- **Okraje se uloží s jejich zdrojový vrchol**. Jinými slovy pro každý vrchol svůj klíč oddílu definuje kde se ukládají společně s jeho odchozí okraje. To se provádí při použití, aby dotazy napříč oddíly `out()` kardinalitu v dotazy na grafy.

- **Dotazy na grafy musí určovat klíč oddílu**. Pokud chcete využívat všech výhod horizontální dělení ve službě Azure Cosmos DB, musí být zadaný klíč oddílu, když je vybraný jeden vrchol, kdykoli to bude možné. Tady jsou dotazy pro výběr jednoho nebo více vrcholy v dělené grafu:

    - `/id` a `/label` nejsou podporovány jako klíče oddílu pro kontejner ve službě Gremlin API nevztahují.


    - Výběr vrcholů podle ID, potom **pomocí `.has()` krok pro určení této vlastnosti klíče oddílu**: 
    
        ```
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Výběr vrcholů podle **zadání řazené kolekce členů, včetně hodnoty klíče oddílu a ID**: 
    
        ```
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Určení **pole řazených kolekcí členů hodnoty klíče oddílu a identifikátorů**:
    
        ```
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Vyberte sadu vrcholů a **určení seznamu hodnot klíče oddílu**: 
    
        ```
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Osvědčené postupy při používání oddílů grafu

K zajištění výkonu a škálovatelnosti při používání oddílů grafů s neomezené kontejnery pomocí následujících pokynů:

- **Vždycky zadat hodnotu klíče oddílu při dotazování na vrcholu**. Načítání vrcholu ze známých oddílu je způsob, jak dosáhnout výkonu.

- **Při dotazování hrany, kdykoli to bude možné použít odchozí směr**. Jak je uvedeno výše, okraje ukládají s jejich zdroj vrcholy v odchozím směru. Při dat a dotazy jsou navržené v tomto modelu v paměti jsou proto minimalizovány pravděpodobnost nutnosti uchýlit se k dotazy napříč oddíly.

- **Zvolit klíč oddílu, který bude rovnoměrně distribuovat data napříč oddíly**. Toto rozhodnutí silně závisí na datovém modelu řešení. Další informace o vytváření klíčem odpovídající typ oddílu v [dělení a škálování ve službě Azure Cosmos DB](partition-data.md).

- **Optimalizace dotazů k získání dat v rámci hranice oddílu**. Optimální strategie dělení by mělo být zarovnáno na dotazování vzory. Dotazy, které získávají data z jednoho oddílu poskytují nejlepší možný výkon.

## <a name="next-steps"></a>Další postup

Potom můžete pokračovat v následujících článcích:

* Další informace o [dělení a škálování ve službě Azure Cosmos DB](partition-data.md).
* Další informace o [podpora Gremlin v rozhraní Gremlin API](gremlin-support.md).
* Další informace o [Úvod do Gremlin API](graph-introduction.md).
