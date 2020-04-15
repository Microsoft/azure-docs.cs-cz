---
title: Konzistence, dostupnost a výkon azure cosmos db kompromisy
description: Kompromisy dostupnosti a výkonu pro různé úrovně konzistence v Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.reviewer: sngun
ms.openlocfilehash: 7cdaa9699b15000359c438bcc410e300415b759a
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81379960"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Kompromisy mezi konzistencí, dostupností a výkonem

Distribuované databáze, které se kvůli zajištění vysoké dostupnosti, nízké latence nebo obojího spoléhají na replikaci, musí dělat kompromisy. Tyto kompromisy jsou mezi konzistencí čtení a dostupností, latencí a propustností.

Azure Cosmos DB přistupuje k konzistenci dat jako spektrum možností. Tento přístup zahrnuje více možností než dva extrémy silné a konečné konzistence. Můžete si vybrat z pěti dobře definovaných úrovní na spektru konzistence. Od nejsilnější chudinky k nejslabším jsou úrovně:

- *Silné*
- *Ohraničená neaktuálnost*
- *Relace*
- *Konzistentní předpona*
- *Nahodilé*

Každá úroveň poskytuje informace o dostupnosti a výkonu a je podpořena komplexními smlouvymi SLA.

## <a name="consistency-levels-and-latency"></a>Úrovně konzistence a latence

Latence čtení pro všechny úrovně konzistence je vždy zaručena být menší než 10 milisekund na 99 percentilu. Tato latence čtení je zálohována sla. Průměrná latence čtení v 50 percentilu je obvykle 4 milisekundy nebo méně.

Latence zápisu pro všechny úrovně konzistence je vždy zaručena být menší než 10 milisekund na 99 percentilu. Tato latence zápisu je zálohována sla. Průměrná latence zápisu v 50 percentilu je obvykle 5 milisekund nebo méně. Azure Cosmos účty, které pokrývají několik oblastí a jsou nakonfigurované se silnou konzistencí jsou výjimkou z této záruky.

### <a name="write-latency-and-strong-consistency"></a>Latence zápisu a silná konzistence

U účtů Azure Cosmos nakonfigurovaných se silnou konzistencí s více než jednou oblastí se latence zápisu rovná dvakrát době odezvy (RTT) mezi některou ze dvou nejvzdálenějších oblastí plus 10 milisekund na 99 percentilu. Vysoká síť RTT mezi oblastmi se promítne do vyšší latence pro požadavky Cosmos DB, protože silná konzistence dokončí operaci pouze po zajištění, že byla potvrzena do všech oblastí v rámci účtu.

Přesná latence RTT je funkce rychlosti světla a topologie sítě Azure. Azure sítě neposkytuje žádné latence SLA pro RTT mezi žádné dvě oblasti Azure. Pro váš účet Azure Cosmos se latence replikace zobrazují na webu Azure Portal. Portál Azure (přejděte na okno Metriky, vyberte kartu Konzistence) můžete sledovat latence replikace mezi různými oblastmi, které jsou přidružené k vašemu účtu Azure Cosmos.

> [!IMPORTANT]
> Silná konzistence pro účty s oblastmi o rozloze více než 5000 mil (8000 kilometrů) je ve výchozím nastavení blokována kvůli vysoké latenci zápisu. Chcete-li tuto funkci povolit, obraťte se na podporu.

## <a name="consistency-levels-and-throughput"></a>Úrovně konzistence a propustnost

- Pro silné a ohraničené neaktuálnost čtení se provádí proti dvě repliky ve čtyřech replik sad (menšinové kvorum) poskytnout záruky konzistence. Relace, konzistentní předpona a případné čtení jedné repliky. Výsledkem je, že pro stejný počet jednotek požadavku je propustnost čtení pro silnou a ohraničenou neaktuálnost poloviční než ostatní úrovně konzistence.

- Pro daný typ operace zápisu, jako je například vložení, nahrazení, upsert a odstranění, je propustnost zápisu pro jednotky požadavku shodná pro všechny úrovně konzistence.

|**Úrovně konzistence**|**Čtení kvora**|**Zápisy kvora**|
|--|--|--|
|**Silné**|Místní menšina|Globální většina|
|**Omezená neaktuálnost**|Místní menšina|Místní většina|
|**Relace**|Jedna replika (pomocí tokenu relace)|Místní většina|
|**Konzistentní předpona**|Jedna replika|Místní většina|
|**Nahodilé**|Jedna replika|Místní většina|

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Úrovně konzistence a odolnost dat

V rámci globálně distribuované databázové prostředí existuje přímý vztah mezi úroveň konzistence a odolnost dat v přítomnosti výpadku celé oblasti. Při vývoji plánu kontinuity provozu je třeba pochopit maximální přijatelnou dobu, než se aplikace plně zotaví po rušivé události. Doba potřebná k úplnému obnovení aplikace se označuje jako **cíl doby obnovení** (**RTO**). Musíte také pochopit maximální dobu nedávných aktualizací dat, kterou aplikace může tolerovat ztrátu při obnovení po rušivé události. Časové období aktualizací, které si můžete dovolit ztratit, se označuje jako **cíl bodu obnovení** (**RPO**).

Níže uvedená tabulka definuje vztah mezi modelem konzistence a odolností dat v přítomnosti výpadku celé oblasti. Je důležité si uvědomit, že v distribuovaném systému, a to i se silnou konzistencí, není možné mít distribuovanou databázi s RPO a RTO nula kvůli Cap theorem. Další informace o tom, proč, najdete [v tématu úrovně konzistence v Azure Cosmos DB](consistency-levels.md).

|**Region (oblasti)**|**Režim replikace**|**Úroveň konzistence**|**Cíl bodu obnovení**|**Rto**|
|---------|---------|---------|---------|---------|
|1|Jeden nebo více hlavních|Jakákoli úroveň konzistence|< 240 minut|<1 týden|
|>1.|Jeden předloha|Relace, konzistentní předpona, eventuální|< 15 minut|< 15 minut|
|>1.|Jeden předloha|Omezená neaktuálnost|*K* & *T*|< 15 minut|
|>1.|Jeden předloha|Silné|0|< 15 minut|
|>1.|Vícehlavních|Relace, konzistentní předpona, eventuální|< 15 minut|0|
|>1.|Vícehlavních|Omezená neaktuálnost|*K* & *T*|0|

*K* = Počet verzí *"K"* (tj. aktualizace) položky.

*T* = Časový interval *"T"* od poslední aktualizace.

## <a name="strong-consistency-and-multi-master"></a>Silná konzistence a multimaster

Účty Cosmos nakonfigurované pro více hlavních částí nelze nakonfigurovat pro silnou konzistenci, protože není možné, aby distribuovaný systém poskytoval rpo nula a RTO nula. Navíc neexistují žádné výhody latence zápisu pro použití silné konzistence s více hlavních, protože jakýkoli zápis do libovolné oblasti musí být replikován a potvrzen do všech nakonfigurovaných oblastí v rámci účtu. Výsledkem je stejná latence zápisu jako jeden hlavní účet.

## <a name="next-steps"></a>Další kroky

Další informace o globální distribuci a obecných kompromisech konzistence v distribuovaných systémech. Viz následující články:

- [Kompromisy konzistence v moderním návrhu distribuovaných databázových systémů](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Vysoká dostupnost](high-availability.md)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
