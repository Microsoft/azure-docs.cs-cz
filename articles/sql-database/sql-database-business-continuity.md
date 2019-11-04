---
title: Provozní kontinuita v cloudu – obnovení databází – SQL Database | Dokumentace Microsoftu
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
ms.openlocfilehash: 69ff1a5681fbb0b434d7114b069610ed34d9e843
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492206"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Přehled provozní kontinuity se službou Azure SQL Database

**Kontinuita podnikových** procesů v Azure SQL Database odkazuje na mechanismy, zásady a postupy, které umožní vašemu podniku pokračovat v práci na tváři přerušení, zejména pro výpočetní infrastrukturu. Ve většině případů Azure SQL Database zpracuje rušivé události, které se mohou vyskytnout v cloudovém prostředí, a udržuje aplikace a obchodní procesy spuštěné. Existují však některé rušivé události, které nemohou být zpracovány SQL Database automaticky jako:

- Uživatel omylem odstranil nebo aktualizoval řádek v tabulce.
- Škodlivý útočník úspěšně odstranil data nebo vyřazení databáze.
- Zemětřesení způsobil výpadek napájení a dočasné zakázané datové centrum.

Tento přehled popisuje možnosti, které služba Azure SQL Database nabízí pro zajištění provozní kontinuity a zotavení po havárii. Seznamte se s možnostmi, doporučeními a kurzy pro obnovování z rušivých událostí, které mohou způsobit ztrátu dat nebo způsobit nedostupnost databáze a aplikace. Přečtěte si, co dělat, když dojde k chybě uživatele nebo aplikace při vlivu na integritu dat, když dojde k výpadku oblasti Azure nebo když vaše aplikace vyžaduje údržbu.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funkce služby SQL Database, pomocí kterých můžete zajistit provozní kontinuitu

Z perspektivy databáze existují čtyři scénáře s případnými výpadky:

- Selhání místních hardwarových nebo softwarových uzlů ovlivňujících uzel databáze, jako je například selhání diskového jednotky.
- Poškození dat nebo odstranění je obvykle způsobeno chybou aplikace nebo lidskou chybou. Taková selhání jsou specifická pro jednotlivé aplikace a obvykle se nedají detekovat databázovou službou.
- Výpadek datového centra, možná způsobené přírodní katastrofou. Tento scénář vyžaduje určitou úroveň geografické redundance s převzetím služeb při selhání do alternativního datového centra.
- Chyby upgradu nebo údržby, neočekávané problémy, ke kterým dojde během plánované údržby nebo údržby infrastruktury, mohou vyžadovat rychlé vrácení zpět do předchozího stavu databáze.

Pro zmírnění selhání místního hardwaru a softwaru SQL Database zahrnuje [architekturu s vysokou dostupností](sql-database-high-availability.md), která garantuje automatické obnovení z těchto selhání s 99,995% dostupností smlouvy SLA.  

Z důvodu ochrany vaší firmy před ztrátou dat SQL Database automaticky vytvoří úplné zálohy databáze každý týden, odrozdílové zálohy databáze každých 12 hodin a zálohování protokolů transakcí každé 5-10 minut. Zálohy se ukládají do úložiště RA-GRS po dobu alespoň 7 dnů pro všechny úrovně služeb. Všechny úrovně služeb s výjimkou podpora Basic konfigurovatelný doba uchovávání záloh pro obnovení k určitému bodu v čase, až 35 dní. 

SQL Database také nabízí několik funkcí provozní kontinuity, které můžete použít ke zmírnění různých neplánovaných scénářů. 

