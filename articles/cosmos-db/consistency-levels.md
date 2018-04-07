---
title: Úrovně konzistence v Azure Cosmos DB | Microsoft Docs
description: Azure Cosmos DB má pět úrovní konzistence můžete vyrovnávat případné konzistence, dostupností a latencí kompromis.
keywords: konzistence typu případné azure cosmos databáze, azure, Microsoft azure
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: 3fe51cfa-a889-4a4a-b320-16bf871fe74c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 512f9e22d01e3bdb90210402aaf123ac6326759a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="tunable-data-consistency-levels-in-azure-cosmos-db"></a>Data přizpůsobitelné úrovně konzistence v Azure Cosmos DB
Azure Cosmos DB slouží od základů až s globální distribuce v paměti pro každý datový model. Je navržen pro nabízejí předvídatelný s nízkou latencí záruky a více dobře definovaný volný konzistence modelů. V současné době Azure Cosmos DB poskytuje pět úrovně konzistence: silnou, s ohraničenou odolností, založenou relace, konzistentní Předpona a případnou. Typu ohraničenou prošlostí, relace, konzistentní předponu a případné jsou označovány jako "volný konzistence modely", jako poskytují menší konzistence než silné, což je většina vysoce konzistentní modelu, který je k dispozici. 

Kromě **silné** a **konzistence typu případné** modely běžně nabízí distribuovaných databází Azure Cosmos DB nabízí tři další modely pečlivě kódované a operationalized konzistence:  **vázaný typu prošlostí**, **relace**, a **konzistentní předponu**. Užitečnost každé z těchto úrovní konzistence byl ověřen vůči skutečných případy použití. Kolektivně tyto úrovně pět konzistence umožňují provést dobře odůvodněnou kompromis mezi konzistencí, dostupností a latencí. 

V následujícím videu demonstruje Azure manažer programu DB Cosmos Andrew Liu připraveného globální distribuční funkce.

