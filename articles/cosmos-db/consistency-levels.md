---
title: Úrovně konzistence ve službě Azure Cosmos DB
description: Azure Cosmos DB má pět úrovní konzistence umožňující zůstatek konečnou konzistencí, dostupností a latencí kompromisy.
keywords: konzistence typu případné, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/27/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 075f79707dda4683c288163ec7bfdbf9807e153f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834863"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Úrovně konzistence ve službě Azure Cosmos DB

Distribuované databáze, které závisí na replikaci pro vysokou dostupnost, s nízkou latencí nebo obojí, ujistěte se, základní kompromis mezi konzistence čtení a dostupnosti, latence a propustnosti. Většina komerčně dostupný distribuovaných databází požádejte vývojáři zvolit mezi těmito dvěma modely extreme konzistence: silnou konzistenci a konečné konzistence.  [Linearizovatelnosti](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) nebo model pro zajištění konzistence je zlatým standardem programovatelnosti data. Ale přidá strmá cenu vyšší latence (v stabilního stavu) a nižší dostupnost (při selhání). Na druhé straně konečné konzistence nabízí vyšší dostupnost a lepší výkon, ale je obtížné, jak programovat aplikace. 

Konzistence dat jako celé spektrum od volby místo dvěma extrémy přistupuje k Azure Cosmos DB. Silná konzistence a konečné konzistence jsou na obou koncích, ale existuje mnoho možností konzistence podél spektra. Mohou vývojáři tyto možnosti volby přesné a detailní kompromisy s ohledem na vysokou dostupnost nebo výkon. 

Vývojáři můžou pomocí služby Azure Cosmos DB, zvolit z pěti jasně definované modely konzistence na spektra konzistence. Od nejsilnější do nejslabší, modely jsou silná, omezená neaktuálnost, relace, konzistentní Předpona a konečný výsledek. Tyto modely jsou dobře definovaný a intuitivní. Je možné použít u konkrétních scénářů reálného světa. Každý model poskytuje [dostupnost a výkon kompromisy](consistency-levels-tradeoffs.md) a je zajištěná komplexní smlouvy SLA. Následující obrázek ukazuje různé úrovním jako škála možností.

![Konzistence jako spektrum](./media/consistency-levels/five-consistency-levels.png)

Úrovně konzistence jsou nezávislá na oblast. Úroveň konzistence vašeho účtu Azure Cosmos je zaručeno, že všechny operace bez ohledu na oblast, ze kterého operací čtení a zápisu jsou poskytovány, počet oblastí přidružených k účtu Azure Cosmos, čtení nebo na to, jestli váš účet je nakonfigurovaný s jeden nebo více oblastí zápisu.

## <a name="scope-of-the-read-consistency"></a>Rozsah konzistence čtení

Konzistence čtení platí pro jednu operaci čtení s rozsahem v rámci rozsahu klíč oddílu nebo logický oddíl. Operace čtení můžou být vystavené vzdáleného klienta nebo uložené procedury.

## <a name="configure-the-default-consistency-level"></a>Konfigurace výchozí úrovně konzistence

