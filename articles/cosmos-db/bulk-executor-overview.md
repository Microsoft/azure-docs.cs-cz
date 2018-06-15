---
title: Přehled knihovny Azure Cosmos DB BulkExecutor | Microsoft Docs
description: Další informace o Azure Cosmos DB BulkExecutor knihovny výhody používání knihovny a jeho architektura.
keywords: Hromadné vykonavatele Java
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: 5f1987009d2277590e32136336340aa1b3be07fd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34610329"
---
# <a name="azure-cosmos-db-bulkexecutor-library-overview"></a>Přehled knihovny Azure Cosmos DB BulkExecutor
 
Azure Cosmos DB je rychlý, flexibilní a globálně distribuované databáze služba, která slouží k pružně horizontální navýšení kapacity pro podporu: 

* Velká čtení a zápisu propustnost (miliony operací za sekundu).  
* Ukládání velký objem (stovky terabajtů nebo dokonce i více) transakcí a provozní data s latencí předvídatelný milisekundu.  

Knihovna BulkExecutor umožňuje využít tuto obrovskou propustnost a úložiště, BulkExecutor knihovny lze provádět hromadné operace v Azure Cosmos DB pomocí hromadného importu a hromadné aktualizace rozhraní API. Další informace o funkcích BulkExecutor knihovny v následujících částech. 

> [!NOTE] 
> V současné době podporuje Bulkxecutor knihovny importu a operace aktualizace a tato knihovna je podporován pouze účty rozhraní API pro Azure Cosmos databáze SQL. V tématu [.NET](sql-api-sdk-bulk-executor-dot-net.md) a [Java](sql-api-sdk-bulk-executor-java.md) poznámky k verzi pro všechny aktualizace do knihovny.
 
## <a name="key-features-of-the-bulkexecutor-library"></a>Klíčové funkce BulkExecutor knihovny  
 
* Významně snižuje klienta výpočetní prostředky potřebné k saturate propustnost přidělené do kontejneru. Jednu zařazování aplikaci, která zapisuje data pomocí že rozhraní API pro hromadný import dosáhne 10 případech větší propustnost zápisu ve srovnání s vícevláknové aplikace, která zapisuje data souběžně při přetížení klienta procesoru počítače.  

* Abstrahuje rychle zdlouhavé úlohy zápisu aplikační logiku a zpracování omezení požadavků, překročení časového limitu požadavku a další přechodné výjimky efektivně je zpracování v knihovně.  

* Poskytuje zjednodušenou mechanismus pro aplikace, které provádí hromadné operace škálování. Jedna instance BulkExecutor systémem virtuálního počítače Azure můžete využívat větší než 500 tis. RU/s a přidáním další instance na jednotlivých klientských virtuálních počítačích můžete dosáhnout vyšší propustnost.  
 
* Import více než terabajt dat se v rámci hodiny hromadně pomocí architektury Škálováním na více systémů.  

* Aktualizace stávající data v Azure Cosmos DB kontejnery ho můžete hromadně jako opravy. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Jak se pracuje vykonavatele hromadně? 

Při hromadné operace importu nebo aktualizovat dokumenty aktivaci pomocí služby batch entit, budou se původně nesprávním místě do kbelíků odpovídající jejich rozsah klíče oddílu Azure Cosmos DB. V rámci každého bloku, který odpovídá rozsahu klíče oddílu že jsou rozdělit do malé dávek a každý zkrácená batch act jako datovou část, která je potvrzena na straně serveru. Knihovna BulkExecutor má integrovaný optimalizace pro souběžné provádění těchto malé-dávek uvnitř i mezi rozsahy klíčů oddílů. Následující obrázek ukazuje, jak BulkExecutory dávek dat do různé klíče oddílů:  

![Architektura vykonavatele hromadné](./media/bulk-executor-overview/bulk-executor-architecture.png)

Knihovna hromadné vykonavatele zajišťuje lze uchovávat využívat propustnost přidělené do kolekce. Používá [mechanismu řízení zahlcení stylu AIMD](https://tools.ietf.org/html/rfc5681) pro každou databázi Cosmos Azure oddílu klíče rozsah pro efektivní zpracování omezení a vypršení časových limitů. 

## <a name="next-steps"></a>Další kroky 
  
* Další informace tak, že zkusíte se ukázkové aplikace, které využívají hromadné vykonavatele knihovny v nástroji [.NET](bulk-executor-dot-net.md) a [Java](bulk-executor-java.md).  
* Podívejte se na BulkExecutor SDK informace a verze poznámek v [.NET](sql-api-sdk-bulk-executor-dot-net.md) a [Java](sql-api-sdk-bulk-executor-java.md).
* Knihovna hromadné vykonavatele je integrována do konektoru Cosmos DB Spark, další informace najdete v tématu [konektor Azure Cosmos DB Spark](spark-connector.md) článku.  
* Knihovna BulkExecutor integrována do nové verze [konektor Azure Cosmos DB](https://aka.ms/bulkexecutor-adf-v2) pro Azure Data Factory ke zkopírování dat.
