---
title: Kontinuita provozu cloudu – obnovení databáze
description: Zjistěte, jak Azure SQL Database podporuje provozní kontinuitu v cloudu a obnovení databází a jak pomáhá udržovat klíčové cloudové aplikace v chodu.
keywords: provozní kontinuita, provozní kontinuita v cloudu, zotavení databáze po havárii, obnovení databáze
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 4f30bf112175742566c2957d78154e5a7abd1733
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096861"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Přehled provozní kontinuity se službou Azure SQL Database

**Kontinuita podnikání** v Azure SQL Database odkazuje na mechanismy, zásady a postupy, které umožňují vaší firmě pokračovat v provozu tváří v tvář narušení, zejména jeho výpočetní infrastruktury. Ve většině případů azure SQL database bude zpracovávat rušivé události, které se mohou stát v cloudovém prostředí a udržet vaše aplikace a obchodní procesy spuštěné. Existují však některé rušivé události, které nelze automaticky zpracovat databází SQL, například:

- Uživatel omylem odstranil nebo aktualizoval řádek v tabulce.
- Útočníkse zlými úmysly úspěšně odstranil data nebo přetažení databáze.
- Zemětřesení způsobilo výpadek proudu a dočasné zakázané datové centrum.

Tento přehled popisuje možnosti, které služba Azure SQL Database nabízí pro zajištění provozní kontinuity a zotavení po havárii. Další informace o možnostech, doporučeních a kurzech pro obnovení z rušivých událostí, které by mohly způsobit ztrátu dat nebo způsobit nedostupnost databáze a aplikace. Zjistěte, co dělat, když chyba uživatele nebo aplikace ovlivňuje integritu dat, oblast Azure má výpadek nebo vaše aplikace vyžaduje údržbu.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funkce služby SQL Database, pomocí kterých můžete zajistit provozní kontinuitu

Z hlediska databáze existují čtyři hlavní scénáře potenciální narušení:

- Místní selhání hardwaru nebo softwaru ovlivňující uzel databáze, například selhání diskové jednotky.
- Poškození dat nebo odstranění obvykle způsobené chybou aplikace nebo lidskou chybou. Tyto chyby jsou specifické pro aplikaci a databázové služby je obvykle nezjistí.
- Výpadek datového centra, pravděpodobně způsobený přírodní katastrofou. Tento scénář vyžaduje určitou úroveň geografické redundance s převzetím služeb při selhání aplikace do alternativního datového centra.
- Chyby upgradu nebo údržby, neočekávané problémy, ke kterým dochází během plánované údržby infrastruktury nebo upgradů, mohou vyžadovat rychlé vrácení do předchozího stavu databáze.

Chcete-li zmírnit selhání místního hardwaru a softwaru, SQL Database obsahuje [architekturu s vysokou dostupností](sql-database-high-availability.md), která zaručuje automatické obnovení z těchto selhání s až 99,995% dostupnost sla.  

Chcete-li chránit vaše podnikání před ztrátou dat, SQL Database automaticky vytvoří úplné zálohování databáze týdně, rozdílové zálohování databáze každých 12 hodin a zálohy transakčníprotokol každých 5 - 10 minut . Zálohy jsou uloženy v úložišti RA-GRS po dobu nejméně 7 dnů pro všechny úrovně služeb. Všechny úrovně služeb s výjimkou základní podpory konfigurovatelné doby uchovávání záloh pro obnovení v čase, až 35 dní. 

SQL Database také poskytuje několik funkcí kontinuity podnikání, které můžete použít ke zmírnění různých neplánovaných scénářů. 

