---
title: Navrhněte řešení pro zotavení po havárii
description: Zjistěte, jak navrhnout cloudové řešení pro zotavení po havárii výběrem správného vzoru převzetí služeb při selhání.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: 4eeaa187142a6d0d97b12f685ebc455f3844606f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825874"
---
# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pools"></a>Strategie zotavení po havárii pro aplikace používající elastické fondy databáze SQL

V průběhu let jsme se dozvěděli, že cloudové služby nejsou spolehlivé a katastrofické incidenty se stávají. SQL Database poskytuje několik funkcí, které poskytují kontinuitu provozu aplikace, když dojde k těmto incidentům. [Elastické fondy](sql-database-elastic-pool.md) a jednotlivé databáze podporují stejný druh možností zotavení po havárii(DR). Tento článek popisuje několik strategií zotavení po havárii pro elastické fondy, které využívají tyto funkce kontinuity podnikání databáze SQL.

Tento článek používá následující kanonický vzor aplikace SaaS ISV:

Moderní cloudová webová aplikace zřizuje jednu databázi SQL pro každého koncového uživatele. Isv má mnoho zákazníků, a proto používá mnoho databází, označované jako databáze klienta. Vzhledem k tomu, že databáze klienta mají obvykle nepředvídatelné vzory aktivit, isv používá elastický fond, aby náklady na databázi velmi předvídatelné po delší časové období. Elastický fond také zjednodušuje správu výkonu při špičkách aktivity uživatele. Kromě databází klienta aplikace také používá několik databází pro správu uživatelských profilů, zabezpečení, shromažďování vzorců využití atd. Dostupnost jednotlivých klientů nemá vliv na dostupnost aplikace jako celku. Dostupnost a výkon databází pro správu je však důležité pro funkci aplikace a pokud jsou databáze pro správu offline, celá aplikace je offline.

Tento článek popisuje strategie zotavení po havárii zahrnující celou řadu scénářů od aplikací pro spuštění s citlivými náklady až po aplikace s přísnými požadavky na dostupnost.

> [!NOTE]
> Pokud používáte databáze Premium nebo Business Critical a elastické fondy, můžete je učinit odolnými vůči regionálním výpadkům jejich převedením na zónovou redundantní konfiguraci nasazení. Viz [Zóna redundantní databáze](sql-database-high-availability.md).

## <a name="scenario-1-cost-sensitive-startup"></a>Scénář 1. Spuštění citlivé na náklady

Jsem startup podnikání a jsem velmi citlivý na náklady.  Chci zjednodušit nasazení a správu aplikace a mohu mít omezenou SLA pro jednotlivé zákazníky. Ale chci zajistit, aby aplikace jako celek nikdy nebyla offline.

Chcete-li splnit požadavek na jednoduchost, nasaďte všechny databáze klientů do jednoho elastického fondu v oblasti Azure podle vašeho výběru a nasaďte databáze správy jako geograficky replikované jediné databáze. Pro zotavení po havárii klientů, použijte geografické obnovení, které nepřichází bez dalších nákladů. Chcete-li zajistit dostupnost databází pro správu, geograficky je replikujte do jiné oblasti pomocí skupiny automatického převzetí služeb při selhání (krok 1). Průběžné náklady na konfiguraci zotavení po havárii v tomto scénáři se rovnají celkovým nákladům sekundárních databází. Tato konfigurace je znázorněna na následujícím diagramu.

