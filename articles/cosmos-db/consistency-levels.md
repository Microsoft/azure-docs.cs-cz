---
title: Úrovně konzistence ve službě Azure Cosmos DB | Dokumentace Microsoftu
description: Azure Cosmos DB má pět úrovní konzistence umožňující zůstatek konečnou konzistencí, dostupností a latencí kompromisy.
keywords: konzistence typu případné, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: andrl
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5cb439f7fe6461fcef0d010535179e16e28c294a
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239165"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Úrovně konzistence ve službě Azure Cosmos DB

Distribuované databáze spoléhat na replikaci pro vysokou dostupnost, s nízkou latencí nebo obojí, ujistěte se, základní kompromis mezi konzistence čtení a dostupnosti, latence a propustnosti. Většina komerčně dostupný distribuovaných databází požádejte vývojáři zvolit mezi těmito dvěma modely extreme konzistence: silnou konzistenci a konečné konzistence. Zatímco [linearizovatelnosti](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) nebo model pro zajištění konzistence je zlatým standardem programovatelnosti data, obsahuje strmá cenu vyšší latence (v stabilního stavu) a nižší dostupnost (při selhání). Na druhé straně konečné konzistence nabízí vyšší dostupnost a lepší výkon, ale je velmi obtížné naprogramovat oproti.

Cosmos DB blíží konzistence dat jako celé spektrum od volby místo dvou extrémů. Silná konzistence a konečné konzistence jsou dva elementy end třídy spektra, existuje mnoho voleb konzistence podél spektra konzistence. Tyto možnosti konzistence umožňují vývojářům přesné volby a kompromisy rozlišovací schopnosti s ohledem na vysokou dostupnost nebo výkon. Cosmos DB umožňuje vývojářům vybrat mezi pět jasně definované modely konzistence ze spektra konzistence (nejsilnější do nejslabší) – **silné**, **omezená neaktuálnost**, **relace** , **konzistentní předpona**, a **konečné**. Každá z těchto modelů konzistence je dobře definovaný a intuitivní a je možné u konkrétních scénářů reálného světa. Každá z pět modelů konzistence poskytnout jasné dostupnost a výkon kompromisy a je zajištěná komplexní smlouvy SLA.

![Konzistence je škála možností](./media/consistency-levels/five-consistency-levels.png)
**je škála možností konzistence**

Všimněte si, že jsou úrovně konzistence nezávislých na oblasti. Konzistence úroveň (a odpovídající záruky konzistence) účtu služby Cosmos DB je zaručeno, že pro všechny operace čtení bez ohledu na to, následující:

- Oblast, ze které se obsluhují čtení a zápisy
- Počet oblastí přidružených k účtu Cosmos
- Určuje, zda váš účet je nakonfigurovaný pomocí jednoho nebo více oblastí zápisu

## <a name="scope-of-the-read-consistency"></a>Rozsah konzistence čtení

Konzistence čtení se vztahuje na jeden obor rozsahu klíč oddílu (označuje se také jako logický oddíl) operace čtení. Operace čtení můžou být vystavené vzdáleného klienta nebo uložené procedury.

## <a name="configuring-the-default-consistency-level"></a>Konfigurace výchozí úroveň konzistence

