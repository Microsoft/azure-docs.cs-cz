---
title: Úrovně konzistence ve službě Azure Cosmos DB
description: Azure Cosmos DB má pět úrovní konzistence umožňující zůstatek konečnou konzistencí, dostupností a latencí kompromisy.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: b5d9df7a0afa9b4270f0eff643e083e5bccfceb8
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78933661"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Úrovně konzistence ve službě Azure Cosmos DB

Distribuované databáze, které závisí na replikaci pro vysokou dostupnost, s nízkou latencí nebo obojí, ujistěte se, základní kompromis mezi konzistence čtení a dostupnosti, latence a propustnosti. Většina komerčně dostupných distribuovaných databází požádá vývojáře o výběr mezi dvěma extrémními modely konzistence: silná *konzistence a* konečná konzistence. Linearizability nebo model silné konzistence je Gold Standard pro programovatelnost dat. Ale přidává cenu vyšší latence (v ustáleném stavu) a omezenou dostupnost (během selhání). Na druhé straně, konečná konzistence nabízí vyšší dostupnost a lepší výkon, ale zpřístupňuje programové aplikace. 

Konzistence dat jako celé spektrum od volby místo dvěma extrémy přistupuje k Azure Cosmos DB. Silná konzistence a konečná konzistence jsou na koncích spektra, ale u spektra existuje mnoho voleb konzistence. Vývojáři můžou tyto možnosti využít k zajištění přesného výběru a rozlišení kompromisů s ohledem na vysokou dostupnost a výkon. 

Vývojáři můžou pomocí služby Azure Cosmos DB, zvolit z pěti jasně definované modely konzistence na spektra konzistence. Od nejsilnější až k odlehčení jsou mezi modely *silná*, *ohraničená neaktuálnost*, *relace*, *konzistentní předpona*a *konečná konzistence* . Modely jsou dobře definované a intuitivní a dají se použít pro konkrétní scénáře reálného světa. Každý model poskytuje [kompromisy k dostupnosti a výkonu](consistency-levels-tradeoffs.md) a je zajištěno SLA. Následující obrázek znázorňuje různé úrovně konzistence jako spektrum.

![Konzistence jako spektrum](./media/consistency-levels/five-consistency-levels.png)

Úrovně konzistence jsou nezávislá oblasti a jsou zaručené pro všechny operace bez ohledu na oblast, ze které jsou předávány čtení a zápisy, počet oblastí přidružených k vašemu účtu Azure Cosmos nebo zda je váš účet nakonfigurován s jedním nebo více oblastí zápisu.

## <a name="scope-of-the-read-consistency"></a>Rozsah konzistence čtení

Konzistence čtení platí pro jednu operaci čtení s rozsahem v rámci rozsahu klíč oddílu nebo logický oddíl. Operace čtení můžou být vystavené vzdáleného klienta nebo uložené procedury.

## <a name="configure-the-default-consistency-level"></a>Konfigurace výchozí úrovně konzistence

