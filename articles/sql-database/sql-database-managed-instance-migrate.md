---
title: Migrace ze serveru SQL Server do spravované instance
description: Zjistěte, jak migrovat databázi z instance SERVERU SQL do instance Azure SQL Database – správa.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
ms.date: 07/11/2019
ms.openlocfilehash: 6bae9e871be2a5d56d057d2a077de53329b8c3ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208934"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migrace instance SQL Serveru do spravované instance Azure SQL Database

V tomto článku se dozvíte o metodách migrace instance SQL Server 2005 nebo novější verze do [instance spravované službou Azure SQL Database](sql-database-managed-instance.md). Informace o migraci do jedné databáze nebo elastického fondu naleznete v [tématu Migrace do jedné nebo sdružené databáze](sql-database-cloud-migrate.md). Informace o migraci z jiných platforem najdete v [tématu Průvodce migrací do databáze Azure](https://datamigration.microsoft.com/).

> [!NOTE]
> Pokud chcete rychle spustit a vyzkoušet spravovanou instanci, můžete místo této stránky přejít na [příručku pro rychlý start.](sql-database-managed-instance-quickstart-guide.md) 

Na vysoké úrovni vypadá proces migrace databáze takto:

![proces migrace](./media/sql-database-managed-instance-migration/migration-process.png)

- [Vyhodnoťte kompatibilitu spravovaných instancí,](#assess-managed-instance-compatibility) kde byste měli zajistit, že neexistují žádné problémy s blokováním, které by mohly zabránit migraci.
  - Tento krok také zahrnuje vytvoření [směrného plánu výkonu](#create-performance-baseline) k určení využití prostředků na zdrojové instanci serveru SQL Server. Tento krok je nutný, pokud chcete o nasadit správně velikost spravované instance a ověřit, že výkony po migraci nejsou ovlivněny.
- [Volba možností připojení aplikace](sql-database-managed-instance-connect-app.md)
- [Nasaďte se do spravované instance optimální velikosti,](#deploy-to-an-optimally-sized-managed-instance) kde si vyberete technické charakteristiky (počet virtuálních jader, velikost paměti) a úroveň výkonu (Důležité obchodní, obecné účely) spravované instance.
- [Vyberte metodu migrace a migrujte,](#select-migration-method-and-migrate) kam migrujete databáze pomocí offline migrace (nativní zálohování/obnovení, import/export databáze) nebo migrace online (Služba migrace dat, transakční replikace).
- [Sledujte aplikace,](#monitor-applications) abyste zajistili očekávaný výkon.

> [!NOTE]
> Pokud chcete migrovat jednotlivou databázi do jedné databáze nebo do elastického fondu, přečtěte si informace [o migraci databáze serveru SQL Server do databáze Azure SQL Database](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Posouzení kompatibility spravovaných instancí

Nejprve určete, zda je spravovaná instance kompatibilní s požadavky na databázi vaší aplikace. Možnost nasazení spravované instance je navržena tak, aby poskytovala snadnou migraci výtahu a přesunu pro většinu existujících aplikací, které používají SQL Server místně nebo na virtuálních počítačích. Někdy však může vyžadovat funkce nebo funkce, které ještě nejsou podporovány a náklady na implementaci řešení jsou příliš vysoké.

Pomocí [Pomocníka pro migraci dat (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) můžete zjistit potenciální problémy s kompatibilitou, které mají vliv na funkce databáze Azure SQL Database. DMA ještě nepodporuje spravovanou instanci jako cíl migrace, ale doporučuje se spustit hodnocení pomocí Azure SQL Database a pečlivě zkontrolovat seznam ohlášených problémů s paritou funkcí a kompatibilitou s dokumentací produktu. Viz [Azure SQL Database funkce](sql-database-features.md) ke kontrole jsou tam některé hlášené blokování problémy, které nejsou blokátory ve spravované instanci, protože většina problémů blokování brání migraci do Azure SQL Database byly odebrány s spravovanou instanci. Například funkce, jako jsou dotazy mezi databázemi, transakce mezi databázemi v rámci stejné instance, propojený server s jinými zdroji SQL, CLR, globální temp tabulky, zobrazení na úrovni instancí, Service Broker a podobně jsou k dispozici ve spravovaných instancích.

Pokud existují některé hlášené problémy s blokováním, které nejsou odebrány pomocí možnosti nasazení spravované instance, možná budete muset zvážit alternativní možnost, jako je [například SQL Server na virtuálních počítačích Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Zde je několik příkladů:

- Pokud požadujete přímý přístup k operačnímu systému nebo systému souborů, například k instalaci třetích stran nebo vlastních agentů na stejném virtuálním počítači se serverem SQL Server.
- Pokud máte striktní závislost na funkcích, které stále nejsou podporovány, jako je FileStream / FileTable, PolyBase a transakce mezi instancemi.
- Pokud je naprosto potřeba zůstat na konkrétní verzi SQL Server (2012, například).
- Pokud vaše požadavky na výpočetní prostředky jsou mnohem nižší, že spravované instance nabízí (jeden virtuální jádro, například) a konsolidace databáze není přijatelná možnost.

Pokud jste vyřešili všechny identifikované blokátory migrace a pokračujete v migraci do spravované instance, všimněte si, že některé změny mohou ovlivnit výkon vašeho pracovního vytížení:
- Povinný úplný model obnovení a pravidelný plán automatického zálohování mohou mít vliv na výkon vašeho pracovního vytížení nebo akcí údržby/ETL, pokud jste pravidelně používali jednoduchý nebo hromadně protokolovaný model nebo zastavili zálohování na vyžádání.
- Různé konfigurace na úrovni serveru nebo databáze, například příznaky trasování nebo úrovně kompatibility
- Nové funkce, které používáte, jako je například transparentní šifrování databáze (TDE) nebo skupiny s podporou převzetí služeb při selhání, mohou mít vliv na využití procesoru a vi.

Spravovaná instance zaručuje 99,99% dostupnost i v kritických scénářích, takže režie způsobená těmito funkcemi nelze zakázat. Další informace naleznete [v hlavních příčinách, které mohou způsobit jiný výkon na serveru SQL Server a spravované instanci](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-performance-baseline"></a>Vytvoření směrného plánu výkonu

Pokud potřebujete porovnat výkon úlohy ve spravované instanci s původní úlohou spuštěnou na SERVERU SQL Server, budete muset vytvořit směrný plán výkonu, který se použije pro porovnání. 

Směrný plán výkonu je sada parametrů, jako je průměrné/maximální využití procesoru, průměrná/maximální latence vstupně-výstupních operací disku, propustnost, IOPS, průměrná/maximální očekávaná životnost stránky, průměrná maximální velikost databáze. Chcete mít podobné nebo ještě lepší parametry po migraci, proto je důležité měřit a zaznamenávat hodnoty směrného plánu pro tyto parametry. Kromě systémových parametrů byste museli vybrat sadu reprezentativních dotazů nebo nejdůležitější dotazy v pracovním vytížení a měřit dobu trvání min/průměr/max, využití procesoru pro vybrané dotazy. Tyto hodnoty by vám umožní porovnat výkon úlohy spuštěné na spravované instanci s původními hodnotami na zdrojovém serveru SQL Server.

Některé parametry, které budete muset měřit na instanci serveru SQL Server jsou: 
- [Sledujte využití procesoru na instanci serveru SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) a zaznamenejte průměrné a špičkové využití procesoru.
- [Sledujte využití paměti v instanci serveru SQL Server](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) a určete velikost paměti používanou různými součástmi, jako je fond vyrovnávacích pamětí, mezipaměť plánu, fond úložiště sloupců, [OLTP v paměti](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)atd. Kromě toho byste měli najít průměrné a špičkové hodnoty čítače výkonu paměti očekávaná životnost stránky.
- Sledování využití vstupně-mailů disku ve zdrojové instanci serveru SQL Server pomocí čítačů zobrazení [sys.dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) nebo [čítačů výkonu](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Monitorování výkonu pracovního vytížení a dotazů nebo instance serveru SQL Server kontrolou zobrazení dynamické správy nebo úložiště dotazů, pokud migrujete z verze SQL Serveru 2016+. Identifikujte průměrnou dobu trvání a využití procesoru nejdůležitějších dotazů ve vaší pracovní zátěži a porovnejte je s dotazy, které jsou spuštěny ve spravované instanci.

> [!Note]
> Pokud zjistíte jakýkoli problém s vaší úlohy na SQL Server, jako je například vysoké využití procesoru, konstantní tlak paměti, tempdb nebo parametrizace problémy, měli byste se pokusit vyřešit na zdrojové instanci SQL Server před přijetím směrného plánu a migrace. Migrace znát problémy s novým systémem migh způsobit neočekávané výsledky a zneplatnit jakékoli porovnání výkonu.

Jako výsledek této aktivity byste měli mít zdokumentované průměrné a špičkové hodnoty pro využití procesoru, paměti a vi ve zdrojovém systému, stejně jako průměrnou a maximální dobu trvání a využití procesoru dominantních a nejkritičtějších dotazů ve vaší pracovní zátěži. Tyto hodnoty byste měli použít později k porovnání výkonu úlohy ve spravované instanci se směrným výkonem úlohy na zdrojovém serveru SQL Server.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Nasazení do spravované instance optimální velikosti

Spravovaná instance je přizpůsobená pro místní úlohy, které plánují přechod do cloudu. Zavádí nový [nákupní model,](sql-database-service-tiers-vcore.md) který poskytuje větší flexibilitu při výběru správné úrovně prostředků pro vaše úlohy. V místním světě jste pravděpodobně zvyklí na velikost i tyto úlohy pomocí fyzických jader a šířky pásma vi. Nákupní model pro spravované instance je založen na virtuálních jádrech neboli "virtuálních jádrech" s dalším úložištěm a vstupně-ti, které jsou k dispozici samostatně. Model virtuálních jader je jednodušší způsob, jak porozumět vašim výpočetním požadavkům v cloudu oproti tomu, co dnes používáte místně. Tento nový model umožňuje správné velikosti cílového prostředí v cloudu. Zde jsou popsány některé obecné pokyny, které vám mohou pomoci vybrat správnou úroveň a charakteristiky služby:
- Na základě výchozího využití procesoru můžete zřídit spravovanou instanci, která odpovídá počtu jader, které používáte na serveru SQL Server, s ohledem na to, že vlastnosti procesoru může být nutné škálovat tak, aby [odpovídaly charakteristikám virtuálních zařízení, kde je nainstalovaná spravovaná instance](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
- Na základě výchozího využití paměti zvolte [úroveň služby, která má odpovídající paměť](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics). Velikost paměti nelze přímo zvolit, takže budete muset vybrat spravovanou instanci s množstvím virtuálních jader, která má odpovídající paměť (například 5.1 GB/vCore v Gen5). 
- Na základě základní latence vstupně-výstupních služeb podsystému souboru si můžete vybrat mezi obecným pro obecné použití (latence větší než 5 ms) a úrovněmi služeb Business Critical (latence menší než 3 ms).
- Na základě směrného plánu propustnost předem přidělit velikost dat nebo soubory protokolu získat očekávaný výkon va.

Můžete si vybrat výpočetní prostředky a prostředky úložiště v době nasazení a potom je změnit bez zavedení prostojů pro vaši aplikaci pomocí [portálu Azure:](sql-database-scale-resources.md)

![změna velikosti spravované instance](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Informace o tom, jak vytvořit infrastrukturu virtuální sítě a spravovanou instanci, najdete [v tématu Vytvoření spravované instance](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> Je důležité, aby cílová virtuální síť a podsíť byly vždy v souladu s [požadavky na virtuální síť spravované instance](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Jakákoli nekompatibilita vám může zabránit ve vytváření nových instancí nebo v použití těch, které jste již vytvořili. Přečtěte si další informace o [vytváření nových](sql-database-managed-instance-create-vnet-subnet.md) a [konfiguraci stávajících](sql-database-managed-instance-configure-vnet-subnet.md) sítí.

## <a name="select-migration-method-and-migrate"></a>Výběr metody migrace a migrace

Možnost nasazení spravované instance cílí na scénáře uživatelů, které vyžadují hromadnou migraci databáze z místních implementací nebo implementace databáze IaaS. Jsou optimální volbou, když potřebujete zvednout a posunout zadní konec aplikací, které pravidelně používají úroveň instance a / nebo funkce mezi databázemi. Pokud se jedná o váš scénář, můžete přesunout celou instanci do odpovídajícího prostředí v Azure bez nutnosti re-architect vaše aplikace.

Chcete-li přesunout instance SQL, musíte pečlivě plánovat:

- Migrace všech databází, které je třeba kolokovat (ty spuštěné na stejné instanci)
- Migrace objektů na úrovni instance, na kterých závisí vaše aplikace, včetně přihlášení, přihlašovacích údajů, úloh sql agenta a operátorů a aktivačních událostí na úrovni serveru.

Spravovaná instance je spravovaná služba, která umožňuje delegovat některé běžné aktivity DBA na platformu, jak jsou integrovány. Proto některá data na úrovni instance není nutné migrovat, jako jsou například úlohy údržby pro pravidelné zálohování nebo vždy na konfiguraci, jako [je integrována vysoká dostupnost.](sql-database-high-availability.md)

Spravovaná instance podporuje následující možnosti migrace databáze (v současné době se jedná o jediné podporované metody migrace):

- Azure Database Migration Service – migrace s téměř nulovými prostoji,
- Nativní `RESTORE DATABASE FROM URL` – používá nativní zálohy ze serveru SQL Server a vyžaduje určité prostoje.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Služba Azure Database Migration Service (DMS)](../dms/dms-overview.md) je plně spravovaná služba navržená tak, aby umožňovala bezproblémové migrace z více zdrojů databáze na datové platformy Azure s minimálními prostoji. Tato služba zjednodušuje úkoly potřebné k přesunutí existujících databází třetích stran a SQL Serveru do Azure. Možnosti nasazení ve verzi Public Preview zahrnují databáze v databázích Azure SQL Database a SQL Server ve virtuálním počítači Azure. DMS je doporučená metoda migrace pro podnikové úlohy.

Pokud používáte SQL Server Integration Services (SSIS) na vašem SQL Server v místním prostředí, DMS ještě nepodporuje migraci katalogu SSIS (SSISDB), který ukládá balíčky SSIS, ale můžete zřídit Azure-SSIS Integration Runtime (IR) v Azure Data Factory (ADF), který vytvoří nový SSISDB ve spravované instanci a pak můžete znovu nasadit balíčky do něj, viz [Vytvoření Azure-SSIS IR v ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Další informace o tomto scénáři a postupech konfigurace pro Službu DMS najdete [v tématu Migrace místní databáze do spravované instance pomocí služby DMS](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Nativní obnovení z adresy URL

OBNOVENÍ nativních záloh (souborů.bak) převzatých z místního sql serveru nebo [SQL Serveru na virtuálních počítačích](https://azure.microsoft.com/services/virtual-machines/sql-server/), které jsou k dispozici ve [službě Azure Storage](https://azure.microsoft.com/services/storage/), je jednou z klíčových možností možnosti nasazení spravované instance, která umožňuje rychlou a snadnou migraci offline databáze.

Následující diagram poskytuje přehled na vysoké úrovni procesu:

![migrační tok](./media/sql-database-managed-instance-migration/migration-flow.png)

Následující tabulka obsahuje další informace o metodách, které můžete použít v závislosti na zdrojové verzi serveru SQL Server, kterou používáte:

|Krok|SQL Engine a verze|Metoda zálohování / obnovení|
|---|---|---|
|Umístění zálohy do Úložiště Azure|Předchozí SQL 2012 SP1 CU2|Nahrání souboru .bak přímo do úložiště Azure|
||2012 SP1 CU2 - 2016|Přímé zálohování pomocí zastaralé syntaxe [pověření](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)|
||2016 a výše|Přímé zálohování pomocí [pověření SAS](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Obnovení z úložiště Azure na spravovanou instanci|[OBNOVENÍ Z adresy URL s pověřením SAS](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - Při migraci databáze chráněné [transparentní šifrování dat](transparent-data-encryption-azure-sql.md) na spravovanou instanci pomocí možnosti nativní obnovení, odpovídající certifikát z místního nebo IaaS SQL Server je třeba migrovat před obnovením databáze. Podrobné kroky najdete v tématu [Migrace certifikátu TDE do spravované instance.](sql-database-managed-instance-migrate-tde-certificate.md)
> - Obnovení systémových databází není podporováno. Chcete-li migrovat objekty na úrovni instance (uložené v hlavních databázích nebo databázích msdb), doporučujeme je skriptovat a spouštět skripty T-SQL v cílové instanci.

Úvodní příručka o obnovení zálohy databáze do spravované instance pomocí pověření SAS najdete v tématu [Obnovení ze zálohy na spravovanou instanci](sql-database-managed-instance-get-started-restore.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>Monitorování aplikací

Po dokončení migrace na spravovanou instanci byste měli sledovat chování aplikace a výkon úlohy. Tento proces zahrnuje následující činnosti:
- [Porovnejte výkon úlohy spuštěné ve spravované instanci](#compare-performance-with-the-baseline) se [směrným plánem výkonu, který jste vytvořili na zdrojovém serveru SQL Server](#create-performance-baseline).
- Průběžně [sledujte výkon pracovního vytížení](#monitor-performance) a sledujte potenciální problémy a zlepšení.

### <a name="compare-performance-with-the-baseline"></a>Porovnání výkonu se směrným plánem

První aktivita, kterou byste měli provést ihned po úspěšné migraci, je porovnání výkonu úlohy s výkonem úlohy podle směrného plánu. Cílem této aktivity je potvrdit, že výkon pracovního vytížení ve spravované instanci odpovídá vašim potřebám. 

Migrace databáze do spravované instance udržuje nastavení databáze a jeho původní úroveň kompatibility ve většině případů. Původní nastavení jsou zachovány, kde je to možné, aby se snížilo riziko některých snížení výkonu ve srovnání se zdrojovým SQL Server. Pokud úroveň kompatibility databáze uživatelů byla 100 nebo vyšší před migrací, zůstane stejná po migraci. Pokud úroveň kompatibility databáze uživatelů byla 90 před migrací, v upgradované databázi je úroveň kompatibility nastavena na 100, což je nejnižší podporovaná úroveň kompatibility ve spravované instanci. Úroveň kompatibility systémových databází je 140. Vzhledem k tomu, že migrace na spravovanou instanci je ve skutečnosti migrace na nejnovější verzi SQL Server Database Engine, měli byste si být vědomi, že je třeba znovu otestovat výkon úlohy, aby se zabránilo některé překvapivé problémy s výkonem.

Jako předpoklad se ujistěte, že jste dokončili následující činnosti:
- Zarovnejte nastavení spravované instance s nastavením ze zdrojové instance serveru SQL Server zkoumáním různých instancí, databáze, nastavení temdb a konfigurací. Před spuštěním prvního porovnání výkonu se ujistěte, že jste nezměnili nastavení, jako jsou úrovně kompatibility nebo šifrování, nebo přijměte riziko, že některé nové funkce, které jste povolili, mohou ovlivnit některé dotazy. Chcete-li snížit rizika migrace, změňte úroveň kompatibility databáze pouze po sledování výkonu.
- Implementujte [pokyny pro osvědčené postupy úložiště pro obecné účely,](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) jako je například předběžné přidělení velikosti souborů, abyste získali lepší výkon.
- Seznamte se s [klíčovými rozdíly prostředí, které mohou způsobit rozdíly ve výkonu mezi spravovanou instancí a SQL Serverem]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) a identifikovat rizika, která by mohla ovlivnit výkon.
- Ujistěte se, že máte povolené úložiště dotazů a automatické ladění spravované instance. Tyto funkce umožňují měřit výkon pracovního vytížení a automaticky opravit potenciální problémy s výkonem. Naučte se používat Query Store jako optimální nástroj pro získání informací o výkonu pracovního vytížení před a po změně úrovně kompatibility databáze, jak je vysvětleno v [zachovat stabilitu výkonu během upgradu na novější verzi serveru SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Jakmile připravíte prostředí, které je co nejvíce srovnatelné s místním prostředím, můžete spustit úlohu a měřit výkon. Proces měření by měl zahrnovat stejné parametry, které jste naměřili [při vytváření základního výkonu měřítek pracovního vytížení na zdrojovém serveru SQL Server](#create-performance-baseline).
V důsledku toho byste měli porovnat parametry výkonu se směrným plánem a identifikovat kritické rozdíly.

> [!NOTE]
> V mnoha případech nebude možné získat přesně odpovídající výkon na spravované instanci a SQL Server. Spravovaná instance je databázový stroj serveru SQL Server, ale infrastruktura a konfigurace vysoké dostupnosti ve spravované instanci mohou způsobit určitý rozdíl. Můžete očekávat, že některé dotazy by být rychlejší, zatímco některé jiné může být pomalejší. Cílem porovnání je ověřit, že výkon pracovního vytížení ve spravované instanci odpovídá výkonu na SERVERU SQL (v průměru) a identifikovat existují nějaké kritické dotazy s výkonem, které neodpovídají původní výkon.

Výsledek porovnání výkonu může být:
- Výkon pracovního vytížení na spravované instanci je zarovnán nebo lepší než výkon úlohy na SQL Serveru. V tomto případě jste úspěšně potvrdili, že migrace je úspěšná.
- Většina parametrů výkonu a dotazy v pracovní zátěži fungují dobře, s některými výjimkami se sníženým výkonem. V takovém případě budete muset identifikovat rozdíly a jejich význam. Pokud existují některé důležité dotazy se sníženým výkonem, měli byste prozkoumat, zda jsou základní plány SQL změněny nebo dotazy jsou přístupná některé limity prostředků. Zmírnění v tomto případě může být použít některé rady na kritické dotazy (například změněna úroveň kompatibility, starší mohutnost odhad) buď přímo nebo pomocí průvodce plán, znovu sestavit nebo vytvořit statistiky a indexy, které by mohly mít vliv na plány. 
- Většina dotazů je pomalejší na spravované instanci ve srovnání se zdrojovým SQL Serverem. V tomto případě se pokuste identifikovat hlavní příčiny rozdílu, jako je [dosažení některých limitů prostředků,]( sql-database-managed-instance-resource-limits.md#service-tier-characteristics) jako jsou limity vstupně-line, limit paměti, limit rychlosti protokolu instancí atd. Pokud neexistují žádné omezení prostředků, které mohou způsobit rozdíl, zkuste změnit úroveň kompatibility databáze nebo změnit nastavení databáze, jako je odhad starší mohutnosti a znovu spustit test. Projděte si doporučení poskytnutá zobrazeními spravované instance nebo úložiště dotazů a identifikujte dotazy, které regresi v oblasti výkonu.

> [!IMPORTANT]
> Spravovaná instance má integrovanou funkci automatické opravy plánu, která je ve výchozím nastavení povolena. Tato funkce zajišťuje, že dotazy, které fungovaly v pastě, by se v budoucnu nesnížily. Ujistěte se, že tato funkce je povolena a že jste provedli úlohu dostatečně dlouho se starým nastavením před změnou nového nastavení, aby bylo možné spravované instance získat informace o výkonu a plánech směrného plánu.

Proveďte změnu parametrů nebo upgradujte úrovně služeb tak, aby se konvergovali s optimální konfigurací, dokud nezískáte výkon pracovního vytížení, který vyhovuje vašim potřebám.

### <a name="monitor-performance"></a>Monitorování výkonu

Spravovaná instance poskytuje mnoho pokročilých nástrojů pro monitorování a řešení potíží a měli byste je použít ke sledování výkonu ve vaší instanci. Některé z parametrů, které by vaše potřeba sledovat, jsou:
- Využití procesoru na instanci k určení má počet virtuálních jader, které jste zřídit, je správná shoda pro vaše úlohy.
- Očekávaná délka životnosti stránky ve spravované instanci k [určení, potřebujete další paměť](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Počkejte `INSTANCE_LOG_GOVERNOR` statistiky jako nebo `PAGEIOLATCH` to řekne, máte problémy s vstupně-io úložiště, zejména na úrovni obecnéúčely, kde možná budete muset předem přidělit soubory, abyste získali lepší výkon vstupně-míst.

## <a name="leverage-advanced-paas-features"></a>Využití pokročilých funkcí PaaS

Jakmile jste na plně spravované platformě a ověřili jste, že výkon y úloh odpovídá výkonu úloh y SQL Server, využijte výhody, které jsou poskytovány automaticky jako součást služby SQL Database. 

I v případě, že během migrace neprovedete některé změny ve spravované instanci, existuje vysoká pravděpodobnost, že byste při práci s instancí zapnuli některé nové funkce, abyste využili nejnovější vylepšení databázového stroje. Některé změny jsou povoleny pouze po [změně úrovně kompatibility databáze](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).


Například není třeba vytvářet zálohy na spravované instanci – služba provádí zálohy pro vás automaticky. Už se nemusíte starat o plánování, přijímání a správu záloh. Spravovaná instance umožňuje obnovit do libovolného bodu v čase v rámci této retenční období pomocí [bodu v čase zotavení (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore). Navíc se nemusíte starat o nastavení vysoké dostupnosti, protože je [vestavěna vysoká dostupnost.](sql-database-high-availability.md)

Chcete-li posílit zabezpečení, zvažte použití [ověřování azure active directory](sql-database-security-overview.md), [auditování](sql-database-managed-instance-auditing.md), [detekce hrozeb](sql-database-advanced-data-security.md), [zabezpečení na úrovni řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)a dynamické [maskování dat).](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)

Kromě pokročilých funkcí správy a zabezpečení poskytuje spravovaná instance sadu pokročilých nástrojů, které vám pomohou [sledovat a optimalizovat úlohu](sql-database-monitor-tune-overview.md). [Azure SQL analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) umožňuje monitorovat velkou sadu spravovaných instancí a centralizovat monitorování velkého počtu instancí a databází. [Automatické ladění](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) ve spravované instanci průběžně monitoruje výkon statistiky spuštění plánu SQL a automaticky opravuje zjištěné problémy s výkonem.

## <a name="next-steps"></a>Další kroky

- Informace o spravovaných instancích najdete [v tématu Co je spravovaná instance?](sql-database-managed-instance.md).
- Kurz, který obsahuje obnovení ze zálohy, najdete v [tématu Vytvoření spravované instance](sql-database-managed-instance-get-started.md).
- Kurz zobrazující migraci pomocí Služby DMS najdete [v tématu Migrace místní databáze do spravované instance pomocí služby DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