![Obrázek 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Pokud dojde k výpadku v primární oblasti, kroky obnovení převést aplikaci do režimu online jsou znázorněny v následujícím diagramu.

* Skupina převzetí služeb při selhání iniciuje automatické převzetí služeb při selhání databáze správy do oblasti zotavení po Havárii. Aplikace se automaticky znovu připojí k nové primární a všechny nové účty a databáze klientů jsou vytvořeny v oblasti zotavení po Havárii. Stávajícím zákazníkům se jejich data dočasně zobrazují jako nedostupná.
* Vytvořte elastický fond se stejnou konfigurací jako původní fond (2).
* Pomocí geografického obnovení vytvořte kopie databází klienta (3). Můžete zvážit aktivaci jednotlivých obnovení pomocí připojení koncových uživatelů nebo použít jiné schéma priority specifické pro aplikaci.

V tomto okamžiku je vaše aplikace zpět online v oblasti zotavení po Havárii, ale někteří zákazníci zaznamenat zpoždění při přístupu k jejich data.

![Obrázek 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Pokud výpadek byl dočasný, je možné, že primární oblast je obnovena Azure před dokončením všech obnovení databáze v oblasti zotavení po Havárii. V tomto případě orchestrate přesunutí aplikace zpět do primární oblasti. Proces provede kroky znázorněné na následujícím diagramu.

* Zrušte všechny nevyřízené požadavky na geografické obnovení.
* Převzetí služeb při selhání databází správy do primární oblasti (5). Po zotavení regionu se staré primárky automaticky staly sekundárními. Teď si zase vyměňují role.
* Změňte připojovací řetězec aplikace tak, aby přecštěkavých zpět na primární oblast. Nyní jsou všechny nové účty a databáze klientů vytvořeny v primární oblasti. Některým stávajícím zákazníkům se jejich data dočasně zobrazují jako nedostupná.
* Nastavte všechny databáze ve fondu zotavení po Havárii jen pro čtení, abyste zajistili, že je nelze v oblasti zotavení po havárii (6) změnit.
* Pro každou databázi ve fondu zotavení po Havárii, která se od obnovení změnila, přejmenujte nebo odstraňte odpovídající databáze v primárním fondu (7).
* Zkopírujte aktualizované databáze z fondu zotavení po Havárii do primárního fondu (8).
* Odstranění fondu zotavení po Havárii (9)

V tomto okamžiku je vaše aplikace online v primární oblasti se všemi databázemi klientů, které jsou k dispozici v primárním fondu.

![Obrázek 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

Hlavní **výhodou** této strategie jsou nízké průběžné náklady na redundanci datové vrstvy. Zálohy jsou automaticky prováděny službou SQL Database bez přepsání aplikace a bez dalších nákladů.  Náklady vznikají pouze v případě, že jsou obnoveny elastické databáze. **Kompromis** je, že úplné obnovení všech databází klienta trvá významnou dobu. Doba závisí na celkovém počtu obnovení, které zahájíte v oblasti zotavení po Havárii, a na celkové velikosti databází klienta. I v případě, že upřednostňujete obnovení některých klientů před jinými, konkurujete všem ostatním obnovením, která jsou iniciována ve stejné oblasti jako rozhodčí a omezení služby, abyste minimalizovali celkový dopad na databáze stávajících zákazníků. Kromě toho obnovení databází klienta nelze spustit, dokud není vytvořen nový elastický fond v oblasti zotavení po Havárii.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Scénář 2. Vyspělá aplikace s vrstvenou službou

Jsem zralá aplikace SaaS s odstupňovanými nabídkami služeb a různými službami SLA pro zkušební zákazníky a pro platící zákazníky. Pro zkušební zákazníky, musím snížit náklady co nejvíce. Zkušební zákazníci mohou mít prostoje, ale chci snížit jeho pravděpodobnost. Pro platící zákazníky je jakékoli prostoje rizikem útěku. Takže se chci ujistit, že platící zákazníci mají vždy přístup ke svým datům.

Chcete-li tento scénář podpořit, oddělte zkušební klienty od placených klientů jejich umístěním do samostatných elastických fondů. Zkušební zákazníci mají nižší eDTU nebo virtuální jádra na klienta a nižší SLA s delší dobou obnovení. Platící zákazníci jsou ve fondu s vyšší eDTU nebo virtuálními jádry na tenanta a vyšší sla. Chcete-li zaručit nejnižší dobu obnovení, jsou databáze klienta platících zákazníků geograficky replikovány. Tato konfigurace je znázorněna na následujícím diagramu.

![Obrázek 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Stejně jako v prvním scénáři jsou databáze pro správu poměrně aktivní, takže pro něj použijete jednu geograficky replikovanou databázi (1). Tím je zajištěn předvídatelný výkon pro nová zákaznická předplatná, aktualizace profilů a další operace správy. Oblast, ve které jsou umístěny primární databáze správy, je primární oblast a oblast, ve které jsou umístěny sekundární databáze správy, je oblast zotavení po havárii.

Databáze klientů platících zákazníků mají aktivní databáze ve fondu "placené" zřízené v primární oblasti. Zřídit sekundární fond se stejným názvem v oblasti zotavení po Havárii. Každý klient je geograficky replikovány do sekundárního fondu (2). To umožňuje rychlé obnovení všech databází klienta pomocí převzetí služeb při selhání.

Pokud dojde k výpadku v primární oblasti, kroky obnovení pro uvedení aplikace do režimu online jsou znázorněny v následujícím diagramu:

![Obrázek 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Okamžitě převzetí služeb při selhání databází správy do oblasti zotavení po Havárii (3).
* Změňte připojovací řetězec aplikace tak, aby přecšlákem na oblast zotavení po Havárii. Nyní jsou všechny nové účty a databáze klientů vytvořeny v oblasti zotavení po Havárii. Stávajícím zákazníkům zkušební verze se jejich data dočasně nedají.
* Převzetí služeb při selhání databází placeného klienta do fondu v oblasti zotavení po Havárii okamžitě obnovit jejich dostupnost (4). Vzhledem k tomu, že převzetí služeb při selhání je rychlá změna úrovně metadat, zvažte optimalizaci, kde jsou jednotlivá převzetí služeb při selhání spuštěna na vyžádání připojením koncových uživatelů.
* Pokud vaše velikost eDTU sekundárního fondu nebo hodnota virtuálního jádra byla nižší než primární, protože sekundární databáze vyžadovaly pouze kapacitu pro zpracování protokolů změn, zatímco byly sekundární, okamžitě zvýšit kapacitu fondu nyní tak, aby vyhovovala úplnému pracovnímu vytížení všech nájemců (5).
* Vytvořte nový elastický fond se stejným názvem a stejnou konfigurací v oblasti zotavení po Havárii pro databáze zkušebních zákazníků (6).
* Po vytvoření fondu zkušebních zákazníků použijte geografické obnovení k obnovení jednotlivých databází zkušebních klientů do nového fondu (7). Zvažte aktivaci jednotlivých obnovení připojení koncových uživatelů nebo použijte jiné schéma priority specifické pro aplikaci.

V tomto okamžiku je aplikace zpět online v oblasti zotavení po Havárii. Všichni platící zákazníci mají přístup ke svým datům, zatímco zkušební zákazníci mají zpoždění při přístupu ke svým datům.

Když primární oblast obnoví Azure *po* obnovení aplikace v oblasti zotavení po Havárii můžete pokračovat ve spuštění aplikace v této oblasti nebo se můžete rozhodnout pro obnovení služeb při selhání do primární oblasti. Pokud primární oblast je obnovena *před* dokončením procesu převzetí služeb při selhání, zvažte vrácení služeb při selhání ihned. Navrácení služeb po selhání provede kroky znázorněné v následujícím diagramu:

![Obrázek 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Zrušte všechny nevyřízené požadavky na geografické obnovení.
* Převzetí služeb při selhání databází pro správu (8). Po obnovení oblasti se staré primární automaticky stanou sekundárními. Nyní se opět stává primárním.  
* Převzetí služeb při selhání databáze placeného klienta (9). Podobně i po zotavení regionu se staré primárky automaticky stávají sekundárními. Teď se znovu stanou primárkami.
* Nastavte obnovené zkušební databáze, které se změnily v oblasti zotavení po Havárii, na jen pro čtení (10).
* Pro každou databázi ve zkušebním fondu zákazníků zotavení po havárii, která se změnila od obnovení, přejmenujte nebo odstraňte odpovídající databázi v primárním fondu zákazníků zkušební verze (11).
* Zkopírujte aktualizované databáze z fondu zotavení po Havárii do primárního fondu (12).
* Odstraňte fond zotavení po havárii (13).

> [!NOTE]
> Operace převzetí služeb při selhání je asynchronní. Chcete-li minimalizovat dobu obnovení, je důležité provést příkaz převzetí služeb při selhání databází klienta v dávkách alespoň 20 databází.

Hlavní **výhodou** této strategie je, že poskytuje nejvyšší sla pro platící zákazníky. Také zaručuje, že nové zkušební verze jsou odblokovány, jakmile je vytvořen fond zkušební verze DR. **Kompromis emituje,** že toto nastavení zvyšuje celkové náklady na databáze klienta o náklady na sekundární fond zotavení po havárii pro placené zákazníky. Kromě toho pokud sekundární fond má jinou velikost, platící zákazníci zaznamenat nižší výkon po převzetí služeb při selhání, dokud upgrade fondu v oblasti zotavení po Havárii je dokončena.

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Scénář 3. Geograficky distribuovaná aplikace s vrstvenou službou

Mám zralou aplikaci SaaS s odstupňovanými nabídkami služeb. Chci nabídnout svým placeným zákazníkům velmi agresivní sla a minimalizovat riziko dopadu, když dojde k výpadkům, protože i krátké přerušení může způsobit nespokojenost zákazníků. Je velmi důležité, aby platící zákazníci měli vždy přístup ke svým údajům. Zkušební verze jsou zdarma a sla není nabízena během zkušebního období.

Pro podporu tohoto scénáře použijte tři samostatné elastické fondy. Zřídit dva fondy stejné velikosti s vysokými eDTU nebo virtuálními jádry na databázi ve dvou různých oblastech, aby obsahovaly databáze klientů placených zákazníků. Třetí fond obsahující zkušební klienty může mít nižší eDTU nebo virtuální jádra na databázi a zřídit v jedné ze dvou oblastí.

Chcete-li zaručit nejnižší dobu obnovení během výpadků, databáze klienta platících zákazníků jsou geograficky replikovány s 50 % primárních databází v každé ze dvou oblastí. Podobně každá oblast má 50 % sekundárních databází. Tímto způsobem, pokud oblast je offline, pouze 50 % databází placených zákazníků jsou ovlivněny a musí převzetí služeb při selhání. Ostatní databáze zůstanou neporušené. Tato konfigurace je znázorněna v následujícím diagramu:

![Obrázek 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Stejně jako v předchozích scénářích jsou databáze pro správu poměrně aktivní, proto je nakonfigurujte jako jednu geograficky replikovanou databázi (1). Tím je zajištěn předvídatelný výkon nových zákaznických předplatných, aktualizací profilů a dalších operací správy. Oblast A je primární oblastí pro databáze správy a oblast B se používá pro obnovení databází pro správu.

Databáze klientů platících zákazníků jsou také geograficky replikovány, ale s primárkami a sekundárními databázemi rozdělenými mezi oblast A a oblast B (2). Tímto způsobem primární databáze klienta ovlivněné výpadku můžete převzetí služeb při selhání do jiné oblasti a být k dispozici. Druhá polovina databáze klienta nejsou ovlivněny vůbec.

Další diagram znázorňuje kroky obnovení, které je třeba provést, pokud dojde k výpadku v oblasti A.

![Obrázek 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Okamžitě převzetí služeb při selhání databází správy do oblasti B (3).
* Změňte připojovací řetězec aplikace tak, aby přešel na databáze správy v oblasti B. Upravte databáze správy a ujistěte se, že nové účty a databáze klientů jsou vytvořeny v oblasti B a že se zde nacházejí také existující databáze klientů. Stávajícím zákazníkům zkušební verze se jejich data dočasně nedají.
* Převzetí služeb při selhání databáze placeného klienta do fondu 2 v oblasti B okamžitě obnovit jejich dostupnost (4). Vzhledem k tomu, že převzetí služeb při selhání je rychlá změna úrovně metadat, můžete zvážit optimalizaci, kde jsou jednotlivá převzetí služeb při selhání spuštěna na vyžádání připojením koncových uživatelů.
* Vzhledem k tomu, že fond 2 obsahuje pouze primární databáze, celkové zatížení ve fondu se zvyšuje a může okamžitě zvýšit jeho velikost eDTU (5) nebo počet virtuálních jader.
* Vytvořte nový elastický fond se stejným názvem a stejnou konfigurací v oblasti B pro databáze zkušebních zákazníků (6).
* Po vytvoření fondu použijte geografické obnovení k obnovení databáze jednotlivých zkušebních klientů do fondu (7). Můžete zvážit aktivaci jednotlivých obnovení pomocí připojení koncových uživatelů nebo použít jiné schéma priority specifické pro aplikaci.

> [!NOTE]
> Operace převzetí služeb při selhání je asynchronní. Chcete-li minimalizovat dobu obnovení, je důležité spustit příkaz převzetí služeb při selhání databáze klienta v dávkách alespoň 20 databází.

V tomto okamžiku je vaše aplikace zpět online v oblasti B. Všichni platící zákazníci mají přístup ke svým datům, zatímco zkušební zákazníci mají zpoždění při přístupu ke svým datům.

Při obnovení oblasti A se musíte rozhodnout, zda chcete použít oblast B pro zkušební zákazníky nebo navrácení služeb po obnovení k použití fondu zkušebních zákazníků v oblasti A. Jedním z kritérií může být % zkušební databáze klienta změněné od obnovení. Bez ohledu na toto rozhodnutí je třeba znovu vyvážit placené nájemníky mezi dvěma fondy. další diagram znázorňuje proces, kdy databáze zkušebního klienta obnovení do oblasti A.  

![Obrázek 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Zrušte všechny nevyřízené požadavky na geografické obnovení do zkušebního fondu zotavení po havárii.
* Převzetí služeb při selhání databáze správy (8). Po obnovení oblasti se staré primární automaticky staly sekundárními. Nyní se opět stává primárním.  
* Vyberte, které placené databáze klientů zpět do fondu 1 a zahájit převzetí služeb při selhání na jejich sekundární (9). Po obnovení oblasti všechny databáze ve fondu 1 se automaticky staly sekundárními. Nyní se 50% z nich opět stane primárkami.
* Zmenšete velikost fondu 2 na původní eDTU (10) nebo počet virtuálních jader.
* Nastavte všechny obnovené zkušební databáze v oblasti B jen pro čtení (11).
* Pro každou databázi ve fondu zkušební zotavení po havárii, která se změnila od obnovení, přejmenujte nebo odstraňte odpovídající databázi ve zkušebním primárním fondu (12).
* Zkopírujte aktualizované databáze z fondu zotavení po Havárii do primárního fondu (13).
* Odstraňte fond zotavení po havárii (14).

Hlavní **výhody** této strategie jsou:

* Podporuje nejagresivnější sla pro platící zákazníky, protože zajišťuje, že výpadek nemůže ovlivnit více než 50 % databází klientů.
* Zaručuje, že nové zkušební verze jsou odblokovány, jakmile je během obnovení vytvořen fond zotavení po havárii.
* Umožňuje efektivnější využití kapacity fondu jako 50 % sekundárnídatabáze ve fondu 1 a fondu 2 je zaručeno, že bude méně aktivní než primární databáze.

Hlavními **kompromisy** jsou:

* Operace CRUD proti databázím pro správu mají nižší latenci pro koncové uživatele připojené k oblasti A než pro koncové uživatele připojené k oblasti B, protože jsou prováděny proti primární masy databází pro správu.
* Vyžaduje složitější návrh databáze pro správu. Například každý záznam klienta má značku umístění, která je třeba změnit během převzetí služeb při selhání a navrácení služeb po selhání.  
* Platící zákazníci mohou zaznamenat nižší výkon než obvykle, dokud nebude dokončen upgrade fondu v oblasti B.

## <a name="summary"></a>Souhrn

Tento článek se zaměřuje na strategie zotavení po havárii pro databázovou vrstvu používanou víceklientské aplikace SaaS ISV. Strategie, kterou zvolíte, je založena na potřebách aplikace, jako je obchodní model, sla, kterou chcete nabídnout svým zákazníkům, omezení rozpočtu atd. Každá popsaná strategie nastiňuje výhody a kompromis, abyste mohli učinit informované rozhodnutí. Vaše konkrétní aplikace také pravděpodobně zahrnuje další součásti Azure. Takže si zkontrolovat jejich kontinuitu podnikání pokyny a organizovat obnovení databázové vrstvy s nimi. Další informace o správě obnovení databázových aplikací v Azure najdete v [části Návrh cloudových řešení pro zotavení po havárii](sql-database-designing-cloud-solutions-for-disaster-recovery.md).  

## <a name="next-steps"></a>Další kroky

* Informace o automatickém zálohování azure sql database najdete v tématu [automatické zálohování databáze SQL Database](sql-database-automated-backups.md).
* Přehled kontinuity provozu a scénáře najdete v tématu [Přehled kontinuity provozu](sql-database-business-continuity.md).
* Další informace o použití automatických záloh pro obnovení naleznete [v tématu obnovení databáze ze záloh iniciovaných službou](sql-database-recovery-using-backups.md).
* Informace o rychlejších možnostech obnovení naleznete [v tématu Aktivní geografická replikace](sql-database-active-geo-replication.md) a [skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md).
* Informace o používání automatických záloh pro archivaci naleznete v [tématu kopírování databáze](sql-database-copy.md).
