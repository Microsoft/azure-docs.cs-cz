---
title: Vytváření oddílů rozhraní Graph API | Microsoft Docs
description: Zjistěte, jak můžete graf oddílů v Azure Cosmos DB.
services: cosmos-db
author: luisbosquez
documentationcenter: ''
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/28/2018
ms.author: lbosq
ms.openlocfilehash: db41efeee467d2cda89f62e0a18cf89cec2d9e63
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Pomocí oddílů grafu v Azure Cosmos DB

Jedním z klíčových funkcích služby rozhraní Graph API v Azure Cosmos DB je schopnost zpracovávat rozsáhlé grafy prostřednictvím vodorovné škálovatelnost. Tento proces je dosaženo pomocí [dělení možnosti v Azure Cosmos DB](partition-data.md#how-does-partitioning-work), ujistěte se, které využívají kolekcí, které jsou také označovány jako kontejnery, které je možné škálovat nezávisle z hlediska úložiště a propustnosti. Azure Cosmos DB podporuje následující typy kontejnery mezi všechna rozhraní API:

- **Pevné kolekce**: těchto kolekcí můžete uložit graf databáze velikost maximálně 10 000 jednotek žádosti za sekundu přidělených až 10 GB. Chcete-li vytvořit kolekci pevné není nutné zadat vlastnost klíče oddílu v datech.

- **Neomezená kolekce**: těchto kolekcí může automaticky škálovat pro uložení grafu překračuje limit 10 GB prostřednictvím vodorovné rozdělení do oddílů. Každý oddíl uloží 10 GB a data budou automaticky rovnoměrně rozdělen na základě **klíč zadaný oddíl**, která bude požadovaný parametr při použití neomezená kolekce. Tento typ kontejneru může ukládat data prakticky neomezené velikosti a můžete povolit až 100 000 jednotek žádosti za sekundu, nebo více [kontaktováním podpory](https://aka.ms/cosmosdbfeedback?subject=Cosmos%20DB%20More%20Throughput%20Request).

V tomto dokumentu jsou specifikace na tom, jak jsou databáze grafu do několika oddílů najdete společně s jeho důsledků pro obě vrcholy (nebo uzlech) a okraje.

## <a name="requirements-for-partitioned-graph"></a>Požadavky pro oddílů grafu

Zde jsou informace, které je třeba rozumět při vytváření oddílů grafu kontejneru:
- **Nastavení dělení bude nutné** Pokud kolekce musí být větší než 10 GB, velikost, nebo pokud přidělení více než 10 000 jednotek žádosti za sekundu (RU/s) se bude vyžadovat.
- **Vrcholy i okraje jsou ukládány jako dokumenty JSON** v back-end kontejner Azure Cosmos DB Graph API.
- **Vrcholy vyžadují klíč oddílu**. Tento klíč určí v oddíl, který se uloží vrchol prostřednictvím algoritmu hash. Název tohoto klíče oddílu je řetězec jednoslovného výrazu bez mezery nebo speciální znaky a je definován při vytváření nové kolekce formátu `/partitioning-key-name` na portálu.
- **Okraje se uloží s jejich zdroje vrchol**. Jinými slovy u jednotlivých vrchol svůj klíč oddílu bude definovat, kde bude uložen spolu s jeho odchozí okraje. Tím se vyhnout dotazy mezi oddílu při použití `out()` kardinalitu v dotazech grafu.
- **Dotazy grafu je nutné zadat klíč oddílu**. Aby naplno využít vodorovné oddíly v Azure Cosmos DB, musí se zadat klíč oddílu, pokud je vybraný jeden vrchol, kdykoli to bude možné. Tady jsou dotazy pro výběr jednoho nebo více vrcholy v oddílů grafu:

    - Výběr vrchol podle ID, potom **pomocí `.has()` krok k určení vlastnost klíče oddílu**: 
    
        ```
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Výběr vrchol podle **zadání řazené kolekce členů včetně hodnotu klíče oddílu a ID**: 
    
        ```
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Určení **pole řazené kolekce členů hodnoty klíče oddílu a ID**:
    
        ```
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Vyberte sadu vrcholy a **zadat seznam hodnot klíče oddílu**: 
    
        ```
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Osvědčené postupy při použití oddílů grafu.

Toto jsou pokyny, které musí být sledována zajistit nejúčinnější výkon a škálovatelnost, při použití oddílů grafy v neomezená kolekce:
- **Při dotazování vrchol vždycky zadat hodnotu klíče oddílu**. Získání vrchol z známé oddílu je nejúčinnější způsob, jak z hlediska výkonu.
- **Při dotazování okraje vždy, když je možné použít odchozí směr**. Jak je uvedeno nahoře, jsou uloženy okraje s jejich vrcholy zdroje v odchozích směru. To znamená, že když data a dotazy jsou navrženy s tento vzor pamatovat, minimalizuje se pravděpodobnost opětné řazení do cross oddílu dotazy.
- **Vyberte klíč oddílu, který rovnoměrně rozdělit data napříč oddíly**. Toto rozhodnutí výraznou závisí na datový model řešení. Další informace o vytváření klíčem odpovídající oddíl v [Partitining a škálování v Azure Cosmos DB](partition-data.md).
- **Optimalizace dotazů k získání dat v rámci hranice oddílu, pokud je to možné**. Strategie dělení optimální by zarovnán k dotazování modelům. Dotazy, které získávají data z jednoho oddílu poskytují nejlepší možný výkon.

## <a name="next-steps"></a>Další postup
V tomto článku se poskytovaly přehled o konceptech a osvědčené postupy pro vytváření oddílů s Cosmos Azure DB Graph API. 

* Další informace o [oddílu a škálování v Azure Cosmos DB](partition-data.md).
* Další informace o [Gremlin podpora v rozhraní Graph API](gremlin-support.md).
* Další informace o [Úvod do rozhraní Graph API](graph-introduction.md).
