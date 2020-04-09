---
title: Přehled knihovny Bulk Executor pro službu Azure Cosmos DB
description: Provádět hromadné operace v Azure Cosmos DB prostřednictvím hromadného importu a hromadné aktualizace API, které nabízí knihovna hromadného vykonavatele.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: af17f9c2ef7eea5eb531327d4df13d5885a49b7e
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985588"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Přehled knihovny Bulk Executor pro službu Azure Cosmos DB
 
Azure Cosmos DB je rychlá, flexibilní a globálně distribuovaná databázová služba navržená tak, aby elasticky horizontálně rozšiřovala svou kapacitu za účelem podpory následujících scénářů: 

* Velká propustnost čtení a zápisu (miliony operací za sekundu)  
* Ukládání velkých objemů (stovky terabajtů nebo ještě více) transakčních a provozních dat s předvídatelnou latencí v řádu milisekund  

Knihovna Bulk Executor vám pomůže využít tuto obrovskou propustnost a úložiště. Knihovna Bulk Executor umožňuje provádět ve službě Azure Cosmos DB hromadné operace prostřednictvím rozhraní API pro hromadný import a hromadné aktualizace. Další informace o funkcích knihovny Bulk Executor najdete v následujících částech. 

> [!NOTE] 
> V současné době knihovna hromadného prováděcího modulu podporuje operace importu a aktualizace a tato knihovna je podporovaná jenom pomocí účtů SQL API Azure Cosmos DB a rozhraní GREMLIN API.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Klíčové vlastnosti knihovny hromadného vykonavatele  
 
* Výrazně snižuje výpočetní prostředky na straně klienta potřebné k nasycení propustnosti přidělené kontejneru. Aplikace s jedním vláknem, která zapisuje data pomocí rozhraní API hromadného importu, dosahuje 10krát větší propustnost zápisu ve srovnání s vícevláknovou aplikací, která zapisuje data paralelně při saurace procesoru klientského počítače.  

* Abstrahuje pryč únavné úkoly psaní aplikační logiky pro zpracování omezení rychlosti požadavku, časový limit požadavku a další přechodné výjimky efektivní zpracování v rámci knihovny.  

* Poskytuje zjednodušený mechanismus pro aplikace provádějící hromadné operace horizontální navýšení kapacity. Jedna hromadná instance vykonavatele spuštěná na virtuálním počítači Azure může spotřebovat více než 500 kB RU/s a můžete dosáhnout vyšší propustnosti přidáním dalších instancí na jednotlivé klientské virtuální počítače.  
 
* Může hromadně importovat více než terabajt dat během jedné hodiny pomocí architektury horizontálnínavýšení kapacity.  

* Může hromadně aktualizovat existující data v kontejnerech Azure Cosmos jako opravy. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Jak funguje hromadný exekutor? 

Když se aktivuje hromadná operace importu nebo aktualizace dokumentů s dávkou entit, jsou zpočátku zamíchány do bloků odpovídajících rozsahu klíčů oddílu Azure Cosmos DB. V rámci každého bloku, který odpovídá rozsahu klíče oddílu, jsou rozděleny do mini-dávky a každý mini-batch působí jako datová část, která je potvrzena na straně serveru. Knihovna hromadného vykonavatele vytvořila optimalizace pro souběžné provádění těchto minidávek v rámci i mezi rozsahy klíčů oddílu. Následující obrázek znázorňuje, jak hromadný prováděcí modul dávky dat do různých klíčů oddílu:  

![Hromadná architektura vykonavatele](./media/bulk-executor-overview/bulk-executor-architecture.png)

Knihovna hromadného vykonavatele zajišťuje maximální využití propustnosti přidělené kolekci. Používá [mechanismus řízení přetížení ve stylu AIMD](https://tools.ietf.org/html/rfc5681) pro každý rozsah klíčů oddílu Azure Cosmos DB pro efektivní zpracování omezení rychlosti a časového limitu. 

## <a name="next-steps"></a>Další kroky 
  
* Další informace naleznete v vyzkoušení ukázkových aplikací, které spotřebovávají knihovnu hromadných prováděcích modulů v [rozhraní .NET](bulk-executor-dot-net.md) a [Vja](bulk-executor-java.md).  
* Podívejte se na informace hromadného vykonavatele sady SDK a poznámky k verzi v [rozhraní .NET](sql-api-sdk-bulk-executor-dot-net.md) a [Vja](sql-api-sdk-bulk-executor-java.md).
* Knihovna hromadného prováděcího modulu je integrovaná do konektoru Cosmos DB Spark, abyste se dozvěděli další informace, přečtěte si článek [konektoru Azure Cosmos DB Spark.](spark-connector.md)  
* Knihovna hromadného prováděcího modulu je také integrovaná do nové verze [konektoru Azure Cosmos DB](../data-factory/connector-azure-cosmos-db.md) pro Azure Data Factory ke kopírování dat.
