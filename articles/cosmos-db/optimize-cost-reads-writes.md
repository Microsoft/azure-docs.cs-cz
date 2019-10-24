---
title: Optimalizace nákladů na čtení a zápisy v Azure Cosmos DB
description: Tento článek vysvětluje, jak snížit náklady na Azure Cosmos DB při provádění operací čtení a zápisu dat.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 934853b80c6e6377923df4c2b5cce7b7d7d57d7c
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754934"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Optimalizujte čtení a zapisuje náklady v Azure Cosmos DB

Tento článek popisuje, jak se počítá náklady nutné ke čtení a zápisu dat z Azure Cosmos DB. Operace čtení zahrnují operace get pro položky a operace zápisu včetně vložení, nahrazení, odstranění a Upsert položek.  

## <a name="cost-of-reads-and-writes"></a>Náklady na čtení a zápisy

Azure Cosmos DB garantuje předvídatelný výkon z hlediska propustnosti a latence pomocí zřízeného modelu propustnosti. Zajištěná propustnost je vyjádřena z hlediska [jednotek žádosti](request-units.md) za sekundu nebo ru/s. Jednotka požadavku (RU) je logická abstrakce nad výpočetními prostředky, jako jsou například CPU, paměť, vstupně-výstupní operace atd., které jsou nutné k provedení žádosti. Zřízená propustnost (ru) je nastavená jako vyhrazená pro váš kontejner nebo databázi, aby poskytovala předvídatelné propustnost a latenci. Zajištěná propustnost umožňuje Azure Cosmos DB poskytovat předvídatelný a konzistentní výkon, zaručenou nízkou latenci a vysokou dostupnost v jakémkoli měřítku. Jednotky žádosti reprezentují normalizovanou měnu, která zjednodušuje určení toho, kolik prostředků aplikace potřebuje. 

Nemusíte myslet na rozdíl od čtení a zápisu jednotek žádostí. Sjednocený model měny jednotek požadavků vytváří efektivitu pro zaměnitelné používání stejné kapacity propustnosti pro čtení i zápis. V následující tabulce jsou uvedeny náklady na čtení a zápisy v souvislosti s RU/s pro položky, které mají velikost 1 KB a 100 KB.

|**Velikost položky**  |**Náklady na jedno čtení** |**Náklady na jeden zápis**|
|---------|---------|---------|
|1 KB |1 RU |5 ru |
|100 KB |10 RU |50 ru |

Čtení položky o velikosti 1 KB se bude načítat náklady o jednu RU. Zápis položky s 1 KB náklady pět ru Náklady na čtení a zápis se použijí při použití výchozí [úrovně konzistence](consistency-levels.md)relace.  Mezi požadavky týkající se ru patří: velikost položky, počet vlastností, konzistence dat, indexované vlastnosti, indexování a vzory dotazů.

## <a name="normalized-cost-for-1-million-reads-and-writes"></a>Normalizované náklady pro čtení a zápisy 1 000 000

Zřizování 1 000 RU/s se překládá na 3 600 000 RU za hodinu a $0,08 náklady na hodinu (v USA a Evropě). U položky 1-KB můžete provádět 3 600 000 čtení nebo 720 000 zápisu (Tato hodnota se vypočítává jako: `3.6 million RU / 5`) za hodinu pomocí této zřízené propustnosti. V normalizovaní na milion čtení a zápisů by cena byla $0,022 pro 1 000 000 čtení (Tato hodnota se vypočítá jako: $0,08/3.6 milionů) a $0,111 pro zápisy po 1 000 000 (Tato hodnota se vypočítá jako: $0,08/3.6 milion).

## <a name="number-of-regions-and-the-request-units-cost"></a>Počet oblastí a náklady na jednotky žádosti

Náklady na zápisy jsou konstantní bez ohledu na počet oblastí přidružených k účtu Azure Cosmos. Jinými slovy, zápis 1 KB bude mít za následek pět ru nezávisle na počtu oblastí, které jsou přidružené k účtu. Replikace, příjem a zpracování provozu replikace v každé oblasti je netriviální množství prostředků, které stráví replikaci. Podrobnosti o optimalizaci nákladů na více oblastí najdete v článku věnovaném [optimalizaci nákladů na účty Cosmos s více oblastmi](optimize-cost-regions.md) .

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