Můžete nakonfigurovat **výchozí úroveň konzistence** na účtu služby Cosmos DB v každém okamžiku. Výchozí úroveň konzistence nakonfigurovaný na vašem účtu se vztahuje na všechny databáze Cosmos (a kontejnery) pod tímto účtem. Všechny dotazy vydaný pro kontejner nebo databáze a čtení použije tuto úroveň konzistence ve výchozím nastavení. Zde uvedený postup [nakonfigurovat výchozí úroveň konzistence](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Záruky související s úrovní konzistence

Komplexní smlouvy SLA k dispozici ve službě Azure Cosmos DB zaručuje, že 100 % požadavků na čtení vyhoví záruce konzistence pro všechny úrovně konzistence, kterou si zvolíte. Žádost o čtení se považuje za splnili konzistence smlouvou SLA, pokud jsou splněny všechny záruky konzistence spojené s úrovní konzistence. Přesné definice úrovní pět konzistence ve službě Cosmos DB s použitím [TLA + specifikačnímu jazyku (TLA +)](http://lamport.azurewebsites.net/tla/tla.html) jsou součástí [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) úložiště GitHub. Sémantika úrovně konzistence pět jsou popsané níže:

- **Úroveň konzistence = "strong"**: nabízí silnou konzistenci [linearizovatelnosti](https://aphyr.com/posts/313-strong-consistency-models) zaručit s čtení zaručeno, že vrátí nejnovější potvrzené verzi položky. Klient nikdy neuvidí nepotvrzených nebo částečné zápisu a je vždycky zaručeno, že přečte nejnovější potvrzené zápisu.
- **Úroveň konzistence = "omezená neaktuálnost"**: čtení má zaručeno případném dalším sdílení dodržovat záruku konzistentní předpony. Může prodleva čtení zápisů maximálně K verzí nebo předpony (například aktualizace) položky nebo nejde "časový interval. Proto pokud zvolíte omezená neaktuálnost, "neaktuálnost" je možné nakonfigurovat dvěma způsoby: číslo verze (kB) položky nebo časový interval (t), podle kterého může prodleva čtení zápisů. Omezená neaktuálnost nabízí celkový globální pořadí s výjimkou v rámci "neaktuálnost okno." Monotónní čtení záruky existovat v rámci oblasti vnitřní a vnější "neaktuálnost okno." Silná konzistence má stejnou sémantiku jako omezená neaktuálnost, ale s "neaktuálnost okno" rovno nule. Omezená neaktuálnost se také označuje jako "zpožděné čas linearizovatelnosti". Když klient provádí operace čtení v oblasti přijímá zápisy, záruky poskytované konzistenci omezená neaktuálnost jsou stejné jako ty, které mají silnou konzistenci.
- **Úroveň konzistence = "relace"**: čtení má zaručeno případném dalším sdílení dodržovat konzistentní předpona, monotónní čtení, monotónní zápisy, čtení zápisů, zápis čtení záruky. Konzistence typu relace je vymezen relace klienta.
- **Úroveň konzistence = "konzistentní předpona"**: vrácené aktualizace jsou předponou všech aktualizací bez mezer. Konzistentní předpona zaručuje, že čtení nikdy neuvidí zápisy mimo pořadí.
- **Úroveň konzistence = "konečné"**: není zaručeno pořadí pro čtení. V dalším zápisům chybí nakonec sloučí repliky.

## <a name="consistency-levels-explained-through-baseball"></a>Úrovně konzistence je vysvětleno pomocí baseballu

Jako [replikovat konzistence dat pomocí baseballu](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) znázorňuje papíru, imagine posloupnost zápisy představující skóre z baseballu hra se skóre inning inning řádku. V této hře hypotetické baseballu je aktuálně uprostřed sedmého inning (proverbial sedmý – inning roztáhnout), a domácí týmu je winning 2 – 5.

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Spuštění** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Návštěvníci** | 0 | 0 | 1 | 0 | 5 | 0 | 0 |  |  | 2 |
| **Domovská stránka** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Kontejneru Cosmos DB uchovává návštěvníkům a spuštění součty domácí týmu. Zatímco probíhá hry, různé čtení záruky může vést k čtení skóre, které se různé klienty. V následující tabulce jsou uvedeny úplnou sadu výsledků, které mohou být vráceny čtení návštěvníci a domácí skóre, které se s jednotlivými záruky konzistence pět. Všimněte si, že návštěvníci skóre je uvedená jako první, a možné návratové hodnoty jsou oddělené čárkami.

| **Úrovně konzistence** | **Skóre** |
| - | - |
| **Silné** | 2 – 5 |
| **Omezená Neaktuálnost** | skóre, které jsou ve většině jeden inning zastaralý"2-3, 2 – 4, 2 – 5 |
| **Relace** | <ul><li>pro modul pro zápis"2 – 5</li><li> pro všechny, kteří zapisovač: 0-0, 0-1, 0-2, 0 3, 0-4, 0-5, 1 – 0, 1-1, 1 – 2, 1 – 3, 1 – 4, 1-5, 2-0, 2-1, 2-2, 2-3, 2 – 4, 2 – 5</li><li>Po přečtení 1-3: 1-3, 1 – 4, 1-5, 2-3, 2 – 4, 2 – 5</li> |
| **Konzistentní předpona** | 0-0, 0-1, 1-1, 1 – 2, 1 – 3, 2-3, 2 – 4, 2 – 5 |
| **Konečný výsledek** | 0-0, 0-1, 0-2, 0 3, 0-4, 0-5, 1-0, 1-1, 1-2, 1 – 3, 1 – 4, 1-5, 2-0, 2-1, 2-2, 2-3, 2 – 4, 2 – 5 |

## <a name="additional-reading"></a>Další čtení

Další informace o konceptech konzistence najdete v následujících článcích:

- [Základní specifikace TLA + pro úrovních pět konzistence nabízených databází Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Replikovat, je vysvětleno konzistence dat pomocí baseballu (video) Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Replikovat, je vysvětleno konzistence dat pomocí baseballu (dokument White Paper) podle Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Relace záruky pro replikovaná Data slabě konzistentní vzhledem k aplikacím](https://dl.acm.org/citation.cfm?id=383631)
- [Konzistence kompromisy moderní systémy distribuované databáze-návrhu: limit je pouze část textu](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
- [Omezená Neaktuálnost pravděpodobnostní (PBS) pro praktické částečné kvor](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Nakonec konzistentní – kdykoli znovu spustit.](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Další postup

Další informace o úrovních konzistence ve službě Cosmos DB najdete v následujících článcích:

* [Výběr správné konzistence úroveň pro vaši aplikaci](consistency-levels-choosing.md)
* [Úrovně konzistence napříč API služby Cosmos DB](consistency-levels-across-apis.md)
* [Dostupnost a výkon kompromisy pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
* [Jak nakonfigurovat výchozí úroveň konzistence](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Jak lze přepsat výchozí úroveň konzistence](how-to-manage-consistency.md#override-the-default-consistency-level)

