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
ms.openlocfilehash: 8d95790dc09f6d26c6ae749ed0cd386053c5cb35
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2018
ms.locfileid: "42060536"
---
# <a name="tunable-data-consistency-levels-in-azure-cosmos-db"></a>Přizpůsobitelné data úrovně konzistence ve službě Azure Cosmos DB
Azure Cosmos DB je od základu navržený nahoru s globální distribucí v úvahu pro každý datový model. Je určená nabízí záruky předvídatelné nízké latence a různé jasně definované mírnější konzistencí modely. V současné době Azure Cosmos DB nabízí pět úrovní konzistence: silná, ohraničená odolnost, relace, konzistentní Předpona a konečný výsledek. Omezená neaktuálnost, relace, konzistentní Předpona a konečný výsledek jsou označovány jako "mírnější konzistencí modely", jako poskytují méně konzistence než silné, což je většina vysoce konzistentní model, který je k dispozici. 

Kromě **silné** a **konečné konzistence** modely běžně nabízí distribuované databáze Azure Cosmos DB nabízí tři další modely konzistence pečlivě kódovanou a zprovozněné:  **omezená neaktuálnost**, **relace**, a **konzistentní předpona**. Užitečnost každého z těchto úrovní konzistence byl ověřen pro případy použití reálného světa. Společně tyto úrovně konzistence pět umožňují vytvářet dobře odůvodněné kompromisy mezi konzistencí, dostupností a latencí. 

Azure Cosmos DB programový manažer Andrew Liu ukazuje funkce globální distribuce na klíč, v následujícím videu.