- [Dočasné tabulky](sql-database-temporal-tables.md) umožňují obnovit verze řádků z jakéhokoli bodu v čase.
- [Integrované automatické zálohování](sql-database-automated-backups.md) a [obnovení bodu v čase](sql-database-recovery-using-backups.md#point-in-time-restore) umožňuje obnovit úplnou databázi do určitého bodu v čase v rámci nakonfigurované doby uchovávání až 35 dní.
- [Odstraněnou databázi](sql-database-recovery-using-backups.md#deleted-database-restore) můžete obnovit do bodu, ve kterém byla **odstraněna,** pokud databázový server SQL nebyl odstraněn .
- [Dlouhodobé uchovávání záloh](sql-database-long-term-retention.md) umožňuje uchovávat zálohy až 10 let.
- [Aktivní geografická replikace](sql-database-active-geo-replication.md) umožňuje vytvořit čitelné repliky a ručně překračovat služby při selhání libovolné repliky v případě výpadku datového centra nebo upgradu aplikace.
- [Skupina automatického převzetí služeb při selhání](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) umožňuje aplikaci automatické obnovení v případě výpadku datového centra.

## <a name="recover-a-database-within-the-same-azure-region"></a>Obnovení databáze ve stejné oblasti Azure

Automatické zálohování databáze můžete použít k obnovení databáze do bodu v čase v minulosti. Tímto způsobem můžete obnovit z poškození dat způsobené lidské chyby. Obnovení bodu v čase umožňuje vytvořit novou databázi na stejném serveru, která představuje stav dat před poškozením události. U většiny databází trvá operace obnovení méně než 12 hodin. Obnovení velmi velké nebo velmi aktivní databáze může trvat déle. Další informace o době obnovení naleznete v [tématu doba obnovení databáze](sql-database-recovery-using-backups.md#recovery-time). 

Pokud maximální podporovaná doba uchovávání záloh pro obnovení bodu v čase (PITR) není dostatečná pro vaši aplikaci, můžete ji prodloužit konfigurací zásad dlouhodobého uchovávání (LTR) pro databáze. Další informace naleznete [v tématu Dlouhodobé uchovávání záloh](sql-database-long-term-retention.md).

## <a name="compare-geo-replication-with-failover-groups"></a>Porovnání geografické replikace se skupinami převzetí služeb při selhání

[Skupiny s automatickým převzetím služeb při selhání](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) zjednodušují nasazení a použití [geografické replikace](sql-database-active-geo-replication.md) a přidávají další funkce, jak je popsáno v následující tabulce:

|                                              | Geografická replikace | Skupiny převzetí služeb při selhání  |
|:---------------------------------------------| :-------------- | :----------------|
| Automatické převzetí služeb při selhání                           |     Ne          |      Ano         |
| Převzetí služeb při selhání více databází současně  |     Ne          |      Ano         |
| Uživatel musí aktualizovat připojovací řetězec po převzetí služeb při selhání      |     Ano         |      Ne          |
| Podporovaná spravovaná instance                   |     Ne          |      Ano         |
| Může být ve stejné oblasti jako primární             |     Ano         |      Ne          |
| Více replik                            |     Ano         |      Ne          |
| Podporuje čtení-měřítko                          |     Ano         |      Ano         |
| &nbsp; | &nbsp; | &nbsp; |


## <a name="recover-a-database-to-the-existing-server"></a>Obnovení databáze na existující server

Přestože je taková situace výjimečná, i u datového centra Azure může dojít k výpadku. Při výpadku dojde k narušení provozu, které může trvat jen několik minut nebo až několik hodin.

- Jednou z možností je počkat, až výpadek skončí a databáze se vrátí do režimu online. Tento postup funguje pro aplikace, které si mohou dovolit mít databázi v režimu offline. Například vývojový projekt nebo bezplatná zkušební verze, na které nemusíte neustále pracovat. Pokud má datové centrum výpadek, nevíte, jak dlouho může výpadek trvat, takže tato možnost funguje pouze v případě, že databázi nějakou dobu nepotřebujete.
- Další možností je obnovení databáze na libovolném serveru v libovolné oblasti Azure pomocí [geograficky redundantních záloh databáze](sql-database-recovery-using-backups.md#geo-restore) (geografické obnovení). Geografické obnovení používá geograficky redundantní zálohu jako zdroj a lze ji použít k obnovení databáze i v případě, že databáze nebo datové centrum je nepřístupné z důvodu výpadku.
- Nakonec můžete rychle obnovit z výpadku, pokud jste nakonfigurovali geosekundární pomocí [aktivní geografické replikace](sql-database-active-geo-replication.md) nebo [skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md) pro vaši databázi nebo databáze. V závislosti na výběru těchto technologií můžete použít ruční nebo automatické převzetí služeb při selhání. Zatímco převzetí služeb při selhání trvá pouze několik sekund, služba bude trvat nejméně 1 hodinu k jeho aktivaci. To je nezbytné k zajištění toho, aby převzetí služeb při selhání bylo odůvodněno rozsahem výpadku. Převzetí služeb při selhání může také způsobit malou ztrátu dat z důvodu povahy asynchronní replikace. 

Při vývoji plánu provozní kontinuity musíte pochopit maximální přijatelnou dobu úplného zotavení aplikace po ničivé události. Doba potřebná pro aplikaci k úplnému obnovení se označuje jako cíl doby obnovení (RTO). Musíte také pochopit maximální dobu nedávných aktualizací dat (časový interval), kterou aplikace může tolerovat ztrátu při zotavení z neplánované rušivé události. Potenciální ztráta dat se označuje jako cíl bodu obnovení (RPO).

Různé metody obnovení nabízejí různé úrovně RPO a RTO. Můžete zvolit konkrétní metodu obnovení nebo použít kombinaci metod k dosažení úplného obnovení aplikace. Následující tabulka porovnává RPO a RTO každé možnosti obnovení. Skupiny s automatickým převzetím služeb při selhání zjednodušují nasazení a použití geografické replikace a přidávají další funkce, jak je popsáno v následující tabulce.

| Metoda obnovy | Rto | Cíl bodu obnovení |
| --- | --- | --- | 
| Geografické obnovení z geograficky replikovaných záloh | 12 h | 1 h |
| Skupiny automatického převzetí služeb při selhání | 1 h | 5 s |
| Ruční převzetí služeb při selhání databáze | 30 s | 5 s |

> [!NOTE]
> *Ruční převzetí služeb při selhání databáze* odkazuje na převzetí služeb při selhání jedné databáze na jeho geograficky replikované sekundární pomocí [neplánovaného režimu](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).
Podrobnosti o rto automaticképřevzetí služeb při selhání a rpo naleznete v tabulce dříve v tomto článku.


Skupiny s podporou převzetí služeb při selhání použijte, pokud vaše aplikace splňuje některé z těchto kritérií:

- Je zvlášť důležitá.
- Má smlouvu o úrovni služeb (SLA), která neumožňuje 12 hodin nebo více prostojů.
- Prostoje mohou mít za následek finanční odpovědnost.
- Má vysokou míru změny dat a 1 hodina ztráty dat není přijatelná.
- Další náklady na aktivní geografickou replikaci jsou nižší než potenciální finanční závazky a související ztráta podnikání.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

Můžete použít kombinaci záloh databáze a aktivní geografické replikace v závislosti na požadavcích aplikace. Diskuse o aspektech návrhu pro samostatné databáze a pro elastické fondy pomocí těchto funkcí kontinuity podnikání, najdete v článku [Navrhnout aplikaci pro cloud zotavení po havárii](sql-database-designing-cloud-solutions-for-disaster-recovery.md) a [elastického fondu strategie zotavení po havárii](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

Následující části poskytují přehled kroků k obnovení pomocí záloh databáze nebo aktivní geografické replikace. Podrobné kroky, včetně požadavků na plánování, kroky po obnovení a informace o tom, jak simulovat výpadek k provedení vrtáku zotavení po havárii, naleznete v [tématu Obnovení databáze SQL z výpadku](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Příprava na výpadek

Bez ohledu na funkce provozní kontinuity, které používáte, musíte:

- Identifikujte a připravte cílový server, včetně pravidel brány firewall IP na úrovni serveru, přihlášení a oprávnění na úrovni hlavní databáze.
- Určit, jak přesměrovat klienty a klientské aplikace na nový server
- Zdokumentovat další závislosti, například nastavení auditování a výstrahy

Pokud se nepřipravíte správně, uvedení aplikací do režimu online po převzetí služeb při selhání nebo obnovení databáze trvá další čas a pravděpodobně také vyžadují řešení potíží v době stresu - špatná kombinace.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Převzetí služeb při selhání do geograficky replikované sekundární databáze

Pokud jako mechanismus obnovení používáte aktivní skupiny geografické replikace nebo automatické převzetí služeb při selhání, můžete nakonfigurovat automatické zásady převzetí služeb při selhání nebo použít [ruční neplánované převzetí služeb při selhání](sql-database-active-geo-replication-portal.md#initiate-a-failover). Po zahájení, převzetí služeb při selhání způsobí, že sekundární, aby se stal novým primární a připraven k záznamu nových transakcí a reagovat na dotazy – s minimální ztrátou dat pro data, která ještě nejsou replikována. Informace o návrhu procesu převzetí služeb při selhání naleznete v [tématu Návrh aplikace pro cloudzotavení po havárii](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Když se datové centrum vrátí do režimu online, staré primárky se automaticky znovu připojí k nové primární a stanou se sekundárními databázemi. Pokud potřebujete přemístit primární zpět do původní oblasti, můžete zahájit plánované převzetí služeb při selhání ručně (navrácení služeb po obnovení).

### <a name="perform-a-geo-restore"></a>Provedení geografického obnovení

Pokud používáte automatické zálohování s geograficky redundantním úložištěm (ve výchozím nastavení povolené), můžete databázi obnovit pomocí [geografického obnovení](sql-database-disaster-recovery.md#recover-using-geo-restore). Obnovení obvykle probíhá během 12 hodin – se ztrátou dat až jednu hodinu určenou tím, kdy byla přijata a replikována poslední záloha protokolu. Dokud se obnovení nedokončí, databáze není schopná zaznamenávat žádné transakce ani reagovat na dotazy. Všimněte si, že geografické obnovení pouze obnoví databázi do posledního dostupného bodu v čase.

> [!NOTE]
> Pokud datové centrum přejde zpět do režimu online před přepnutím aplikace do obnovené databáze, můžete obnovení zrušit.

### <a name="perform-post-failover--recovery-tasks"></a>Provedení úloh po převzetí služeb při selhání nebo obnovení

Po obnovení s použitím libovolného mechanismu musíte provést následující dodatečné úlohy, abyste pro uživatele zprovoznili své aplikace:

- Přesměrujte klienty a klientské aplikace na nový server a obnovenou databázi.
- Ujistěte se, že jsou k tomu, aby se uživatelé mohli připojovat nebo používat [brány firewall na úrovni databáze,](sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) aby bylo možné povolit příslušná pravidla, zajistěte, aby byla zavedena vhodná pravidla.
- Ujistěte se, že se používají odpovídající přihlášení a oprávnění na úrovni hlavní databáze (nebo použijte [obsažené uživatelé](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable))
- Podle potřeby nakonfigurujte auditování.
- Podle potřeby nakonfigurujte výstrahy.

> [!NOTE]
> Pokud používáte skupinu převzetí služeb při selhání a připojit k databázím pomocí lstener pro čtení a zápis, přesměrování po převzetí služeb při selhání dojde automaticky a transparentně do aplikace.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Upgrade aplikace s minimálními výpadky

Někdy aplikace musí být přepnuta do režimu offline z důvodu plánované údržby, jako je například upgrade aplikace. [Správa upgradů aplikací](sql-database-manage-application-rolling-upgrade.md) popisuje, jak pomocí aktivní geografické replikace povolit postupné upgrady vaší cloudové aplikace, aby se minimalizovaly prostoje během upgradů a poskytnout cestu pro obnovení, pokud se něco pokazí.

## <a name="next-steps"></a>Další kroky

Diskuse o aspektech návrhu aplikace pro samostatné databáze a pro elastické fondy najdete v [tématu Návrh aplikace pro cloud zotavení po havárii](sql-database-designing-cloud-solutions-for-disaster-recovery.md) a [elastické fondy strategie zotavení po havárii](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
