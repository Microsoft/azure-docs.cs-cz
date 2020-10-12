---
title: Migrace z SQL Server do spravované instance Azure SQL
description: Přečtěte si, jak migrovat databázi z SQL Server do spravované instance Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: migration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: ''
ms.date: 07/11/2019
ms.openlocfilehash: a33ff6b927045389c3692201fa70839c6a466ede
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90887655"
---
# <a name="sql-server-instance-migration-to-azure-sql-managed-instance"></a>Migrace instance SQL Server do spravované instance Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

V tomto článku se dozvíte o metodách migrace instance verze SQL Server 2005 nebo novější do [spravované instance Azure SQL](sql-managed-instance-paas-overview.md). Informace o migraci na izolovanou databázi nebo elastický fond najdete v tématu [migrace na SQL Database](../database/migrate-to-database-from-sql-server.md). Informace o migraci z jiných platforem najdete v tématu [Průvodce migrací databáze Azure](https://datamigration.microsoft.com/).

> [!NOTE]
> Pokud chcete rychle začít a vyzkoušet spravovanou instanci Azure SQL, můžete místo této stránky přejít na [příručku pro rychlý](quickstart-content-reference-guide.md) Start.

V případě vysoké úrovně proces migrace databáze vypadá takto:

![Proces migrace](./media/migrate-to-instance-from-sql-server/migration-process.png)

- [Vyhodnoťte kompatibilitu spravované instance SQL](#assess-sql-managed-instance-compatibility) , kde byste měli zajistit, že neexistují žádné blokující problémy, které by mohly bránit migraci.
  
  Tento krok zahrnuje také vytvoření [směrného plánu výkonu](#create-a-performance-baseline) pro určení využití prostředků ve zdrojové SQL Server instanci. Tento krok je nutný, pokud chcete nasadit správnou velikost spravované instance a ověřit, že výkon po migraci neovlivní.
- [Vyberte možnosti připojení aplikace](connect-application-instance.md).
- [Nasaďte do optimální velikosti spravované instance](#deploy-to-an-optimally-sized-managed-instance) , kde budete vybírat technické charakteristiky (počet virtuální jádra, velikost paměti) a úroveň výkonu (Pro důležité obchodní informace, pro obecné účely) vaší spravované instance.
- [Vyberte způsob migrace a migrujte](#select-a-migration-method-and-migrate) , kam migrujete své databáze pomocí offline migrace (nativní zálohování a obnovení, import/export databáze) nebo online migrace (Azure Data Migration Service, transakční replikace).
- [Monitorujte aplikace](#monitor-applications) , abyste měli jistotu, že máte očekávaný výkon.

> [!NOTE]
> Chcete-li migrovat jednotlivé databáze do jediné databáze nebo elastického fondu, přečtěte si téma [migrace databáze SQL Server do Azure SQL Database](../database/migrate-to-database-from-sql-server.md).

## <a name="assess-sql-managed-instance-compatibility"></a>Posouzení kompatibility spravované instance SQL

Nejdřív Zjistěte, jestli je spravovaná instance SQL kompatibilní s požadavky na databázi vaší aplikace. Spravovaná instance SQL je navržená tak, aby poskytovala snadnou migraci pomocí přezvednutí a posunutí pro většinu stávajících aplikací, které používají SQL Server. Někdy ale můžete vyžadovat funkce nebo možnosti, které ještě nejsou podporované, a náklady na implementaci alternativního řešení jsou moc vysoké.

Použijte [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview) k detekci potenciálních problémů s kompatibilitou, které mají vliv na funkčnost databáze na Azure SQL Database. Pokud dojde k nějakým chybám blokujícím blokování, možná budete muset vzít v úvahu alternativní možnost, například [SQL Server na virtuálním počítači Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Tady je několik příkladů:

- Pokud požadujete přímý přístup k operačnímu systému nebo systému souborů, například pro instalaci jiných agentů nebo vlastních agentů na stejný virtuální počítač s SQL Server.
- Pokud máte striktní závislost na funkcích, které stále nejsou podporované, jako jsou FileStream/soubor, základ a transakce mezi instancemi.
- Pokud je nezbytně nutné zůstat v určité verzi SQL Server (například 2012).
- Pokud jsou vaše požadavky na výpočetní výkon mnohem nižší než nabídky spravované instance (jeden vCore, například instance) a konsolidace databáze není přijatelnou možností.

Pokud jste vyřešili všechny identifikované blokující bloky migrace a pokračujete na migraci do spravované instance SQL, pamatujte na to, že některé změny mohou ovlivnit výkon úlohy:

- Povinný model úplného obnovení a pravidelný plán automatického zálohování může mít vliv na výkon úloh nebo údržby/ETL, pokud jste pravidelně používali jednoduchý nebo hromadně protokolovaný model nebo zastavili zálohování na vyžádání.
- Různé konfigurace na úrovni serveru nebo databáze, jako jsou příznaky trasování nebo úrovně kompatibility.
- Nové funkce, které používáte jako transparentní šifrování databáze (TDE) nebo skupiny automatického převzetí služeb při selhání, můžou mít vliv na využití CPU a vstupně-výstupních operací.

Spravovaná instance SQL garantuje 99,99% dostupnost i v kritických scénářích, takže režijní náklady způsobené těmito funkcemi se nedají zakázat. Další informace najdete v části [hlavní příčiny, které můžou způsobit jiný výkon SQL Server a Azure SQL Managed instance](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-a-performance-baseline"></a>Vytvoření standardních hodnot výkonu

Pokud potřebujete porovnat výkon svého zatížení na spravované instanci s původní úlohou běžící na SQL Server, budete muset vytvořit směrný plán výkonu, který se použije k porovnání.

Základní hodnota výkonu je sada parametrů, jako je průměrné nebo maximální využití procesoru, průměrná/maximální latence vstupně-výstupních operací disku, propustnost, IOPS, průměr/maximum očekávané životnosti stránky a průměrná maximální velikost databáze tempdb. Po migraci budete chtít mít podobné nebo ještě lepší parametry, takže je důležité měřit a zaznamenávat hodnoty standardních hodnot pro tyto parametry. Kromě systémových parametrů byste museli vybrat sadu reprezentativních dotazů nebo nejdůležitější dotazy v rámci úlohy a měřit minimální/průměrnou a maximální dobu trvání a využití procesoru pro vybrané dotazy. Tyto hodnoty vám umožní porovnat výkon úlohy běžící na spravované instanci s původními hodnotami v instanci zdrojového SQL Server.

Některé parametry, které byste potřebovali pro měření SQL Server instance, jsou:

- [Monitorujte využití CPU na instanci SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) a zaznamenejte průměrné a špičkové využití procesoru.
- [Monitorujte využití paměti ve vaší instanci SQL Server](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) a určete množství paměti využívané různými součástmi, jako je fond vyrovnávací paměti, mezipaměť plánu, fond úložiště sloupce, [OLTP v paměti](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017), atd. Kromě toho byste měli najít průměrnou a maximální hodnotu čítače výkonu životnosti stránky očekávané paměti.
- Monitorujte využití v/v disku u zdrojové SQL Server instance pomocí zobrazení [Sys.dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) nebo [čítačů výkonu](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Monitorujte výkon úloh a dotazů nebo svou instanci SQL Server tím, že prozkoumáte zobrazení dynamické správy nebo úložiště dotazů, pokud migrujete z SQL Server 2016 + verze. Identifikujte Průměrné trvání a využití procesoru nejdůležitějších dotazů v úloze a porovnejte je s dotazy, které jsou spuštěny ve spravované instanci.

> [!Note]
> Pokud si všimnete jakýchkoli potíží s úlohou v SQL Server, jako je například vysoké využití procesoru, konstantní tlak paměti nebo Parametrizace, nebo problémy s databází tempdb nebo, měli byste je před provedením standardních hodnot a migrace zkusit vyřešit na své zdrojové SQL Server instanci. Migrace známých problémů do jakéhokoli nového systému může způsobit neočekávané výsledky a zrušení platnosti všech porovnání výkonu.

Jako výsledek této aktivity byste měli mít přehlednou průměrnou a maximální hodnotu pro využití procesoru, paměti a vstupně-výstupních operací ve zdrojovém systému a také průměrnou a maximální dobu trvání a využití procesoru dominantního a nejzávažných dotazů v rámci úlohy. Tyto hodnoty byste měli později použít k porovnání výkonu úloh ve spravované instanci s výkonem standardních hodnot úloh na zdrojové SQL Server instanci.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Nasazení do optimální velikosti spravované instance

Spravovaná instance SQL je přizpůsobená pro místní úlohy, které plánují přesun do cloudu. Zavádí [Nový model nákupu](../database/service-tiers-vcore.md) , který poskytuje větší flexibilitu při výběru správné úrovně prostředků pro vaše úlohy. V místním světě jste pravděpodobně zvyklí velikost těchto úloh použít fyzickými jádry a šířkou pásma v/v. Nákupní model pro spravovanou instanci je založený na virtuálních jádrech neboli "virtuální jádra", a navíc je k dispozici další úložiště a vstupně-výstupní operace samostatně. Model vCore je jednodušší způsob, jak pochopit požadavky na výpočetní výkon v cloudu a co dnes používáte místně. Tento nový model vám umožní správnou velikost cílového prostředí v cloudu. Tady jsou některé obecné pokyny, které vám pomohou zvolit správnou úroveň služby a charakteristiky:

- Na základě základní využití procesoru můžete zřídit spravovanou instanci, která odpovídá počtu jader, které používáte v SQL Server. je třeba mít na paměti, že je potřeba škálovat vlastnosti procesoru tak, aby odpovídaly [vlastnostem virtuálního počítače, kde je spravovaná instance nainstalovaná](resource-limits.md#hardware-generation-characteristics).
- Na základě základní využití paměti vyberte [úroveň služby, která má odpovídající paměť](resource-limits.md#hardware-generation-characteristics). Velikost paměti se nedá přímo vybrat, takže byste museli vybrat spravovanou instanci s množstvím virtuální jádra, které má odpovídající paměť (například 5,1 GB/vCore v Gen5).
- V závislosti na latenci v/v podsystému souborů vyberte mezi Pro obecné účely (latence větší než 5 ms) a Pro důležité obchodní informace (latence menší než 3 MS) úrovně služeb.
- Na základě propustnosti směrného plánu předem přidělte velikost dat nebo souborů protokolu, aby se získal očekávaný vstupně-výstupní výkon.

Můžete zvolit výpočetní prostředky a prostředky úložiště v době nasazení a pak ji později změnit, aniž byste museli zavádět prostoje aplikace pomocí [Azure Portal](../database/scale-resources.md):

![Velikost spravované instance](./media/migrate-to-instance-from-sql-server/managed-instance-sizing.png)

Informace o tom, jak vytvořit infrastrukturu virtuální sítě a spravovanou instanci, najdete v tématu [Vytvoření spravované instance](instance-create-quickstart.md).

> [!IMPORTANT]
> Je důležité zachovat virtuální síť VNet a podsíť v souladu s požadavky na [virtuální síť spravované instance](connectivity-architecture-overview.md#network-requirements). Jakékoli nekompatibility vám může zabránit v vytváření nových instancí nebo používání těch, které jste už vytvořili. Přečtěte si další informace o [vytváření nových](virtual-network-subnet-create-arm-template.md) a [konfigurování stávajících](vnet-existing-add-subnet.md) sítí.

## <a name="select-a-migration-method-and-migrate"></a>Výběr metody migrace a migrace

Spravovaná instance SQL cílí na scénáře uživatelů, které vyžadují migraci databáze z místního prostředí nebo implementace databáze virtuálních počítačů Azure. Mají optimální volbu, pokud potřebujete navrátit a posunout back-end aplikací, které pravidelně používají úrovni instance nebo mezidatabázové funkce. Pokud se jedná o váš scénář, můžete celou instanci přesunout do odpovídajícího prostředí v Azure, aniž byste museli své aplikace znovu architektovat.

Chcete-li přesunout instance SQL, je třeba pečlivě naplánovat:

- Migrace všech databází, které je třeba společně umístěného (spuštěné ve stejné instanci).
- Migrace objektů na úrovni instance, na kterých vaše aplikace závisí, včetně přihlášení, přihlašovacích údajů, úloh a operátorů SQL agenta a triggerů na úrovni serveru.

Spravovaná instance SQL je spravovaná služba, která umožňuje delegovat některé běžné aktivity DBA v DBA na platformu, jak jsou integrovány. Proto je třeba, aby některá data na úrovni instance nemusela být migrována, například úlohy údržby pro pravidelné zálohování nebo konfiguraci služby Always On, protože je integrovaná [Vysoká dostupnost](../database/high-availability-sla.md) .

Spravovaná instance SQL podporuje následující možnosti migrace databáze (aktuálně se jedná o jediné podporované metody migrace):

- Azure Database Migration Service – migrace s téměř nulovým výpadkem.
- Nativní `RESTORE DATABASE FROM URL` – používá nativní zálohy z SQL Server a vyžaduje určité výpadky.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Azure Database Migration Service](../../dms/dms-overview.md) je plně spravovaná služba navržená tak, aby umožňovala bezproblémové migrace z více databázových zdrojů do datových platforem Azure s minimálními výpadky. Tato služba zjednodušuje úlohy potřebné k přesunu stávajících databází třetích stran a SQL Server do Azure. Mezi možnosti nasazení ve verzi Public Preview patří databáze v Azure SQL Database a databáze SQL Server na virtuálním počítači Azure. Database Migration Service je doporučená metoda migrace pro vaše podnikové úlohy.

Pokud používáte služba SSIS (SQL Server Integration Services) (SSIS) v SQL Server místně, Database Migration Service ještě nepodporuje migraci katalogu SSIS (SSISDB), který ukládá balíčky SSIS, ale můžete zřídit Azure-SSIS Integration Runtime (IR) v Azure Data Factory, což vytvoří nový SSISDB ve spravované instanci, abyste mohli balíčky znovu nasadit do tohoto prostředí. Viz téma [Create Azure-SSIS IR in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Další informace o tomto scénáři a postupu konfigurace pro Database Migration Service najdete v tématu [migrace místní databáze do spravované instance pomocí Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Nativní obnovení z adresy URL

OBNOVENÍ nativních záloh (souborů. bak) pořízených z SQL Server instance, která je k dispozici na [Azure Storage](https://azure.microsoft.com/services/storage/), je jednou z klíčových funkcí spravované instance SQL, která umožňuje rychlou a jednoduchou migraci offline databáze.

Následující diagram poskytuje podrobný přehled procesu:

![Diagram zobrazuje SQL Server se šipkou, která je označena jako zálohování nebo nahrání, do přetékání adres URL do Azure Storage a druhá šipka s označením obnovení z adresy URL z Azure Storage do spravované instance SQL.](./media/migrate-to-instance-from-sql-server/migration-flow.png)

V následující tabulce najdete další informace týkající se metod, které můžete použít v závislosti na zdrojové SQL Server verzi, kterou používáte:

|Krok|Stroj a verze SQL|Metoda Backup/Restore|
|---|---|---|
|Vložit zálohu do Azure Storage|Před 2012 SP1 CU2|Nahrání souboru. bak přímo do Azure Storage|
||2012 SP1 CU2-2016|Přímá záloha pomocí syntaxe [přihlašovacích údajů](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) zastaralá|
||2016 a vyšší|Přímé zálohování pomocí [s přihlašovacími údaji SAS](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Obnovení z Azure Storage do spravované instance|[OBNOVIT z adresy URL s PŘIHLAŠOVACÍmi údaji SAS](restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - Pokud migrujete databázi chráněnou [transparentní šifrování dat](../database/transparent-data-encryption-tde-overview.md) do spravované instance pomocí možnosti nativní obnovení, musí být před obnovením databáze migrován odpovídající certifikát z místního nebo virtuálního počítače Azure SQL Server. Podrobný postup najdete v tématu [migrace TDE certifikátu do spravované instance](tde-certificate-migrate.md).
> - Obnovení systémových databází se nepodporuje. Chcete-li migrovat objekty na úrovni instance (uložené v databázi Master nebo msdb), doporučujeme je vyskriptovat a spustit skripty T-SQL v cílové instanci.

Rychlý Start ukazující, jak obnovit zálohu databáze do spravované instance pomocí přihlašovacích údajů SAS, najdete v tématu [obnovení ze zálohy do spravované instance](restore-sample-database-quickstart.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>Monitorování aplikací

Po dokončení migrace do spravované instance byste měli sledovat chování aplikace a výkon vašich úloh. Tento proces zahrnuje následující činnosti:

- [Porovnejte výkon úloh spuštěných ve spravované instanci](#compare-performance-with-the-baseline) se [směrným plánem výkonu, který jste vytvořili ve zdrojové SQL Server instanci](#create-a-performance-baseline).
- Nepřetržitě [monitorujte výkon svých úloh](#monitor-performance) a Identifikujte případné problémy a zlepšení.

### <a name="compare-performance-with-the-baseline"></a>Porovnání výkonu se směrným plánem

První aktivita, kterou byste museli provést hned po úspěšné migraci, je porovnávat výkon úloh s výkonem standardních úloh. Cílem této aktivity je potvrdit, že výkon zatížení vaší spravované instance vyhovuje vašim potřebám.

Migrace databáze do spravované instance udržuje nastavení databáze a její původní úroveň kompatibility ve většině případů. Původní nastavení jsou zachována, pokud je to možné, aby se snížilo riziko některých snížení výkonu ve srovnání s vaší zdrojovou SQL Server instancí. Pokud byla před migrací úroveň kompatibility uživatelské databáze 100 nebo vyšší, zůstane stejná i po migraci. Pokud byla úroveň kompatibility uživatelské databáze 90 před migrací, v upgradované databázi je úroveň kompatibility nastavena na 100, což je nejnižší podporovaná úroveň kompatibility ve spravované instanci. Úroveň kompatibility systémových databází je 140. Vzhledem k tomu, že migrace do spravované instance se ve skutečnosti migruje na nejnovější verzi SQL Server databázového stroje, měli byste si uvědomit, že abyste se vyhnuli problémům s výkonem překvapivé, je potřeba, abyste si všimli, že budete potřebovat znovu otestovat výkon

V rámci předpokladu se ujistěte, že jste dokončili následující aktivity:

- Pomocí nastavení ze zdrojové instance SQL Server zarovnejte nastavení ze zdrojové instance, a prozkoumáním různých instancí, databází, nastavení databáze tempdb a konfigurací. Před spuštěním prvního porovnání výkonu se ujistěte, že jste nezměnili nastavení, jako jsou úrovně kompatibility nebo šifrování, nebo přijměte riziko, že některé nové funkce, které jste povolili, mohou ovlivnit některé dotazy. Chcete-li snížit rizika migrace, změňte úroveň kompatibility databáze pouze po sledování výkonu.
- Implementujte [pokyny k osvědčeným postupům úložiště pro pro obecné účely](https://techcommunity.microsoft.com), jako je třeba předběžně přidělit velikost souborů, abyste získali lepší výkon.
- Přečtěte si o [klíčových rozdílech v prostředích, které by mohly způsobit rozdíly v výkonu mezi spravovanou instancí a SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/), a Identifikujte rizika, která mohou ovlivnit výkon.
- Ujistěte se, že jste zachovali povolené úložiště dotazů a automatické ladění na spravované instanci. Tyto funkce umožňují měřit výkon úloh a automaticky opravovat potenciální problémy s výkonem. Naučte se používat úložiště dotazů jako optimální nástroj pro získání informací o výkonu úloh před a po změně úrovně kompatibility databáze, jak je vysvětleno v [části zachování stability výkonu během upgradu na novější verzi SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Po přípravě prostředí, které je co nejvíc porovnatelné s vaším místním prostředím, můžete začít spouštět úlohy a měřit výkon. Proces měření by měl zahrnovat stejné parametry, které jste naměřeni [při vytváření směrného plánu pro úlohy na zdrojové SQL Server instanci](#create-a-performance-baseline).
V důsledku toho byste měli porovnat parametry výkonu se směrným plánem a určit kritické rozdíly.

> [!NOTE]
> V mnoha případech nebudete moci získat přesně vyhovující výkon na spravované instanci a SQL Server. Spravovaná instance Azure SQL je SQL Server databázový stroj, ale konfigurace infrastruktury a vysoké dostupnosti ve spravované instanci může způsobit určité rozdíly. Můžete očekávat, že některé dotazy budou rychlejší, zatímco jiné můžou být pomalejší. Cílem porovnání je ověřit, zda výkon úlohy ve spravované instanci odpovídá výkonu na SQL Server (v průměru) a identifikovat všechny kritické dotazy s výkonem, které neodpovídají původnímu výkonu.

Výsledek porovnání výkonu může být následující:

- Výkon úloh na spravované instanci je zarovnán nebo lepší, než je výkon úloh na SQL Server. V tomto případě jste úspěšně potvrdili, že migrace proběhla úspěšně.
- Většina parametrů výkonu a dotazy v práci s úlohou funguje s některými výjimkami, které mají snížený výkon. V takovém případě byste museli určit rozdíly a jejich důležitost. Pokud existují některé důležité dotazy s sníženým výkonem, měli byste prozkoumat, zda se změnily podkladové plány SQL, nebo že dotazy jsou v některých omezeních prostředků. Omezení rizik v tomto případě by mohlo být použití některých doporučení pro kritické dotazy (například změněné úrovně kompatibility, starší mohutnost estimator), a to buď přímo, nebo pomocí vodítek plánů, opětovného sestavování nebo vytváření statistik a indexů, které mohou mít vliv na plány.
- Většina dotazů je ve srovnání se zdrojovou instancí SQL Server pomalejší na spravované instanci. V takovém případě se pokuste identifikovat hlavní příčiny rozdílů, jako je například omezení [počtu prostředků](resource-limits.md#service-tier-characteristics) , jako jsou limity v/v, limit paměti, limit četnosti protokolu instance atd. Pokud neexistují žádná omezení prostředků, která by mohla způsobit rozdíl, zkuste změnit úroveň kompatibility databáze nebo změnit nastavení databáze, jako je například odhad mohutnosti starší verze, a spusťte test znovu. Přečtěte si doporučení poskytovaná zobrazeními spravované instance nebo úložiště dotazů a Identifikujte dotazy, které navrácený výkon.

> [!IMPORTANT]
> Spravovaná instance Azure SQL má vestavěnou funkci automatického naplánování, která je ve výchozím nastavení povolená. Tato funkce zajišťuje, že dotazy, které pracovaly správně v rámci vložení, nebudou v budoucnu degradované. Ujistěte se, že je tato funkce povolená a že jste před změnou nových nastavení spustili úlohu dostatečně dlouho se starým nastavením, aby mohla spravovaná instance získat informace o výkonu a plánech směrného plánu.

Proveďte změnu parametrů nebo upgradujte úrovně služeb tak, aby se sblíženy s optimální konfigurací, dokud nezískáte výkon úloh, který vyhovuje vašim potřebám.

### <a name="monitor-performance"></a>Monitorování výkonu

SQL Managed instance poskytuje mnoho pokročilých nástrojů pro monitorování a odstraňování potíží a je vhodné je použít k monitorování výkonu vaší instance. Některé parametry, které byste měli monitorovat, jsou:

- Využití CPU u instance k určení, jestli počet virtuální jádra, který jste zřídili, je správná shoda pro vaše zatížení.
- Očekávané životního cyklu stránky na spravované instanci, abyste zjistili, [jestli potřebujete další paměť](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Statistiky, jako je `INSTANCE_LOG_GOVERNOR` nebo `PAGEIOLATCH` , budou vědět, jestli máte problémy v/v úložiště, zejména na úrovni pro obecné účely, kde možná budete muset předem přidělit soubory, abyste získali lepší vstupně-výstupní operace.

## <a name="leverage-advanced-paas-features"></a>Využití pokročilých funkcí PaaS

Jakmile budete na plně spravované platformě a ověříte, že výkon úloh odpovídá vašemu SQL Server výkonu úloh, využijte výhody, které jsou k dispozici automaticky jako součást služby.

I v případě, že během migrace neprovedete změny ve spravované instanci, je velmi pravděpodobné, že byste zapnuli některé nové funkce, zatímco pracujete s vaší instancí, abyste mohli využít nejnovější vylepšení databázového stroje. Některé změny se aktivují až po [změně úrovně kompatibility databáze](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).

Například nemusíte vytvářet zálohy na spravované instanci – služba provádí zálohování automaticky. Už si nemusíte dělat starosti s plánováním, správou a správou záloh. SQL Managed instance poskytuje možnost obnovení do libovolného bodu v čase v rámci této doby uchování pomocí funkce [Obnovení bodu v čase (PITR)](../database/recovery-using-backups.md#point-in-time-restore). Kromě toho se nemusíte starat o nastavení vysoké dostupnosti, protože je integrovaná [Vysoká dostupnost](../database/high-availability-sla.md) .

Pokud chcete posílit zabezpečení, zvažte použití [Azure Active Directory ověřování](../database/security-overview.md), [auditování](auditing-configure.md), [detekce hrozeb](../database/azure-defender-for-sql.md), [zabezpečení na úrovni řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)a [dynamického maskování dat](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking).

Kromě pokročilých funkcí správy a zabezpečení poskytuje spravovaná instance sadu pokročilých nástrojů, které vám pomůžou [monitorovat a ladit vaše úlohy](../database/monitor-tune-overview.md). [Azure SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) vám umožní monitorovat velkou sadu spravovaných instancí a centralizovat monitorování velkého počtu instancí a databází. [Automatické ladění](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) ve spravovaných instancích nepřetržitě monitoruje výkon statistik spuštění plánu SQL a automaticky opravuje zjištěné problémy s výkonem.

## <a name="next-steps"></a>Další kroky

- Informace o spravované instanci Azure SQL najdete v tématu [co je Azure SQL Managed instance?](sql-managed-instance-paas-overview.md).
- Kurz, který obsahuje obnovení ze zálohy, najdete v tématu [Vytvoření spravované instance](instance-create-quickstart.md).
- Kurz znázorňující migraci pomocí Database Migration Service najdete v tématu [migrace místní databáze do spravované instance Azure SQL pomocí Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).  
