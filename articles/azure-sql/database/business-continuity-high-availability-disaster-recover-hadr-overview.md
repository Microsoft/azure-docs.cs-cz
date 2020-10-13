---
title: Provozní kontinuita cloudu – obnovení databáze
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Přečtěte si, jak Azure SQL Database a SQL Managed instance podporují cloudovou provozní kontinuitu a obnovení databáze a pomůžou udržet důležité cloudové aplikace spuštěné.
keywords: provozní kontinuita, provozní kontinuita v cloudu, zotavení databáze po havárii, obnovení databáze
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 06/25/2019
ms.openlocfilehash: a69332f1534e32a85ce084289dd00533612cc282
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327557"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Přehled provozní kontinuity se službou Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

**Kontinuita podnikových** procesů v Azure SQL Database a SQL Managed instance odkazuje na mechanismy, zásady a postupy, které umožní vašemu podniku pokračovat v práci na konci přerušení, zejména pro výpočetní infrastrukturu. Ve většině případů aplikace SQL Database a SQL Managed instance budou zpracovávat rušivé události, které se můžou vyskytnout v cloudovém prostředí, a zachovat spuštěné aplikace a obchodní procesy. Existují však některé rušivé události, které nemohou být zpracovány SQL Database automaticky jako:

- Uživatel omylem odstranil nebo aktualizoval řádek v tabulce.
- Škodlivý útočník úspěšně odstranil data nebo vyřazení databáze.
- Zemětřesení způsobil výpadek napájení a dočasné zakázané datové centrum.

Tento přehled popisuje možnosti, které SQL Database a které spravované instance SQL poskytují provozní kontinuitu a zotavení po havárii. Seznamte se s možnostmi, doporučeními a kurzy pro obnovování z rušivých událostí, které mohou způsobit ztrátu dat nebo způsobit nedostupnost databáze a aplikace. Přečtěte si, co dělat, když dojde k chybě uživatele nebo aplikace při vlivu na integritu dat, když dojde k výpadku oblasti Azure nebo když vaše aplikace vyžaduje údržbu.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funkce služby SQL Database, pomocí kterých můžete zajistit provozní kontinuitu

Z perspektivy databáze existují čtyři scénáře s případnými výpadky:

- Selhání místních hardwarových nebo softwarových uzlů ovlivňujících uzel databáze, jako je například selhání diskového jednotky.
- Poškození dat nebo odstranění je obvykle způsobeno chybou aplikace nebo lidskou chybou. Taková selhání jsou specifická pro jednotlivé aplikace a obvykle se nedají detekovat databázovou službou.
- Výpadek datového centra, možná způsobené přírodní katastrofou. Tento scénář vyžaduje určitou úroveň geografické redundance s převzetím služeb při selhání do alternativního datového centra.
- Chyby upgradu nebo údržby, neočekávané problémy, ke kterým dojde během plánované údržby nebo údržby infrastruktury, mohou vyžadovat rychlé vrácení zpět do předchozího stavu databáze.

Pro zmírnění selhání místního hardwaru a softwaru SQL Database zahrnuje [architekturu s vysokou dostupností](high-availability-sla.md), která garantuje automatické obnovení z těchto selhání s 99,995% dostupností smlouvy SLA.  

Za účelem ochrany vaší firmy před ztrátou dat SQL Database a SQL Managed instance automaticky vytvoří úplné zálohy databáze každý týden, odrozdílové zálohy databáze každých 12 hodin a zálohování protokolů transakcí každé 5-10 minut. Zálohy se ukládají do úložiště RA-GRS po dobu alespoň 7 dnů pro všechny úrovně služeb. Všechny úrovně služeb s výjimkou podpora Basic konfigurovatelný doba uchovávání záloh pro obnovení k určitému bodu v čase, až 35 dní.

SQL Database a SQL Managed instance také poskytují několik funkcí provozní kontinuity, které můžete použít ke zmírnění různých neplánovaných scénářů.