>[!VIDEO https://www.youtube.com/embed/-4FsGysVD14]

## <a name="distributed-databases-and-consistency"></a>Distribuované databáze a konzistence
Komerčně distribuované databáze spadají do dvou kategorií: databáze, které vůbec nenabízejí jasně definované osvědčené volby konzistence a databází, které nabízejí dvě extrémně programovatelné volby (silná vs. nahodilá konzistence). 

Databáze uvedené jako první zatěžují vývojáře aplikací podrobnostmi protokolů replikace a očekávají od nich náročné kompromisy mezi konzistencí, dostupností, latencí a propustností. Druhá možnost klade tlak na volbu jednoho ze dvou extrémů. Navzdory spoustě průzkumů a návrhů více než 50 modelů konzistence nemohla komunita pro distribuované databáze komercionalizovat úrovně konzistence nad rámec silné a nahodilé konzistence. Cosmos DB umožňuje vývojářům vybrat mezi pět jasně definované modely konzistence podél spektra konzistence – silná, omezená neaktuálnost, [relace](http://dl.acm.org/citation.cfm?id=383631), konzistentní Předpona a konečný výsledek. 

![Azure Cosmos DB nabízí více správně definovaných (uvolněných) modelů konzistence, z nichž je možné vybírat.](./media/consistency-levels/five-consistency-levels.png)

Následující tabulka uvádí konkrétní záruky, které poskytují jednotlivé úrovně konzistence.
 
**Úrovně konzistence a záruky**

| Úrovně konzistence | Záruky |
| --- | --- |
| Silné | Linearizovatelnosti. Čtení má zaručeno vrátí nejnovější verzi položky.|
| Omezená neaktuálnost | Konzistentní předpona Prodleva čtení mezi zápisy podle nejvíce předpon k nebo intervalu t |
| Relace   | Konzistentní předpona Monotónní čtení, monotónní zápisy, čtení zápisů, zápisy po čtení |
| Konzistentní předpona | Vrácené aktualizace jsou předponou všech aktualizací bez mezer |
| Nahodilé  | Čtení mimo pořadí |

Můžete nakonfigurovat výchozí úroveň konzistence účtu Cosmos DB (a později přepsat konzistenci v konkrétním požadavku pro čtení). Výchozí úroveň konzistence se interně, vztahuje na data v rámci sad oddílů, které může zahrnovat oblasti. O 73 % tenanti služby Azure Cosmos DB, použijte konzistence typu relace a 20 % raději omezená neaktuálnost. Přibližně 3 % zákazníci služby Azure Cosmos DB experimentovat s různými úrovněmi konzistence zpočátku před spuštěním na konkrétní konzistence volbou pro svou aplikaci. Pouze 2 % tenantů Azure Cosmos DB přepsání úrovně konzistence na základě žádosti. 

Ve službě Cosmos DB použijí relace, konzistentní předpona pro čtení a konečné konzistence se dvakrát jako levné jako čtení pomocí velký nebo omezená neaktuálnost, konzistence. Cosmos DB má špičkové komplexní smlouvy SLA, včetně záruky konzistence spolu s dostupnosti, propustnosti a latence. Využívá Azure Cosmos DB [linearizovatelnosti kontrola](http://dl.acm.org/citation.cfm?id=1806634), která neustále pracuje na telemetrie služby a zveřejněno oznámí porušení konzistence pro vás. Azure Cosmos DB pro omezená neaktuálnost, monitoruje a oznámí porušení k hranice k a t. Pro všechny úrovně pět mírnější konzistencí, Azure Cosmos DB také sestavy [probabilistically omezená neaktuálnost metrika](http://dl.acm.org/citation.cfm?id=2212359) přímo pro vás.  

## <a name="service-level-agreements"></a>Smlouvy o úrovni služeb

Azure Cosmos DB nabízí komplexní 99,99 % [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/cosmos-db/) záruku propustnosti, konzistencí, dostupností a latencí pro službu Azure Cosmos DB, databáze účtů s rozsahem v jedné oblasti Azure nakonfigurovaný s žádným z pěti konzistence úrovně nebo účty databáze pokrývající víc oblastí Azure, které jsou nakonfigurované všechny čtyři úrovně mírnější konzistencí. Kromě toho nezávisle volbu úrovně konzistence, Azure Cosmos DB nabízí SLA 99,999 % dostupnost pro čtení pro účty databáze pokrývající dva nebo více oblastí Azure.

## <a name="scope-of-consistency"></a>Rozsah konzistence
Členitost konzistence je vymezen na požadavek na jednoho uživatele. Žádost o zápis může odpovídat insert, replace, upsert nebo odstranit transakce. Stejně jako u zápisu transakce čtení a dotazovat také vymezené na požadavek na jednoho uživatele. Uživatel může být nutné stránkování přes velké sady výsledků, pokrývající několik oddílů, ale každý přečíst transakce je omezená na jednu stránku a obsluhovat z v rámci jednoho oddílu.

## <a name="consistency-levels"></a>Úrovně konzistentnosti
V rámci vašeho účtu služby Cosmos DB můžete nakonfigurovat výchozí úroveň konzistence na váš účet databáze, která se vztahuje na všechny kontejnery (a databází). Ve výchozím nastavení používají výchozí úroveň konzistence na databázový účet všechny dotazy vydaný pro uživatelsky definovaných prostředků a čtení. Úroveň konzistence pomocí žádosti o specifické pro čtení nebo dotazů ve všech podporovaných rozhraní API můžete uvolnit. Existuje pět typů úrovní konzistence podporované protokolem replikace služby Azure Cosmos DB, které poskytují jasné kompromis mezi záruky konkrétní konzistence a výkon, jak je popsáno v této části.

<a id="strong"></a>
**Silná**: 

* Nabízí silnou konzistenci [linearizovatelnosti](https://aphyr.com/posts/313-strong-consistency-models) zaručit s čtení zaručeno, že vrátí nejnovější verzi položky. 
* Silná konzistence zaručuje, že zápis je viditelné pouze poté, co je potvrdí většinové kvorum replik. Zápis je buď synchronně potvrdí většinové primárním serveru i kvora sekundárních replik, nebo byla zrušena. Čtení je vždycky projdou potvrzením většinovým kvorem pro čtení, klient nikdy neuvidí nepotvrzených nebo částečné zápisu a je vždycky zaručeno, že přečte nejnovější potvrzený zápis. 
* Účty Azure Cosmos DB, které jsou nakonfigurovány pro použití silné konzistence nelze přidružit více než jedné oblasti Azure pomocí svého účtu Azure Cosmos DB.  
* Náklady na operace čtení (z hlediska [jednotky žádostí](request-units.md) spotřebované) s silná konzistence je vyšší než relace a konečný výsledek, ale stejný jako omezená neaktuálnost.

<a id="bounded-staleness"></a>
**Omezená neaktuálnost**: 

* Omezená neaktuálnost záruky konzistence, které mohou prodleva čtení zápisy maximálně *K* verze nebo předpony položky nebo *t* časový interval. 
* Proto pokud zvolíte omezená neaktuálnost, "neaktuálnost" je možné nakonfigurovat dvěma způsoby: číslo verze *K* položky, podle kterého prodleva čtení mezi zápisy a časový interval *t* 
* Omezená neaktuálnost nabízí celkový globální pořadí s výjimkou v rámci "neaktuálnost okno." Monotónní čtení záruky existovat v rámci oblasti vnitřní a vnější "neaktuálnost okno." 
* Omezená neaktuálnost nabízí silnější záruky konzistence než relace, konzistentní předpona nebo konečné konzistence. Pro globálně distribuované aplikace doporučujeme, abyste že použijete omezená neaktuálnost pro scénáře, ve kterém chcete mít silné konzistence, ale také chcete 99,99 % dostupností a nízkou latencí.   
* Účty Azure Cosmos DB, které jsou nakonfigurovány s konzistenci omezená neaktuálnost můžete přidružit libovolného počtu oblastí Azure se svým účtem služby Azure Cosmos DB. 
* Náklady na operace čtení (z hlediska počet ru spotřebovaných) s omezená neaktuálnost je vyšší než relace a konečné konzistence, ale stejný jako silná konzistence.

<a id="session"></a>
**Relace**: 

* Na rozdíl od globální soudržnosti modely, které nabízí úrovně konzistence silná, ohraničená odolnost konzistence typu relace je vymezen relace klienta. 
* Konzistence typu relace je ideální pro všechny scénáře, kde je zahrnuta zařízení nebo uživatele, relace, od zaručuje monotónní čtení, monotónní zápisy a čtení vlastních zápisů (RYW) zaručuje. 
* Konzistence typu relace poskytuje předvídatelný konzistence pro relaci, a maximální propustnost čtení při nabízí nejnižší latenci zápisů a čtení. 
* Účty Azure Cosmos DB, které jsou nakonfigurovány s konzistence typu relace můžete přidružit libovolného počtu oblastí Azure se svým účtem služby Azure Cosmos DB. 
* Náklady na operace čtení (z hlediska počet ru spotřebovaných) s úrovní konzistence relace je menší než silné a omezená neaktuálnost, ale více než konečnou konzistenci.

<a id="consistent-prefix"></a>
**Konzistentní předpona**: 

* Konzistentní předpona zaručuje, že v neexistence dalším zápisům replik v rámci skupiny nakonec sloučit. 
* Konzistentní předpona zaručuje, že čtení nikdy neuvidí zápisy mimo pořadí. Pokud byly zápisy provedeny v pořadí, `A, B, C`, pak klient se zobrazí buď `A`, `A,B`, nebo `A,B,C`, ale nikdy mimo pořadí jako `A,C` nebo `B,A,C`.
* Účty Azure Cosmos DB, které jsou nakonfigurovány s konzistentní předpona konzistence můžete přidružit libovolného počtu oblastí Azure se svým účtem služby Azure Cosmos DB. 

<a id="eventual"></a>
**Konečný výsledek**: 

* Konzistence typu případné zaručuje, že neexistence dalším zápisům replik v rámci skupiny nakonec sloučit. 
* Konečné konzistence je nejslabší formu konzistence, kde klient může získat hodnoty, které jsou starší než ty, které jsme viděli před.
* Konzistence typu případné poskytuje nejslabší konzistence čtení, ale nabízí nejnižší latencí pro operace čtení i zápisu.
* Účty Azure Cosmos DB, které jsou nakonfigurovány s konečnou konzistencí, můžete přidružit libovolného počtu oblastí Azure se svým účtem služby Azure Cosmos DB. 
* Náklady na operace čtení (z hlediska počet ru spotřebovaných) s konečnou konzistencí úroveň je nejnižší všech úrovní konzistence služby Azure Cosmos DB.

## <a name="configuring-the-default-consistency-level"></a>Konfigurace výchozí úroveň konzistence
1. V [webu Azure portal](https://portal.azure.com/), na panelu vlevo klikněte na tlačítko **služby Azure Cosmos DB**.
2. Na stránce **Azure Cosmos DB** vyberte účet databáze, který chcete upravit.
3. Na stránce účtu klikněte na **výchozí konzistence**.
4. V **výchozí konzistence** stránky, vyberte novou úroveň konzistence a klikněte na tlačítko **Uložit**.
   
    ![Snímek obrazovky zvýraznění na ikonu nastavení a položky výchozí konzistence](./media/consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>Úrovně konzistence pro dotazy
Ve výchozím nastavení pro uživatelsky definovaných prostředků úrovně konzistence pro dotazy je stejná jako úroveň konzistence pro čtení. Ve výchozím nastavení je synchronně aktualizace indexu na každý vložení, nahrazení nebo odstranění položky do kontejneru Cosmos DB. To umožňuje dotazům případném dalším sdílení dodržovat stejné úrovně konzistence, který bod čtení. Azure Cosmos DB je optimalizovaná zápisu, která podporuje trvalý objem zápisy, synchronní index údržby a poskytování konzistentního dotazů, můžete nakonfigurovat některé kontejnery k aktualizaci jejich rejstřík laxně. Opožděné indexování dále zvyšuje rychlost zápisu a je ideální pro scénáře ingestování hromadné především pro čtení náročná na výkon při zatížení.  

| Indexování režimu | Čtení | Dotazy |
| --- | --- | --- |
| Konzistentní (výchozí) |Výběrem silná, omezená neaktuálnost, relace, konzistentní Předpona a konečný výsledek |Vyberte z silná, omezená neaktuálnost, relace, nebo konečné |
| Opožděné |Výběrem silná, omezená neaktuálnost, relace, konzistentní Předpona a konečný výsledek |Nahodilé |
| Žádný |Výběrem silná, omezená neaktuálnost, relace, konzistentní Předpona a konečný výsledek |Neuvedeno |

Jako s požadavky na čtení, může snížit úroveň konzistence požadavku konkrétní dotaz na každé rozhraní API.

## <a name="consistency-levels-for-the-mongodb-api"></a>Úrovně konzistence pro rozhraní MongoDB API

Azure Cosmos DB aktuálně implementuje MongoDB verze 3.4, která má dvě nastavení konzistence silné a nahodilé. Protože Azure Cosmos DB používá více rozhraní API, používá se nastavení konzistence na úrovni účtu, přičemž prosazování konzistence je řízeno jednotlivými rozhraními API.  Do verze MongoDB 3.6 se nepoužívala konzistence typu Relace, pokud tedy v účtu rozhraní MongoDB API nastavíte použití konzistence typu Relace, při používání rozhraní MongoDB API se úroveň konzistence sníží na nahodilou. Pokud pro účet rozhraní MongoDB API potřebujete zaručit čtení vlastních zápisů, měla by se výchozí úroveň konzistence tohoto účtu nastavit na silnou nebo na omezenou neaktuálnost.

## <a name="next-steps"></a>Další postup
Pokud jste chtěli udělat další výklad o úrovních konzistence a kompromisy, doporučujeme následující zdroje:

* [Vysvětlení konzistence replikovaných dat pomocí baseballu (video) Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
* [Vysvětlení konzistence replikovaných dat pomocí baseballu (dokument White Paper) podle Doug Terry](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
* [Relace záruky pro replikovaná Data slabě konzistentní vzhledem k aplikacím](http://dl.acm.org/citation.cfm?id=383631)
* [Konzistence kompromisy moderní systémy distribuované databáze-návrhu: limit je pouze část textu](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Omezená Neaktuálnost pravděpodobnostní (PBS) pro praktické částečné kvor](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
* [Konečná konzistentní – kdykoli znovu spustit.](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
* [Zatížení, kapacity a dostupnosti systémů kvora, SIAM deníku na výpočetních](http://epubs.siam.org/doi/abs/10.1137/S0097539795281232)
* [Řada produktů: dokončení a automatické linearizovatelnosti požadovaných součástí, in: Proceedings of 2010 ACM SIGPLAN conference na programovací jazyk návrh a implementace](http://dl.acm.org/citation.cfm?id=1806634)
* [Probabilistically omezená neaktuálnost pro praktické částečné kvor](http://dl.acm.org/citation.cfm?id=2212359)