Kdykoli můžete nakonfigurovat výchozí úroveň konzistence na vašem účtu Azure Cosmos. Výchozí úroveň konzistence nakonfigurovaná na vašem účtu se vztahuje na všechny databáze a kontejnery Azure Cosmos pod tímto účtem. Všechny dotazy vydaný pro kontejner nebo databáze a čtení použít úroveň konzistence zadané ve výchozím nastavení. Další informace najdete v tématu Postup [Konfigurace výchozí úrovně konzistence](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Záruky související s úrovní konzistence

Komplexní smlouvy SLA k dispozici ve službě Azure Cosmos DB záruku, že splňují 100 procent požadavků na čtení záruce konzistence pro všechny úrovně konzistence, že kterou si zvolíte. Žádost o čtení splňuje konzistence smlouvy SLA, pokud jsou splněny všechny záruky konzistence spojené s úrovní konzistence. Přesné definice pěti úrovní konzistence v Azure Cosmos DB používání jazyka TLA + Specification jsou k dispozici v úložišti GitHub [Azure-Cosmos-tla](https://github.com/Azure/azure-cosmos-tla) .

Sémantika úrovně konzistence pět jsou popsány zde:

- **Strong**: silná konzistence nabízí záruku linearizability. Linearizability odkazuje na obsluhu souběžných požadavků. Čtení má zaručeno vrátí nejnovější potvrzené verzi položky. Klient nikdy vidí nepotvrzených nebo částečné zápisu. Přečtěte si nejnovější potvrzené zápisu uživatele je vždycky zaručená.

  Následující obrázek znázorňuje silnou konzistenci se hudebními poznámkami. Po zapsání dat do oblasti "Východní USA" se při čtení dat z jiných oblastí zobrazí nejnovější hodnota:

  ![video](media/consistency-levels/strong-consistency.gif)

- **Ohraničená neaktuálnost**: čtení jsou zaručena, aby se zaručila záruka konzistentní s předponou. Čtení mohou prodlevy za zápisem do maximální verze *"K"* (tj. "aktualizace") položky nebo časového intervalu *"T"* . Jinými slovy, pokud vyberete možnost ohraničená neaktuálnost, lze nakonfigurovat "zastaralost" dvěma způsoby: 

  * Počet verzí položky (*KB*)
  * Časový interval (*T*), kterým mohou číst prodlevy za zápisy 

  Omezená neaktuálnost nabízí celkový globální pořadí s výjimkou v rámci "neaktuálnost okno." Monotónní čtení záruky existovat v rámci oblasti uvnitř i mimo časový interval pro odolnost. Silná konzistence má stejnou sémantiku jako ta, která je nabízena pomocí ohraničené zastaralosti. Okno neaktuálnost je rovna hodnotě nula. Omezená neaktuálnost se také označuje jako doba zpoždění linearizovatelnosti. Když klient provádí operace čtení v rámci oblasti, která přijímá zápisy, záruky poskytnuté konzistencí neaktuálnosti jsou stejné jako záruky silnými konzistencí.

  Ohraničená neaktuálnost se často volí globálně distribuovanými aplikacemi, které očekávají nízkou latenci zápisu, ale vyžadují celkovou záruku globální objednávky. Ohraničená neaktuálnost je ideální pro aplikace, které nabízí spolupráci skupin a sdílení, burzovní, doplňování a publikování a zařazování do fronty atd. Následující obrázek znázorňuje konzistenci s ohraničenou neaktuálností pomocí hudebních poznámek. Po zapsání dat do oblasti "Východní USA" přečtou oblasti "Západní USA" a "Austrálie – východ" písemnou hodnotu na základě nakonfigurovaného maximálního času prodlevy nebo maximálního počtu operací:

  ![video](media/consistency-levels/bounded-staleness-consistency.gif)

- **Relace**: v rámci jediného čtení klientské relace jsou zaručené respektování konzistentní předpony (za předpokladu jediné "zapisovače" relace), monotónní čtení, monotónní zápisu, čtení a zápisů a záruky za čtení. Klientům mimo relaci, která provádí zápisy, se zobrazí konečná konzistence.

  Konzistence relace je široce využitá úroveň konzistence pro jednu oblast i pro globálně distribuované aplikace. Poskytuje latence zápisu, dostupnost a propustnost čtení srovnatelné s tím, že má konečnou konzistenci, ale také poskytuje záruky konzistence, které vyhovují potřebám aplikací zapsaných v kontextu uživatele. Následující obrázek znázorňuje konzistenci relace se hudebními poznámkami. Oblast "Západní USA" a "Východní USA" používají stejnou relaci (relace A), aby obě data četly současně. Vzhledem k tomu, že oblast Austrálie – východ používá "relaci B", získá data později, ale ve stejném pořadí jako zápisy.

  ![video](media/consistency-levels/session-consistency.gif)

- **Konzistentní předpona**: vrácené aktualizace obsahují předponu všech aktualizací bez mezer. Konzistentní předpona úrovně konzistence, kterou čtení nikdy nevidí zápisy mimo pořadí.

  Pokud se zápisy prováděly v pořadí `A, B, C`, klient uvidí buď `A`, `A,B`, nebo `A,B,C`, ale nikdy nefunguje jinak, jako `A,C` nebo `B,A,C`. Konzistentní předpona poskytuje latence zápisu, dostupnost a propustnost čtení srovnatelné s tím, že má konečnou konzistenci, ale také poskytuje pořadí záruk, které vyhovuje potřebám scénářů, ve kterých je pořadí důležité. Následující obrázek znázorňuje konzistenci předpon konzistence se hudebními poznámkami. Ve všech oblastech čtení nikdy nevidí zápisy mimo pořadí:

  ![video](media/consistency-levels/consistent-prefix.gif)

- Kdy **: neexistuje**záruka na řazení pro čtení. Chybí dalším zápisům nakonec sloučí replik.  
Konečná konzistence představuje slabší formu konzistence, protože klient může číst hodnoty, které jsou starší než ty, které se předtím četly. Konečná konzistence je ideální, pokud aplikace nevyžaduje žádné záruky na řazení. Mezi příklady patří počet re, podobně jako u jiných než vlákenných komentářů. Následující obrázek znázorňuje konečnou konzistenci se hudebními poznámkami.

  ![video](media/consistency-levels/eventual-consistency.gif)

## <a name="additional-reading"></a>Další čtení

Další informace o konceptech konzistence najdete v následujících článcích:

- [Vysoké úrovně TLA + specifikace pro pět úrovní konzistence nabízených službou Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Vysvětlení konzistence replikovaných dat prostřednictvím Doug Terryu (video)](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Vysvětlení konzistence replikovaných dat vysvětlených prostřednictvím baseballu (dokument White Paper) Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Záruka na relaci pro slabě konzistentní replikovaná data](https://dl.acm.org/citation.cfm?id=383631)
- [Kompromisy konzistence v moderních systémech Distributed Database System design: CAP je jenom součástí tohoto scénáře.](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Pravděpodobnostníá neaktuálnost (PBS) pro praktické částečné kvora](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Nakonec konzistentní – znovu navštěvované](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Další kroky

Další informace o úrovních konzistence ve službě Azure Cosmos DB najdete v následujících článcích:

* [Volba správné úrovně konzistence pro vaši aplikaci](consistency-levels-choosing.md)
* [Úrovně konzistence napříč Azure Cosmos DBmi rozhraními API](consistency-levels-across-apis.md)
* [Kompromisy týkající se dostupnosti a výkonu pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
* [Konfigurace výchozí úrovně konzistence](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Přepsat výchozí úroveň konzistence](how-to-manage-consistency.md#override-the-default-consistency-level)

