---
title: Optimalizace nákladů na čtení a zápisy v Azure Cosmos DB
description: Tento článek vysvětluje, jak snížit náklady na Azure Cosmos DB při provádění operací čtení a zápisu na data.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 725876594a7e7c5f3b3a02802f487dc5fdfb64dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535931"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Optimalizace nákladů na čtení a zápisy v Azure Cosmos DB

Tento článek popisuje, jak se počítají náklady potřebné ke čtení a zápisu dat z Azure Cosmos DB. Operace čtení zahrnují operace získání položek a operace zápisu zahrnují vložení, nahrazení, odstranění a upsert položek.  

## <a name="cost-of-reads-and-writes"></a>Náklady na čtení a zápisy

Azure Cosmos DB zaručuje předvídatelný výkon z hlediska propustnosti a latence pomocí zřízeného modelu propustnosti. Zřízená propustnost je reprezentována ve smyslu [jednotek požadavku](request-units.md) za sekundu nebo RU/s. Jednotka požadavku (RU) je logická abstrakce nad výpočetními prostředky, jako je procesor, paměť, vo atd., které jsou nutné k provedení požadavku. Zřízená propustnost (RU) je vyhrazena a vyhrazena pro váš kontejner nebo databázi, aby poskytovala předvídatelnou propustnost a latenci. Zřízená propustnost umožňuje Azure Cosmos DB poskytovat předvídatelný a konzistentní výkon, zaručenou nízkou latenci a vysokou dostupnost v libovolném měřítku. Jednotky požadavků představují normalizovanou měnu, která zjednodušuje uvažování o tom, kolik prostředků aplikace potřebuje. 

Nemusíte přemýšlet o rozlišování jednotek požadavků mezi čtením a zápisy. Jednotný měnový model jednotek požadavků vytváří efektivitu pro zaměnitelné použití stejné kapacity propustnosti pro čtení i zápisy. V následující tabulce jsou uvedeny náklady na čtení a zápisy z hlediska RU/s pro položky, které mají velikost 1 KB a 100 kB.

|**Velikost položky**  |**Náklady na jedno čtení** |**Náklady na jeden zápis**|
|---------|---------|---------|
|1 kB |1 ŽP |5 Ru |
|100 KB |10 RU |50 RU |

Čtení položky o velikosti 1 KB stojí jeden ŽP. Zápis položky, která je 1 KB stojí pět ru. Náklady na čtení a zápis jsou použitelné při použití výchozí [úrovně konzistence](consistency-levels.md)relace .  Důležité informace o ru patří: velikost položky, počet vlastností, konzistence dat, indexované vlastnosti, indexování a vzorky dotazů.

## <a name="optimize-the-cost-of-writes-and-reads"></a>Optimalizace nákladů na zápisy a čtení

Při provádění operací zápisu byste měli zřídit dostatečnou kapacitu pro podporu počtu zápisů potřebných za sekundu. Zřízenou propustnost můžete zvýšit pomocí sady SDK, portálu, cli před provedením zápisu a potom snížit propustnost po dokončení zápisu. Vaše propustnost pro období zápisu je minimální propustnost potřebná pro daná data, plus propustnost požadovanou pro vkládání úloh za předpokladu, že nejsou spuštěny žádné jiné úlohy. 

Pokud současně spouštíte jiné úlohy, například dotaz/čtení/aktualizaci/odstranění, měli byste přidat další jednotky požadavků požadované pro tyto operace. Pokud jsou operace zápisu omezené, můžete přizpůsobit zásady opakování a backoff pomocí sad Azure Cosmos DB SDK. Například můžete zvýšit zatížení, dokud malá míra požadavků získá omezenou rychlost. Pokud dojde k omezení rychlosti, klientská aplikace by měla ustoupit na požadavky na omezení rychlosti pro zadaný interval opakování. Před opakováním zápisů byste měli mít minimální časové prodyšnosti mezi opakovanými pokusy. Podpora zásad opakování je součástí sad SQL .NET, Java, Node.js a Pythonu SDK a všech podporovaných verzí sad SDK jádra .NET. 

Můžete také hromadně vložit data do Azure Cosmos DB nebo zkopírovat data z libovolného úložiště dat podporovaného zdroje do Azure Cosmos DB pomocí [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). Azure Data Factory se nativně integruje s rozhraním API Hromadné Azure Cosmos DB, aby poskytovala nejlepší výkon při psaní dat.

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci nákladů v Azure Cosmos DB najdete v následujících článcích:

* Další informace o [optimalizaci pro vývoj a testování](optimize-dev-test.md)
* Další informace o [vysvětlení vaší faktury z DB Služby Azure Cosmos](understand-your-bill.md)
* Další informace o [optimalizaci nákladů na propustnost](optimize-cost-throughput.md)
* Další informace o [optimalizaci nákladů na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizaci nákladů na dotazy](optimize-cost-queries.md)
* Další informace o [optimalizaci nákladů na účty Azure Cosmos s více oblastmi](optimize-cost-regions.md)
