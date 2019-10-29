---
title: Kompromisy týkající se dostupnosti a výkonu pro různé úrovně konzistence v Azure Cosmos DB
description: Kompromisy týkající se dostupnosti a výkonu pro různé úrovně konzistence v Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: f241f243860635db443b732f94d12956bbe0f9d8
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990621"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Kompromisy mezi konzistencí, dostupností a výkonem 

Distribuované databáze, které spoléhají na replikaci při vysoké dostupnosti, nízké latenci nebo obojí, musí dělat kompromisy. Mezi kompromisy patří konzistence čtení oproti dostupnosti, latenci a propustnosti.

Azure Cosmos DB přistupuje k konzistenci dat jako spektrum možností. Tento přístup zahrnuje více možností než dvě extrémní silná a konečná konzistence. Pro spektrum konzistence si můžete vybrat z pěti dobře definovaných modelů. Od nejpřísnější až nejslabších jsou modely:

- *Silnější*
- *Ohraničená neaktuálnost*
- *Jednání*
- *Konzistentní předpona*
- *Konečné*

Každý model poskytuje kompromisy k dostupnosti a výkonu a je zajištěný ucelenou SLA.

## <a name="consistency-levels-and-latency"></a>Úrovně konzistence a latence

Latence čtení pro všechny úrovně konzistence je vždycky zaručená za méně než 10 milisekund na 99. percentilu. Tato latence čtení se zálohuje smlouvou SLA. Průměrná latence čtení v 50. percentilu má obvykle 2 milisekundy nebo méně. Účty Azure Cosmos, které jsou v několika oblastech a jsou nakonfigurované se silnými konzistencí, představují výjimku z této záruky.

Latence zápisu pro všechny úrovně konzistence je vždycky zaručená za méně než 10 milisekund na 99. percentilu. Tato latence zápisu se zálohuje smlouvou SLA. Průměrná latence zápisu na 50. percentilu je obvykle 5 milisekund nebo méně.

U účtů Azure Cosmos nakonfigurovaných se silnou konzistencí s více než jednou oblastí je latence zápisu zaručená, aby byla kratší než dvojnásobná doba odezvy (RTT) mezi kteroukoli ze dvou nejvzdálenějších oblastí plus 10 milisekund na 99 percentilu.

Přesná latence RTT je funkce rychlosti a topologie sítě Azure. Azure Networking neposkytuje žádnou latenci SLA pro dobu odezvy mezi dvěma oblastmi Azure. Pro váš účet Azure Cosmos se latence replikace zobrazují v Azure Portal. K monitorování latencí replikace mezi různými oblastmi, které jsou přidružené k vašemu účtu Azure Cosmos, můžete použít Azure Portal (Přejít na okno metriky).

## <a name="consistency-levels-and-throughput"></a>Úrovně konzistence a propustnost

- Stejný počet jednotek žádostí, relaci, konzistenci a konečné úrovně konzistence poskytují přibližně dvojnásobek propustnosti čtení ve srovnání se silnými a ohraničenými neaktuálními hodnotami.

- Pro daný typ operace zápisu, například INSERT, Replace, Upsert a DELETE, je propustnost zápisu pro jednotky požadavků stejná pro všechny úrovně konzistence.

## <a id="rto"></a>Úrovně konzistence a trvanlivost dat

V globálně distribuovaném databázovém prostředí existuje přímý vztah mezi úrovní konzistence a odolností s daty v oblasti výpadku v rámci oblasti. Při vývoji plánu provozní kontinuity musíte pochopit maximální přijatelnou dobu, než se aplikace kompletně obnoví po přerušení události. Čas potřebný k úplnému obnovení aplikace je známý jako **cíl doby obnovení** (**RTO**). Také je potřeba porozumět maximálnímu intervalu nedávných aktualizací dat, které může aplikace tolerovat při obnovování po přerušení události. Časové období aktualizací, které můžete chtít ztratit, se označuje jako **cíl bodu obnovení** (**RPO**).

Následující tabulka definuje vztah mezi modelem konzistence a odolností dat při výpadku oblasti v oblasti výpadku v síti. Je důležité si uvědomit, že v distribuovaném systému, a to i se silnou konzistencí, není možné mít distribuovanou databázi s cílem RPO a RTO nula z důvodu věta CAP. Další informace o tom, proč najdete [v tématu úrovně konzistence v Azure Cosmos DB](consistency-levels.md).

|**Oblast (y)**|**Režim replikace**|**Úroveň konzistence**|**OBNOVENÍ**|**RTO**|
|---------|---------|---------|---------|---------|
|1\. místo|Jedna nebo více hlavních serverů|Jakákoli úroveň konzistence|< 240 minut|< 1 týden|
|> 1|Jedna hlavní|Relace, konzistentní předpona, případný|< 15 minut|< 15 minut|
|> 1|Jedna hlavní|Omezená neaktuálnost|*K*  & *t*|< 15 minut|
|> 1|Jedna hlavní|Strong|0|< 15 minut|
|> 1|Vícenásobný hlavní|Relace, konzistentní předpona, případný|< 15 minut|0|
|> 1|Vícenásobný hlavní|Omezená neaktuálnost|*K*  & *t*|0|

*K* = počet verzí *"K"* (tj. aktualizace) položky.

*T* = časový interval *"t"* od poslední aktualizace.

## <a name="strong-consistency-and-multi-master"></a>Silná konzistence a vícenásobný hlavní

Účty Cosmos nakonfigurované pro sadu multi-Master nelze nakonfigurovat pro zajištění vysoké konzistence, protože distribuovaný systém nemůže poskytnout RPO s hodnotou 0 a RTO nula. Navíc neexistují žádné výhody latence zápisu pro použití silné konzistence s více hlavními servery, které by měly být replikovány do jakékoli oblasti a jsou potvrzeny do všech nakonfigurovaných oblastí v rámci daného účtu. Výsledkem je stejná latence zápisu jako jeden hlavní účet.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o globální distribuci a obecných kompromisech konzistence v distribuovaných systémech. Viz následující články:

- [Návrh moderních systémů distribuovaných databází – kompromisy v konzistenci](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Vysoká dostupnost](high-availability.md)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
