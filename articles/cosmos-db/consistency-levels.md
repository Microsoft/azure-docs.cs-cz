---
title: Úrovně konzistence ve službě Azure Cosmos DB
description: Azure Cosmos DB má pět úrovní konzistence, které vám pomůžou vyvážit případné kompromisy v konzistenci, dostupnosti a latenci.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 8f482c4fe6817c75079ceb98e981c846c395ad13
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396021"
---
# <a name="what-are-consistency-levels-in-azure-cosmos-db"></a>Co jsou úrovně konzistence v Azure Cosmos DB?

Distribuované databáze, které využívají replikaci pro vysokou dostupnost, nízkou latenci nebo obojí, provedou základní kompromisy mezi konzistencí čtení a dostupností, latencí a propustností. Většina komerčně dostupných distribuovaných databází požádá vývojáře o výběr mezi dvěma extrémními modely konzistence: silná *konzistence a* konečná konzistence. *strong* Linearizability modelu silné konzistence je Gold Standard pro programovatelnost dat. Ale přidává cenu vyšší latence zápisu (v ustáleném stavu) a omezenou dostupnost (během selhání). Na druhé straně, konečná konzistence nabízí vyšší dostupnost a lepší výkon, ale zpřístupňuje programové aplikace.

Azure Cosmos DB přistupuje k konzistenci dat jako spektrum možností místo dvou extrémních hodnot. Vývojáři můžou tyto možnosti využít k zajištění přesného výběru a rozlišení kompromisů s ohledem na vysokou dostupnost a výkon.

S Azure Cosmos DB můžou vývojáři vybrat z pěti jasně definovaných úrovní konzistence v rámci spektra konzistence. Mezi tyto úrovně patří *silná*, *ohraničená neaktuálnost*, *relace*, *konzistentní předpona*a konečná *konzistence* . Úrovně jsou jasně definované a intuitivní a dají se použít pro konkrétní scénáře reálného světa. Každá úroveň poskytuje [kompromisy k dostupnosti a výkonu](consistency-levels-tradeoffs.md) a jsou zajištěny SLA. Následující obrázek znázorňuje různé úrovně konzistence jako spektrum.

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="Konzistence jako spektrum" border="false" :::

Úrovně konzistence jsou nezávislá oblasti a jsou zaručené pro všechny operace bez ohledu na oblast, ze které jsou předávány čtení a zápisy, počet oblastí přidružených k vašemu účtu Azure Cosmos nebo zda je váš účet nakonfigurován s jednou nebo více oblastmi zápisu.

## <a name="scope-of-the-read-consistency"></a>Rozsah konzistence čtení

Konzistence čtení se vztahuje na jeden obor operace čtení v rámci logického oddílu. Operaci čtení je možné vystavit pomocí vzdáleného klienta nebo uložené procedury.

## <a name="configure-the-default-consistency-level"></a>Konfigurace výchozí úrovně konzistence

