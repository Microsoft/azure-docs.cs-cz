---
title: Azure SQL Database omezení prostředků | Microsoft Docs
description: Tento článek poskytuje přehled Azure SQL Databasech omezení prostředků pro izolované databáze a elastické fondy. Poskytuje také informace o tom, co se stane, když dojde k dosažení nebo překročení těchto omezení prostředků.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 11/19/2019
ms.openlocfilehash: 40b277f0b1bfb3501bb246e555d46db5e1ee9f95
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279305"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>SQL Database omezení prostředků a zásad správného řízení prostředků

Tento článek poskytuje přehled SQL Databasech omezení prostředků pro SQL Database Server, který spravuje izolované databáze a elastické fondy. Poskytuje informace o tom, co se stane, když dojde k dosažení nebo překročení těchto omezení prostředků, a popisuje mechanismy řízení prostředků, které se používají k vykonání těchto limitů.

> [!NOTE]
> Omezení pro spravovaná instance najdete v tématu [SQL Database omezení prostředků pro spravované instance](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Maximum omezení prostředků

| Prostředek | Omezení |
| :--- | :--- |
| Databáze na server | 5000 |
| Výchozí počet serverů na předplatné v libovolné oblasti | 20 |
| Maximální počet serverů na předplatné v libovolné oblasti | 200 |  
| Kvóta DTU/eDTU na server | 54,000 |  
| vCore kvóta na server/instanci | 540 |
| Maximální počet fondů na server | Omezeno počtem DTU nebo virtuální jádra. Pokud je například každý fond 1000 DTU, může server podporovat fondy 54.|
|||

> [!NOTE]
> Pokud chcete získat větší kvótu DTU/eDTU, vCore kvótu nebo více serverů než výchozí množství, můžete odeslat novou žádost o podporu v Azure Portal pro předplatné s typem problému "kvóta". Kvóta DTU/eDTU a omezení databáze na server omezuje počet elastických fondů na jeden server.

> [!IMPORTANT]
> Vzhledem k tomu, že počet databází se blíží limitu pro SQL Database Server, může dojít k následujícím akcím:
>
> - Zvýšení latence při spouštění dotazů na hlavní databázi.  Patří sem zobrazení statistik využití prostředků, jako je například sys. resource_stats.
> - Zvýšení latence při operacích správy a v pohledech na portál pro vykreslování, které zahrnují vytváření výčtu databází na serveru.

### <a name="storage-size"></a>Velikost úložiště

V případě velikostí úložiště prostředků pro izolované databáze se v omezeních velikosti úložiště na cenové úrovni podívejte na omezení [prostředků na základě DTU](sql-database-dtu-resource-limits-single-databases.md) nebo na [Vcore omezení prostředků](sql-database-vcore-resource-limits-single-databases.md) .

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Co se stane, když se dosáhnou omezení prostředků databáze

### <a name="compute-dtus-and-edtus--vcores"></a>COMPUTE (DTU a eDTU/virtuální jádra)

V případě, že využití výpočetních databází (měřené od DTU a eDTU nebo virtuální jádra) bude vysoké, zvýšení latence dotazu a dotazy mohou dokonce i vyprší časový limit. Za těchto podmínek můžou být dotazy ve službě zařazené do fronty a k dispozici jsou prostředky pro spuštění, protože prostředky budou bezplatné.
Pokud se setkáte s vysokým využitím výpočetních prostředků, zahrnují možnosti zmírnění:

- Zvýšením výpočetní velikosti databáze nebo elastického fondu poskytnete databázi s více výpočetními prostředky. Viz téma [škálování prostředků jedné databáze](sql-database-single-database-scale.md) a [škálování prostředků elastického fondu](sql-database-elastic-pool-scale.md).
- Optimalizace dotazů pro snížení využití prostředků každého dotazu. Další informace najdete v tématu [ladění a hinty dotazů](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

Když využité místo v databázi dosáhne limitu maximální velikosti, vkládání a aktualizace databáze, které zvyšují velikost dat, selžou a klienti obdrží [chybovou zprávu](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md). Příkazy SELECT a DELETE budou i nadále úspěšné.

Při zjištění vysokého využití místa mají tyto možnosti omezení:

- Zvýšení maximální velikosti databáze nebo elastického fondu nebo Přidání úložiště. Viz téma [škálování prostředků jedné databáze](sql-database-single-database-scale.md) a [škálování prostředků elastického fondu](sql-database-elastic-pool-scale.md).
- Pokud se databáze nachází v elastickém fondu, pak je možné databázi přesunout mimo fond, aby její prostor úložiště nebyl sdílen s ostatními databázemi.
- Zmenšete databázi pro uvolnění nevyužitého místa. Další informace najdete v tématu [Správa místa v souboru v Azure SQL Database](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Relace a pracovní procesy (požadavky)

Maximální počet relací a pracovních procesů závisí na úrovni služby a velikosti výpočtů (DTU/eDTU nebo virtuální jádra. Nové žádosti jsou odmítnuty, když jsou dosažena omezení relace nebo pracovního procesu, a klienti obdrží chybovou zprávu. I když může být počet dostupných připojení řízen aplikací, počet souběžných pracovních procesů je často těžší odhadnout a řídit. To platí hlavně během období špičky načítání při dosažení limitů prostředků databáze a pracovníci se budou moct dodávat v důsledku delšího spouštění dotazů, velkých blokujících řetězců nebo nadměrného paralelismu dotazů.

Pokud se setkáte s vysokým využitím relace nebo pracovního procesu, zahrnují možnosti zmírňování:

- Zvýšení úrovně služby nebo výpočetní velikosti databáze nebo elastického fondu. Viz téma [škálování prostředků jedné databáze](sql-database-single-database-scale.md) a [škálování prostředků elastického fondu](sql-database-elastic-pool-scale.md).
- Optimalizujte dotazy, aby se snížilo využití prostředků každého dotazu, pokud příčinou zvýšení využití pracovního procesu je spor pro výpočetní prostředky. Další informace najdete v tématu [ladění a hinty dotazů](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="resource-governance"></a>Zásady správného řízení prostředků

Aby bylo možné vyhodnotit omezení prostředků, Azure SQL Database využívá implementaci zásad správného řízení prostředků, která je založená na SQL Server [Správce prostředků](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor), upravená a rozšířená ke spuštění služby SQL Server Database Service v Azure. V každé instanci SQL Server ve službě existuje více [fondů zdrojů](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) a [skupin úloh](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)s omezeními prostředků nastavenými na úrovni fondu a skupin, aby poskytovaly [vyváženou databázi jako službu](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). Úlohy uživatelů a interní úlohy jsou klasifikovány do samostatných fondů zdrojů a skupin úloh. Uživatelské zatížení primárních a čitelných sekundárních replik, včetně geografických replik, je klasifikované do fondu prostředků `SloSharedPool1` a `UserPrimaryGroup.DBId[N]` skupiny úloh, kde `N` představuje hodnotu ID databáze. Kromě toho existuje více fondů zdrojů a skupin úloh pro různé interní úlohy.

Kromě použití Správce zdrojů k řízení prostředků v rámci procesu SQL Server Azure SQL Database používá také [objekty úlohy](https://docs.microsoft.com/windows/win32/procthread/job-objects) Windows pro řízení prostředků na úrovni procesu a [souborový server Windows Správce prostředků (Správce prostředků)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) pro správu kvót úložiště.

Zásady správného řízení prostředků Azure SQL Database jsou hierarchické. Od shora dolů se limity vynutily na úrovni operačního systému a na úrovni svazku úložiště pomocí mechanismů zásad správného řízení prostředků operačního systému a správce prostředků, pak na úrovni fondu zdrojů pomocí Správce zdrojů a pak na úrovni skupiny úloh pomocí Správce zdrojů. Omezení zásad správného řízení prostředků pro aktuální databázi nebo elastický fond jsou v zobrazení [Sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) . 

### <a name="data-io-governance"></a>Řízení v/v data

Řízení v/v data je proces v Azure SQL Database slouží k omezení fyzické vstupně-výstupní operace čtení i zápisu proti datovým souborům databáze. Omezení IOPS se nastavují pro každou úroveň služby, aby se minimalizoval účinek "hlučného souseda", aby se zajistilo přidělení prostředků v rámci víceklientské služby a bylo možné zůstat v rámci schopností základního hardwaru a úložiště.

U izolovaných databází se limity skupin úloh použijí pro všechny vstupně-výstupní operace v databázi, zatímco limity fondu zdrojů se vztahují na veškerou vstupně-výstupní operace úložiště pro všechny databáze na stejné instanci SQL Server, včetně databáze `tempdb`. U elastických fondů se limity skupin úloh vztahují na každou databázi ve fondu, zatímco limit fondu zdrojů platí pro celý elastický fond, včetně databáze `tempdb`, která je sdílená mezi všemi databázemi ve fondu. Obecně platí, že omezení fondu zdrojů nemusí být pro úlohy dosažitelná v databázi (buď samostatně nebo ve fondu), protože limity skupin úloh jsou nižší než limity fondu zdrojů a omezují IOPS/propustnost dřív. Limity fondu ale můžou být dostupné v rámci kombinovaného zatížení s více databázemi na stejné instanci SQL Server.

Pokud dotaz například vygeneruje 1000 IOPS bez zásad správného řízení prostředků v/v, ale hodnota maximálního počtu IOPS pro skupinu úloh je nastavena na 900 IOPS, dotaz nebude moci vygenerovat více než 900 IOPS. Pokud je ale limit počtu IOPS pro fond zdrojů nastavený na 1500 IOPS a celkový počet vstupně-výstupních operací ze všech skupin úloh přidružených ke fondu zdrojů překračuje 1500 IOPS, pak se v/v v případě stejného dotazu může snížit pod limit pracovní skupiny 900 IOPS.

Hodnoty vstupně-výstupních operací a minimální/maximální propustnosti vrácené zobrazením [Sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) fungují jako limity a velká a nikoli jako záruky. Zásady správného řízení prostředků navíc nezaručují žádnou konkrétní latenci úložiště. Nejlepší dosažitelná latence, IOPS a propustnost pro danou úlohu uživatele závisí nejen na omezeních zásad správného řízení prostředků v/v, ale také na kombinaci používaných velikostí v/v a na funkcích základního úložiště. SQL Server používá IOs, které mají různou velikost mezi 512 KB a 4 MB. Pro účely vynucení limitu IOPS jsou všechny vstupně-výstupní operace zaúčtovány bez ohledu na její velikost, s výjimkou databází s datovými soubory v Azure Storage. V takovém případě se IOs větší než 256 KB zobrazí jako více než 256 KB IOs, aby se zarovnaly Azure Storage v/v monitorování účtů.

Pro databáze Basic, Standard a Pro obecné účely, které používají datové soubory v Azure Storage, nemusí být hodnota `primary_group_max_io` dosažitelná, pokud databáze nemá k dispozici dostatek datových souborů k kumulativnímu poskytnutí tohoto počtu IOPS, nebo pokud nejsou data rovnoměrně rozdělena mezi soubory nebo pokud úroveň výkonu základních objektů BLOB omezuje počet vstupně-výstupních operací za sekundu v rámci limitu řízení prostředků. Podobně s malým protokolem IOs generovaným častým potvrzováním transakcí může být `primary_max_log_rate` hodnota dosažitelná úlohou kvůli limitu IOPS v základní službě Azure Storage BLOB.

Hodnoty využití prostředků, například `avg_data_io_percent` a `avg_log_write_percent`, hlášené v zobrazeních [Sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [Sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)a [Sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) , se počítají jako procentuální hodnoty maximálního řízení prostředků. Proto pokud jiné faktory než zásady správného řízení prostředků omezují počet vstupně-výstupních operací za sekundu, můžete zobrazit sloučení vstupně-výstupních operací za sekundu a latence při zvýšení zatížení, a to i v případě, že hlášené využití prostředků zůstává nižší než 100%. Pokud chcete zobrazit vstupně-výstupní operace čtení a zápisu, propustnost a latence na databázový soubor, použijte funkci [Sys. dm_io_virtual_file_stats ()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) . Tato funkce obchází všechny vstupně-výstupní operace na databázi, včetně vstupně-výstupních operací na pozadí, které nejsou k dis`avg_data_io_percent`, ale využívají IOPS a propustnost základního úložiště a můžou mít vliv na zjištěnou latenci úložiště.

### <a name="transaction-log-rate-governance"></a>Řízení sazeb transakčního protokolu

Řízení sazeb za transakční protokol je proces v Azure SQL Database, který slouží k omezení vysoké sazby příjmu pro úlohy, jako jsou hromadné vložení, výběr a sestavení indexů. Tato omezení jsou sledována a vyhodnocena na základě druhé úrovně na míru četnosti vytváření záznamů protokolu, což omezuje propustnost bez ohledu na to, kolik IOs může být pro datové soubory vystaveno.  Frekvence generování protokolu transakcí aktuálně škáluje lineárně až do bodu, který je závislý na hardwaru, přičemž maximální povolený počet protokolů je 96 MB/s s modelem nákupu vCore. 

> [!NOTE]
> Skutečné fyzické soubory protokolu IOs na transakce se neřídí ani nepodporují.

Sazby za protokol jsou nastavené tak, aby je bylo možné dosáhnout a udržovat v nejrůznějších scénářích, zatímco celkový systém může udržovat svou funkčnost s minimalizovaným dopadem na uživatelské zatížení. Zásady správného řízení protokolu zajistí, že zálohy protokolu transakcí zůstávají v rámci publikované možnosti obnovování SLA.  Tento zásady správného řízení taky brání nadměrnému počtu nevyřízených položek na sekundárních replikách.

Po vygenerování záznamů protokolu se každá operace vyhodnotí a vyhodnotí, jestli by se měla zpozdit, aby se zachovala maximální požadovaná rychlost protokolu (MB/s za sekundu). Zpoždění se přidávají, když se záznamy protokolu vyprázdní do úložiště, místo toho se uplatní zásady správného řízení protokolu během generování rychlosti protokolu.

Skutečné sazby za generování protokolů, které jsou uvedené v době běhu, můžou být ovlivněné pomocí mechanismů zpětné vazby, ale dočasné snížení přípustných sazeb protokolu, aby systém mohl stabilizovat. Správa místa souboru protokolu, což nezpůsobuje nevyužité podmínky místa v protokolu a mechanismy replikace skupin dostupnosti dočasně snižují celkový počet systémových omezení.

Tvarování provozu správce míry přenosu dat se prochází prostřednictvím následujících typů čekání (zveřejněné v [Sys. dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV):

| Typ čekání | Poznámky: |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Omezení databáze |
| POOL_LOG_RATE_GOVERNOR | Omezení fondu |
| INSTANCE_LOG_RATE_GOVERNOR | Omezení na úrovni instance |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Řízení zpětné vazby, fyzická replikace skupiny dostupnosti v Premium/Pro důležité obchodní informace Neudržovat |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Řízení zpětné vazby, omezování sazeb pro zamezení nedostatku místa v protokolu |
|||

Pokud se setkáte s limitem přenosové rychlosti, která brání požadovanou škálovatelnost, vezměte v úvahu následující možnosti:
- Pokud chcete získat maximální rychlost protokolu 96 MB/s, můžete škálovat až na vyšší úroveň služby. 
- Pokud jsou data načítána přechodná, například pracovní data v procesu ETL, je možné je načíst do databáze tempdb (která je popsána v minimálním protokolu). 
- V případě analytických scénářů se načtěte do clusterované tabulky columnstore. Tím se sníží požadovaná rychlost protokolu z důvodu komprimace. Tato technika zvýší využití procesoru a vztahuje se pouze na datové sady, které využívají clusterované indexy columnstore. 

## <a name="next-steps"></a>Další kroky

- Informace o obecných omezeních Azure najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-subscription-service-limits.md).
- Informace o DTU a eDTU najdete v tématu [DTU a eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Informace o omezení velikosti databáze tempdb najdete [v tématu databáze tempdb v Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
