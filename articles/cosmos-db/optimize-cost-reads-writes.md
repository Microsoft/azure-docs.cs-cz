---
title: Optimalizovat náklady na operace čtení a zápisu ve službě Azure Cosmos DB
description: Tento článek vysvětluje, vysvětluje, jak snížit náklady na služby Azure Cosmos DB při provádění pro čtení a zápisu operace s daty.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: 0cb40e4cbbfccaaf60e53010603e1d547e45dab9
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53263808"
---
# <a name="optimize-the-cost-required-to-read-and-write-data-from-azure-cosmos-db"></a>Optimalizovat náklady na požadované ke čtení a zápisu dat ze služby Azure Cosmos DB

Tento článek popisuje, jak vypočítat náklady na požadované ke čtení a zápisu dat ze služby Azure Cosmos DB. Operace čtení zahrnují operace get u položek a operace zápisu zahrnuje vložení, nahradit, odstranění a upsert položek.  

## <a name="cost-of-reads-and-writes"></a>Náklady na operace čtení a zápisy

Azure Cosmos DB garantuje předvídatelný výkon z hlediska latence a propustnosti za použití modelu zřízenou propustnost. Zřízenou propustnost je reprezentován z hlediska [jednotek žádostí](request-units.md) za sekundu neboli RU/s. Jednotku požadavku (RU) je logické abstrakce přes výpočetní prostředky, například CPU, paměti, vstupně-výstupních operací, atd., které jsou nutné k provedení požadavku. Zřízená propustnost (ru) je vyhrazené a vyhrazené pro váš kontejner nebo databázi, aby zajistil předvídatelnou propustnost a latenci. Zřízená propustnost umožňuje službě Azure Cosmos DB zajistit konzistentní a předvídatelný výkon, zaručené nízké latence a vysoká dostupnost v libovolném měřítku. Jednotky žádosti představují normalizované měny, která zjednodušuje uvažování o tom, kolik prostředků, které aplikace potřebuje. 

Není nutné zvážit odlišení těchto jednotkách požadavků mezi operací čtení a zápisu. Model jednotný měny jednotek žádosti ze vytvoří efektivitu Zaměnitelně používat stejné kapacity propustnosti pro operace čtení i zápisu. Následující tabulka ukazuje náklady na operace čtení a zápisy z hlediska RU/s pro položky, které jsou do velikosti 1 KB a 100 KB.

|**Velikost položky**  |**Náklady na jeden pro čtení** |**Náklady na jeden zápis**|
|---------|---------|---------|
|1 KB |1 RU |5 ru |
|100 KB |10 RU |50 jednotek ru |

Čtení položky, který je 1 KB velikost náklady na jednu RU. Zápis položky, který je 1 KB stojí 5 RU. Při použití výchozí relace platí náklady na čtení a zápis [úrovně konzistence](consistency-levels.md).  Mezi aspekty kolem ru patří: velikost, počet vlastností, konzistenci dat, indexované vlastnosti, indexování, klikněte na ni a vzory pro dotazování.

## <a name="normalized-cost-for-1-million-reads-and-writes"></a>Normalizovaná náklady na 1 milion čte a zapisuje

Zřizování 1 000 RU/s se přeloží na 3.6 miliony RU za hodinu a stojí 0,08 USD za hodinu (v USA a Evropa). Pro 1 KB položku, můžete provádět 3.6 milionů čtení nebo zápisu 0,72 milionů (Tato hodnota se vypočítá jako: `3.6 million RU / 5`) HODINOVĚ, přičemž tento zřízenou propustnost. Normalizovány na milion operací čtení a zápisu, náklady by byly 0,022 $ 1 milion čtení (Tato hodnota se vypočítá jako: $0.08/3.6 milionů) a 0.111 pro zápis 1 milion (Tato hodnota se vypočítá jako: $0.08/0.72 milionů).

## <a name="number-of-regions-and-the-request-units-cost"></a>Počet oblastí a jednotkách požadavků nákladů

Náklady na zápisy jsou konstantní bez ohledu na počet oblastí přidružených k účtu Azure Cosmos. Jinými slovy zápis 1 KB stojí 5 ru nezávislé na počtu oblastí, které jsou přidružená k účtu. Je netriviální množství prostředků stráveného replikaci, příjem a zpracování provoz replikace na každou oblast. Podrobnosti o optimalizaci nákladů ve více oblastech najdete v tématu [optimalizovat náklady na účty ve více oblastech Cosmos](optimize-cost-regions.md) článku.

## <a name="optimize-the-cost-of-writes-and-reads"></a>Optimalizovat náklady na zápisy a čtení

Při provádění operace zápisu, měli byste zřídit dostatečnou kapacitu pro podporu počet zápisů potřeby za sekundu. Zřízená propustnost můžete zvýšit pomocí sady SDK, portálu, rozhraní příkazového řádku, než se pustíte do zápisy a potom snížení propustnosti po dokončení zápisu. Propustnost za období zápisu je minimální propustnost potřebné pro daná data a požadované propustnosti pro vložení úloh za předpokladu, že jsou spuštěné žádné úlohy. 

Pokud jsou spuštěny další zátěže souběžně, například dotazu, čtení, aktualizace nebo odstranění, měli byste přidat další požadavek jednotky, vyžaduje se pro tyto operace příliš. Pokud operace zápisu se míra časově omezené, můžete přizpůsobit zásady opakování/omezení rychlosti s použitím sady SDK služby Azure Cosmos DB. Například můžete zvýšit zatížení dokud malý počet žádostí o získá míra limited. Pokud dojde k omezení četnosti, klientská aplikace by měla Regrese v omezení rychlosti požadavků pro interval zadaný opakování. Než to zkusíte znovu zápisy, měli byste co nejkratším časové prodlevy mezi opakovanými pokusy. Podpora zásad opakování je součástí SQL .NET, Java, Node.js a sady SDK pro Python a všech podporovaných verzích .NET Core SDK. 

Můžete také hromadné vložení dat do služby Azure Cosmos DB nebo kopírování dat ze všech podporovaných zdrojů úložišť dat do služby Azure Cosmos DB pomocí [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). Azure Data Factory nativně integruje s Azure Cosmos DB hromadné API poskytovat nejlepší výkon, když zapisujete data.

## <a name="next-steps"></a>Další postup

Potom můžete přejít k další informace o optimalizaci nákladů ve službě Azure Cosmos DB najdete v následujících článcích:

* Další informace o [optimalizace pro vývoj a testování](optimize-dev-test.md)
* Další informace o [vysvětlení vašeho vyúčtování Azure Cosmos DB](understand-your-bill.md)
* Další informace o [optimalizace propustnosti náklady](optimize-cost-throughput.md)
* Další informace o [optimalizovat náklady na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizovat náklady na dotazy](optimize-cost-queries.md)
* Další informace o [optimalizovat náklady na účty ve více oblastech Azure Cosmos](optimize-cost-regions.md)
