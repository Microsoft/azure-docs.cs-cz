---
title: Optimalizace nákladů na čtení a zápisy v Azure Cosmos DB
description: Tento článek vysvětluje, jak snížit náklady na Azure Cosmos DB při provádění operací čtení a zápisu dat.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: bbdd1bedb7b9a9f00a0b65ccc4c108ba6fd2638c
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78398955"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Optimalizujte čtení a zapisuje náklady v Azure Cosmos DB

Tento článek popisuje, jak se počítá náklady nutné ke čtení a zápisu dat z Azure Cosmos DB. Operace čtení zahrnují operace get pro položky a operace zápisu včetně vložení, nahrazení, odstranění a Upsert položek.  

## <a name="cost-of-reads-and-writes"></a>Náklady na operace čtení a zápisy

Azure Cosmos DB garantuje předvídatelný výkon z hlediska propustnosti a latence pomocí zřízeného modelu propustnosti. Zajištěná propustnost je vyjádřena z hlediska [jednotek žádosti](request-units.md) za sekundu nebo ru/s. Jednotka požadavku (RU) je logická abstrakce nad výpočetními prostředky, jako jsou například CPU, paměť, vstupně-výstupní operace atd., které jsou nutné k provedení žádosti. Zřízená propustnost (ru) je nastavená jako vyhrazená pro váš kontejner nebo databázi, aby poskytovala předvídatelné propustnost a latenci. Zajištěná propustnost umožňuje Azure Cosmos DB poskytovat předvídatelný a konzistentní výkon, zaručenou nízkou latenci a vysokou dostupnost v jakémkoli měřítku. Jednotky žádosti reprezentují normalizovanou měnu, která zjednodušuje určení toho, kolik prostředků aplikace potřebuje. 

Nemusíte myslet na rozdíl od čtení a zápisu jednotek žádostí. Sjednocený model měny jednotek požadavků vytváří efektivitu pro zaměnitelné používání stejné kapacity propustnosti pro čtení i zápis. V následující tabulce jsou uvedeny náklady na čtení a zápisy v souvislosti s RU/s pro položky, které mají velikost 1 KB a 100 KB.

|**Velikost položky**  |**Náklady na jedno čtení** |**Náklady na jeden zápis**|
|---------|---------|---------|
|1 kB |1 RU |5 ru |
|100 KB |10 RU |50 jednotek ru |

Čtení položky o velikosti 1 KB se bude načítat náklady o jednu RU. Zápis položky s 1 KB náklady pět ru Náklady na čtení a zápis se použijí při použití výchozí [úrovně konzistence](consistency-levels.md)relace.  Mezi požadavky týkající se ru patří: velikost položky, počet vlastností, konzistence dat, indexované vlastnosti, indexování a vzory dotazů.

## <a name="optimize-the-cost-of-writes-and-reads"></a>Optimalizace nákladů na zápisy a čtení

Při provádění operací zápisu byste měli zřídit dostatečnou kapacitu pro podporu počtu potřebných zápisů za sekundu. Zřízenou propustnost můžete zvýšit před provedením zápisů pomocí sady SDK, portálu, rozhraní příkazového řádku a následným snížením propustnosti po dokončení zápisů. Propustnost za dobu zápisu je minimální propustností potřebnou pro zadaná data a navíc propustnost, která je nutná pro vkládání úloh, za předpokladu, že nejsou spuštěny žádné jiné úlohy. 

Pokud spouštíte jiné úlohy, například dotaz, čtení, aktualizace nebo odstranění, měli byste přidat i další jednotky žádostí vyžadované pro tyto operace. Pokud jsou operace zápisu omezené na míru, můžete upravit zásadu opakování/omezení rychlosti pomocí sad SDK pro Azure Cosmos DB. Můžete například zvýšit zatížení, dokud malá míra požadavků nezíská omezení na úrovni. Pokud dojde k překročení limitu četnosti, klientské aplikace by se měly zálohovat na požadavky na omezení rychlosti pro zadaný interval opakování. Před opakovaným pokusem o zápisy byste měli mít minimální časovou prodlevu mezi opakovanými pokusy. Podpora zásad opakování je obsažená v sadách SQL .NET, Java, Node. js a Python SDK a v podporovaných verzích sady .NET Core SDK. 

Můžete také hromadně vkládat data do Azure Cosmos DB nebo kopírovat data z libovolného podporovaného zdrojového úložiště dat do Azure Cosmos DB pomocí [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). Azure Data Factory nativně integruje s rozhraním API Azure Cosmos DB Bulk pro zajištění nejlepšího výkonu při psaní dat.

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci nákladů v Azure Cosmos DB najdete v následujících článcích:

* Další informace o [optimalizaci pro vývoj a testování](optimize-dev-test.md)
* Další informace o [Azure Cosmos DB vyúčtování](understand-your-bill.md)
* Další informace o [optimalizaci nákladů na propustnost](optimize-cost-throughput.md)
* Další informace o [optimalizaci nákladů na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizaci nákladů na dotazy](optimize-cost-queries.md)
* Další informace o [optimalizaci nákladů na účty Azure Cosmos ve více oblastech](optimize-cost-regions.md)
