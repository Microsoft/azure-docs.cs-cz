---
title: Konzistence, dostupnost a výkon azure cosmos db kompromisy
description: Kompromisy dostupnosti a výkonu pro různé úrovně konzistence v Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: a16acfc8f9be820e9cc9b3bd59d6675b7f75d2ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445552"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Kompromisy mezi konzistencí, dostupností a výkonem 

Distribuované databáze, které se kvůli zajištění vysoké dostupnosti, nízké latence nebo obojího spoléhají na replikaci, musí dělat kompromisy. Tyto kompromisy jsou mezi konzistencí čtení a dostupností, latencí a propustností.

Azure Cosmos DB přistupuje k konzistenci dat jako spektrum možností. Tento přístup zahrnuje více možností než dva extrémy silné a konečné konzistence. Můžete si vybrat z pěti dobře definovaných modelů na spektru konzistence. Od nejsilnějších po nejslabší, modely jsou:

- *Silné*
- *Ohraničená neaktuálnost*
- *Relace*
- *Konzistentní předpona*
- *Nahodilé*

Každý model poskytuje informace o dostupnosti a výkonu a je podpořen komplexními smlouvymi SLA.

## <a name="consistency-levels-and-latency"></a>Úrovně konzistence a latence

Latence čtení pro všechny úrovně konzistence je vždy zaručena být menší než 10 milisekund na 99 percentilu. Tato latence čtení je zálohována sla. Průměrná latence čtení v 50 percentilu je obvykle 2 milisekundy nebo méně. Azure Cosmos účty, které pokrývají několik oblastí a jsou nakonfigurované se silnou konzistencí jsou výjimkou z této záruky.

Latence zápisu pro všechny úrovně konzistence je vždy zaručena být menší než 10 milisekund na 99 percentilu. Tato latence zápisu je zálohována sla. Průměrná latence zápisu v 50 percentilu je obvykle 5 milisekund nebo méně.

U účtů Azure Cosmos nakonfigurovaných se silnou konzistencí s více než jednou oblastí je zaručena latence zápisu menší než dvakrát doba odezvy (RTT) mezi některou ze dvou nejvzdálenějších oblastí plus 10 milisekund na 99 percentilu.

Přesná latence RTT je funkce rychlosti světla a topologie sítě Azure. Azure sítě neposkytuje žádné latence SLA pro RTT mezi žádné dvě oblasti Azure. Pro váš účet Azure Cosmos se latence replikace zobrazují na webu Azure Portal. Portál Azure (přejděte na okno Metriky) můžete použít ke sledování latencí replikace mezi různými oblastmi, které jsou přidružené k vašemu účtu Azure Cosmos.

## <a name="consistency-levels-and-throughput"></a>Úrovně konzistence a propustnost

- Pro stejný počet jednotek požadavku poskytuje relace, konzistentní předpona a případné úrovně konzistence přibližně dvakrát propustnost čtení ve srovnání se silnou a ohraničenou neaktuálností.

- Pro daný typ operace zápisu, jako je například vložení, nahrazení, upsert a odstranění, je propustnost zápisu pro jednotky požadavku shodná pro všechny úrovně konzistence.

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
