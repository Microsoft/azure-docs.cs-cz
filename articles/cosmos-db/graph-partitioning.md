---
title: Dělení ve službě Azure Cosmos DB Gremlin API
description: Zjistěte, jak můžete graf oddílů ve službě Azure Cosmos DB.
services: cosmos-db
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 27b0d9d7ca22ba346dbc288020f704dc7d27aa6a
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837190"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Použití dělené grafu ve službě Azure Cosmos DB

Jednou z klíčových funkcí rozhraní Gremlin API ve službě Azure Cosmos DB je schopnost zpracování rozsáhlých grafů prostřednictvím vodorovné škálovatelnost. Tento proces můžete dosáhnout [rozdělení funkcí ve službě Azure Cosmos DB](partition-data.md), ujistěte se, která pomocí kontejnerů, které je možné nezávisle škálovat z hlediska úložiště a propustnosti. Azure Cosmos DB podporuje následující typy kontejnerů na všechna rozhraní API:

- **Pevný kontejner**: těchto kontejnerů můžete uložit graf databáze až 10 GB velikosti s délkou maximálně 10 000 jednotek žádostí za sekundu přiřazen. Vytvořte kontejner pevnou není nutné zadat vlastnost klíče oddílu v datech.

- **Neomezený kontejner**: těchto kontejnerů může automaticky škálovat na uložení grafu nad limit 10 GB až horizontální dělení. Každý oddíl uloží 10 GB a data budou automaticky vyváženy podle **klíč zadaný oddíl**, které budou požadovaný parametr při používání neomezeného kontejneru. Tento typ kontejneru můžete uložit data prakticky neomezené velikosti a můžete povolit až 100 000 jednotek žádostí za sekundu, nebo více [kontaktovat podporu](https://aka.ms/cosmosdbfeedback?subject=Cosmos%20DB%20More%20Throughput%20Request).

V tomto dokumentu specifika na jak dělí databáze grafů najdete společně s jeho důsledky pro vrcholy (i uzly) a hrany.

## <a name="requirements-for-partitioned-graph"></a>Požadavky pro dělené grafu

Toto jsou podrobnosti, které je třeba porozumět při vytváření oddílů grafu kontejneru:

- **Nastavení dělení bude nutné** Pokud kontejner má mít více než 10 GB a/nebo pokud přidělení více než 10 000 jednotek žádostí za sekundu (RU/s) se bude vyžadovat.

- **Vrcholů a hran jsou ukládány jako dokumenty JSON** v back endu kontejneru Gremlin API služby Azure Cosmos DB.

- **Klíč oddílu vyžadovat vrcholy**. Tento klíč určí, do kterého oddílu se uloží vrchol pomocí algoritmu hash. Název tohoto klíče oddílu je řetězec jednoslovnou bez mezery ani speciální znaky a je definován při vytváření nového kontejneru pomocí formátu `/partitioning-key-name` na portálu.

- **Okraje se uloží s jejich zdrojový vrchol**. Jinými slovy pro každý vrchol svůj klíč oddílu budou definovat kam se má uložit spolu s jeho odchozí okraje. To se provádí při použití, aby dotazy napříč oddíly `out()` kardinalitu v dotazy na grafy.

- **Dotazy na grafy musí určovat klíč oddílu**. Pokud chcete využívat všech výhod horizontální dělení ve službě Azure Cosmos DB, musí být zadaný klíč oddílu, když je vybraný jeden vrchol, kdykoli to bude možné. Tady jsou dotazy pro výběr jednoho nebo více vrcholy v dělené grafu:

    - `/id` a `/label` nejsou podporovány jako klíče oddílu pro kontejner ve službě rozhraní Gremlin API...


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

Toto jsou pokyny, které byste měli dodržet, aby nejúčinnější výkon a škálovatelnost při používání oddílů grafů v neomezené kontejnery:

- **Vždycky zadat hodnotu klíče oddílu při dotazování na vrcholu**. Získávání vrcholu ze známých oddílu je nejúčinnější způsob, jak z hlediska výkonu.

- **Při dotazování hrany, kdykoli to bude možné použít odchozí směr**. Jak je uvedeno výše, okraje ukládají s jejich zdroj vrcholy v odchozím směru. To znamená, že při dat a dotazy jsou navržené v tomto modelu v paměti, minimalizuje se pravděpodobnost nutnosti uchýlit se k dotazy napříč oddíly.

- **Zvolit klíč oddílu, který bude rovnoměrně distribuovat data napříč oddíly**. Toto rozhodnutí silně závisí na datovém modelu řešení. Další informace o vytváření klíčem odpovídající typ oddílu v [dělení a škálování ve službě Azure Cosmos DB](partition-data.md).

- **Optimalizace dotazů k získání dat v rámci hranic oddílů, pokud je to možné**. Optimální strategie dělení by mělo být zarovnáno na dotazování vzory. Dotazy, které získávají data z jednoho oddílu poskytují nejlepší možný výkon.

## <a name="next-steps"></a>Další postup
V tomto článku se poskytla Přehled konceptů a osvědčené postupy pro dělení s Gremlin API Azure Cosmos DB. 

* Další informace o [dělení a škálování ve službě Azure Cosmos DB](partition-data.md).
* Další informace o [podpora Gremlin v rozhraní Gremlin API](gremlin-support.md).
* Další informace o [Úvod do Gremlin API](graph-introduction.md).
