---
title: Migrace databáze z instance systému SQL Server do Azure SQL Database – Managed instance | Dokumentace Microsoftu
description: Zjistěte, jak migrovat databázi z instance systému SQL Server do Azure SQL Database – spravovaná instance.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
manager: craigg
ms.date: 02/11/2019
ms.openlocfilehash: 9fe6ab797eaa325ad802702e95f5a0e5b8e4fef4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070414"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migrace instance SQL serveru do služby Azure SQL Database managed instance

V tomto článku se dozvíte o metodách migrace systému SQL Server 2005 nebo novější verze instance [Azure SQL Database managed instance](sql-database-managed-instance.md). Informace o migraci na elastický fond nebo izolovanou databázi, naleznete v tématu [migrovat jednu, nebo součástí fondu databáze](sql-database-cloud-migrate.md). Informace o migraci o migraci z jiných platforem, naleznete v tématu [Průvodce migrací databází Azure](https://datamigration.microsoft.com/).

Na vysoké úrovni procesu migrace databáze vypadá takto:

![Proces migrace](./media/sql-database-managed-instance-migration/migration-process.png)

- [Vyhodnocení kompatibility spravované instance](#assess-managed-instance-compatibility)
- [Možnost připojení k aplikaci](sql-database-managed-instance-connect-app.md)
- [Nasazení do optimální velikosti spravované instance](#deploy-to-an-optimally-sized-managed-instance)
- [Vyberte metodu migrace a migrace](#select-migration-method-and-migrate)
- [Monitorování aplikací](#monitor-applications)

> [!NOTE]
> Migrace jednotlivých databází do izolované databáze nebo elastického fondu, naleznete v tématu [migrace databáze SQL serveru do služby Azure SQL Database](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Vyhodnocení kompatibility spravované instance

Nejprve určete, zda managed instance je kompatibilní s požadavky na databázi aplikace. Možnost nasazení spravované instance je navržené pro poskytování snadno výtah a posunout migrace pro většinu z existující aplikace, které používají místní SQL Server nebo na virtuálních počítačích. Však můžete někdy potřebovat funkce nebo funkce, které se zatím nepodporují a náklady na implementaci řešení jsou příliš vysoká.

Použití [Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) kompatibility detekovat potenciální problémy s ovlivňuje funkčnost databází na Azure SQL Database. DMA nepodporuje, ale podpora spravovaná instance jako cíl migrace, ale doporučuje spustit posouzení na databázi SQL Azure a pečlivě zkontrolovat seznam paritu funkcí ohlášené a problémy s kompatibilitou proti dokumentaci k produktu. Zobrazit [funkce služby Azure SQL Database](sql-database-features.md) ke kontrole existují některé hlášené omezujícím problémům, které nejsou blockers ve spravované instanci, protože většina blokující problémy, které brání migrace do Azure SQL Database byly odebrány s spravované instance. Pro instanci, funkce, jako jsou dotazy napříč databázemi, mezidatabázové transakce v rámci stejné instance, propojený server do jiných zdrojů, CLR, globální dočasné tabulky SQL jsou k dispozici ve spravované instance úrovně zobrazení instance, služba Service Broker a podobně.

Pokud jsou některé hlášené omezujícím problémům, které se neodeberou s možností nasazení spravované instance, můžete potřebovat zvážit alternativní možnosti, jako například [systému SQL Server na virtuálních počítačích Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Následuje několik příkladů:

- Pokud budete vyžadovat přímý přístup k operačnímu systému nebo systému souborů, například instalace třetích stran nebo vlastních agentů na stejný virtuální počítač s SQL serverem.
- Pokud máte striktní závislost na funkcích, které se ještě nepodporují, jako je například FileStream a FileTable, PolyBase a transakce mezi instance.
- Pokud naprosto nutné udržela na konkrétní verzi systému SQL Server (2012, například).
- Pokud vaše požadavky na výpočetní výkon jsou mnohem nižší nabízí spravované instance (jedno virtuální jádro pro instanci) a konsolidace databáze není nepřijatelnou možností.

Pokud byly vyřešeny všechny identifikovat problémy blokující migraci a pokračuje migraci do spravované Instance, mějte na paměti, že některé změny mohou ovlivnit výkon vaší úlohy:
- Povinné úplný model obnovení a automatizované plánu pravidelného zálohování může ovlivnit výkon úloh nebo akce údržby/ETL pravidelně použít jednoduché nebo hromadně protokolovaného modelu nebo zastavit zálohování na vyžádání.
- Jiný server nebo databázi konfigurace úrovně například příznaků trasování nebo úrovně kompatibility
- Nové funkce, které používáte, jako je transparentní šifrování databáze (TDE) nebo automatické převzetí služeb při selhání skupiny může mít vliv na využití procesoru a vstupů/výstupů.

Spravované Instance garance 99,99 % dostupnost i v důležitých scénářů, tak režijní náklady způsobené tyto funkce nejde zakázat. Další informace najdete v tématu [základní příčiny, které by mohly způsobit jiné výkonové na SQL serveru a Managed Instance](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-performance-baseline"></a>Vytvoření standardních hodnot výkonu

Pokud potřebujete porovnat výkon vašich úloh na Managed Instance pomocí původní úlohy běžící na serveru SQL Server, musíte vytvořit základní úrovně výkonu, který se použije k porovnání. Zde jsou některé parametry, které by bylo nutné měřit na instanci systému SQL Server: 
- [Monitorování využití procesoru na instanci serveru SQL](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) a zaznamenávat průměr a využití procesoru ve špičce.
- [Monitorovat využití paměti na instanci serveru SQL](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) a určit množství paměti používané různých komponent, jako je například fondu vyrovnávací paměti plánování mezipaměti, fondu úložiště sloupce [OLTP v paměti](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)atd. Kromě toho byste měli najít průměrné a špičku hodnoty čítače výkonu paměti životnost stránky.
- Monitorování využití vstupně-výstupních operací disku na instanci systému SQL Server zdroje pomocí [sys.dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) zobrazení nebo [čítače výkonu](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Monitorovat zatížení a výkonu dotazování umožňujícímu nebo instanci SQL serveru tím, že kontroluje zobrazení dynamické správy nebo Store dotazu, pokud provádíte migraci z SQL serveru 2016 + verze. Identifikujte průměrné doby trvání a nejdůležitější dotazy v vaše úloha má být porovnán s dotazy, které jsou spuštěny na spravované instanci podle využití procesorů.

> [!Note]
> Pokud si všimnete, jakýkoli problém s vaší úlohy na serveru SQL Server, jako je vysoké využití procesoru, tlaku na paměť konstant, problémy s databází tempdb nebo parametrization, by měl akci k jejich vyřešení před přepnutím směrného plánu a migrace na zdrojové instanci SQL serveru. Migrace vědět problémy, které všechny nové migh systému vést k neočekávaným výsledkům a zneplatnit všechny porovnání výkonu.

Jako výsledku této aktivity můžete by měl mít dokumentované průměr a maximální hodnoty pro využití procesoru, paměti a vstupně-výstupních operací ve zdrojovém systému, stejně jako maximální a průměrná doba trvání a využití procesoru dominantní a nejdůležitější dotazy ve vašich úloh. By měla tyto hodnoty použijete později k porovnání výkonu vašich úloh na Managed Instance pomocí standardních hodnot výkonu úloh na zdrojovém serveru SQL Server.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Nasazení do optimálně dimenzované mi

Spravovaná instance je vytvořený na míru pro místní úlohy, které chcete přesunout do cloudu. Přináší [nové nákupní model](sql-database-service-tiers-vcore.md) , která poskytuje větší flexibilitu při výběru správnou úroveň prostředky pro vaše úlohy. Ve světě v místním jste zvyklí pravděpodobně velikosti tyto úlohy s použitím fyzických jader a šířku pásma vstupně-výstupních operací. Nákupní model pro spravovanou instanci je na základě virtuálních jader, nebo "virtuálních jader, za" s další úložiště a vstupně-výstupní operace k dispozici samostatně. Modelu virt. jader je jednodušší způsob, jak pochopit vaše požadavky na výpočetní výkon v cloudu můžete použít místní ještě dnes. Tento nový model vám umožní k nastavení správné velikosti vaše cílové prostředí v cloudu. Některé obecné pokyny, které vám mohou pomoci při výběru správné úrovně a vlastnosti jsou popsány zde:
- [Monitorování využití procesoru na instanci serveru SQL](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) a kontrola kolik výpočetního výkonu, které aktuálně používáte (pomocí zobrazení dynamické správy SQL Server Management Studio a další monitorovací nástroje). Můžete zřídit Managed Instance, která odpovídá počtu jader, které používáte na SQL serveru, s tím, že charakteristiky využití procesoru může být nutné škálovat tak, aby odpovídaly [charakteristik virtuálních počítačů, ve kterém je nainstalovaný Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#hardware-generation-characteristics).
- Zkontrolujte velikost dostupné paměti v instanci SQL serveru a zvolte [úroveň služby, který má odpovídající paměti](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#hardware-generation-characteristics). Bylo by užitečné k měření životnost stránky na instanci serveru SQL k určení [je potřeba další paměť](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Měření vstupně-výstupní latence subsystému souboru si vybrat mezi úrovně služeb pro obecné účely a pro důležité obchodní informace.

Můžete také výpočetní prostředky a prostředky úložiště v nasazení čas a pak ji později změnit bez vnášení výpadku pro vaši aplikaci s použitím [webu Azure portal](sql-database-scale-resources.md):

![Změna velikosti spravované instance](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Zjistěte, jak vytvořit virtuální síť infrastruktury a managed instance, najdete v článku [vytvoření managed instance](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> Je důležité udržovat vaše cílové virtuální síť a podsíť vždy v souladu s [managed instance požadavky na virtuální síť](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Jakékoli nekompatibility můžete zabránit vám v vytváření nových instancí nebo pomocí ty, které jste už vytvořili. Další informace o [vytváří se nová](sql-database-managed-instance-create-vnet-subnet.md) a [konfigurace stávající](sql-database-managed-instance-configure-vnet-subnet.md) sítě.

## <a name="select-migration-method-and-migrate"></a>Vyberte metodu migrace a migrace

Databáze spravované instance možnost cíle uživatelské scénáře nasazení které vyžadují velkokapacitní databáze migrace z místní nebo IaaS implementace. Když budete potřebovat k přenosu back-endu aplikací, které pravidelně použít úrovni instance a / nebo mezidatabázové funkce jsou optimální volbou. Pokud je to váš scénář, můžete přesunout celou instance na odpovídající prostředí v Azure, aniž byste museli upravovat architekturu, když vaše aplikace.

Přesunutí instance SQL, musíte pečlivě naplánovat:

- Migrace všech databází, které musí být společně umístěného (ty běžící na stejnou instanci)
- Migrace objektů na úrovni instance, které vaše aplikace závisí, včetně přihlášení, přihlašovací údaje, úlohy agenta serveru SQL a operátory a aktivační události na úrovni serveru.

Managed instance je spravovaná služba, která umožňuje přidělovat některé regulární aktivity DBA na platformu, jako jsou součástí. Proto některé dat na úrovni instance není potřeba migrovat, jako je například úlohy údržby pro pravidelné zálohování nebo Always On konfigurace jako [vysoké dostupnosti](sql-database-high-availability.md) je součástí.

Spravovaná instance podporuje následující možnosti migrace databáze (aktuálně to jsou metody pouze podporované migrace):

- Azure Database Migration Service – migrace s téměř nulovými výpadky
- Nativní `RESTORE DATABASE FROM URL` - využívá nativní zálohování SQL serveru a vyžaduje určitý výpadek.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Azure Database Migration Service (DMS)](../dms/dms-overview.md) je plně spravovaná služba, která umožňují bezproblémovou migraci z několika databázových zdrojů na platformu Azure Data s minimálními výpadky. Tato služba zjednodušuje úlohy potřebné k migraci existujícího třetích stran a databáze SQL serveru do Azure. Možnosti nasazení ve verzi public preview zahrnují databáze v databázích Azure SQL Database a SQL Server ve virtuálním počítači Azure. DMS je doporučená metoda migrace pro podnikové procesy.

Pokud používáte SQL Server Integration Services (SSIS) na serveru SQL Server v místním prostředí, DMS zatím nepodporuje migrace katalogu služby SSIS (SSISDB), která ukládá balíčků služby SSIS, ale můžete zřídit prostředí Azure-SSIS Integration Runtime (IR) v Azure Data Factory (ADF), který se bude Vytvoření nové databáze SSISDB do spravované instance a potom znovu nasaďte své balíčky do ní, naleznete v tématu [vytvořit prostředí Azure-SSIS IR ve službě ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Další informace o krocích, které tento scénář a konfiguraci pro systém DMS, najdete v tématu [migrovat místní databázi do spravované instance pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Nativní obnovení z adresy URL

OBNOVENÍ záloh nativní (soubory .bak) z místního SQL serveru nebo [systému SQL Server na virtuálních počítačích](https://azure.microsoft.com/services/virtual-machines/sql-server/), která je dostupná na [služby Azure Storage](https://azure.microsoft.com/services/storage/), je jedním z klíčových funkcí nasazení spravované instance možnost, která umožňuje rychlé a snadné databáze offline migraci.

Následující diagram představuje podrobný přehled procesu:

![Postup migrace](./media/sql-database-managed-instance-migration/migration-flow.png)

Následující tabulka obsahuje další informace týkající se metod, které můžete použít v závislosti na verzi zdrojového systému SQL Server, kterou používáte:

|Krok|Modul SQL a verze|Zálohování a obnově – metoda|
|---|---|---|
|Umístění zálohy do služby Azure Storage|Prior SQL 2012 SP1 CU2|Nahrajte soubor .bak přímo do služby Azure storage|
||2012 SP1 CU2 - 2016|Přímé zálohování pomocí zastaralé [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) syntaxe|
||2016 a vyšší|Přímé zálohování pomocí [pomocí pověření SAS](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Obnovení z Azure storage do spravované instance|[OBNOVENÍ z adresy URL pomocí pověření SAS](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - Při migraci databáze chráněn [transparentního šifrování dat](transparent-data-encryption-azure-sql.md) do spravované instance pomocí možnosti nativní obnovení, je třeba migrovat před databáze odpovídající certifikát z místní nebo IaaS SQL Server obnovení. Podrobné pokyny najdete v článku [cert TDE migraci do spravované instance](sql-database-managed-instance-migrate-tde-certificate.md)
> - Obnovení databází systému se nepodporuje. Pokud chcete migrovat objekty na úrovni instance (uložené v databázi master a databázi msdb databáze), doporučujeme je skriptování a spouštění skriptů T-SQL v cílové instanci.

Rychlý start ukazuje, jak obnovit zálohu databáze do spravované instance pomocí pověření SAS, naleznete v tématu [obnovit ze zálohy do spravované instance](sql-database-managed-instance-get-started-restore.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>Monitorování aplikací

Po dokončení migrace do Managed Instance, je vhodné sledovat chování aplikace a výkon vašich úloh. Tento proces zahrnuje následující činnosti:
- [Porovnat úlohu spuštěnou na Managed Instance](#compare-performance-with-the-baseline) s [základní úrovně výkonu, který jste vytvořili na zdrojovém serveru SQL Server](#create-performance-baseline).
- Průběžně [sledování výkonu vašich úloh](#monitor-performance) k identifikaci potenciálních problémů a vylepšení.

### <a name="compare-performance-with-the-baseline"></a>Porovnat se standardními hodnotami

První aktivita, která je třeba provést okamžitě po úspěšné migraci je porovnat výkon úloh s výkonem úloh směrného plánu. Cílem této aktivity je potvrďte, že výkon úloh na spravované instanci bude vyhovovat vašim potřebám. 

Migrace databází do spravované Instance uchovává nastavení databáze a její původní úroveň kompatibility ve většině případů. Původní nastavení se zachovají, kde je to možné, aby bylo možné snížit riziko záznamu některé výkon ve srovnání s vašeho zdrojového systému SQL Server. Pokud se úroveň kompatibility databáze 100 a vyšší před migrací, zůstává stejná po migraci. Pokud byla úroveň kompatibility databáze 90 před migrací, upgradovaná databáze úroveň kompatibility nastavená na 100, což je úroveň nejnižší podporované kompatibility ve spravované instanci. Úroveň kompatibility databáze systému je 140. Vzhledem k tomu, že migrace do Managed Instance je ve skutečnosti Migrace na nejnovější verzi SQL Server Database Engine, byste měli vědět, že je potřeba znovu testování výkonu vašich úloh, aby se zabránilo překvapivé problémy s výkonem.

Předpokladem je Ujistěte se, že jste dokončili následující činnosti:
- Zarovnejte zjišťováním různé instance, databáze, temdb nastavení a konfigurace nastavení na spravované instanci s nastavením ze zdrojové instanci SQL serveru. Ujistěte se, že jste nezměnili nastavení, jako je úrovně kompatibility nebo šifrování, před spuštěním prvního porovnání výkonu nebo přijmout riziko, že některé nové funkce, které jste povolili mohou ovlivnit některé dotazy. Ke snížení rizik migrace, změňte úroveň kompatibility databáze až po monitorování výkonu.
- Implementace [pravidla osvědčených postupů úložiště pro obecné účely](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) jako předem přidělování velikost soubory, které chcete dosáhnout lepšího výkonu.
- Další informace o [klíče rozdíly prostředí, které může způsobit, že rozdíly ve výkonu mezi Managed Instance a SQL serverem]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) a určit rizika, které mohou ovlivnit výkon.
- Ujistěte se, abyste povoleno Query Store a automatické ladění na Managed Instance. Tyto funkce umožňují měřit výkon úloh a automaticky vyřešit potenciální problémy s výkonem. Další informace o použití Query Store jako ideální nástroj pro získání informací o výkon úloh před a po změně úrovně kompatibility databáze, jak je vysvětleno v [během upgradu zachovat výkon, stabilitu na novější verzi systému SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Jakmile připravíte prostředí, který je srovnatelné co největší míře s vaším místním prostředím můžete začít spouštět vaše úlohy a měření výkonu. Proces měření by měl obsahovat stejné parametry, které je měřená [při vytváření standardních hodnot výkonu měr úloh na zdrojovém serveru SQL Server](#create-performance-baseline).
V důsledku toho by měl porovnání výkonu parametry se standardními hodnotami a identifikovat důležité rozdíly.

> [!NOTE]
> V mnoha případech nebude moct získat přesně odpovídající výkon pro Managed Instance a SQL Server. Spravovaná Instance je databázový stroj SQL serveru, ale infrastrukturu a konfiguraci vysoké dostupnosti na spravované instanci může představovat některé rozdíl. Očekáváte, že některé dotazy budou rychlejší při některých dalších může být pomalejší. Cílem porovnání je ověřte, že výkon úloh ve spravované instanci shoduje výkonu na serveru SQL Server (v průměru) a identifikovat existují nejdůležitější dotazy s výkonem, které neodpovídají původní výkonu.

Výsledek porovnání výkonu může být:
- Výkon úloh na spravované instanci zarovnán nebo, lepší výkon úloh v systému SQL Server. V tomto případě úspěšně ověříte, jestli se migrace úspěšně dokončila.
- Většina parametrů výkonu a dotazy v pořádku, s několika výjimkami s důvodem sníženého výkonu úlohy práce. V takovém případě je třeba k identifikaci rozdíly a jejich význam. Pokud jsou některé důležité dotazy s snížený výkon, měli byste prošetřit základní plány SQL se mění nebo dotazy výskytu některé limity prostředků. Omezení rizik v tomto případě může být použít několik pomocných parametrů na nejdůležitější dotazy (například změněná úroveň kompatibility, starší verze kardinality) buď přímo nebo pomocí Průvodce plánem, znovu sestavit nebo vytvořit statistiky a indexy, které by mohly ovlivnit plány. 
- Většina dotazů, které jsou pomalejší na Managed Instance, ve srovnání s vašeho zdrojového systému SQL Server. V tomto případě pokuste se identifikovat hlavní příčiny rozdílu například [dosažení limitu některé prostředků]( sql-database-managed-instance-resource-limits.md#instance-level-resource-limits) , jako je omezení vstupně-výstupních operací, omezení paměti, limit frekvence protokolu instance atd. Pokud neexistují žádná omezení prostředků, které mohou způsobit rozdíl, zkuste změnit úroveň kompatibility databáze nebo změnit nastavení databáze, jako je odhad kardinality starší verze a znovu spusťte test. Zkontrolujte doporučení poskytovaných službou Managed Instance nebo Query Store zobrazení identifikovat dotazy, který poklesl výkonu.

> [!IMPORTANT]
> Managed Instance má integrované funkce automatické korekce plánu, který je ve výchozím nastavení povolené. Tato funkce zajišťuje, že by dotazy, které bez problémů fungovaly v vkládání snížit v budoucnu. Ujistěte se, že tato funkce je povolená a, že jste spustili dostatečně dlouho úlohy s původní nastavení, než změníte nová nastavení, chcete-li povolit Managed Instance pro další informace o standardních hodnot výkonu a plány.

Proveďte požadovanou změnu parametrů, nebo upgradujte úrovně služby a umožňuje konvergovat optimální konfiguraci, dokud se nedostanete na výkon úloh, která nejlépe vyhovuje vašim potřebám.

### <a name="monitor-performance"></a>Monitorování výkonu

Jakmile jsou na plně spravované platformě a ověříte, že přínos úlohy jsou odpovídající je výkon úloh SQL serveru, využijte výhody, které jsou poskytovány automaticky jako součást služby SQL Database. 

I v případě, že během migrace není ve spravované instanci provedení změn, existují vysoká pravděpodobnost, které by zapnete některé z nových funkcí, zatímco pracujete vaší instance využívat nejnovější vylepšení modulu databáze. Některé změny jsou povoleny pouze jednou [došlo ke změně úrovně kompatibility databáze](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).


Například nemusíte vytvářet zálohy na spravované instanci – služba provádí zálohování za vás automaticky. Je již nutné starat o plánování, provádění a Správa zálohování. Spravovaná instance máte možnost obnovit do libovolného bodu v čase během období uchovávání dat pomocí [bodu v čase obnovení (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore). Kromě toho není nutné starat o nastavení vysoké dostupnosti jako [vysoké dostupnosti](sql-database-high-availability.md) je součástí.

K posílení zabezpečení, zvažte použití [ověřování Azure Active Directory](sql-database-security-overview.md), [auditování](sql-database-managed-instance-auditing.md), [detekce hrozeb](sql-database-advanced-data-security.md), [zabezpečenínaúrovnířádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security), a [dynamické maskování dat](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ).

Kromě pokročilé funkce správy a funkce zabezpečení Managed Instance poskytuje sadu pokročilé nástroje, které vám mohou pomoci při [sledování a ladění vašich úloh](sql-database-monitor-tune-overview.md). [Azure SQL analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) vám umožní monitorovat velkou sadu spravovaných instancí a centralizovat monitorování velkého počtu instancí a databáze. [Automatické ladění](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) ve spravované instanci neustále monitorovat výkon vašich statistik spuštění plánu SQL a automaticky opravovat problémy zjištěné výkonu.

## <a name="next-steps"></a>Další postup

- Informace o spravované instance najdete v tématu [co je managed instance?](sql-database-managed-instance.md).
- Kurz zahrnuje obnovení ze zálohy, najdete v tématu [vytvoření managed instance](sql-database-managed-instance-get-started.md).
- Kurz zobrazující migrace pomocí DMS, naleznete v tématu [migrovat místní databázi do spravované instance pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
