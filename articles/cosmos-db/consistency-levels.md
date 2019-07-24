---
title: Úrovně konzistence ve službě Azure Cosmos DB
description: Azure Cosmos DB má pět úrovní konzistence umožňující zůstatek konečnou konzistencí, dostupností a latencí kompromisy.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/16/2019
ms.openlocfilehash: bb1fda48119785f5173790246f8069d3e11b6dae
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297746"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Úrovně konzistence ve službě Azure Cosmos DB

Distribuované databáze, které závisí na replikaci pro vysokou dostupnost, s nízkou latencí nebo obojí, ujistěte se, základní kompromis mezi konzistence čtení a dostupnosti, latence a propustnosti. Většina komerčně dostupných distribuovaných databází požádá vývojáře o výběr mezi dvěma extrémními modely konzistence: *silná* konzistence *a* konečná konzistence. Linearizability nebo model silné konzistence je Gold Standard pro programovatelnost dat. Ale přidává cenu vyšší latence (v ustáleném stavu) a omezenou dostupnost (během selhání). Na druhé straně, konečná konzistence nabízí vyšší dostupnost a lepší výkon, ale zpřístupňuje programové aplikace. 

Konzistence dat jako celé spektrum od volby místo dvěma extrémy přistupuje k Azure Cosmos DB. Silná konzistence a konečná konzistence jsou na koncích spektra, ale u spektra existuje mnoho voleb konzistence. Vývojáři můžou tyto možnosti využít k zajištění přesného výběru a rozlišení kompromisů s ohledem na vysokou dostupnost a výkon. 

Vývojáři můžou pomocí služby Azure Cosmos DB, zvolit z pěti jasně definované modely konzistence na spektra konzistence. Od nejsilnější až k odlehčení jsou mezi modely *silná*, *ohraničená neaktuálnost*, *relace*, *konzistentní předpona*a konečná konzistence. Modely jsou dobře definované a intuitivní a dají se použít pro konkrétní scénáře reálného světa. Každý model poskytuje [kompromisy k dostupnosti a výkonu](consistency-levels-tradeoffs.md) a je zajištěno SLA. Následující obrázek znázorňuje různé úrovně konzistence jako spektrum.

![Konzistence jako spektrum](./media/consistency-levels/five-consistency-levels.png)

Úrovně konzistence jsou nezávislá oblasti a jsou zaručené pro všechny operace bez ohledu na oblast, ze které jsou předávány čtení a zápisy, počet oblastí přidružených k vašemu účtu Azure Cosmos nebo zda je váš účet nakonfigurován s jedním nebo více oblastí zápisu.

## <a name="scope-of-the-read-consistency"></a>Rozsah konzistence čtení

Konzistence čtení platí pro jednu operaci čtení s rozsahem v rámci rozsahu klíč oddílu nebo logický oddíl. Operace čtení můžou být vystavené vzdáleného klienta nebo uložené procedury.

## <a name="configure-the-default-consistency-level"></a>Konfigurace výchozí úrovně konzistence

