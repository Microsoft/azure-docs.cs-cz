---
title: Přehled knihovny prováděcí modul hromadného služby Azure Cosmos DB | Dokumentace Microsoftu
description: Další informace o službě Azure Cosmos DB hromadné prováděcí modul knihovny, výhody používání knihovny a jeho architektura.
keywords: Prováděcí modul hromadného Java
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: 823cb536a1cd0b8f5da7442906b14447c15ae044
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091349"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Přehled knihovny prováděcí modul hromadného služby Azure Cosmos DB
 
Azure Cosmos DB je rychlý, flexibilní a globálně distribuovaná databázová služba, který je navržený pro elastické škálování na více systémů pro podporu: 

* Velké čtení a zápis propustnost (milionů operací za sekundu).  
* Ukládat velké objemy (stovky terabajtů nebo dokonce i více) transakční a provozní data s latencí předvídatelné milisekund.  

Knihovna prováděcí modul hromadného umožňuje využívat tuto obrovskou propustnost a úložiště. Hromadné prováděcí modul knihovny lze provádět hromadné operace ve službě Azure Cosmos DB pomocí hromadného importu a hromadně aktualizovat rozhraní API. Další informace o funkcích hromadné prováděcí modul knihovny v následujících částech. 

> [!NOTE] 
> V současné době hromadné prováděcí modul knihovny podporuje import a aktualizace operace a tato knihovna je podporován pouze účty SQL API služby Azure Cosmos DB. Zobrazit [.NET](sql-api-sdk-bulk-executor-dot-net.md) a [Java](sql-api-sdk-bulk-executor-java.md) poznámky k verzi pro všechny aktualizace do knihovny.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Klíčové funkce knihovny hromadné prováděcího modulu  
 
* Významně snižuje na straně klienta výpočetní prostředky potřebné k saturate propustnost přidělené kontejneru. Jedna vláken aplikace zapíše data s využitím že rozhraní API hromadného importu dosáhne 10 vícekrát vyšší propustnost zápisu ve srovnání s vícevláknové aplikaci, která zapisuje data souběžně při zvýšení sytosti klienta procesoru počítače.  

* Ji odděluje tedious úlohy vytváření aplikací logiky, která by rychlosti požadavek, vypršení časového limitu požadavku a další přechodným výjimkám díky efektivnímu zpracování je v rámci knihovny.  

* Poskytuje zjednodušenou mechanismus pro aplikace budou provádět hromadné operace pro horizontální navýšení kapacity. Instanci prováděcího modulu jednou hromadnou běžící na Virtuálním počítači Azure mohou využívat větší než 500 tisíc RU/s a vyšší propustnosti můžete dosáhnout tak, že přidáte další instance na individuální klientské virtuální počítače.  
 
* Importovat více než terabajtu dat ji do hodiny hromadně pomocí architektury horizontální navýšení kapacity.  

* Jako opravy ji provést hromadnou aktualizaci existujících dat v kontejnerech služby Azure Cosmos DB. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Jak pracuje prováděcí modul hromadného? 

Při operaci hromadného importu nebo aktualizovat dokumenty se aktivuje pomocí služby batch entit, že jsou zpočátku které se náhodně pomíchají do intervalů odpovídající jejich rozsah klíče oddílu služby Azure Cosmos DB. V rámci každého kbelíku, která odpovídá na rozsah klíče oddílu že se člení na mini dávek a každý act zkrácené batch jako datovou část, která je potvrzena na straně serveru. Optimalizace pro souběžné spouštění těchto mini-dávek i mezi nimi rozsahů klíče oddílů má integrované knihovny hromadné prováděcího modulu. Následující obrázek ukazuje, jak hromadně prováděcí modul seskupuje data do dávek na různé klíče oddílů:  

![Hromadné prováděcí modul architektury](./media/bulk-executor-overview/bulk-executor-architecture.png)

Knihovna prováděcí modul hromadného zajistí, aby lze uchovávat využití propustnosti přidělené do kolekce. Používá [mechanismus řízení zahlcení AIMD – vizuální styl](https://tools.ietf.org/html/rfc5681) pro každou službu Azure Cosmos DB rozdělit rozsah klíče pro efektivní zpracování omezení rychlosti a vypršení časového limitu. 

## <a name="next-steps"></a>Další kroky 
  
* Další informace a vyzkoušejte si ukázkové aplikace využívající prováděcí modul hromadného knihovny [.NET](bulk-executor-dot-net.md) a [Java](bulk-executor-java.md).  
* Podívejte se na hromadné prováděcí modul SDK informace a vydání poznámek [.NET](sql-api-sdk-bulk-executor-dot-net.md) a [Java](sql-api-sdk-bulk-executor-java.md).
* Prováděcí modul hromadného knihovny je integrovaná do konektoru Cosmos DB Spark, další informace najdete v tématu [konektor Azure Cosmos DB Spark](spark-connector.md) článku.  
* Knihovna prováděcí modul hromadného je integrovaná taky na novou verzi [konektor služby Azure Cosmos DB](https://aka.ms/bulkexecutor-adf-v2) pro službu Azure Data Factory pro kopírování dat.
