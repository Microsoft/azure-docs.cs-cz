---
title: Migrace z SQL Server do spravované instance Azure SQL
description: Přečtěte si, jak migrovat databázi z SQL Server do spravované instance Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
ms.date: 07/11/2019
ms.openlocfilehash: c0b34e17c202cb060773c53aa5775343ade9c2ee
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2020
ms.locfileid: "84193775"
---
# <a name="sql-server-instance-migration-to-azure-sql-managed-instance"></a>Migrace instance SQL Server do spravované instance Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

V tomto článku se dozvíte o metodách migrace instance verze SQL Server 2005 nebo novější do [spravované instance Azure SQL](sql-managed-instance-paas-overview.md). Informace o migraci na izolovanou databázi nebo elastický fond najdete v tématu [migrace do jedné nebo ve fondu databáze](../database/migrate-to-database-from-sql-server.md). Informace o migraci z jiných platforem najdete v tématu [Průvodce migrací databáze Azure](https://datamigration.microsoft.com/).

> [!NOTE]
> Pokud chcete rychle začít a vyzkoušet spravovanou instanci Azure SQL, můžete místo této stránky přejít na [stručnou Úvodní příručku](quickstart-content-reference-guide.md) .

V případě vysoké úrovně proces migrace databáze vypadá takto:

![proces migrace](./media/migrate-to-instance-from-sql-server/migration-process.png)

- [Vyhodnoťte kompatibilitu spravované instance SQL](#assess-sql-managed-instance-compatibility) , kde byste měli zajistit, že neexistují žádné blokující problémy, které by mohly bránit migraci.
  - Tento krok zahrnuje také vytváření [standardních hodnot výkonu](#create-performance-baseline) k určení využití prostředků ve zdrojové SQL Server instanci. Tento krok je nutný, pokud chcete nasadit správnou velikost spravované instance SQL a ověřit, že po migraci nebudou ovlivněny žádné funkční věci.
- [Zvolit možnosti připojení aplikace](connect-application-instance.md)
- [Nasaďte se na optimální velikost spravované instance SQL](#deploy-to-an-optimally-sized-managed-instance) , kde budete vybírat technické charakteristiky (počet virtuální jádra, velikost paměti) a úroveň výkonu (Pro důležité obchodní informace, pro obecné účely) spravované instance SQL.
- [Vyberte způsob migrace a migrujte](#select-migration-method-and-migrate) , kam migrujete databáze pomocí offline migrace (nativní zálohování a obnovení, import/export databáze) nebo online migrace (služba migrace dat, transakční replikace).
- [Monitorujte aplikace](#monitor-applications) , abyste měli jistotu, že máte očekávaný výkon.

> [!NOTE]
> Chcete-li migrovat jednotlivé databáze do jediné databáze nebo elastického fondu, přečtěte si téma [migrace databáze SQL Server do Azure SQL Database](../database/migrate-to-database-from-sql-server.md).

## <a name="assess-sql-managed-instance-compatibility"></a>Posouzení kompatibility spravované instance SQL

Nejdřív Zjistěte, jestli je spravovaná instance SQL kompatibilní s požadavky na databázi vaší aplikace. Spravovaná instance SQL je navržená tak, aby poskytovala snadnou migraci pomocí přezvednutí a posunutí pro většinu stávajících aplikací, které používají SQL Server. Někdy ale můžete vyžadovat funkce nebo možnosti, které ještě nejsou podporované, a náklady na implementaci alternativního řešení jsou moc vysoké.

Použijte [Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) k detekci potenciálních problémů s kompatibilitou, které mají vliv na funkčnost databáze v Azure SQL Database. Pokud dojde k nějakým chybám blokujícím blokování, možná budete muset vzít v úvahu jinou možnost, například [SQL Server na virtuálních počítačích Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Zde je několik příkladů:

- Pokud vyžadujete přímý přístup k operačnímu systému nebo systému souborů, například k instalaci jiných agentů nebo vlastních agentů na stejný virtuální počítač s SQL Server.
- Pokud máte striktní závislost na funkcích, které stále nejsou podporované, jako jsou FileStream/soubor, základ a transakce mezi instancemi.
- Pokud naprosto potřebuje zůstat v určité verzi SQL Server (například 2012).
- Pokud jsou vaše požadavky na výpočetní výkon mnohem nižší, než jsou možnosti spravované instance (jedna vCore, instance) a konsolidace databází přijatelné.

Pokud jste vyřešili všechny identifikované bloky migrace a budete pokračovat v migraci do spravované instance SQL, pamatujte na to, že některé změny mohou ovlivnit výkon úlohy:

- Povinný model úplného obnovení a pravidelný plán automatického zálohování může mít vliv na výkon úloh nebo údržby/ETL, pokud jste pravidelně používali jednoduchý nebo hromadně protokolovaný model nebo zastavili zálohování na vyžádání.
- Různé konfigurace na úrovni serveru nebo databáze, jako jsou příznaky trasování nebo úrovně kompatibility
- Nové funkce, které používáte jako transparentní šifrování databáze (TDE) nebo skupiny automatického převzetí služeb při selhání, můžou mít vliv na využití CPU a vstupně-výstupních operací.

Spravovaná instance SQL garantuje 99,99% dostupnost i v kritických scénářích, takže režijní náklady způsobené těmito funkcemi se nedají zakázat. Další informace najdete v části [hlavní příčiny, které můžou způsobit jiný výkon SQL Server a Azure SQL Managed instance](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-performance-baseline"></a>Vytvořit směrný plán výkonu

Pokud potřebujete porovnat výkon úloh na spravované instanci s původní úlohou běžící na SQL Server, budete muset vytvořit směrný plán výkonu, který se použije pro porovnání.

Základní hodnota výkonu je sada parametrů, jako je průměrné nebo maximální využití procesoru, průměrná/maximální latence vstupně-výstupních operací disku, propustnost, IOPS, průměr/maximum očekávané životnosti stránky, průměrná maximální velikost databáze tempdb. Po migraci budete chtít mít podobné nebo ještě lepší parametry, takže je důležité měřit a zaznamenávat hodnoty standardních hodnot pro tyto parametry. Kromě systémových parametrů byste museli vybrat sadu reprezentativních dotazů nebo nejdůležitější dotazy v rámci úlohy a měřit minimální/průměrné/maximální trvání využití procesoru pro vybrané dotazy. Tyto hodnoty vám umožní porovnat výkon úlohy běžící na spravované instanci s původními hodnotami na zdrojovém SQL Server.

Některé parametry, které byste potřebovali pro měření SQL Server instance, jsou:

- [Monitorujte využití CPU na instanci SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) a zaznamenejte průměrné a špičkové využití procesoru.
- [Monitorujte využití paměti ve vaší instanci SQL Server](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) a určete množství paměti využívané různými součástmi, jako je fond vyrovnávací paměti, mezipaměť plánu, fond úložiště sloupce, [OLTP v paměti](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017), atd. Kromě toho byste měli najít průměrnou a maximální hodnotu čítače výkonu životnosti stránky očekávané paměti.
- Monitorujte využití v/v disku u zdrojové SQL Server instance pomocí zobrazení [Sys. dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) a [čítačů výkonu](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Monitorujte výkon úloh a dotazů nebo instanci SQL Server tím, že prozkoumáte zobrazení dynamické správy nebo úložiště dotazů, pokud migrujete z SQL Server 2016 + verze. Identifikujte Průměrné trvání a využití procesoru nejdůležitějších dotazů v úloze a porovnejte je s dotazy, které jsou spuštěny ve spravované instanci.

> [!Note]
> Pokud si všimnete jakýchkoli potíží s úlohou na SQL Server, jako je například vysoké využití procesoru, konstantní tlak paměti, databáze tempdb nebo Parametrizace, měli byste je před provedením standardních hodnot a migrace zkusit vyřešit na své zdrojové SQL Server instanci. Migrace s cílem informovat všechny nové systémové migh způsobí neočekávané výsledky a zruší všechny porovnání výkonu.

Jako výsledek této aktivity byste měli mít přehlednou průměrnou a maximální hodnotu pro využití procesoru, paměti a vstupně-výstupních operací ve zdrojovém systému a také průměrnou a maximální dobu trvání a využití procesoru dominantního a nejzávažných dotazů v rámci úlohy. Tyto hodnoty byste měli později použít k porovnání výkonu úloh na spravované instanci s výkonem standardních hodnot zatížení na zdrojovém SQL Server.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Nasazení na optimální spravovanou instanci

Spravovaná instance SQL je přizpůsobená pro místní úlohy, které plánují přesun do cloudu. Zavádí [Nový model nákupu](../database/service-tiers-vcore.md) , který poskytuje větší flexibilitu při výběru správné úrovně prostředků pro vaše úlohy. V místním světě jste pravděpodobně zvyklí velikost těchto úloh použít fyzickými jádry a šířkou pásma v/v. Nákupní model pro spravovanou instanci je založený na virtuálních jádrech neboli "virtuální jádra", a navíc je k dispozici další úložiště a vstupně-výstupní operace samostatně. Model vCore je jednodušší způsob, jak pochopit požadavky na výpočetní výkon v cloudu a co dnes používáte místně. Tento nový model vám umožní správnou velikost cílového prostředí v cloudu. Tady jsou některé obecné pokyny, které vám pomohou zvolit správnou úroveň služby a charakteristiky:

- Na základě základní využití procesoru můžete zřídit spravovanou instanci, která odpovídá počtu jader, které používáte v SQL Server. je třeba mít na paměti, že je potřeba škálovat vlastnosti procesoru tak, aby odpovídaly [vlastnostem virtuálního počítače, kde je spravovaná instance nainstalovaná](resource-limits.md#hardware-generation-characteristics).
- Na základě základní využití paměti vyberte [úroveň služby, která má odpovídající paměť](resource-limits.md#hardware-generation-characteristics). Velikost paměti se nedá přímo vybrat, takže byste museli vybrat spravovanou instanci s množstvím virtuální jádra, které má odpovídající paměť (například 5,1 GB/vCore v Gen5).
- Na základě základní latence v/v subsystému souborů vyberte mezi Pro obecné účely (latence větší než 5ms) a Pro důležité obchodní informace úrovně služeb (latence menší než 3 MS).
- Na základě propustnosti směrného plánu předem přidělte velikost dat nebo souborů protokolu, aby bylo možné získat očekávaný vstupně-výstupní výkon.

Můžete zvolit výpočetní prostředky a prostředky úložiště v době nasazení a pak ji později změnit, aniž byste museli zavádět prostoje aplikace pomocí [Azure Portal](../database/scale-resources.md):

![velikost spravované instance](./media/migrate-to-instance-from-sql-server/managed-instance-sizing.png)

Informace o tom, jak vytvořit infrastrukturu virtuální sítě a spravovanou instanci, najdete v tématu [Vytvoření spravované instance](instance-create-quickstart.md).

> [!IMPORTANT]
> Je důležité udržovat svou cílovou virtuální síť a podsíť vždycky v souladu s [požadavky na virtuální síť spravované instance](connectivity-architecture-overview.md#network-requirements). Jakékoli nekompatibility vám může zabránit v vytváření nových instancí nebo používání těch, které jste už vytvořili. Přečtěte si další informace o [vytváření nových](virtual-network-subnet-create-arm-template.md) a [konfigurování stávajících](vnet-existing-add-subnet.md) sítí.

## <a name="select-migration-method-and-migrate"></a>Výběr metody migrace a migrace

Spravovaná instance SQL cílí na scénáře uživatelů, které vyžadují migraci databáze z místního prostředí nebo implementace databáze virtuálních počítačů Azure. Jsou optimální volbou v případě, že potřebujete navrátit a posunout back-end aplikací, které pravidelně používají úroveň instance nebo mezidatabázové funkce. Pokud se jedná o váš scénář, můžete celou instanci přesunout do odpovídajícího prostředí v Azure, aniž byste museli své aplikace znovu architektovat.

Chcete-li přesunout instance SQL, je třeba pečlivě naplánovat:

- Migrace všech databází, které je třeba společně umístěného (spuštěné ve stejné instanci)
- Migrace objektů na úrovni instance, na kterých vaše aplikace závisí, včetně přihlášení, přihlašovacích údajů, úloh a operátorů SQL agenta a triggerů na úrovni serveru.

Spravovaná instance SQL je spravovaná služba, která umožňuje delegovat některé běžné aktivity DBA v DBA na platformu, jak jsou integrovány. Z tohoto důvodu je třeba, aby některá data na úrovni instance nemusela být migrována, například úlohy údržby pro pravidelné zálohování nebo konfiguraci služby Always On, protože je integrovaná [Vysoká dostupnost](../database/high-availability-sla.md) .

Spravovaná instance SQL podporuje následující možnosti migrace databáze (aktuálně se jedná o jediné podporované metody migrace):

- Azure Database Migration Service – migrace s téměř nulovým výpadkem,
- Nativní `RESTORE DATABASE FROM URL` – používá nativní zálohy z SQL Server a vyžaduje určité výpadky.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Azure Database Migration Service (DMS)](../../dms/dms-overview.md) je plně spravovaná služba navržená tak, aby umožňovala bezproblémové migrace z více databázových zdrojů do datových platforem Azure s minimálními výpadky. Tato služba zjednodušuje úlohy potřebné k přesunu stávajících databází třetích stran a SQL Server do Azure. Mezi možnosti nasazení ve verzi Public Preview patří databáze v Azure SQL Database a databáze SQL Server na virtuálním počítači Azure. DMS je doporučená metoda migrace pro vaše podnikové úlohy.

Pokud používáte služba SSIS (SQL Server Integration Services) (SSIS) v SQL Server místně, DMS ještě nepodporuje migraci katalogu SSIS (SSISDB), který ukládá balíčky SSIS, ale můžete zřídit Azure-SSIS Integration Runtime (IR) v Azure Data Factory (ADF), která vytvoří nový SSISDB ve spravované instanci a pak můžete balíčky znovu nasadit do tohoto úložiště v tématu [vytvoření Azure-SSIS IR v ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Další informace o tomto scénáři a postupu konfigurace pro DMS najdete v tématu [migrace místní databáze do spravované instance pomocí DMS](../../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Nativní obnovení z adresy URL

OBNOVENÍ nativních záloh (souborů. bak) pořízených z SQL Server instance, která je k dispozici v [Azure Storage](https://azure.microsoft.com/services/storage/), je jednou z klíčových funkcí spravované instance SQL, která umožňuje rychlou a jednoduchou migraci offline databáze.

Následující diagram poskytuje podrobný přehled procesu:

![migrace – tok](./media/migrate-to-instance-from-sql-server/migration-flow.png)

V následující tabulce najdete další informace týkající se metod, které můžete použít v závislosti na zdrojové SQL Server verzi, kterou používáte:

|Krok|Stroj a verze SQL|Metoda Backup/Restore|
|---|---|---|
|Vložit zálohu do Azure Storage|Předchozí verze SQL 2012 SP1 CU2|Nahrání souboru. bak přímo do Azure Storage|
||2012 SP1 CU2-2016|Přímá záloha pomocí syntaxe [přihlašovacích údajů](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) zastaralá|
||2016 a vyšší|Přímé zálohování pomocí [s přihlašovacími údaji SAS](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Obnovení z úložiště Azure do spravované instance|[OBNOVIT z adresy URL s PŘIHLAŠOVACÍmi údaji SAS](restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - Při migraci databáze chráněné nástrojem [transparentní šifrování dat](../database/transparent-data-encryption-tde-overview.md) do spravované instance pomocí možnosti nativní obnovení musí být před obnovením databáze migrován odpovídající certifikát z místního nebo virtuálního počítače Azure SQL Server. Podrobný postup najdete v tématu [Migrace certifikátu TDE do spravované instance](tde-certificate-migrate.md) .
> - Obnovení systémových databází se nepodporuje. Chcete-li migrovat objekty úrovně instance (uložené v databázi Master nebo msdb), doporučujeme je vyskriptovat a spustit skripty T-SQL v cílové instanci.

Rychlý Start ukazující, jak obnovit zálohu databáze do spravované instance pomocí přihlašovacích údajů SAS, najdete v tématu [obnovení ze zálohy do spravované instance](restore-sample-database-quickstart.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>Monitorování aplikací

Po dokončení migrace do spravované instance byste měli sledovat chování aplikace a výkon vašich úloh. Tento proces zahrnuje následující činnosti:

- [Porovnejte výkon úloh spuštěných ve spravované instanci](#compare-performance-with-the-baseline) se [směrným plánem výkonu, který jste vytvořili ve zdrojovém SQL Server](#create-performance-baseline).
- Nepřetržitě [monitorujte výkon svých úloh](#monitor-performance) a Identifikujte případné problémy a zlepšení.

### <a name="compare-performance-with-the-baseline"></a>Porovnání výkonu se směrným plánem

První aktivita, kterou byste museli provést hned po úspěšné migraci, je porovnávat výkon úloh s výkonem standardních úloh. Cílem této aktivity je potvrdit, že výkon zatížení vaší spravované instance vyhovuje vašim potřebám.

Migrace databáze do spravované instance udržuje nastavení databáze a její původní úroveň kompatibility ve většině případů. Původní nastavení jsou zachována, pokud je to možné, aby se snížilo riziko některých snížení výkonu ve srovnání se zdrojovým SQL Server. Pokud byla před migrací úroveň kompatibility uživatelské databáze 100 nebo vyšší, zůstane stejná i po migraci. Pokud byla úroveň kompatibility uživatelské databáze 90 před migrací, v upgradované databázi je úroveň kompatibility nastavena na 100, což je nejnižší podporovaná úroveň kompatibility ve spravované instanci. Úroveň kompatibility systémových databází je 140. Vzhledem k tomu, že migrace do spravované instance se ve skutečnosti migruje na nejnovější verzi SQL Server databázového stroje, měli byste si uvědomit, že pokud chcete zabránit problémům s výkonem překvapivé, je potřeba znovu otestovat výkon svých úloh.

V rámci předpokladu se ujistěte, že jste dokončili následující aktivity:

- Pomocí nastavení ze zdrojové SQL Server instance zarovnejte nastavení ze zdrojové instance tím, že prozkoumáte různé instance, databáze, nastavení temdb a konfigurace. Před spuštěním prvního porovnání výkonu se ujistěte, že jste nezměnili nastavení, jako jsou úrovně kompatibility nebo šifrování, nebo přijměte riziko, že některé nové funkce, které jste povolili, mohou ovlivnit některé dotazy. Chcete-li snížit rizika migrace, změňte úroveň kompatibility databáze pouze po sledování výkonu.
- Implementujte [pokyny k osvědčeným postupům úložiště pro pro obecné účely](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) , jako je třeba předběžně přidělit velikost souborů, abyste získali lepší výkon.
- Přečtěte si o [klíčových rozdílech v prostředích, které by mohly způsobit rozdíly ve výkonu mezi spravovanou instancí a SQL Server]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) a identifikovat rizika, která mohou ovlivnit výkon.
- Ujistěte se, že jste zachovali povolené úložiště dotazů a automatické ladění na spravované instanci. Tyto funkce umožňují měřit výkon úloh a automaticky opravovat potenciální problémy s výkonem. Naučte se používat úložiště dotazů jako optimální nástroj pro získání informací o výkonu úloh před a po změně úrovně kompatibility databáze, jak je vysvětleno v [části zachování stability výkonu během upgradu na novější verzi SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Po přípravě prostředí, které je co nejvíc porovnatelné s vaším místním prostředím, můžete začít spouštět úlohy a měřit výkon. Proces měření by měl zahrnovat stejné parametry, které jste naměřeni [při vytváření směrného výkonu pro vaše míry úloh na zdrojovém SQL Server](#create-performance-baseline).
V důsledku toho byste měli porovnat parametry výkonu se směrným plánem a určit kritické rozdíly.

> [!NOTE]
> V mnoha případech nebudete moci získat přesně vyhovující výkon pro spravovanou instanci a SQL Server. Spravovaná instance Azure SQL je SQL Server databázový stroj, ale konfigurace infrastruktury a vysoké dostupnosti ve spravované instanci může přinést nějaký rozdíl. Můžete očekávat, že některé dotazy budou rychlejší, zatímco jiné můžou být pomalejší. Cílem porovnání je ověřit, že výkon úloh ve spravované instanci odpovídá výkonu na SQL Server (v průměru) a určit, zda existují kritické dotazy s výkonem, které neodpovídají původnímu výkonu.

Výsledek porovnání výkonu může být následující:

- Výkon úloh na spravované instanci je zarovnán nebo je lepší, protože výkon úloh SQL Server. V takovém případě jste úspěšně potvrdili, že migrace proběhla úspěšně.
- Většina parametrů výkonu a dotazů v práci s úlohou funguje s některými výjimkami, které mají snížený výkon. V takovém případě byste museli určit rozdíly a jejich důležitost. Pokud existují některé důležité dotazy s sníženým výkonem, měli byste prozkoumat, že se změnily příslušné plány SQL, nebo že dotazy jsou v některých omezeních prostředků. Omezení rizik v tomto případě by mohlo být použití některých doporučení pro kritické dotazy (například změněné úrovně kompatibility, starší mohutnost estimator), a to buď přímo, nebo pomocí vodítek plánů, opětovného sestavování nebo vytváření statistik a indexů, které mohou mít vliv na plány.
- Většina dotazů je ve srovnání se zdrojovou SQL Server pomalejší než u spravované instance. V tomto případě se pokusíte identifikovat hlavní příčiny rozdílů, jako je například omezení [počtu prostředků]( resource-limits.md#service-tier-characteristics) , jako jsou limity v/v, limit paměti, limit četnosti protokolu instance atd. Pokud neexistují žádná omezení prostředků, která by mohla způsobit rozdíl, zkuste změnit úroveň kompatibility databáze nebo změnit nastavení databáze, jako je například odhad mohutnosti, a spusťte test znovu. Přečtěte si doporučení poskytovaná zobrazeními spravované instance nebo úložiště dotazů a Identifikujte dotazy, které navrácený výkon.

> [!IMPORTANT]
> Spravovaná instance Azure SQL má vestavěnou funkci automatického naplánování, která je ve výchozím nastavení povolená. Tato funkce zajišťuje, že dotazy, které pracovaly správně v rámci vložení, nebudou v budoucnu degradované. Ujistěte se, že je tato funkce povolená a že jste před změnou nových nastavení spustili úlohu dostatečně dlouho se starým nastavením, aby mohla spravovaná instance získat informace o výkonu a plánech směrného plánu.

Proveďte změnu parametrů nebo upgradujte úrovně služeb tak, aby se sblíženy s optimální konfigurací, dokud nezískáte výkon úloh, který vyhovuje vašim potřebám.

### <a name="monitor-performance"></a>Monitorování výkonu

SQL Managed instance poskytuje mnoho pokročilých nástrojů pro monitorování a odstraňování potíží a je vhodné je použít k monitorování výkonu vaší instance. Některé parametry, které by bylo potřeba monitorovat, jsou:

- Využití procesoru u instance k určení určuje počet virtuální jádra, které jste zřídili, správnou shodu pro vaše zatížení.
- Očekávané životního cyklu stránky na spravované instanci, abyste zjistili [, že potřebujete další paměť](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Čekejte na `INSTANCE_LOG_GOVERNOR` statistiky `PAGEIOLATCH` , jako nebo, které vám sdělí, že máte vstupně-výstupní problémy s úložištěm, zejména na úrovni pro obecné účely, kde možná budete muset předem přidělit soubory, abyste získali lepší vstupně-výstupní operace.

## <a name="leverage-advanced-paas-features"></a>Využití pokročilých funkcí PaaS

Jakmile budete na plně spravované platformě a ověříte, že výkon úloh odpovídá vašemu SQL Server výkonu úloh, využijte výhody, které jsou k dispozici automaticky jako součást služby.

I v případě, že během migrace neprovedete některé změny ve spravované instanci, je velmi pravděpodobné, že byste zapnuli některé nové funkce, zatímco vy vaše instance pracujete, abyste mohli využít výhod nejnovějších vylepšení databázového stroje. Některé změny se aktivují až po [změně úrovně kompatibility databáze](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).

Například nemusíte vytvářet zálohy na spravované instanci – služba provádí zálohování automaticky. Už si nemusíte dělat starosti s plánováním, správou a správou záloh. Managed instance poskytuje možnost obnovení do libovolného bodu v čase v rámci této doby uchování pomocí funkce [Obnovení bodu v čase (PITR)](../database/recovery-using-backups.md#point-in-time-restore). Navíc se nemusíte starat o nastavení vysoké dostupnosti, protože je integrovaná [Vysoká dostupnost](../database/high-availability-sla.md) .

Pokud chcete posílit zabezpečení, zvažte použití [Azure Active Directory ověřování](../database/security-overview.md), [auditování](auditing-configure.md), [detekce hrozeb](../database/advanced-data-security.md), [zabezpečení na úrovni řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)a [dynamického maskování dat](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ).

Kromě pokročilých funkcí správy a zabezpečení poskytuje spravovaná instance sadu pokročilých nástrojů, které vám pomůžou [monitorovat a ladit vaše úlohy](../database/monitor-tune-overview.md). [Azure SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) umožňuje monitorovat velkou sadu spravovaných instancí a centralizovat monitorování velkého počtu instancí a databází. [Automatické ladění](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) ve spravované instanci nepřetržitě monitoruje výkon statistik spuštění plánu SQL a automaticky opravuje zjištěné problémy s výkonem.

## <a name="next-steps"></a>Další kroky

- Informace o spravované instanci Azure SQL najdete v tématu [co je Azure SQL Managed instance?](sql-managed-instance-paas-overview.md).
- Kurz, který obsahuje obnovení ze zálohy, najdete v tématu [Vytvoření spravované instance Azure SQL](instance-create-quickstart.md).
- Kurz znázorňující migraci pomocí DMS najdete v tématu [migrace místní databáze do spravované instance Azure SQL pomocí DMS](../../dms/tutorial-sql-server-to-managed-instance.md).  