>[!VIDEO https://www.youtube.com/embed/-4FsGysVD14]

## <a name="distributed-databases-and-consistency"></a>Distribuovaná databáze a konzistence
Komerční distribuované databáze rozdělit do dvou kategorií: databáze, které nenabízejí volby dobře definovaný zaručenou konzistence na všech a databází, které nabízí dvě možnosti extrémně programovatelnosti (silné oproti konzistence typu případné). 

Databáze uvedené jako první zatěžují vývojáře aplikací podrobnostmi protokolů replikace a očekávají od nich náročné kompromisy mezi konzistencí, dostupností, latencí a propustností. Druhá možnost klade tlak na volbu jednoho ze dvou extrémů. Navzdory spoustě průzkumů a návrhů více než 50 modelů konzistence nemohla komunita pro distribuované databáze komercionalizovat úrovně konzistence nad rámec silné a nahodilé konzistence. Cosmos DB umožňuje vývojářům si vybrat mezi pěti modely dobře definovaný konzistence podél spektra konzistence – nejsilnější, typu s ohraničenou prošlostí, [relace](http://dl.acm.org/citation.cfm?id=383631), konzistentní předponu a případnou. 

![Azure Cosmos DB nabízí více správně definovaných (uvolněných) modelů konzistence, z nichž je možné vybírat.](./media/consistency-levels/five-consistency-levels.png)

Následující tabulka uvádí konkrétní záruky, které poskytují jednotlivé úrovně konzistence.
 
**Úrovně konzistence a záruky**

| Úrovně konzistence | Záruky |
| --- | --- |
| Silné | Linearizability. Čtení je zaručeno vrátit nejnovější verzi položky.|
| Omezená neaktuálnost | Konzistentní předpona Prodleva čtení mezi zápisy podle předpon k nebo intervalu t |
| Relace   | Konzistentní předpona Monotónní čtení, monotónní zápisy, čtení zápisů, zápisy po čtení |
| Konzistentní předpona | Vrácené aktualizace jsou předponou všech aktualizací bez mezer |
| Nahodilé  | Čtení mimo pořadí |

Můžete nakonfigurovat výchozí úroveň konzistence účtu Cosmos DB (a později přepsat konzistenci v konkrétním požadavku pro čtení). Výchozí úroveň konzistence interně, se vztahuje na data v rámci sady oddílu, což může mít rozsah oblasti. O 73 % Azure Cosmos DB klienty pomocí relace konzistence a 20 % přednost typu s ohraničenou prošlostí. Přibližně 3 % Azure Cosmos DB zákazníků experimentovat s různé úrovně konzistence původně před spuštěním na konkrétní konzistence volba pro svou aplikaci. Pouze 2 % klienty Azure Cosmos DB přepsat úrovně konzistence na základě žádosti. 

V databázi Cosmos čtení zpracovat v relaci konzistentní předponu a konzistence typu případné jsou dvakrát jako levných jako čtení s konzistence typu silné nebo ohraničenou prošlostí. Cosmos DB má špičkové komplexní SLA, včetně záruk konzistence společně s dostupnosti, propustnosti a latence. Využívá Azure Cosmos DB [linearizability kontrolu](http://dl.acm.org/citation.cfm?id=1806634), který nepřetržitě funguje přes služby telemetrie a zveřejněno sestavy veškerá porušení zásad konzistence pro vás. Pro typu s ohraničenou prošlostí Azure Cosmos DB monitoruje a oznámí všechny porušení tisíc a t hranice. Pro všechny pět úrovní volný konzistence Azure Cosmos DB také sestavy [probabilistically ohraničenou typu prošlostí metrika](http://dl.acm.org/citation.cfm?id=2212359) přímo pro vás.  

## <a name="service-level-agreements"></a>Smlouvy o úrovni služeb

Azure Cosmos DB nabízí komplexní 99,99 % [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) propustnost záruka, konzistence, dostupnost a latence pro Azure Cosmos DB databáze účtů rozsah k jedné oblasti Azure, které jsou nakonfigurované s žádným z pěti konzistence úrovně, nebo databáze účtů pokrývání uzlů několika oblastmi Azure nakonfigurovaný s žádným z čtyři úrovně konzistence volný. Kromě toho nezávislé Volba úrovně konzistence, Azure Cosmos DB nabízí 99.999 % SLA pro dostupnost pro čtení pro účty databáze pokrývání uzlů dvou nebo více oblastech Azure.

## <a name="scope-of-consistency"></a>Rozsah konzistence
Členitost konzistence je vymezen na žádost o jednoho uživatele. Žádost o zápis může odpovídat insert, replace, upsert a odstraňovat transakce. Stejně jako u zápisy, má také transakce čtení nebo dotazu obor na žádost o jednoho uživatele. Uživatel může být nutné stránkování přes velkým sadu výsledků dotazu, pokrývání uzlů více oddílů, ale každý přečíst transakce je omezená na jednu stránku a zpracování z v rámci jednoho oddílu.

## <a name="consistency-levels"></a>Úrovně konzistentnosti
Výchozí úroveň konzistence můžete nakonfigurovat na vašem účtu databáze, která platí pro všechny kolekce (a databází) v rámci účtu Cosmos DB. Ve výchozím nastavení všechny čtení a dotazy vydaný pro uživatelem definované prostředky používají výchozí úroveň konzistence zadaný u databázového účtu. Můžete uvolnit úroveň konzistence pomocí požadavek specifického pro čtení nebo dotazu v každé z podporovaných rozhraní API. Existují pět typy úrovně konzistence nepodporuje Azure Cosmos DB protokol replikace, které poskytují zrušte kompromis mezi záruky konkrétní konzistence a výkon, jak je popsáno v této části.

<a id="strong"></a>
**Silná**: 

* Nabízí silnou konzistenci [linearizability](https://aphyr.com/posts/313-strong-consistency-models) zaručit s čtení zaručit vrátit nejnovější verzi položky. 
* Silnou konzistenci zaručuje, že zápis se zobrazí po jeho je trvale potvrzený většinou kvora replik. Zápis je buď synchronně trvale potvrzený primárním serverem a kvora sekundárních databází nebo byl přerušen. Čtení vždy potvrdí se většinou číst kvora, klient nikdy uvidí zápisu nepotvrzené nebo jeho část a vždy záruku, přečtěte si nejnovější potvrzené zápisu. 
* Azure Cosmos DB účty, které jsou nakonfigurovány pro použití silnou konzistenci nelze přiřadit více než jedné oblasti Azure pomocí svého účtu Azure Cosmos DB.  
* Náklady na operace čtení (z hlediska [požadované jednotky](request-units.md) spotřebované) se silnou konzistenci je vyšší než relaci a případnou, ale stejný jako typu s ohraničenou prošlostí.

<a id="bounded-staleness"></a>
**Vázaný typu prošlostí**: 

* Vázaný typu prošlostí konzistence záruky, které čtení může funkce lag za zápisy podle maximálně *tisíc* verze nebo předpony položky nebo *t* časovém intervalu. 
* Proto pokud výběr ohraničenou typu prošlostí, typu "prošlostí" je možné nakonfigurovat dvěma způsoby: číslo verze *tisíc* položky, pomocí kterého čtení funkce lag za zápisů a časový interval *t* 
* Vázaný typu prošlostí nabízí celkové globální pořadí s výjimkou "typu prošlostí oknu." Monotónní čtení záruky existovat v rámci oblasti uvnitř i mimo "typu prošlostí okna." 
* Typu s ohraničenou prošlostí poskytuje silnější záruku konzistence než relace, konzistentní předpony nebo konzistence typu případné. Globálně distribuované aplikace doporučujeme, že používáte typu s ohraničenou prošlostí pro scénáře, kde chcete mít silnou konzistenci, ale také chcete 99,99 % dostupnost a s nízkou latencí.   
* Azure Cosmos DB účty, které jsou nakonfigurovány s konzistence typu s ohraničenou prošlostí můžete přidružit libovolný počet oblastí Azure pomocí svého účtu Azure Cosmos DB. 
* Náklady na operace čtení (z hlediska RUs spotřebované) s typu s ohraničenou prošlostí je vyšší než relace a konzistence typu případné, ale stejný jako silnou konzistenci.

<a id="session"></a>
**Relace**: 

* Na rozdíl od globální konzistence modely, které nabízí úrovně konzistence typu silné a ohraničenou prošlostí je vymezen konzistence typu relace k relaci klienta. 
* Konzistence typu relace je ideální pro všechny scénáře, kde je zahrnuta relaci zařízení nebo uživatele, protože zaručuje monotónní čtení, monotónní zápisů a čtení zaručuje vlastní zápisy (RYW). 
* Konzistence typu relace poskytuje předvídatelnou konzistenci pro relaci a maximální propustnost čtení při nabízí nejnižší latenci zápisů a čtení. 
* Azure Cosmos DB účty, které jsou nakonfigurovány s konzistence typu relace můžete přidružit libovolný počet oblastí Azure pomocí svého účtu Azure Cosmos DB. 
* Náklady na operace čtení (z hlediska RUs spotřebované) s úrovní konzistence relace je typu s méně než silné a ohraničenou prošlostí, ale více než případné konzistence.

<a id="consistent-prefix"></a>
**Konzistentní předponu**: 

* Konzistentní předponu zaručuje, že při absenci dalších zápisů budou repliky ve skupině konvergovat. 
* Konzistentní předponu zaručuje, že čtení nikdy neuvidí mimo pořadí zápisy. Pokud zápisy se prováděly v pořadí `A, B, C`, pak klient uvidí buď `A`, `A,B`, nebo `A,B,C`, ale nikdy mimo pořadí jako `A,C` nebo `B,A,C`.
* Azure Cosmos DB účty, které jsou nakonfigurovány s konzistentní předponu konzistence můžete přidružit libovolný počet oblastí Azure pomocí svého účtu Azure Cosmos DB. 

<a id="eventual"></a>
**Závěrečné**: 

* Konzistence typu případné zaručuje, že při absenci dalších zápisů budou repliky ve skupině konvergovat. 
* Konzistence typu případné je nejslabších formu konzistence, kde může klient získat hodnoty, které jsou starší než ty, které měl viděli.
* Konzistence typu případné poskytuje nejnižší konzistenci čtení, ale nabízí nejnižší latenci pro čtení i zápisy.
* Azure Cosmos DB účty, které jsou nakonfigurovány s konzistence typu případné můžete přidružit libovolný počet oblastí Azure pomocí svého účtu Azure Cosmos DB. 
* Náklady na operace čtení (z hlediska RUs spotřebované) s konzistence typu případné úroveň je nejnižší všech úrovní konzistence Azure Cosmos DB.

## <a name="configuring-the-default-consistency-level"></a>Konfigurace výchozí úroveň konzistence
1. V [portál Azure](https://portal.azure.com/), na panelu vlevo klikněte na tlačítko **Azure Cosmos DB**.
2. Na stránce **Azure Cosmos DB** vyberte účet databáze, který chcete upravit.
3. Na stránce účtu klikněte na tlačítko **výchozí konzistence**.
4. V **výchozí konzistence** , vyberte novou úroveň konzistence a klikněte na tlačítko **Uložit**.
   
    ![Snímek obrazovky zvýraznění ikonu nastavení a vstupního výchozí konzistence](./media/consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>Úrovně konzistence pro dotazy
Ve výchozím nastavení pro uživatelem definované prostředky úroveň konzistence pro dotazy je stejná jako úroveň konzistence pro čtení. Ve výchozím nastavení je synchronně aktualizace indexu na každý insert, replace nebo odstranit položky do kontejneru Cosmos DB. To umožňuje dotazy vyhovět stejnou úroveň konzistence jako bod čtení. Zatímco Azure Cosmos DB je optimalizovaná zápisu která podporuje dlouhodobě svazky zápisy, synchronní indexu údržby a poskytování konzistentní dotazů, můžete nakonfigurovat určité kolekce k aktualizaci svého indexu líné. Opožděné indexování další součást zvyšuje výkon zápisu a je ideální pro hromadné přijímání scénáře při zatížení je primárně náročné na čtení.  

| Indexování režimu | Čtení | Dotazy |
| --- | --- | --- |
| Konzistentní (výchozí) |Vyberte z typu silné a ohraničenou prošlostí, relace, konzistentní předpony nebo případné |Vyberte, ze silného typu s ohraničenou prošlostí, relaci nebo případné |
| Lazy |Vyberte z typu silné a ohraničenou prošlostí, relace, konzistentní předpony nebo případné |Nahodilé |
| Žádný |Vyberte z typu silné a ohraničenou prošlostí, relace, konzistentní předpony nebo případné |Neuvedeno |

Jako s požadavky na čtení, můžete snížit úroveň konzistence požadavku specifického dotazu v každé rozhraní API.

## <a name="consistency-levels-for-the-mongodb-api"></a>Úrovně konzistence pro rozhraní API MongoDB

Azure Cosmos DB aktuálně implementuje MongoDB verze 3.4, který má dvě nastavení konzistence silné a případnou. Protože Azure Cosmos DB více api, toto nastavení konzistence lze použít na úrovni účtu a vynucení konzistence se řídí každé rozhraní API.  Dokud MongoDB 3.6, byl žádná koncepce konzistence relace, takže pokud nastavíte účet rozhraní API MongoDB chcete použít konzistence typu relace, je při použití rozhraní API MongoDB snížit na případné konzistence. Pokud potřebujete záruku pro čtení a vaše vlastníte zápis pro účet rozhraní API MongoDB, výchozí úroveň konzistence pro účet by mělo být nastavené silné nebo ohraničenou typu prošlostí.

## <a name="next-steps"></a>Další postup
Pokud chcete provést další výklad o úrovně konzistence a kompromisy, doporučujeme v následujících zdrojích informací:

* [Vysvětlení konzistence replikovaných dat prostřednictvím baseballové (video) Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
* [Vysvětlení konzistence replikovaných dat prostřednictvím podle Doug Terry baseballové (dokument White Paper)](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
* [Relace záruky pro slabě konzistentní replikovaná Data](http://dl.acm.org/citation.cfm?id=383631)
* [Konzistence kompromisy moderní distribuované návrhu databáze systémy: Zakončení je pouze část textu](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Pravděpodobnosti typu s ohraničenou Prošlostí (PBS) pro praktické částečné kvor](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
* [Závěrečné konzistentní – kdykoli znovu spustit.](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
* [Zatížení, kapacity a dostupnosti systémů kvora, SIAM deníku na výpočetních](http://epubs.siam.org/doi/abs/10.1137/S0097539795281232)
* [Řada produktů: nástroj úplný a automatické linearizability pro kontrolu, řízení 2010 konference ACM SIGPLAN na programovací jazyk návrhu a implementace](http://dl.acm.org/citation.cfm?id=1806634)
* [Typu s probabilistically ohraničenou prošlostí pro praktické částečné kvor](http://dl.acm.org/citation.cfm?id=2212359)