- [Dočasné tabulky](../temporal-tables.md) umožňují obnovit verze řádků z jakéhokoli bodu v čase.
- [Integrované automatické zálohování](automated-backups-overview.md) a obnovení k určitému [bodu v čase](recovery-using-backups.md#point-in-time-restore) umožňují obnovení kompletní databáze k určitému časovému okamžiku v rámci nakonfigurované doby uchování až do 35 dnů.
- [Odstraněnou databázi můžete obnovit](recovery-using-backups.md#deleted-database-restore) do bodu, ve kterém byla odstraněna, pokud nebyl **Server odstraněn**.
- [Dlouhodobé uchovávání záloh](long-term-retention-overview.md) umožňuje uchovávat zálohy až po dobu 10 let. Toto je omezená verze Public Preview pro spravovanou instanci SQL.
- [Aktivní geografická replikace](active-geo-replication-overview.md) umožňuje vytvářet čitelné repliky a ručně převzetí služeb při selhání pro všechny repliky v případě výpadku datového centra nebo upgradu aplikace.
- [Skupina automatického převzetí služeb při selhání](auto-failover-group-overview.md#terminology-and-capabilities) umožňuje aplikaci automaticky obnovit v případě výpadku datového centra.

## <a name="recover-a-database-within-the-same-azure-region"></a>Obnovení databáze ve stejné oblasti Azure

Automatické zálohování databáze můžete použít k obnovení databáze k určitému bodu v čase v minulosti. Tímto způsobem se můžete zotavit z poškození dat způsobených lidmi chybami. Obnovení k určitému bodu v čase umožňuje vytvořit novou databázi na stejném serveru, který představuje stav dat před poškozenou událostí. U většiny databází trvá operace obnovení méně než 12 hodin. Obnovení velmi rozsáhlé nebo vysoce aktivní databáze může trvat déle. Další informace o čase obnovení najdete v tématu [čas obnovení databáze](recovery-using-backups.md#recovery-time).

Pokud není maximální podporovaná doba uchovávání záloh pro obnovení k určitému bodu v čase (PITR) dostatečná pro vaši aplikaci, můžete ji prodloužit konfigurací zásad pro dlouhodobé uchovávání (LTR) pro databáze. Další informace najdete v tématu [dlouhodobé uchovávání záloh](long-term-retention-overview.md).

## <a name="compare-geo-replication-with-failover-groups"></a>Porovnání geografické replikace se skupinami převzetí služeb při selhání

[Skupiny s automatickým převzetím služeb při selhání](auto-failover-group-overview.md#terminology-and-capabilities) zjednodušují nasazení a používání [geografické replikace](active-geo-replication-overview.md) a přidávají další možnosti, jak je popsáno v následující tabulce:

|                                              | Geografická replikace | Skupiny převzetí služeb při selhání  |
|:---------------------------------------------| :-------------- | :----------------|
| **Automatické převzetí služeb při selhání**                          |     No          |      Yes         |
| **Převzetí služeb při selhání více databází současně**  |     No          |      Yes         |
| **Uživatel musí po převzetí služeb při selhání aktualizovat připojovací řetězec**      |     Yes         |      No          |
| **Podpora SQL Managed Instance**                   |     No          |      Yes         |
| **Může být ve stejné oblasti jako primární**             |     Yes         |      No          |
| **Více replik**                            |     Yes         |      No          |
| **Podporuje škálování čtení**                          |     Yes         |      Yes         |


## <a name="recover-a-database-to-the-existing-server"></a>Obnovení databáze na existujícím serveru

I když může výpadek Azure datacentrum nejenom výjimečné. Při výpadku dojde k narušení provozu, které může trvat jen několik minut nebo až několik hodin.

- Jednou z možností je počkat, až se databáze vrátí zpátky do režimu online, když dojde k výpadku datového centra. Tento postup funguje pro aplikace, které si mohou dovolit mít databázi v režimu offline. Například vývojový projekt nebo bezplatná zkušební verze, na které nemusíte neustále pracovat. Když dojde k výpadku datového centra, nevíte, jak dlouho může výpadek trvat, takže tato možnost funguje jenom v případě, že nepotřebujete databázi po dobu.
- Další možností je obnovit databázi na jakémkoli serveru v libovolné oblasti Azure pomocí [geograficky redundantních záloh databáze](recovery-using-backups.md#geo-restore) (geografické obnovení). Geografické obnovení používá jako zdroj geograficky redundantní zálohu a dá se použít k obnovení databáze i v případě, že je databáze nebo datacentrum nedostupné kvůli výpadku.
- Nakonec můžete rychle obnovit z výpadku, pokud jste nakonfigurovali geograficky sekundární pomocí [aktivní geografické replikace](active-geo-replication-overview.md) nebo [skupiny automatického převzetí služeb při selhání](auto-failover-group-overview.md) pro vaši databázi nebo databáze. V závislosti na vaší volbě těchto technologií můžete použít buď ruční nebo automatické převzetí služeb při selhání. I když převzetí služeb při selhání zabere jenom pár sekund, bude tato služba při aktivaci trvat aspoň 1 hodinu. To je nezbytné k tomu, aby bylo zajištěno, že převzetí služeb při selhání je v rámci škálování výpadku oprávněné. Převzetí služeb při selhání může také vést k malé ztrátě dat z důvodu povahy asynchronní replikace.

Při vývoji plánu provozní kontinuity musíte pochopit maximální přijatelnou dobu úplného zotavení aplikace po ničivé události. Čas potřebný k úplnému obnovení aplikace je známý jako cíl doby obnovení (RTO). Také je potřeba porozumět maximálnímu intervalu nedávných aktualizací dat (časový interval), které může aplikace tolerovat při obnovování z neplánované události rušivého vlivu na ztrátu. Potenciální ztráta dat se označuje jako cíl bodu obnovení (RPO).

Různé metody obnovení nabízejí různé úrovně bodu RPO a RTO. Můžete zvolit konkrétní metodu obnovení nebo použít kombinaci metod k dosažení úplného obnovení aplikace. Následující tabulka porovnává RPO a RTO jednotlivých možností obnovení. Skupiny automatického převzetí služeb při selhání zjednodušují nasazení a využití geografické replikace a přidávají další možnosti, jak je popsáno v následující tabulce.

| Metoda obnovení | RTO | RPO |
| --- | --- | --- |
| Geografické obnovení ze geograficky replikovaných záloh | 12 h | 1 h |
| Skupiny automatického převzetí služeb při selhání | 1 h | 5 s |
| Ruční převzetí služeb při selhání databáze | 30 s | 5 s |

> [!NOTE]
> *Ruční převzetí služeb při selhání databáze* odkazuje na převzetí služeb při selhání izolované databáze na geograficky replikovanou sekundární práci pomocí [neplánovaného režimu](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities).
Podrobnosti o automatických převzetí služeb při selhání RTO a RPO najdete v tabulce výše v tomto článku.

Pokud vaše aplikace splňuje některá z těchto kritérií, použijte skupiny automatického převzetí služeb při selhání:

- Je zvlášť důležitá.
- Má smlouvu o úrovni služeb (SLA), která neumožňuje 12 hodin nebo více výpadků.
- Výpadek může vést k finanční zodpovědnosti.
- Má vysokou míru změny dat a 1 hodina ztráty dat není přijatelné.
- Další náklady na aktivní geografickou replikaci jsou nižší než potenciální finanční závazky a související ztráta podnikání.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

V závislosti na požadavcích vaší aplikace se můžete rozhodnout použít kombinaci záloh databáze a aktivní geografické replikace. Diskuzi o požadavcích na návrh pro samostatné databáze a elastické fondy, které využívají tyto funkce pro provozní kontinuitu, najdete v tématu [Návrh aplikace pro zotavení po havárii cloudu](designing-cloud-solutions-for-disaster-recovery.md) a [strategie zotavení po havárii elastického fondu](disaster-recovery-strategies-for-applications-with-elastic-pool.md).

Následující části obsahují přehled kroků pro obnovení pomocí záloh databáze nebo aktivní geografické replikace. Podrobný postup, včetně požadavků na plánování, kroků po obnovení a informací o simulaci výpadku při provádění postupu zotavení po havárii, najdete v tématu [obnovení databáze v SQL Database z výpadku](disaster-recovery-guidance.md).

### <a name="prepare-for-an-outage"></a>Příprava na výpadek

Bez ohledu na funkce provozní kontinuity, které používáte, musíte:

- Identifikujte a připravte cílový server, včetně pravidel brány firewall na úrovni serveru, přihlašovacích údajů a oprávnění na úrovni hlavní databáze.
- Určit, jak přesměrovat klienty a klientské aplikace na nový server
- Zdokumentovat další závislosti, například nastavení auditování a výstrahy

Pokud nebudete připravovat správně, po převzetí služeb při selhání nebo obnovení databáze bude trvat déle a pravděpodobně budete potřebovat řešení potíží v době zátěže – Chybná kombinace.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Převzetí služeb při selhání geograficky replikovanou sekundární databází

Pokud jako mechanismus obnovení používáte aktivní geografickou replikaci nebo skupiny s automatickým převzetím služeb při selhání, můžete nakonfigurovat zásady automatického převzetí služeb při selhání nebo použít [Ruční neplánované převzetí služeb při](active-geo-replication-configure-portal.md#initiate-a-failover)selhání. Po zahájení převzetí služeb při selhání způsobí, že se sekundární stane novou primární a připravený k zaznamenávání nových transakcí a reakci na dotazy – s minimální ztrátou dat pro data, která se ještě nereplikují. Informace o návrhu procesu převzetí služeb při selhání najdete v tématu [Návrh aplikace pro zotavení po havárii cloudu](designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Když se datacentrum vrátí do režimu online, původní základní primární se automaticky znovu připojí k nové primární databázi a stanou se sekundárními databázemi. Pokud potřebujete přemístit primární zpátky do původní oblasti, můžete plánované převzetí služeb při selhání iniciovat ručně (navrácení služeb po obnovení).

### <a name="perform-a-geo-restore"></a>Provedení geografického obnovení

Pokud používáte automatizované zálohování s geograficky redundantním úložištěm (povoleno ve výchozím nastavení), můžete obnovit databázi pomocí [geografického obnovení](disaster-recovery-guidance.md#recover-using-geo-restore). K obnovení obvykle dochází během 12 hodin – s ztrátou dat po dobu až jedné hodiny, kterou určuje čas, kdy byla provedena a replikována poslední záloha protokolu. Dokud se obnovení nedokončí, databáze není schopná zaznamenávat žádné transakce ani reagovat na dotazy. Poznámka: geografická obnova obnovuje databázi pouze do posledního dostupného bodu v čase.

> [!NOTE]
> Pokud se datacentrum vrátí do režimu online před přepnutím aplikace do obnovené databáze, můžete obnovení zrušit.

### <a name="perform-post-failover--recovery-tasks"></a>Provedení úloh po převzetí služeb při selhání nebo obnovení

Po obnovení s použitím libovolného mechanismu musíte provést následující dodatečné úlohy, abyste pro uživatele zprovoznili své aplikace:

- Přesměrujte klienty a klientské aplikace na nový server a obnovenou databázi.
- Zajistěte, aby se pro uživatele připojovala příslušná pravidla brány firewall na úrovni serveru, aby je bylo možné povolit, aby mohli používat [brány firewall na úrovni databáze](firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) .
- Zajistěte, aby byla k dismístě příslušná přihlášení a oprávnění na úrovni hlavní databáze (nebo použijte [omezení uživatelů](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)).
- Podle potřeby nakonfigurujte auditování.
- Podle potřeby nakonfigurujte výstrahy.

> [!NOTE]
> Pokud používáte skupinu převzetí služeb při selhání a připojujete se k databázím pomocí naslouchacího procesu pro čtení a zápis, přesměrování po převzetí služeb při selhání proběhne automaticky a transparentně do aplikace.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Upgrade aplikace s minimálními výpadky

V některých případech je třeba aplikaci odebrat z důvodu plánované údržby, jako je například upgrade aplikace. [Správa upgradů aplikací](manage-application-rolling-upgrade.md) : popisuje, jak používat aktivní geografickou replikaci k zajištění postupného upgradu vaší cloudové aplikace za účelem minimalizace výpadků během upgradů a k zadání cesty pro obnovení, pokud se něco nepovede.

## <a name="next-steps"></a>Další kroky

Diskuzi o požadavcích na návrh aplikace pro izolované databáze a elastické fondy najdete v tématu [Návrh aplikace pro zotavení po havárii cloudu](designing-cloud-solutions-for-disaster-recovery.md) a [strategie zotavení po havárii elastického fondu](disaster-recovery-strategies-for-applications-with-elastic-pool.md).
