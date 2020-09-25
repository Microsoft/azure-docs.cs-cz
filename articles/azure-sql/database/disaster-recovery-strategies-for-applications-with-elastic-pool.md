---
title: Návrh řešení pro zotavení po havárii
description: Pomocí správného vzoru pro převzetí služeb při selhání se dozvíte, jak navrhnout cloudové řešení pro zotavení po havárii.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb-1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 6887371e50f5b7e8706cac0a0700873c42bdac06
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321641"
---
# <a name="disaster-recovery-strategies-for-applications-using-azure-sql-database-elastic-pools"></a>Strategie zotavení po havárii pro aplikace používající Azure SQL Database elastické fondy
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database poskytuje několik možností pro zajištění provozní kontinuity aplikace při výskytu závažných incidentů. [Elastické fondy](elastic-pool-overview.md) a izolované databáze podporují stejný druh možností zotavení po havárii (Dr). Tento článek popisuje několik strategií zotavení po havárii pro elastické fondy, které využívají tyto Azure SQL Database funkce pro provozní kontinuitu.

Tento článek používá následující kanonický vzor aplikace SaaS ISV:

Moderní cloudová webová aplikace zřídí jednu databázi pro každého koncového uživatele. ISV má mnoho zákazníků, proto používá mnoho databází označovaných jako databáze klientů. Vzhledem k tomu, že databáze tenantů mají typicky nepředvídatelné vzorce aktivity, používá ISV elastický fond, aby bylo možné zajistit, aby se databáze po delší dobu nepředvídatelná. Elastický fond také zjednodušuje správu výkonu při špičkách aktivity uživatele. Kromě databází tenantů používá aplikace také několik databází ke správě profilů uživatelů, zabezpečení, shromažďování způsobů využití atd. Dostupnost jednotlivých tenantů nemá vliv na dostupnost aplikace jako celek. Dostupnost a výkon databází pro správu je ale pro funkci aplikace zásadní a pokud jsou databáze pro správu offline, celá aplikace je offline.

Tento článek pojednává o strategiích zotavení po havárii, které pokrývají řadu scénářů od necitlivých spouštěcích aplikací až po požadavky s přísnou dostupností

> [!NOTE]
> Pokud používáte databáze úrovně Premium nebo Pro důležité obchodní informace a elastické fondy, můžete je v případě, že je převedete do konfigurace redundantního nasazení zóny, zajistit odolnost proti regionálním výpadkům. Viz [zóna – redundantní databáze](high-availability-sla.md).

## <a name="scenario-1-cost-sensitive-startup"></a>Scénář 1. Po spuštění citlivé náklady

Jsem po spuštění firmy a extrémně citlivé náklady.  Chci zjednodušit nasazování a správu aplikace a můžu mít omezené smlouvy SLA pro jednotlivé zákazníky. Ale chci mít jistotu, že aplikace jako celek není nikdy offline.

Abyste splnili požadavky na jednoduchost, nasaďte všechny databáze tenantů do jednoho elastického fondu v oblasti Azure podle vašeho výběru a nasaďte databáze pro správu jako geograficky replikované izolované databáze. Pro zotavení po havárii klientů použijte geografické obnovení, které přináší bez dalších nákladů. Aby se zajistila dostupnost databází pro správu, geograficky se replikují do jiné oblasti pomocí skupiny automatického převzetí služeb při selhání (krok 1). Průběžné náklady na konfiguraci zotavení po havárii v tomto scénáři se rovnají celkové ceně sekundárních databází. Tato konfigurace je znázorněna v dalším diagramu.