Kdykoli můžete nakonfigurovat výchozí úroveň konzistence na vašem účtu Azure Cosmos. Výchozí úroveň konzistence nakonfigurovaná na vašem účtu se vztahuje na všechny databáze a kontejnery Azure Cosmos pod tímto účtem. Všechny dotazy vydaný pro kontejner nebo databáze a čtení použít úroveň konzistence zadané ve výchozím nastavení. Další informace najdete v tématu Jak [nakonfigurovat výchozí úroveň konzistence](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Záruky související s úrovní konzistence

Komplexní smlouvy SLA k dispozici ve službě Azure Cosmos DB záruku, že splňují 100 procent požadavků na čtení záruce konzistence pro všechny úrovně konzistence, že kterou si zvolíte. Žádost o čtení splňuje konzistence smlouvy SLA, pokud jsou splněny všechny záruky konzistence spojené s úrovní konzistence. Přesné definice pěti úrovní konzistence v Azure Cosmos DB používání jazyka TLA + Specification jsou k dispozici v úložišti GitHub [Azure-Cosmos-tla](https://github.com/Azure/azure-cosmos-tla) .

Sémantika úrovně konzistence pět jsou popsány zde:

- **Silné**: Silná konzistence nabízí záruku linearizability. Linearizability odkazuje na obsluhu souběžných požadavků. Čtení má zaručeno vrátí nejnovější potvrzené verzi položky. Klient nikdy vidí nepotvrzených nebo částečné zápisu. Přečtěte si nejnovější potvrzené zápisu uživatele je vždycky zaručená.

- **Ohraničená neaktuálnost**: Pro čtení jsou zaručeny dodržování konzistence v souladu s konzistentní předponou. Čtení mohou prodlevy za zápisem do většiny *"K"* verzí (tj. "aktualizace") položky nebo časového intervalu *"T"* . Jinými slovy, pokud vyberete možnost ohraničená neaktuálnost, lze nakonfigurovat "zastaralost" dvěma způsoby: 

  * Počet verzí položky (*KB*)
  * Časový interval (*T*), kterým mohou číst prodlevy za zápisy 

  Omezená neaktuálnost nabízí celkový globální pořadí s výjimkou v rámci "neaktuálnost okno." Monotónní čtení záruky existovat v rámci oblasti uvnitř i mimo časový interval pro odolnost. Silná konzistence má stejnou sémantiku jako ta, která je nabízena pomocí ohraničené zastaralosti. Okno neaktuálnost je rovna hodnotě nula. Omezená neaktuálnost se také označuje jako doba zpoždění linearizovatelnosti. Když klient provádí operace čtení v rámci oblasti, která přijímá zápisy, záruky poskytnuté konzistencí neaktuálnosti jsou stejné jako záruky silnými konzistencí.

- **Relace**:  V rámci jediného čtení klientské relace jsou zaručené respektování konzistentní předpony (za předpokladu, že se předpokládá jedna "zapisovač"), monotónní čtení, monotónní zápisy, čtení a zápisy a záruky za psaní za čtení. Klientům mimo relaci, která provádí zápisy, se zobrazí konečná konzistence.

- **Konzistentní předpona**: Vrácené aktualizace obsahují předponu všech aktualizací bez mezer. Konzistentní předpony úrovně konzistence, které nemají nikdy vidět zápisy mimo pořadí.

- **Případné**: Pro čtení neexistuje záruka na řazení. Chybí dalším zápisům nakonec sloučí replik.

## <a name="consistency-levels-explained-through-baseball"></a>Úrovně konzistence je vysvětleno pomocí baseballu

Pojďme se na scénář herní baseballu jako příklad. Představte si posloupnost zápisů, které představují skóre z baseballu hru. Skóre inning inning řádku je popsána v [replikovat konzistence dat pomocí baseballu](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) papíru. V této hře hypotetické baseballu aktuálně provádí sedmého inning. Je sedmý – inning stretch. Návštěvníci mají za vás skóre 2 až 5, jak je znázorněno níže:

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Spuštění** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Návštěvníci** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **Domovská stránka** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Kontejner Azure Cosmos obsahuje součty spuštění pro návštěvníky a domácí týmy. Zatímco probíhá hry, různé čtení záruky může vést k čtení skóre, které se různé klienty. V následující tabulce jsou uvedeny úplnou sadu výsledků, které může být vrácen čtení návštěvníků a domácí skóre, které se s jednotlivými záruky konzistence pět. Skóre návštěvníci je uvedená jako první. Možné návratové hodnoty jsou oddělené čárkami.

| **Úrovně konzistence** | **Skóre (Návštěvníci, domů)** |
| - | - |
| **Silné** | 2 – 5 |
| **Omezená neaktuálnost** | Skóre, která jsou nejvýše 1 inning, jsou aktuální: 2-3, 2-4, 2-5 |
| **Relace** | <ul><li>Pro zapisovač: 2 – 5</li><li> Pro kohokoli kromě zapisovače: 0-0, 0-1, 0-2, 0 3, 0-4, 0-5, 1-0, 1-1, 1-2, 1 – 3, 1 – 4, 1-5, 2-0, 2-1, 2-2, 2-3, 2 – 4, 2 – 5</li><li>Po čtení 1-3: 1-3, 1-4, 1-5, 2-3, 2-4, 2-5</li> |
| **Konzistentní předpona** | 0-0, 0-1, 1-1, 1 – 2, 1 – 3, 2-3, 2 – 4, 2 – 5 |
| **Konečný výsledek** | 0-0, 0-1, 0-2, 0 3, 0-4, 0-5, 1-0, 1-1, 1-2, 1 – 3, 1 – 4, 1-5, 2-0, 2-1, 2-2, 2-3, 2 – 4, 2 – 5 |

## <a name="additional-reading"></a>Další čtení

Další informace o konceptech konzistence najdete v následujících článcích:

- [Základní specifikace TLA + pro úrovních pět konzistence nabízených databází Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Replikovaná Data konzistence je vysvětleno prostřednictvím baseballu (video) podle Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Replikovaná Data konzistence je vysvětleno prostřednictvím baseballu (dokument White Paper) podle Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Relace záruky pro replikovaná data slabě konzistentní vzhledem k aplikacím](https://dl.acm.org/citation.cfm?id=383631)
- [Návrh moderních distribuovaných databázových systémů – kompromisy pro konzistenci: CAP je jenom součástí scénáře.](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Omezená Neaktuálnost pravděpodobnostní (PBS) pro praktické částečné kvor](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Nakonec konzistentní – kdykoli znovu spustit.](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Další postup

Další informace o úrovních konzistence ve službě Azure Cosmos DB najdete v následujících článcích:

* [Vyberte úroveň konzistence správné pro vaši aplikaci](consistency-levels-choosing.md)
* [Úrovně konzistence napříč API služby Azure Cosmos DB](consistency-levels-across-apis.md)
* [Dostupnost a výkon kompromisy pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
* [Konfigurovat výchozí úroveň konzistence](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Přepsat výchozí úroveň konzistence](how-to-manage-consistency.md#override-the-default-consistency-level)