Výchozí úroveň konzistence můžete v účtu Azure Cosmos nakonfigurovat kdykoli. Výchozí úroveň konzistence nakonfigurovaná na vašem účtu se vztahuje na všechny databáze a kontejnery Azure Cosmos pod tímto účtem. Všechny čtení a dotazy vydané pro kontejner nebo databázi používají ve výchozím nastavení zadanou úroveň konzistence. Další informace najdete v tématu Postup [Konfigurace výchozí úrovně konzistence](how-to-manage-consistency.md#configure-the-default-consistency-level). U konkrétního požadavku můžete také přepsat výchozí úroveň konzistence. Další informace najdete v článku postup [přepsání výchozí úrovně konzistence](how-to-manage-consistency.md?#override-the-default-consistency-level) .

## <a name="guarantees-associated-with-consistency-levels"></a>Záruky spojené s úrovněmi konzistence

Komplexní SLA poskytované Azure Cosmos DB zaručují, že 100 procento požadavků na čtení vyhovuje záruku konzistence pro jakoukoli zvolenou úroveň konzistence. Požadavek na čtení splňuje podmínky SLA konzistence, pokud jsou splněné veškeré záruky konzistence spojené s úrovní konzistence. Přesné definice pěti úrovní konzistence v Azure Cosmos DB používání jazyka TLA + Specification jsou k dispozici v úložišti GitHub [Azure-Cosmos-tla](https://github.com/Azure/azure-cosmos-tla) .

Sémantika pěti úrovní konzistence je popsána zde:

- **Strong**: silná konzistence nabízí záruku linearizability. Linearizability odkazuje na obsluhu souběžných požadavků. U čtení je zaručeno, že vrátí nejnovější potvrzenou verzi položky. Klient nikdy nevidí nepotvrzené nebo částečné zápisy. Uživatelům se vždycky ručí, že si přečtou poslední potvrzený zápis.

  Následující obrázek znázorňuje silnou konzistenci se hudebními poznámkami. Po zapsání dat do oblasti "Západní USA 2" se při čtení dat z jiných oblastí zobrazí nejnovější hodnota:

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="video":::

- **Ohraničená neaktuálnost**: čtení jsou zaručena, aby se zaručila záruka konzistentní s předponou. Čtení můžou na konci zápisu zajímat maximálně *"K"* verzí (to znamená "aktualizace") položky nebo časového intervalu *"T"* , podle toho, co je dosaženo jako první. Jinými slovy, pokud vyberete možnost ohraničená neaktuálnost, lze nakonfigurovat "zastaralost" dvěma způsoby:

- Počet verzí položky (*KB*)
- Časový interval (*T*), kterým mohou číst prodlevy za zápisy

Ohraničená neaktuálnost nabízí celkové globální pořadí mimo "okno pro zastaralost". Když klient provádí operace čtení v rámci oblasti, která přijímá zápisy, záruky poskytnuté konzistencí neaktuálnosti jsou stejné jako záruky silnými konzistencí.

V rámci okna zastaralost poskytuje ohraničená neaktuálnost následující záruky konzistence:

- Konzistence klientů ve stejné oblasti pro jeden hlavní účet = silná
- Konzistence klientů v různých oblastech pro jeden hlavní účet = konzistentní předpona
- Konzistence pro klienty zapisujících do jedné oblasti pro vícenásobný hlavní účet = konzistentní předpona
- Konzistence pro klienty zapisujících do různých oblastí pro více hlavních účtů = případné

  Ohraničená neaktuálnost se často volí globálně distribuovanými aplikacemi, které očekávají nízkou latenci zápisu, ale vyžadují celkovou záruku globální objednávky. Ohraničená neaktuálnost je ideální pro aplikace, které nabízí spolupráci skupin a sdílení, burzovní, doplňování a publikování a zařazování do fronty atd. Následující obrázek znázorňuje konzistenci s ohraničenou neaktuálností pomocí hudebních poznámek. Po zapsání dat do oblasti "Západní USA 2" přečtou oblasti "Východní USA 2" a "Austrálie – východ" písemnou hodnotu na základě nakonfigurovaného maximálního času prodlevy nebo maximálního počtu operací:

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="video":::

- **Relace**: v rámci jediného čtení klientské relace jsou zaručené respektování konzistentní předpony, monotónní čtení, monotónní zápisu, čtení a zápisů a záruky za zápis. Předpokládá se jedna relace "zapisovače" nebo sdílení tokenu relace pro více modulů pro zápis.

Klientům mimo relaci, která provádí zápis, se zobrazí následující záruky:

- Konzistence pro klienty ve stejné oblasti pro jeden hlavní účet = konzistentní předpona
- Konzistence klientů v různých oblastech pro jeden hlavní účet = konzistentní předpona
- Konzistence pro klienty zapisujících do jedné oblasti pro vícenásobný hlavní účet = konzistentní předpona
- Konzistence klientů zapisujících do více oblastí pro více hlavních účtů = případné

  Konzistence relací je nejčastěji používaná úroveň konzistence pro jednu oblast i pro globálně distribuované aplikace. Poskytuje latence zápisu, dostupnost a propustnost čtení srovnatelné s tím, že má konečnou konzistenci, ale také poskytuje záruky konzistence, které vyhovují potřebám aplikací zapsaných v kontextu uživatele. Následující obrázek znázorňuje konzistenci relace se hudebními poznámkami. "Západní USA 2 Writer" a "Západní USA 2 Reader" používají stejnou relaci (relaci A), aby obě současně četly stejná data. Vzhledem k tomu, že oblast Austrálie – východ používá "relaci B", získá data později, ale ve stejném pořadí jako zápisy.

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="video":::

- **Konzistentní předpona**: vrácené aktualizace obsahují předponu všech aktualizací bez mezer. Konzistentní předpony úrovně konzistence, které nemají nikdy vidět zápisy mimo pořadí.

Pokud byla zápisy provedena v pořadí `A, B, C` , klient uvidí buď `A` , `A,B` nebo `A,B,C` , ale nikdy mimo pořadí, například `A,C` nebo `B,A,C` . Konzistentní předpona poskytuje latence zápisu, dostupnost a propustnost čtení srovnatelné s tím, že má konečnou konzistenci, ale také poskytuje pořadí záruk, které vyhovuje potřebám scénářů, ve kterých je pořadí důležité. 

Níže jsou uvedené záruky konzistence pro konzistentní předpony:

- Konzistence pro klienty ve stejné oblasti pro jeden hlavní účet = konzistentní předpona
- Konzistence klientů v různých oblastech pro jeden hlavní účet = konzistentní předpona
- Konzistence pro klienty zapisujících do jedné oblasti pro vícenásobný hlavní účet = konzistentní předpona
- Konzistence klientů zapisujících do více oblastí pro více hlavních účtů = případné

Následující obrázek znázorňuje konzistenci předpon konzistence se hudebními poznámkami. Ve všech oblastech čtení nikdy nevidí zápisy mimo pořadí:

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="video":::

- Kdy **: neexistuje**záruka na řazení pro čtení. Pokud nedojde k žádným dalším operacím zápisu, repliky se nakonec konvergují.  
Konečná konzistence představuje slabší formu konzistence, protože klient může číst hodnoty, které jsou starší než ty, které se předtím četly. Konečná konzistence je ideální, pokud aplikace nevyžaduje žádné záruky na řazení. Mezi příklady patří počet re, podobně jako u jiných než vlákenných komentářů. Následující obrázek znázorňuje konečnou konzistenci se hudebními poznámkami.

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="video":::

## <a name="additional-reading"></a>Další materiály ke čtení

Další informace o konceptech konzistence najdete v následujících článcích:

- [Vysoké úrovně TLA + specifikace pro pět úrovní konzistence nabízených službou Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Vysvětlení konzistence replikovaných dat prostřednictvím Doug Terryu (video)](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Vysvětlení konzistence replikovaných dat vysvětlených prostřednictvím baseballu (dokument White Paper) Doug Terry](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [Záruka na relaci pro slabě konzistentní replikovaná data](https://dl.acm.org/citation.cfm?id=383631)
- [Kompromisy konzistence v moderních systémech Distributed Database System design: CAP je jenom součástí tohoto scénáře.](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Pravděpodobnostníá neaktuálnost (PBS) pro praktické částečné kvora](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Nakonec konzistentní – znovu navštěvované](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Další kroky

Další informace o úrovních konzistence v Azure Cosmos DB najdete v následujících článcích:

- [Volba správné úrovně konzistence pro vaši aplikaci](consistency-levels-choosing.md)
- [Úrovně konzistence napříč Azure Cosmos DBmi rozhraními API](consistency-levels-across-apis.md)
- [Kompromisy týkající se dostupnosti a výkonu pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
- [Konfigurace výchozí úrovně konzistence](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Přepsání výchozí úrovně konzistence](how-to-manage-consistency.md#override-the-default-consistency-level)