![Obrázek 1](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Pokud dojde k výpadku v primární oblasti, kroky obnovení pro uvedení vaší aplikace do režimu online jsou znázorněny v dalším diagramu.

* Skupina převzetí služeb při selhání inicializuje automatické převzetí služeb při selhání databáze pro správu do oblasti zotavení po havárii. Aplikace se automaticky znovu připojí k nové primární databázi a všechny nové účty a databáze tenantů se vytvoří v oblasti zotavení po havárii. Stávajícím zákazníkům uvidí data dočasně nedostupná.
* Vytvořte elastický fond se stejnou konfigurací, jakou má původní fond (2).
* Pomocí geografického obnovení můžete vytvořit kopie databází tenanta (3). Můžete zvážit aktivaci jednotlivých obnovení pomocí připojení koncových uživatelů nebo použít jiné schéma priority specifické pro aplikaci.

V tomto okamžiku je vaše aplikace zase online v oblasti zotavení po havárii, ale při přístupu k datům se někteří zákazníci setkávají bez prodlení.

![Obrázek 2](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Pokud došlo k dočasnému výpadku, je možné, že je primární oblast obnovená Azure, než se všechna obnovení databáze dokončí v oblasti zotavení po havárii. V takovém případě orchestruje přesun aplikace zpátky do primární oblasti. Tento proces provede kroky popsané v dalším diagramu.

* Zruší všechny zbývající požadavky na geografickou obnovu.
* Převzetí služeb při selhání databáze správy do primární oblasti (5). Po obnovení oblasti se původní základní primární počítače automaticky stanou sekundárními. Teď znovu přepnete role.
* Změňte připojovací řetězec aplikace tak, aby odkazoval zpátky na primární oblast. Všechny nové účty a databáze tenantů se teď vytvoří v primární oblasti. Někteří stávající zákazníci uvidí svá data dočasně nedostupné.
* Nastavte všechny databáze ve fondu DR na jen pro čtení, abyste se ujistili, že je nelze upravovat v oblasti DR (6).
* Pro každou databázi ve fondu DR, která se od obnovení změnila, přejmenujte nebo odstraňte odpovídající databáze v primárním fondu (7).
* Zkopírujte aktualizované databáze z fondu DR do primárního fondu (8).
* Odstranit fond DR (9)

V tomto okamžiku je vaše aplikace online v primární oblasti se všemi databázemi klientů dostupnými v primárním fondu.

![Obrázek 3](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

### <a name="benefit"></a>Výhoda

Klíčovou výhodou této strategie je nízké průběžné náklady na redundanci datové vrstvy. Azure SQL Database automaticky zálohuje databáze bez dalšího přepisu aplikace bez dalších nákladů. Náklady se účtují pouze v případě, že jsou elastické databáze obnoveny. 

### <a name="trade-off"></a>Obchodování – vypnuto

Kompromisem je, že kompletní obnovení všech databází tenanta trvá poměrně dlouho. Doba závisí na celkovém počtu obnovení, které zahájíte v oblasti zotavení po havárii, a na celkové velikosti databází klientů. I když určíte, že se některé klienty budou obnovovat přes ostatní, budete soutěžit se všemi ostatními obnovenými možnostmi, které jsou iniciovány ve stejné oblasti jako arbitrates služby, a omezit celkový dopad na databáze stávajících zákazníků. Obnovení databází klientů se navíc nemůže spustit, dokud se nevytvoří nový elastický fond v oblasti zotavení po havárii.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Scénář 2. Vyspělá aplikace s vrstvenou službou

Jsem vyspělou SaaSou aplikací s nabídkami vrstvených služeb a různými SLA pro zákazníky se zkušební verzí a pro zákazníky s platbami. Pro zákazníky se zkušební verzí je potřeba snížit náklady co nejvíce. Zákazníci se zkušební verzí můžou pořizovat prostoje, ale chtěli byste snížit jeho pravděpodobnost. Pro placené zákazníky je jakékoli výpadky letového rizika. Proto chci mít jistotu, že budou mít zákazníci vždycky přístup k datům.

Pro podporu tohoto scénáře oddělte zkušební klienty od placených tenantů jejich vložením do samostatných elastických fondů. Zákazníci se zkušební verzí mají nižší eDTU nebo virtuální jádra na tenanta a nižší SLA s delší dobou obnovení. Zákazníci s platbami jsou ve fondu s vyššími eDTU nebo virtuální jádra na tenanta a vyšší smlouvou SLA. Aby bylo zaručeno nejnižší čas obnovení, jsou geograficky replikovány i databáze tenantů platící zákazníky. Tato konfigurace je znázorněna v dalším diagramu.

![Obrázek 4](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Stejně jako v prvním scénáři jsou databáze správy poměrně aktivní, takže pro ni používáte jednu geograficky replikovanou databázi (1). Tím zajistíte předvídatelný výkon pro nové předplatné zákazníků, aktualizace profilu a další operace správy. Oblast, ve které jsou umístěny základní části databází správy, je primární oblastí a oblastí, ve které jsou umístěny sekundární databáze správy, je oblast DR.

Databáze tenantů platící zákazníky mají aktivní databáze v "placeném" fondu zřízeném v primární oblasti. V oblasti zotavení po havárii zřídí sekundární fond se stejným názvem. Každý tenant je geograficky replikován do sekundárního fondu (2). To umožňuje rychlé obnovení všech databází klientů pomocí převzetí služeb při selhání.

Pokud dojde k výpadku v primární oblasti, kroky obnovení pro uvedení vaší aplikace do režimu online jsou znázorněny v dalším diagramu:

![Obrázek 5](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Okamžitě převezme služby databáze pro správu do oblasti DR (3).
* Změňte připojovací řetězec aplikace tak, aby odkazoval na oblast zotavení po havárii. Všechny nové účty a databáze tenantů se teď vytvoří v oblasti zotavení po havárii. Stávající zákazníci zkušební verze uvidí svá data dočasně nedostupné.
* Převzetí služeb při selhání databáze placeného tenanta do fondu v oblasti zotavení po havárii pro okamžitou obnovu dostupnosti (4). Vzhledem k tomu, že převzetí služeb při selhání je rychlá změna úrovně metadat, vezměte v úvahu optimalizaci, ve kterém se jednotlivé převzetí služeb při selhání aktivují prostřednictvím připojení koncových uživatelů.
* Pokud byla velikost eDTU sekundárního fondu nebo hodnota vCore nižší než primární, protože sekundární databáze vyžadovala pouze kapacitu pro zpracování protokolů změn v době, kdy se jednalo o sekundární databáze, okamžitě zvyšuje kapacitu fondu tak, aby vyhovovala celému zatížení všech tenantů (5).
* Vytvoří nový elastický fond se stejným názvem a stejnou konfigurací v oblasti DR pro databáze zákazníků zkušební verze (6).
* Jakmile se vytvoří fond zkušebních zákazníků, pomocí geografického obnovení obnovte jednotlivé databáze tenantů klienta do nového fondu (7). Zvažte možnost aktivovat jednotlivá obnovení prostřednictvím připojení koncových uživatelů nebo použít jiné schéma priority specifické pro danou aplikaci.

V tomto okamžiku je aplikace znovu online v oblasti zotavení po havárii. Všichni zákazníci s platbami mají přístup ke svým datům, zatímco při přístupu ke svým datům budou mít zákazníci zkušební období zpoždění.

Když Azure obnoví primární oblast *po* obnovení aplikace v oblasti zotavení po havárii, můžete pokračovat v používání aplikace v této oblasti nebo se můžete rozhodnout, že navrácení služeb po obnovení do primární oblasti. Pokud je primární oblast obnovena *před* dokončením procesu převzetí služeb při selhání, zvažte možnost okamžitého navrácení služeb po obnovení. Navrácení služeb po obnovení provede kroky popsané v následujícím diagramu:

![Obrázek 6](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Zruší všechny zbývající požadavky na geografickou obnovu.
* Převzetí služeb při selhání v databázích správy (8). Po obnovení oblasti se stará primární primární automaticky stane sekundární. Nově se teď znovu vytvoří jako primární.  
* Převzetí služeb při selhání pro placené databáze tenantů (9). Podobně po obnovení oblasti se původní základní primární počítače automaticky stanou sekundárními. Nyní se znovu stanou primárními.
* Nastavte obnovené databáze zkušební verze, které se změnily v oblasti DR na hodnotu jen pro čtení (10).
* Pro každou databázi ve zkušební verzi, kterou si zákazníci od obnovení změnili, přejmenujte nebo odstraňte odpovídající databázi v primárním fondu zákazníků zkušební verze (11).
* Zkopírujte aktualizované databáze z fondu DR do primárního fondu (12).
* Odstraňte fond DR (13).

> [!NOTE]
> Operace převzetí služeb při selhání je asynchronní. K minimalizaci doby obnovení je důležité, abyste v dávkách s nejméně 20 databázemi spustili příkaz převzetí služeb při selhání databáze klienta.

### <a name="benefit"></a>Výhoda

Klíčovou výhodou této strategie je, že poskytuje nejvyšší smlouvu SLA pro placené zákazníky. Také zaručuje, že nové zkušební verze budou odblokovány, jakmile bude vytvořen nový fond pro zotavení po havárii. 

### <a name="trade-off"></a>Obchodování – vypnuto

V důsledku toho, že tato instalace zvyšuje celkové náklady na databáze tenantů, se za cenu sekundárního fondu DR vyplatí pro placené zákazníky. Navíc platí, že pokud má sekundární fond jinou velikost, platíte zákazníkům po převzetí služeb při selhání nižší výkon, dokud se nedokončí upgrade fondu v oblasti zotavení po havárii.

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Scénář 3. Geograficky distribuovaná aplikace s vrstvenou službou

Mám vyspělou aplikaci SaaS s nabídkami vrstvených služeb. Chci nabízet velmi agresivní smlouvu SLA pro moje placené zákazníky a minimalizovat riziko dopadu v případě výpadku, protože dokonce i krátké přerušení může způsobit nespokojenost zákazníků. Je důležité, aby zákazníci s platbami měli vždycky přístup ke svým datům. Zkušební verze jsou bezplatné a během zkušebního období se nenabídne smlouva SLA.

Pro podporu tohoto scénáře použijte tři samostatné elastické fondy. Zřizování dvou fondů stejné velikosti s vysokou eDTU nebo virtuální jádra na databázi ve dvou různých oblastech tak, aby obsahovaly databáze tenantů placených zákazníků. Třetí fond obsahující zkušební klienty může mít nižší eDTU nebo virtuální jádra na databázi a musí se zřídit v jedné ze dvou oblastí.

Aby bylo zaručeno nejnižší čas obnovení během výpadků, jsou databáze tenantů platící zákazníky geograficky replikovány pomocí 50% primárních databází v každé ze dvou oblastí. Podobně každá oblast má 50% sekundárních databází. Tímto způsobem platí, že pokud je oblast offline, bude ovlivněná jenom 50% databází placených zákazníků a převezme se služby při selhání. Ostatní databáze zůstávají beze změn. Tato konfigurace je znázorněna v následujícím diagramu:

![Obrázek 4](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Stejně jako v předchozích scénářích jsou databáze pro správu poměrně aktivní, takže je nakonfigurujte jako jednu geograficky replikovanou databázi (1). Tím zajistíte předvídatelný výkon nových zákaznických předplatných, aktualizací profilů a dalších operací správy. Oblast A je primární oblastí pro databáze pro správu a oblast B se používá pro obnovení databází správy.

Databáze tenantů placené zákazníky jsou taky geograficky replikované, ale s primárními a sekundárními rozděleními mezi oblastí A a oblastí B (2). Tímto způsobem můžou primární databáze tenanta ovlivněné výpadkem převzít služby při selhání do jiné oblasti a budou k dispozici. Na druhou polovinu databází tenanta nebude mít žádný vliv.

Další diagram znázorňuje kroky obnovení, které je potřeba provést, pokud dojde k výpadku v oblasti A.

![Obrázek 5](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Okamžitě převezme služby databáze pro správu do oblasti B (3).
* Změňte připojovací řetězec aplikace tak, aby odkazoval na databáze pro správu v oblasti B. Upravte databáze pro správu, abyste se ujistili, že jsou vytvořeny nové účty a databáze tenantů v oblasti B a zda jsou také nalezeny existující databáze tenantů. Stávající zákazníci zkušební verze uvidí svá data dočasně nedostupné.
* Převzetí služeb při selhání databáze placeného tenanta do fondu 2 v oblasti B pro okamžité obnovení dostupnosti (4). Vzhledem k tomu, že převzetí služeb při selhání je rychlá změna úrovně metadat, můžete uvažovat o optimalizaci, při které se jednotlivé převzetí služeb při selhání spouštějí při připojení koncových uživatelů.
* Vzhledem k tomu, že fond 2 obsahuje pouze primární databáze, zvyšuje celková zatížení fondu a může okamžitě zvýšit velikost eDTU (5) nebo počet virtuální jádra.
* Vytvoří nový elastický fond se stejným názvem a stejnou konfigurací v oblasti B pro databáze zákazníků zkušební verze (6).
* Po vytvoření fondu pomocí geografického obnovení obnovte jednotlivé databáze klienta zkušební verze do fondu (7). Můžete zvážit aktivaci jednotlivých obnovení pomocí připojení koncových uživatelů nebo použít jiné schéma priority specifické pro aplikaci.

> [!NOTE]
> Operace převzetí služeb při selhání je asynchronní. K minimalizaci doby obnovení je důležité, abyste v dávkách s nejméně 20 databázemi spustili příkaz převzetí služeb při selhání databáze klienta.

V tomto okamžiku je vaše aplikace zase online v oblasti B. Všichni zákazníci s platbami mají přístup ke svým datům, zatímco při přístupu ke svým datům budou mít zákazníci zkušební období zpoždění.

Když se obnoví oblast A, musíte se rozhodnout, jestli chcete použít oblast B pro zákazníky zkušební verze nebo navrácení služeb po obnovení na používání fondu zákazníků zkušební verze v oblasti A. Jedním z kritérií může být% zkušebních databází tenantů, které byly od obnovení změněny. Bez ohledu na toto rozhodnutí je potřeba znovu vyvážit placené klienty mezi dvěma fondy. Další diagram znázorňuje proces, když se databáze tenantů zkušební verze vrátí do oblasti A.  

![Obrázek 6](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Zruší všechny zbývající požadavky na geografickou obnovu na vyzkoušení fondu zotavení po havárii.
* Převzetí služeb při selhání v databázi pro správu (8). Po obnovení oblasti se stará primární databáze automaticky stala sekundární. Nově se teď znovu vytvoří jako primární.  
* Vyberte, které placené databáze tenantů se vrátí do fondu 1, a inicializujte převzetí služeb při selhání sekundárním (9). Po obnovení oblasti se všechny databáze ve fondu 1 automaticky staly sekundárními. Nyní se v 50% z nich stanou znovu primárními.
* Snižte velikost fondu 2 na původní eDTU (10) nebo počet virtuální jádra.
* Nastavte všechny obnovené databáze zkušební verze v oblasti B na jen pro čtení (11).
* Pro každou databázi ve fondu zotavení po havárii, která se změnila od posledního obnovení, přejmenujte nebo odstraňte odpovídající databázi ve zkušebním primárním fondu (12).
* Zkopírujte aktualizované databáze z fondu DR do primárního fondu (13).
* Odstraňte fond DR (14).

### <a name="benefit"></a>Výhoda

Klíčové výhody této strategie:

* Podporuje nejagresivní smlouvu SLA pro placené zákazníky, protože zajišťuje, že výpadek nemůže mít vliv na více než 50% databází klientů.
* Zaručuje, že nové zkušební verze budou odblokovány, jakmile bude během obnovování vytvořen fond DR.
* Umožňuje efektivnější využití kapacity fondu, jako 50% sekundárních databází ve fondu 1 a ve fondu 2 je zaručeno, že budou méně aktivní než primární databáze.

### <a name="trade-offs"></a>Kompromisy

Hlavní kompromisy jsou:

* Operace CRUD s databázemi správy mají nižší latenci pro koncové uživatele, kteří se připojují k oblasti A, než koncoví uživatelé připojení k oblasti B při jejich spuštění s primární databází pro správu.
* Vyžaduje složitější návrh databáze správy. Každý záznam tenanta má například značku umístění, kterou je třeba změnit během převzetí služeb při selhání a navrácení služeb po obnovení.  
* Zákazníci s platbami můžou vyskytnout nižší výkon než obvykle, dokud se nedokončí upgrade fondu v oblasti B.

## <a name="summary"></a>Shrnutí

Tento článek se zaměřuje na strategie zotavení po havárii pro databázovou vrstvu, kterou používá aplikace SaaS ISV pro více tenantů. Zvolená strategie je založená na potřebách aplikace, jako je obchodní model, smlouva SLA, kterou chcete zákazníkům nabídnout, a omezení rozpočtu atd. V každé popsané strategii se dozvíte o výhodách a obchodu, abyste mohli učinit rozhodnutí v informování. Vaše konkrétní aplikace taky může zahrnovat jiné součásti Azure. Proto si Projděte pokyny pro provozní kontinuitu a orchestrujte obnovení databázové vrstvy. Další informace o správě obnovení databázových aplikací v Azure najdete v tématu [navrhování cloudových řešení pro zotavení po havárii](designing-cloud-solutions-for-disaster-recovery.md).  

## <a name="next-steps"></a>Další kroky

* Další informace o Azure SQL Database automatizovaných zálohách najdete v tématu [Azure SQL Database automatizované zálohy](automated-backups-overview.md).
* Přehled provozní kontinuity a scénáře najdete v tématu [Přehled provozní kontinuity](business-continuity-high-availability-disaster-recover-hadr-overview.md).
* Další informace o použití automatizovaných záloh pro obnovení najdete v tématu [obnovení databáze ze zálohy iniciované službou](recovery-using-backups.md).
* Další informace o možnostech rychlejšího obnovení najdete v tématu [Aktivní geografická replikace](active-geo-replication-overview.md) a [skupiny automatického převzetí služeb při selhání](auto-failover-group-overview.md).
* Další informace o použití automatizovaných záloh k archivaci najdete v tématu [kopie databáze](database-copy.md).
