---
title: Přehled knihovny Bulk Executor pro službu Azure Cosmos DB
description: Provádějte hromadné operace v Azure Cosmos DB prostřednictvím hromadného importu a rozhraní API hromadné aktualizace nabízené knihovnou hromadného prováděcího modulu.
author: tknandu
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 211fc85f97069fcf3251048a074d625e777f8e7d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100469"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Přehled knihovny Bulk Executor pro službu Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
 
Azure Cosmos DB je rychlá, flexibilní a globálně distribuovaná databázová služba navržená tak, aby elasticky škálovala na více instancí za účelem podpory následujících scénářů: 

* Velká propustnost čtení a zápisu (miliony operací za sekundu)  
* Ukládání velkých objemů (stovky terabajtů nebo ještě více) transakčních a provozních dat s předvídatelnou latencí v řádu milisekund  

Knihovna Bulk Executor vám pomůže využít tuto obrovskou propustnost a úložiště. Knihovna Bulk Executor umožňuje provádět ve službě Azure Cosmos DB hromadné operace prostřednictvím rozhraní API pro hromadný import a hromadné aktualizace. Další informace o funkcích knihovny Bulk Executor najdete v následujících částech. 

> [!NOTE] 
> Knihovna hromadných prováděcích modulů v současné době podporuje operace importu a aktualizace a tato knihovna je podporována pouze pomocí Azure Cosmos DB SQL API a účtů rozhraní API Gremlin.

> [!IMPORTANT]
> Knihovna hromadného prováděcího modulu není aktuálně podporovaná na účtech bez [serveru](serverless.md) . V rozhraní .NET se doporučuje používat [hromadnou podporu](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk/) dostupnou ve verzi V3 sady SDK.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Klíčové funkce knihovny hromadného prováděcího modulu  
 
* Významně snižuje výpočetní prostředky na straně klienta potřebné k sytosti propustnosti přidělené kontejneru. Jediná aplikace s více vlákny, která zapisuje data pomocí hromadného rozhraní API pro hromadné importy, dosahuje při porovnání s vícevláknovou aplikací, která zapisuje data paralelně, při sytosti procesoru klientského počítače.  

* Abstrakce nezpůsobuje zdlouhavé úlohy při psaní aplikační logiky za účelem zpracování míry omezení požadavků, časových limitů požadavků a dalších přechodných výjimek, a to díky jejich efektivnímu zpracování v rámci knihovny.  

* Nabízí zjednodušený mechanismus pro aplikace, které provádějí hromadné operace pro horizontální navýšení kapacity. Jedna instance hromadného prováděcího modulu spuštěná na virtuálním počítači Azure může spotřebovat víc než 500 000 RU/s a můžete dosáhnout vyšší míry propustnosti přidáním dalších instancí na jednotlivé klientské virtuální počítače.  
 
* Může hromadně importovat více než terabajt dat za hodinu pomocí architektury škálování na více instancí.  

* Může hromadně aktualizovat existující data v kontejnerech Azure Cosmos jako opravy. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Jak hromadný prováděcí modul funguje? 

Když se Hromadná operace pro import nebo aktualizaci dokumentů aktivuje pomocí dávky entit, zpočátku se převedou do intervalů odpovídajících jejich Azure Cosmos DB rozsahu klíčů oddílu. V rámci každého intervalu, který odpovídá rozsahu klíče oddílu, se rozdělí na Mini dávky a každá z nich se bude chovat jako datová část, která je potvrzena na straně serveru. Knihovna hromadných prováděcích modulů obsahuje integrované optimalizace pro souběžné provádění těchto Mini dávek v rámci i napříč rozsahy klíčů oddílů. Následující obrázek znázorňuje, jak hromadně prováděč dávkuje data do různých klíčů oddílů:  

:::image type="content" source="./media/bulk-executor-overview/bulk-executor-architecture.png" alt-text="Architektura hromadného prováděcího modulu" :::

Knihovna hromadného prováděcího modulu zajišťuje maximální využití propustnosti přidělené kolekci. Pro každý Azure Cosmos DB rozsah klíčů oddílu používá [mechanismus pro řízení zahlcení ve stylu](https://tools.ietf.org/html/rfc5681) , který umožňuje efektivně zpracovávat omezení a časové limity při četnosti. 

## <a name="next-steps"></a>Další kroky 
  
* Další informace získáte vyzkoušením ukázkových aplikací, které využívají knihovnu hromadných prováděcích modulů v jazycích [.NET](bulk-executor-dot-net.md) a [Java](bulk-executor-java.md).  
* Projděte si informace o sadě SDK hromadného prováděcího modulu a poznámky k verzi v jazycích [.NET](sql-api-sdk-bulk-executor-dot-net.md) a [Java](sql-api-sdk-bulk-executor-java.md).
* Knihovna hromadného prováděcího modulu je integrovaná do konektoru Cosmos DB Spark. Další informace najdete v článku [Azure Cosmos DB Spark Connector](spark-connector.md) .  
* Knihovna hromadného prováděcího modulu je integrována také v nové verzi [konektoru Azure Cosmos DB](../data-factory/connector-azure-cosmos-db.md) pro Azure Data Factory ke kopírování dat.