- [Dočasné tabulky](sql-database-temporal-tables.md) umožňují obnovit verze řádků z jakéhokoli bodu v čase.
- [Integrované automatické zálohování](sql-database-automated-backups.md) a obnovení k určitému [bodu v čase](sql-database-recovery-using-backups.md#point-in-time-restore) umožňují obnovení kompletní databáze k určitému časovému okamžiku v rámci nakonfigurované doby uchování až do 35 dnů.
- [Odstraněnou databázi můžete obnovit](sql-database-recovery-using-backups.md#deleted-database-restore) do bodu, ve kterém byla odstraněna, pokud **SQL Database Server nebyl odstraněn**.
- [Dlouhodobé uchovávání záloh](sql-database-long-term-retention.md) umožňuje uchovávat zálohy až po dobu 10 let.
- [Aktivní geografická replikace](sql-database-active-geo-replication.md) umožňuje vytvářet čitelné repliky a ručně převzetí služeb při selhání pro všechny repliky v případě výpadku datového centra nebo upgradu aplikace.
- [Skupina automatického převzetí služeb při selhání](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) umožňuje aplikaci automaticky obnovit v případě výpadku datového centra.

## <a name="recover-a-database-within-the-same-azure-region"></a>Obnovení databáze ve stejné oblasti Azure

Automatické zálohování databáze můžete použít k obnovení databáze k určitému bodu v čase v minulosti. Tímto způsobem se můžete zotavit z poškození dat způsobených lidmi chybami. Obnovení k určitému bodu v čase umožňuje vytvořit novou databázi na stejném serveru, který představuje stav dat před poškozenou událostí. U většiny databází trvá operace obnovení méně než 12 hodin. Obnovení velmi rozsáhlé nebo vysoce aktivní databáze může trvat déle. Další informace o čase obnovení najdete v tématu [čas obnovení databáze](sql-database-recovery-using-backups.md#recovery-time). 

Pokud není maximální podporovaná doba uchovávání záloh pro obnovení k určitému bodu v čase (PITR) dostatečná pro vaši aplikaci, můžete ji prodloužit konfigurací zásad pro dlouhodobé uchovávání (LTR) pro databáze. Další informace najdete v tématu [dlouhodobé uchovávání záloh](sql-database-long-term-retention.md).

## <a name="compare-geo-replication-with-failover-groups"></a>Porovnání geografické replikace se skupinami převzetí služeb při selhání

[Skupiny s automatickým převzetím služeb při selhání](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) zjednodušují nasazení a používání [geografické replikace](sql-database-active-geo-replication.md) a přidávají další možnosti, jak je popsáno v následující tabulce:

|                                              | Geografická replikace | Skupiny převzetí služeb při selhání  |
|:---------------------------------------------| :-------------- | :----------------|
| Automatické převzetí služeb při selhání                           |     Ne          |      Ano         |
| Převzetí služeb při selhání více databází současně  |     Ne          |      Ano         |
| Po převzetí služeb při selhání aktualizovat připojovací řetězec      |     Ano         |      Ne          |
| Spravovaná instance je podporovaná.                   |     Ne          |      Ano         |
| Může být ve stejné oblasti jako primární             |     Ano         |      Ne          |
| Více replik                            |     Ano         |      Ne          |
| Podporuje čtení i škálování.                          |     Ano         |      Ano         |
| &nbsp; | &nbsp; | &nbsp; |


## <a name="recover-a-database-to-the-existing-server"></a>Obnovení databáze na existujícím serveru

Přestože je taková situace výjimečná, i u datového centra Azure může dojít k výpadku. Při výpadku dojde k narušení provozu, které může trvat jen několik minut nebo až několik hodin.

- Jednou z možností je počkat, až výpadek skončí a databáze se vrátí do režimu online. Tento postup funguje pro aplikace, které si mohou dovolit mít databázi v režimu offline. Například vývojový projekt nebo bezplatná zkušební verze, na které nemusíte neustále pracovat. Když dojde k výpadku datového centra, nevíte, jak dlouho může výpadek trvat, takže tato možnost funguje jenom v případě, že už nepotřebujete databázi.
- Další možností je obnovit databázi na jakémkoli serveru v libovolné oblasti Azure pomocí [geograficky redundantních záloh databáze](sql-database-recovery-using-backups.md#geo-restore) (geografické obnovení). Geografické obnovení používá jako zdroj geograficky redundantní zálohu a dá se použít k obnovení databáze i v případě, že je databáze nebo datacentrum nedostupné kvůli výpadku.
- Nakonec můžete rychle obnovit z výpadku, pokud jste nakonfigurovali geograficky sekundární pomocí [aktivní geografické replikace](sql-database-active-geo-replication.md) nebo [skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md) pro vaši databázi nebo databáze. V závislosti na vaší volbě těchto technologií můžete použít buď ruční nebo automatické převzetí služeb při selhání. I když převzetí služeb při selhání zabere jenom pár sekund, bude tato služba při aktivaci trvat aspoň 1 hodinu. To je nezbytné k tomu, aby bylo zajištěno, že převzetí služeb při selhání je v rámci škálování výpadku oprávněné. Převzetí služeb při selhání může také vést k malé ztrátě dat z důvodu povahy asynchronní replikace. 

Při vývoji plánu provozní kontinuity musíte pochopit maximální přijatelnou dobu úplného zotavení aplikace po ničivé události. Čas potřebný k úplnému obnovení aplikace je známý jako cíl doby obnovení (RTO). Také je potřeba porozumět maximálnímu intervalu nedávných aktualizací dat (časový interval), které může aplikace tolerovat při obnovování z neplánované události rušivého vlivu na ztrátu. Potenciální ztráta dat se označuje jako cíl bodu obnovení (RPO).

Různé metody obnovení nabízejí různé úrovně bodu RPO a RTO. Můžete zvolit konkrétní metodu obnovení nebo použít kombinaci metod k dosažení úplného obnovení aplikace. Následující tabulka porovnává RPO a RTO jednotlivých možností obnovení. Skupiny automatického převzetí služeb při selhání zjednodušují nasazení a využití geografické replikace a přidávají další možnosti, jak je popsáno v následující tabulce.

| Metoda obnovení | RTO | OBNOVENÍ |
| --- | --- | --- | 
| Geografické obnovení ze geograficky replikovaných záloh | 12 h | 1 h |
| Skupiny automatického převzetí služeb při selhání | 1 h | 5 s |
| Ruční převzetí služeb při selhání databáze | 30 s | 5 s |

> [!NOTE]
> *Ruční převzetí služeb při selhání databáze* odkazuje na převzetí služeb při selhání izolované databáze na geograficky replikovanou sekundární práci pomocí [neplánovaného režimu](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).
Podrobnosti o automatických převzetí služeb při selhání RTO a RPO najdete v tabulce výše v tomto článku.


Pokud vaše aplikace splňuje některá z těchto kritérií, použijte skupiny automatického převzetí služeb při selhání:

- Je zvlášť důležitá.
- Má smlouvu o úrovni služeb (SLA), která neumožňuje 12 hodin nebo více výpadků.
- Výpadek může vést k finanční zodpovědnosti.
- Má vysokou míru změny dat a 1 hodina ztráty dat není přijatelné.
- Další náklady na aktivní geografickou replikaci jsou nižší než potenciální finanční závazky a související ztráta podnikání.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

V závislosti na požadavcích vaší aplikace se můžete rozhodnout použít kombinaci záloh databáze a aktivní geografické replikace. Diskuzi o požadavcích na návrh pro samostatné databáze a elastické fondy, které využívají tyto funkce pro provozní kontinuitu, najdete v tématu [Návrh aplikace pro zotavení po havárii cloudu](sql-database-designing-cloud-solutions-for-disaster-recovery.md) a [strategie zotavení po havárii elastického fondu](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

Následující části obsahují přehled kroků pro obnovení pomocí záloh databáze nebo aktivní geografické replikace. Podrobný postup, včetně požadavků na plánování, kroků po obnovení a informací o tom, jak simulovat výpadky při provádění postupu zotavení po havárii, najdete v tématu [obnovení SQL Database při výpadku](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Příprava na výpadek

Bez ohledu na funkce provozní kontinuity, které používáte, musíte:

- Identifikujte a připravte cílový server, včetně pravidel brány firewall na úrovni serveru, přihlašovacích údajů a oprávnění na úrovni hlavní databáze.
- Určit, jak přesměrovat klienty a klientské aplikace na nový server
- Zdokumentovat další závislosti, například nastavení auditování a výstrahy

Pokud nebudete připravovat správně, po převzetí služeb při selhání nebo obnovení databáze bude trvat déle a pravděpodobně budete potřebovat řešení potíží v době zátěže – Chybná kombinace.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Převzetí služeb při selhání geograficky replikovanou sekundární databází

Pokud jako mechanismus obnovení používáte aktivní geografickou replikaci nebo skupiny s automatickým převzetím služeb při selhání, můžete nakonfigurovat zásady automatického převzetí služeb při selhání nebo použít [Ruční neplánované převzetí služeb při](sql-database-active-geo-replication-portal.md#initiate-a-failover)selhání. Po zahájení převzetí služeb při selhání způsobí, že se sekundární stane novou primární a připravený k zaznamenávání nových transakcí a reakci na dotazy – s minimální ztrátou dat pro data, která se ještě nereplikují. Informace o návrhu procesu převzetí služeb při selhání najdete v tématu [Návrh aplikace pro zotavení po havárii cloudu](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Když se datové centrum vrátí do režimu online, staré primární základní (Base) se automaticky znovu připojí k nové primární databázi a stanou se sekundárními databázemi. Pokud potřebujete přemístit primární zpátky do původní oblasti, můžete plánované převzetí služeb při selhání iniciovat ručně (navrácení služeb po obnovení).

### <a name="perform-a-geo-restore"></a>Provést geografickou obnovu

Pokud používáte automatizované zálohování s geograficky redundantním úložištěm (povoleno ve výchozím nastavení), můžete obnovit databázi pomocí [geografického obnovení](sql-database-disaster-recovery.md#recover-using-geo-restore). K obnovení obvykle dochází během 12 hodin – s ztrátou dat po dobu až jedné hodiny, kterou určuje čas, kdy byla provedena a replikována poslední záloha protokolu. Dokud se obnovení nedokončí, databáze není schopná zaznamenávat žádné transakce ani reagovat na dotazy. Poznámka: geografická obnova obnovuje databázi pouze do posledního dostupného bodu v čase.

> [!NOTE]
> Pokud se datové centrum vrátí do režimu online předtím, než přepnete aplikaci do obnovené databáze, můžete obnovení zrušit.

### <a name="perform-post-failover--recovery-tasks"></a>Provedení úloh po převzetí služeb při selhání nebo obnovení

Po obnovení s použitím libovolného mechanismu musíte provést následující dodatečné úlohy, abyste pro uživatele zprovoznili své aplikace:

- Přesměrujte klienty a klientské aplikace na nový server a obnovenou databázi.
- Zajistěte, aby se pro uživatele připojovala příslušná pravidla brány firewall na úrovni serveru, aby je bylo možné povolit, aby mohli používat [brány firewall na úrovni databáze](sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) .
- Ujistěte se, že se používají odpovídající přihlášení a oprávnění na úrovni hlavní databáze (nebo použijte [obsažené uživatelé](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable))
- Podle potřeby nakonfigurujte auditování.
- Podle potřeby nakonfigurujte výstrahy.

> [!NOTE]
> Pokud používáte skupinu převzetí služeb při selhání a připojujete se k databázím pomocí lstener pro čtení i zápis, přesměrování po převzetí služeb při selhání proběhne automaticky a transparentně do aplikace.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Upgrade aplikace s minimálními výpadky

V některých případech je třeba aplikaci odebrat z důvodu plánované údržby, jako je například upgrade aplikace. [Správa upgradů aplikací](sql-database-manage-application-rolling-upgrade.md) : popisuje, jak používat aktivní geografickou replikaci k zajištění postupného upgradu vaší cloudové aplikace za účelem minimalizace výpadků během upgradů a k zadání cesty pro obnovení, pokud se něco nepovede.

## <a name="next-steps"></a>Další kroky

Diskuzi o faktorech návrhu aplikací pro samostatné databáze a elastické fondy najdete v tématu [Návrh aplikace pro zotavení po havárii cloudu](sql-database-designing-cloud-solutions-for-disaster-recovery.md) a [strategie zotavení po havárii elastického fondu](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