Kdykoli můžete nakonfigurovat výchozí úroveň konzistence na vašem účtu Azure Cosmos. Výchozí úroveň konzistence nakonfigurovaný na vašem účtu se vztahuje na všechny databáze Azure Cosmos DB a kontejnery pod tímto účtem. Všechny dotazy vydaný pro kontejner nebo databáze a čtení použít úroveň konzistence zadané ve výchozím nastavení. Další informace najdete v tématu Jak [nakonfigurovat výchozí úroveň konzistence](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Záruky související s úrovní konzistence

Komplexní smlouvy SLA k dispozici ve službě Azure Cosmos DB záruku, že splňují 100 procent požadavků na čtení záruce konzistence pro všechny úrovně konzistence, že kterou si zvolíte. Žádost o čtení splňuje konzistence smlouvy SLA, pokud jsou splněny všechny záruky konzistence spojené s úrovní konzistence. Přesné definice úrovní pět konzistence ve službě Azure Cosmos DB s použitím [TLA + specifikačnímu jazyku](http://lamport.azurewebsites.net/tla/tla.html) jsou součástí [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) úložiště GitHub. 

Sémantika úrovně konzistence pět jsou popsány zde:

- **Silná**: nabízí silnou konzistenci [linearizovatelnosti](https://aphyr.com/posts/313-strong-consistency-models) zaručit. Čtení má zaručeno vrátí nejnovější potvrzené verzi položky. Klient nikdy vidí nepotvrzených nebo částečné zápisu. Přečtěte si nejnovější potvrzené zápisu uživatele je vždycky zaručená.

- **Omezená neaktuálnost**: čtení má zaručeno případném dalším sdílení dodržovat záruku konzistentní předpony. Může prodleva čtení zápisů maximálně verzemi "K" (které je "aktualizací") položky nebo "t" časovém intervalu. Pokud si zvolíte omezená neaktuálnost, "neaktuálnost" můžete nakonfigurovat dvěma způsoby: 

  * Počet verzí (kB)
  * Časový interval (t), podle kterého může prodleva čtení zápisů 

  Omezená neaktuálnost nabízí celkový globální pořadí s výjimkou v rámci "neaktuálnost okno." Monotónní čtení záruky existovat v rámci oblasti uvnitř i mimo časový interval pro odolnost. Silná konzistence má stejnou sémantiku jako ty, které nabízí omezená neaktuálnost. Okno neaktuálnost je rovna hodnotě nula. Omezená neaktuálnost se také označuje jako doba zpoždění linearizovatelnosti. Když klient provádí operace čtení v oblasti, která přijímá zápisy, záruky poskytované konzistenci omezená neaktuálnost jsou stejné jako záruk se silnou konzistenci.

- **Relace**: čtení má zaručeno případném dalším sdílení dodržovat konzistentní předpona (za předpokladu, že jeden "zapisovač" relace), monotónní čtení, monotónní zápisy, čtení zápisů a zápis čtení záruky. Konzistence typu relace je vymezen relace klienta.

- **Konzistentní předpona**: aktualizace, které jsou vráceny obsahují předponou všech aktualizací bez mezer. Konzistentní předpona zaručuje, že čtení nikdy neuvidí zápisy mimo pořadí.

- **Konečný výsledek**: není zaručeno pořadí pro čtení. Chybí dalším zápisům nakonec sloučí replik.

## <a name="consistency-levels-explained-through-baseball"></a>Úrovně konzistence je vysvětleno pomocí baseballu

Pojďme se na scénář herní baseballu jako příklad. Představte si posloupnost zápisů, které představují skóre z baseballu hru. Skóre inning inning řádku je popsána v [replikovat konzistence dat pomocí baseballu](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) papíru. V této hře hypotetické baseballu aktuálně provádí sedmého inning. Je sedmý – inning stretch. Návštěvníkům jsou za se skóre 2 až 5.

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Spuštění** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Návštěvníci** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **Domovská stránka** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Kontejner služby Azure Cosmos DB obsahuje návštěvníci a domácí týmu spustit celkový počet položek. Zatímco probíhá hry, různé čtení záruky může vést k čtení skóre, které se různé klienty. V následující tabulce jsou uvedeny úplnou sadu výsledků, které může být vrácen čtení návštěvníků a domácí skóre, které se s jednotlivými záruky konzistence pět. Skóre návštěvníci je uvedená jako první. Možné návratové hodnoty jsou oddělené čárkami.

| **Úrovně konzistence** | **Skóre** |
| - | - |
| **Silné** | 2 – 5 |
| **Omezená neaktuálnost** | Skóre, které jsou maximálně jeden inning zastaralá: 2-3, 2 – 4, 2 – 5 |
| **Relace** | <ul><li>Pro modul pro zápis: 2 – 5</li><li> pro všechny, kteří zapisovač: 0-0, 0-1, 0-2, 0 3, 0-4, 0-5, 1 – 0, 1-1, 1 – 2, 1 – 3, 1 – 4, 1-5, 2-0, 2-1, 2-2, 2-3, 2 – 4, 2 – 5</li><li>Po přečtení 1-3: 1-3, 1 – 4, 1-5, 2-3, 2 – 4, 2 – 5</li> |
| **Konzistentní předpona** | 0-0, 0-1, 1-1, 1 – 2, 1 – 3, 2-3, 2 – 4, 2 – 5 |
| **Konečný výsledek** | 0-0, 0-1, 0-2, 0 3, 0-4, 0-5, 1-0, 1-1, 1-2, 1 – 3, 1 – 4, 1-5, 2-0, 2-1, 2-2, 2-3, 2 – 4, 2 – 5 |

## <a name="additional-reading"></a>Další čtení

Další informace o konceptech konzistence najdete v následujících článcích:

- [Základní specifikace TLA + pro úrovních pět konzistence nabízených databází Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Replikovaná Data konzistence je vysvětleno prostřednictvím baseballu (video) podle Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Replikovaná Data konzistence je vysvětleno prostřednictvím baseballu (dokument White Paper) podle Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Relace záruky pro replikovaná data slabě konzistentní vzhledem k aplikacím](https://dl.acm.org/citation.cfm?id=383631)
- [Konzistence kompromisy moderní systémy distribuované databáze-návrhu: limit je pouze část textu](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
- [Omezená Neaktuálnost pravděpodobnostní (PBS) pro praktické částečné kvor](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Nakonec konzistentní – kdykoli znovu spustit.](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Další postup

Další informace o úrovních konzistence ve službě Azure Cosmos DB najdete v následujících článcích:

* [Vyberte úroveň konzistence správné pro vaši aplikaci](consistency-levels-choosing.md)
* [Úrovně konzistence napříč API služby Azure Cosmos DB](consistency-levels-across-apis.md)
* [Dostupnost a výkon kompromisy pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
* [Konfigurovat výchozí úroveň konzistence](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Přepsat výchozí úroveň konzistence](how-to-manage-consistency.md#override-the-default-consistency-level)

