---
title: Dostupnost a výkon kompromisy pro různé úrovně konzistence ve službě Azure Cosmos DB
description: Dostupnost a výkon kompromisy pro různé úrovně konzistence ve službě Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 09777a9980e4576a5d00123516e33696e845dcac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65990225"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Kompromisy mezi konzistencí, dostupností a výkonem 

Distribuované databáze, které závisí na replikaci pro vysokou dostupnost a nízkou latencí musíte dělat kompromisy. Nevýhody jsou mezi konzistence čtení a dostupnosti, latence a propustnosti.

Azure Cosmos DB blíží konzistence dat jako celé spektrum od volby. Tento přístup zahrnuje více možností než dvěma extrémy silné a konečné konzistence. Můžete vybrat z pěti jasně definované modely na spektra konzistence. Od nejsilnější do nejslabší, modely jsou:

- *Silné*
- *Omezená neaktuálnost*
- *Relace*
- *Konzistentní předpona*
- *Konečný výsledek*

Každý model poskytuje dostupnost a výkon kompromisy a je zajištěná komplexní smlouvy SLA.

## <a name="consistency-levels-and-latency"></a>Úrovně konzistence a latence

Latence čtení pro všechny úrovně konzistence je vždycky zaručená být kratší než 10 milisekund na 99. percentilu. Latence čtení je zajištěná smlouva SLA. Průměrná latence na 50. percentil, čtení, je obvykle 2 MS nebo nižší. Účty služby Azure Cosmos span několik oblastí, jež jsou nakonfigurovány s silná konzistence se výjimky této záruky.

Latence zápisu pro všechny úrovně konzistence je vždycky zaručená být kratší než 10 milisekund na 99. percentilu. Latence zápisu je zajištěná smlouva SLA. Zápis průměrnou latenci, na 50. percentil, je obvykle 5 MS nebo nižší.

Pro účty služby Azure Cosmos nakonfigurovanou silná konzistence s více než jedné oblasti latence zápisu je zaručeno, že bude menší než dvěma časy odezvy doba mezi dvěma oblastmi nejvíce plus 10 milisekund na 99. percentilu.

Přesné čekací doba požadavku je funkce rychlostí světla vzdálenosti a topologii sítě Azure. Sítě Azure neposkytuje žádné latence smlouvy o úrovni služeb pro požadavku mezi všechny dvou oblastech Azure. Pro váš účet Azure Cosmos latenci replikace se zobrazují na webu Azure Portal. Na webu Azure portal (přejděte do okna metrik) můžete použít ke sledování latence replikace mezi různými oblastmi, které jsou přidružené k vašemu účtu Azure Cosmos.

## <a name="consistency-levels-and-throughput"></a>Úrovně konzistence a propustnosti

- Pro stejný počet jednotek žádostí zadejte asi dvakrát propustnost čtení při porovnání s silná, ohraničená odolnost relace, konzistentní Předpona a úrovně konzistence typu případné.

- Pro daný typ operace zápisu, jako jsou insert, replace, funkcí upsert a delete zápis propustnost za jednotky žádosti je shodné pro všechny úrovně konzistence.

## <a id="rto"></a>Konzistence úrovně a data odolnosti

V prostředí s globálně distribuovanou databázi je přímý vztah mezi konzistence odolnosti úroveň a dat za přítomnosti výpadku celé oblasti. Při vývoji plánu provozní kontinuity musíte pochopit maximální přijatelnou dobu, než úplného obnovení aplikace po ničivé události. Čas potřebný pro aplikaci k plnému obnovení se označuje jako **plánovaná doba obnovení** (**RTO**). Také musíte pochopit maximální období posledních aktualizací dat aplikace může tolerovat možnost ztráty při obnovení po ničivé události. Časové období aktualizací, které si může dovolit přijít o se označuje jako **cíl bodu obnovení** (**cíle bodu obnovení**).

Následující tabulka definuje vztahy mezi konzistence modelu a datům odolnosti za přítomnosti široké výpadku oblasti. Je důležité si uvědomit, že v distribuovaném systému i přes silná konzistence, není možné mít distribuované databáze s RPO a RTO nula kvůli věty. Další informace o tom, proč najdete v tématu [úrovně konzistence ve službě Azure Cosmos DB](consistency-levels.md).

|**Položky nakoupené**|**Režim replikace**|**Úrovně konzistence**|**CÍL BODU OBNOVENÍ**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Jednoho nebo několika hlavními databázemi|Všechny úrovně konzistence|< 240 minut|< 1 týden|
|>1|Single Master|Relace, konzistentní předpona, konečný výsledek|< 15 minut|< 15 minut|
|>1|Single Master|Omezená neaktuálnost|*K* & *T*|< 15 minut|
|>1|Single Master|Silné|0|< 15 minut|
|>1|Více hlavních databází|Relace, konzistentní předpona, konečný výsledek|< 15 minut|0|
|>1|Více hlavních databází|Omezená neaktuálnost|*K* & *T*|0|

*K* = počet *"K"* verze (například aktualizace) položky.

*T* = časový interval *"T"* od poslední aktualizace.

## <a name="next-steps"></a>Další postup

Další informace o globální distribuci a obecné konzistence kompromisů v distribuovaných systémech. Viz následující články:

- [Konzistence kompromisy v návrhu systémy moderní distribuované databáze](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Vysoká dostupnost](high-availability.md)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
