---
title: 'SQL Server Azure SQL Database: Přehled migrace'
description: Seznamte se s nástroji a možnostmi, které jsou k dispozici pro migraci SQL Serverch databází do Azure SQL Database.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: f515725ea0f306546039b92d953254a093b15b8b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065153"
---
# <a name="migration-overview-sql-server-to-azure-sql-database"></a>Přehled migrace: SQL Server pro Azure SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Seznamte se s možnostmi a důležitými informacemi pro migraci SQL Serverch databází do Azure SQL Database. 

Můžete migrovat SQL Server databáze spuštěné místně nebo na: 

- SQL Server v Azure Virtual Machines.  
- Cloud COMPUTE AWS (EC2) pro elastické výpočetní prostředky (). Amazon Web Services
- Služba AWS relačních databází (RDS).
- Výpočetní modul v Google Cloud Platform (GCP).  
- Cloudový SQL pro SQL Server v GCP. 

Další příručky k migraci najdete v tématu [migrace databáze](https://docs.microsoft.com/data-migration). 

## <a name="overview"></a>Přehled

[Azure SQL Database](../../database/sql-database-paas-overview.md) je doporučenou možností cíle pro SQL Server úlohy, které vyžadují plně spravovanou platformu jako službu (PaaS). SQL Database zpracovává většinu funkcí správy databáze. Má také integrované možnosti vysoké dostupnosti, inteligentního zpracování dotazů, škálovatelnosti a výkonu pro různé typy aplikací. 

SQL Database poskytuje flexibilitu s více [modely nasazení](../../database/sql-database-paas-overview.md#deployment-models) a [úrovněmi služeb](../../database/service-tiers-vcore.md#service-tiers) , které nabízí pro různé typy aplikací nebo úloh.

Jednou z klíčových výhod migrace na SQL Database je, že aplikaci můžete modernizovat pomocí možností PaaS. Pak můžete eliminovat veškerou závislost na technických součástech, které jsou vymezeny na úrovni instance, jako jsou například úlohy agenta SQL.

Můžete také ušetřit náklady pomocí [Zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) SQL Server k migraci SQL Server místních licencí na Azure SQL Database. Tato možnost je k dispozici, pokud zvolíte [nákupní model založený na Vcore](../../database/service-tiers-vcore.md).

## <a name="considerations"></a>Požadavky 

Mezi klíčové faktory, které je potřeba vzít v úvahu při hodnocení možností migrace, patří: 

- Počet serverů a databází
- Velikost databází
- Přijatelné provozní výpadky během migrace 

Možnosti migrace uvedené v této příručce mají tyto faktory v úvahu. Při migraci logických dat na Azure SQL Database může čas migrace záviset na počtu objektů v databázi a na velikosti databáze. 

K dispozici jsou nástroje pro různé úlohy a předvolby uživatele. Některé nástroje lze použít k rychlé migraci izolované databáze prostřednictvím nástroje založeného na uživatelském rozhraní. Další nástroje mohou automatizovat migraci více databází, aby bylo možné zpracovávat migrace ve velkém měřítku. 

## <a name="choose-an-appropriate-target"></a>Zvolit vhodný cíl

Vezměte v úvahu obecné pokyny, které vám pomohou vybrat správný model nasazení a vrstvu služby Azure SQL Database. Během nasazování můžete zvolit výpočetní prostředky a prostředky úložiště a pak [je později změnit pomocí Azure Portal](../../database/scale-resources.md) bez výpadků aplikace.

**Modely nasazení**: Seznámení s úlohou vaší aplikace a vzorem využití, které se rozhodují mezi jedinou databází nebo elastickým fondem. 

- [Jediná databáze](../../database/single-database-overview.md) představuje plně spravovanou databázi, která je vhodná pro většinu moderních cloudových aplikací a mikroslužeb.
- [Elastický fond](../../database/elastic-pool-overview.md) je kolekce izolovaných databází se sdílenou sadou prostředků, jako je například procesor nebo paměť. Je vhodný pro kombinování databází ve fondu s předvídatelnými vzory využití, které můžou efektivně sdílet stejnou sadu prostředků.

**Nákupy modelů**: Vyberte si mezi modely Vcore, Database Transaction Unit (DTU) nebo bez serveru. 

- [Model Vcore](../../database/service-tiers-vcore.md) vám umožňuje vybrat počet virtuální jádra pro Azure SQL Database, takže při překladu z místní SQL Server je nejjednodušší. Toto je jediná možnost, která podporuje ukládání licenčních nákladů s [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). 
- [Model DTU](../../database/service-tiers-dtu.md) vyabstrakce základní výpočetní prostředky, paměť a vstupně-výstupní prostředky pro zajištění blendované DTU. 
- [Model bez serveru](../../database/serverless-tier-overview.md) je pro úlohy, které vyžadují automatické škálování na vyžádání s využitím výpočetních prostředků, které se účtují za sekundu. Výpočetní vrstva bez serveru automaticky pozastaví databáze během neaktivních období (kde se účtuje jenom úložiště). Automaticky obnoví databáze při návratu aktivity. 

**Úrovně služeb**: vyberte tři úrovně služeb navržené pro různé typy aplikací.

- [Pro obecné účely/standardní úroveň služeb](../../database/service-tier-general-purpose.md) nabízí vyváženou možnost rozpočtu s výpočetními a úložnými prostředky vhodnými k dodávání aplikací v prostřední a nižší úrovni. Redundance je integrovaná na vrstvě úložiště, která umožňuje zotavení po selhání. Je určený pro většinu databázových úloh. 
- [Úroveň služby pro důležité obchodní informace/Premium](../../database/service-tier-business-critical.md) je určena pro vysoce vrstvené aplikace, které vyžadují vysoké sazby za transakce, vstupně-výstupní operace s nízkou latencí a vysokou úroveň odolnosti. Sekundární repliky jsou k dispozici pro převzetí služeb při selhání a přesměrování zatížení pro čtení.
- [Úroveň služby s škálovatelným škálováním](../../database/service-tier-hyperscale.md) je pro databáze, které mají rostoucí objemy dat, a v případě velikosti databáze je potřeba automaticky škálovat až 100 TB. Je navržen pro velmi velké databáze. 

> [!IMPORTANT]
> [Rychlost transakčního protokolu se řídí](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) v Azure SQL Database k omezení vysoké sazby pro přijímání. Během migrace možná budete muset škálovat prostředky cílové databáze (virtuální jádra nebo DTU), aby se usnadnil tlak na procesor nebo propustnost. Vyberte vhodnou velikost cílové databáze, ale podle potřeby Naplánujte škálování prostředků v migraci. 


### <a name="sql-server-vm-alternative"></a>Alternativní virtuální počítač SQL Server

Vaše firma může mít požadavky, které zajistí [SQL Server v Azure Virtual Machines](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) vhodnějším cílem než Azure SQL Database. 

Pokud se pro vaši firmu platí jedna z následujících podmínek, zvažte místo toho přechod na SQL Server virtuální počítač (VM): 

- Vyžadujete přímý přístup k operačnímu systému nebo systému souborů, například k instalaci agentů třetích stran nebo vlastních agentů na stejný virtuální počítač s SQL Server. 
- Máte přísnou závislost na funkcích, které pořád nejsou podporované, jako jsou FileStream/FileTable, základna a transakce mezi instancemi. 
- Musíte zůstat na konkrétní verzi SQL Server (například 2012). 
- Vaše požadavky na výpočetní výkon jsou mnohem nižší než nabídky spravované instance (například jeden vCore) a konsolidace databáze není přijatelnou možností. 


## <a name="migration-tools"></a>Nástroje pro migraci 

Doporučujeme následující nástroje pro migraci: 

|Technologie | Popis|
|---------|---------|
| [Azure Migrate](../../../migrate/how-to-create-azure-sql-assessment.md) | Tato služba Azure vám pomůže zjistit a posoudit vaše datové úložiště SQL ve velkém měřítku na VMware. Poskytuje doporučení pro nasazení SQL Azure, velikost cíle a měsíční odhady. | 
|[Data Migration Assistant](/sql/dma/dma-migrateonpremsqltosqldb)|Tento desktopový nástroj od Microsoftu poskytuje bezproblémový odhad SQL Server a migrace jedné databáze do Azure SQL Database (schéma i data). </br></br>Nástroj se dá nainstalovat na místní server nebo do místního počítače, který má připojení ke svým zdrojovým databázím. Proces migrace je logický pohyb dat mezi objekty ve zdrojové a cílové databázi.|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-azure-sql.md)|Tato služba Azure může migrovat SQL Server databáze Azure SQL Database prostřednictvím Azure Portal nebo automaticky prostřednictvím PowerShellu. Database Migration Service vyžaduje, abyste během zřizování vybrali upřednostňovanou virtuální síť Azure, abyste měli jistotu, že máte připojení ke svým zdrojovým databázím SQL Server. Můžete migrovat jednotlivé databáze nebo škálování. |
| | |


V následující tabulce jsou uvedeny alternativní nástroje pro migraci: 

|Technologie |Popis  |
|---------|---------|
|[Transakční replikace](../../database/replication-to-sql-database.md)|Replikujte data ze zdrojových databázových tabulek SQL Server do Azure SQL Database tím, že poskytnete možnost migrace typu předplatitele vydavatele a zachováte konzistenci transakcí. Přírůstkové změny dat se šíří u odběratelů, kteří se k nim vyskytují na vydavatelích.|
|[Import služby export/BACPAC](../../database/database-import.md)|[BacPac](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) je soubor Windows s příponou. BacPac, který zapouzdřuje schéma a data databáze. BACPAC můžete použít k exportu dat ze zdroje SQL Server a k importu dat do Azure SQL Database. Soubor BACPAC se dá importovat do nové databáze SQL prostřednictvím Azure Portal. </br></br> Pro účely škálování a výkonu velkých databází nebo velkého počtu databází zvažte použití nástroje příkazového řádku [SqlPackage](../../database/database-import.md#using-sqlpackage) k exportu a importu databází.|
|[Hromadná kopie](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|[Nástroj program pro hromadné kopírování (BCP)](/sql/tools/bcp-utility) kopíruje data z instance SQL Server do datového souboru. Pomocí nástroje exportujte data ze zdroje a importujte datový soubor do cílové databáze SQL. </br></br> U vysokorychlostních operací hromadného kopírování k přesunu dat na Azure SQL Database můžete použít nástroj pro [inteligentní hromadné kopírování](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) k maximalizaci rychlosti přenosu tím, že využijete paralelní kopírování úloh.|
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)|[Aktivita kopírování](../../../data-factory/copy-activity-overview.md) v Azure Data Factory migruje data ze zdrojových databází SQL Server do Azure SQL Database pomocí integrovaných konektorů a [prostředí Integration runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> Data Factory podporuje široké spektrum [konektorů](../../../data-factory/connector-overview.md) pro přesun dat ze zdrojů SQL Server do Azure SQL Database.|
|[Synchronizace dat SQL](../../database/sql-data-sync-data-sql-server-sql-database.md)|Synchronizace dat SQL je služba založená na Azure SQL Database, která umožňuje obousměrnou synchronizaci vybraných dat napříč více databázemi v místním prostředí i v cloudu.</br>Synchronizace dat je užitečná v případech, kdy je potřeba udržovat data v různých databázích ve Azure SQL Database nebo SQL Server.|
| | |

## <a name="compare-migration-options"></a>Porovnat možnosti migrace

Porovnejte možnosti migrace a vyberte cestu, která je vhodná pro vaše obchodní potřeby. 

Následující tabulka porovnává možnosti migrace, které doporučujeme: 

|Možnost migrace  |Kdy je použít  |Požadavky  |
|---------|---------|---------|
|[Data Migration Assistant](/sql/dma/dma-migrateonpremsqltosqldb) | – Migrujte jednotlivé databáze (schéma i data).  </br> – Může během procesu migrace dat podvyhovovat výpadkům. </br> </br> Podporované zdroje: </br> – SQL Server (2005 až 2019) místní nebo Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP COMPUTE SQL Server virtuální počítač | – Aktivita migrace provádí přesun dat mezi databázovými objekty (ze zdroje do cíle), proto doporučujeme, abyste ji spouštěli v době mimo špičku. </br> -Data Migration Assistant oznamuje stav migrace na databázový objekt, včetně počtu migrovaných řádků.  </br> – Pro velké migrace (počet databází nebo velikost databáze) použijte Azure Database Migration Service.|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-azure-sql.md)| – Migrujte jednotlivé databáze nebo škálování. </br> – Může během procesu migrace pojmout výpadky. </br> </br> Podporované zdroje: </br> – SQL Server (2005 až 2019) místní nebo Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP COMPUTE SQL Server virtuální počítač | – Migrace ve velkém měřítku může být automatizovaná přes [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md). </br> -Doba k dokončení migrace závisí na velikosti databáze a počtu objektů v databázi. </br> – Vyžaduje, aby byla zdrojová databáze nastavena jen pro čtení. |
| | | |

Následující tabulka porovnává alternativní možnosti migrace: 

|Metoda nebo technologie |Kdy je použít    |Požadavky  |
|---------|---------|---------|
|[Transakční replikace](../../database/replication-to-sql-database.md)| – Migrace provede průběžně publikovanými změnami ze zdrojových databázových tabulek do cílových SQL Database tabulek. </br> – Proveďte úplné nebo částečné migrace databází z vybraných tabulek (podmnožina databáze).  </br> </br> Podporované zdroje: </br> - [SQL Server (2016 až 2019) s některými omezeními](/sql/relational-databases/replication/replication-backward-compatibility) </br> – AWS EC2  </br> – GCP COMPUTE SQL Server virtuální počítač  | – Instalace je poměrně složitá v porovnání s dalšími možnostmi migrace.   </br> – Poskytuje možnost průběžné replikace pro migraci dat (aniž byste museli přenášet databáze do režimu offline).  </br> – Transakční replikace má při nastavování vydavatele na zdrojové instanci SQL Server omezení. Další informace najdete v tématu [omezení pro publikování objektů](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) . </br>– Je možné [monitorovat aktivitu replikace](/sql/relational-databases/replication/monitor/monitoring-replication).    |
|[Import služby export/BACPAC](../../database/database-import.md)| – Migrujte jednotlivé databáze obchodních aplikací. </br>– Hodí se pro menší databáze.  </br>  – Nevyžaduje samostatnou službu nebo nástroj pro migraci. </br> </br> Podporované zdroje: </br> – SQL Server (2005 až 2019) místní nebo Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP COMPUTE SQL Server virtuální počítač  |  – Vyžaduje prostoje, protože data musí být exportována ve zdroji a importována v cílovém umístění.   </br> – Formáty souborů a datové typy používané při exportu nebo importu musí být konzistentní se schématy tabulek, aby nedošlo k chybám při zkracování nebo neshodě typů dat.  </br> – Doba potřebná k exportu databáze s velkým počtem objektů může být výrazně vyšší.       |
|[Hromadná kopie](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| – Proveďte úplné nebo částečné migrace dat. </br> – Může vyhovovat výpadkům. </br> </br> Podporované zdroje: </br> – SQL Server (2005 až 2019) místní nebo Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP COMPUTE SQL Server virtuální počítač   | – Vyžaduje prostoje při exportu dat ze zdroje a importu do cíle. </br> – Formáty souborů a datové typy používané při exportu nebo importu musí být konzistentní se schématy tabulek. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)| – Migrujte nebo Transformujte data ze zdrojových SQL Server databází. </br> – Sloučení dat z více zdrojů dat do Azure SQL Database je typicky pro úlohy business intelligence (BI).  |  – Vyžaduje vytvoření kanálů přesunu dat v Data Factory k přesunu dat ze zdroje do cíle.   </br> - [Náklady](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) jsou důležitým aspektem a jsou založené na faktorech, jako jsou triggery kanálu, spuštění aktivit a doba trvání přesunu dat. |
|[Synchronizace dat SQL](../../database/sql-data-sync-data-sql-server-sql-database.md)| -Synchronizovat data mezi zdrojovou a cílovou databází.</br> – Vhodné ke spuštění průběžné synchronizace mezi Azure SQL Database a místními SQL Server v obousměrném toku. | -Azure SQL Database musí být databáze centra pro synchronizaci s místní databází SQL Server jako členský databáze.</br> – Ve srovnání s transakční replikací podporuje Synchronizace dat SQL obousměrnou synchronizaci dat mezi místními a Azure SQL Database. </br> – Může mít vyšší dopad na výkon v závislosti na zatížení.|
| | | |

## <a name="feature-interoperability"></a>Interoperabilita funkcí 

Při migraci úloh, které spoléhají na jiné funkce SQL Server, jsou potřeba další informace.

### <a name="sql-server-integration-services"></a>Služba SSIS (SQL Server Integration Services)
Migrujte balíčky služba SSIS (SQL Server Integration Services) (SSIS) do Azure tím, že je znovu nasadíte do prostředí Azure-SSIS runtime v [Azure Data Factory](../../../data-factory/introduction.md). Azure Data Factory [podporuje migraci balíčků SSIS](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) tím, že poskytuje modul runtime sestavený tak, aby SPOUŠTĚL balíčky SSIS v Azure. Alternativně můžete znovu napsat logiku SSIS ETL (extrakce, transformace, načítání) nativně v Azure Data Factory pomocí [datových toků](../../../data-factory/concepts-data-flow-overview.md).


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
Migrace sestav SQL Server Reporting Services (SSRS) na stránkované sestavy v Power BI. Použijte [Nástroj pro migraci RDL](https://github.com/microsoft/RdlMigration) , který vám umožní připravit a migrovat sestavy. Společnost Microsoft vyvinula tento nástroj, který zákazníkům umožňuje migrovat sestavy Report Definition Language (RDL) ze svých serverů SSRS na Power BI. Je k dispozici na GitHubu a dokumentuje podrobný návod k migračnímu scénáři. 

### <a name="high-availability"></a>Vysoká dostupnost
Ruční nastavení SQL Serverch funkcí s vysokou dostupností, jako jsou Always na instancích clusteru s podporou převzetí služeb při selhání a skupiny dostupnosti Always On, se v cílové databázi SQL zastaral Architektura s vysokou dostupností je už integrovaná v [pro obecné účely (standardní model dostupnosti)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) a na úrovních služby [pro důležité obchodní informace (model dostupnosti premium)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) pro Azure SQL Database. Úroveň služby Pro důležité obchodní informace/Premium také poskytuje škálování na více instancí, které umožňuje připojení k jednomu ze sekundárních uzlů pro účely jen pro čtení. 

Kromě architektury s vysokou dostupností, která je součástí Azure SQL Database, umožňuje funkce [skupin s automatickým převzetím služeb při selhání](../../database/auto-failover-group-overview.md) spravovat replikaci a převzetí služeb při selhání databáze ve spravované instanci do jiné oblasti. 

### <a name="sql-agent-jobs"></a>Úlohy agenta SQL
Úlohy agenta SQL nejsou přímo podporovány v Azure SQL Database a je třeba je nasadit do [úloh elastické databáze (Preview)](../../database/job-automation-overview.md).

### <a name="logins-and-groups"></a>Přihlášení a skupiny
Přesunout přihlašovací údaje SQL ze zdroje SQL Server do Azure SQL Database pomocí Database Migration Service v offline režimu. Pomocí podokna **Vybraná přihlášení** v Průvodci migrací migrujte přihlášení do cílové databáze SQL. 

Uživatele a skupiny systému Windows můžete také migrovat přes Database Migration Service tím, že na stránce **konfigurace** Database Migration Service povolíte odpovídající přepínač. 

Alternativně můžete použít [Nástroj PowerShell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) , který je speciálně navržený architekty migrace dat Microsoftu. Nástroj používá PowerShell k vytvoření skriptu Transact-SQL (T-SQL) pro opětovné vytvoření přihlašovacích údajů a výběru uživatelů databáze ze zdroje do cíle. 

Nástroj PowerShell automaticky mapuje účty služby Active Directory Windows serveru na účty Azure Active Directory (Azure AD) a může provádět vyhledávání UPN pro každé přihlášení ke zdrojové instanci služby Active Directory. Nástroj skripty vlastní server a databázová role společně s členstvím rolí a uživatelskými oprávněními. Databáze s omezením nejsou zatím podporovány a jsou spouštěny pouze podmnožina možných SQL Server oprávnění. 

### <a name="system-databases"></a>Systémové databáze
V případě Azure SQL Database jsou k dispozici pouze [Hlavní](/sql/relational-databases/databases/master-database) systémové databáze a databáze tempdb. Další informace najdete v tématu [databáze tempdb v Azure SQL Database](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="advanced-features"></a>Pokročilé funkce 

Nezapomeňte využít výhod pokročilých cloudových funkcí v SQL Database. Například nemusíte se starat o správu záloh, protože služba to dělá za vás. Můžete obnovit k jakémukoli [bodu v čase v rámci doby uchování](../../database/recovery-using-backups.md#point-in-time-restore). 

Pokud chcete posílit zabezpečení, zvažte použití [ověřování Azure AD](../../database/authentication-aad-overview.md), [auditování](../../database/auditing-overview.md), [detekce hrozeb](../../database/azure-defender-for-sql.md), [zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security)a [dynamického maskování dat](/sql/relational-databases/security/dynamic-data-masking).

Kromě pokročilých funkcí správy a zabezpečení poskytuje SQL Database nástroje, které vám pomůžou [monitorovat a ladit vaše úlohy](../../database/monitor-tune-overview.md). [Azure SQL Analytics (Preview)](../../../azure-monitor/insights/azure-sql.md) je pokročilé řešení pro monitorování výkonu všech vašich databází v Azure SQL Database škálované a napříč několika předplatnými v jednom zobrazení. Azure SQL Analytics shromažďuje a vizualizuje klíčové metriky výkonu pomocí integrovaných inteligentních funkcí pro řešení potíží s výkonem.

[Automatické ladění](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   nepřetržitě monitoruje výkon plánu provádění SQL a automaticky opravuje zjištěné problémy s výkonem. 


## <a name="migration-assets"></a>Prostředky migrace 

Další pomoc najdete v následujících materiálech, které byly vyvinuty pro projekty migrace z reálného světa.

|Prostředek  |Description  |
|---------|---------|
|[Model a nástroj pro vyhodnocení datových úloh](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Tento nástroj nabízí navrženou cílovou platformu "nejlépe vyhovující", připravenost na Cloud a úroveň nápravy aplikace nebo databáze pro zatížení. Nabízí jednoduché výpočetní operace s jedním kliknutím a generování sestav, které pomáhají zrychlit vyhodnocení rozsáhlých nemovitostí poskytnutím automatizovaného a jednotného rozhodovacího procesu pro cílové platformy.|
|[Nástroj DBLoader](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|Pomocí DBLoader můžete načíst data z textových souborů s oddělovači do SQL Server. Tento nástroj konzoly Windows používá SQL Server nativním rozhraním hromadného načtení klienta. Rozhraní funguje na všech verzích SQL Server společně s Azure SQL Database.|
|[Hromadné vytváření databází pomocí PowerShellu](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|Můžete použít sadu tří skriptů PowerShellu, které vytvoří skupinu prostředků (create_rg.ps1), [logický Server v Azure](../../database/logical-servers.md) (create_sqlserver.ps1) a databázi SQL (create_sqldb.ps1). Skripty obsahují možnosti smyčky, takže můžete iterovat a vytvářet tolik serverů a databází, kolik je potřeba.|
|[Hromadné nasazení schématu pomocí MSSQL-Scripter a PowerShellu](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|Tento prostředek vytvoří skupinu prostředků, vytvoří jeden nebo několik [logických serverů v Azure](../../database/logical-servers.md) , aby mohl hostovat Azure SQL Database, exportuje všechna schémata z místní instance SQL Server (nebo několik instancí SQL Server 2005 +) a importuje schémata do Azure SQL Database.|
|[Převod úloh agenta SQL Server na úlohy elastické databáze](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|Tento skript migruje úlohy agenta zdroje SQL Server do elastické databázové úlohy.|
|[Odeslat e-maily z Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|Toto řešení je alternativou k možnosti SendMail a je k dispozici pro místní SQL Server. Používá Azure Functions a službu SendGrid k posílání e-mailů z Azure SQL Database.|
|[Nástroj pro přesun místních SQL Server přihlášení do Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Skript PowerShellu může vytvořit příkaz skriptu T-SQL pro opětovné vytvoření přihlašovacích údajů a vybrat uživatele databáze z místního SQL Server k Azure SQL Database. Nástroj umožňuje automatické mapování účtů služby Windows Server Active Directory na účty Azure AD a volitelně také migraci SQL Server nativních přihlášení.|
|[Automatizace shromažďování dat Perfmon pomocí programu Logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Nástroj Logman můžete použít ke shromažďování dat o výkonu (které vám pomůžou pochopit základní výkon) a získat doporučení pro cíle migrace. Tento nástroj používá logman.exe k vytvoření příkazu, který bude vytvářet, spouštět, zastavovat a odstraňovat čítače výkonu nastavené na vzdáleném SQL Server instanci.|
|[Migrace databáze na Azure SQL Database pomocí BACPAC](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|Tento dokument white paper poskytuje pokyny a postupy, které vám pomůžou zrychlit migrace z SQL Server na Azure SQL Database pomocí souborů BACPAC.|

Data tým SQL Engineering vyvinuli tyto prostředky. Základní Chart týmu je odblokování a urychlení komplexní modernizace pro projekty migrace datové platformy na datovou platformu Azure od Microsoftu.


## <a name="next-steps"></a>Další kroky

- Pokud chcete začít migrovat databáze SQL Server do Azure SQL Database, přečtěte si [Průvodce migrací SQL Server na Azure SQL Database](sql-server-to-sql-database-guide.md).

- Matrici služeb a nástrojů, které vám pomůžou s scénáři migrace databáze a dat a také speciálními úkoly, najdete v tématu [služby a nástroje pro migraci dat](../../../dms/dms-tools-matrix.md).

- Další informace o SQL Database najdete v těchto tématech:
   - [Přehled Azure SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkulačka celkových nákladů na vlastnictví Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Další informace o cyklu rozhraní a přijetí pro migrace do cloudu najdete tady:
   -  [Cloud Adoption Framework pro Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Osvědčené postupy pro určování nákladů a velikostí úloh migrovaných do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Chcete-li vyhodnotit vrstvu přístupu aplikace, přečtěte si část [Data Access Migration Toolkit (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).

- Podrobnosti o tom, jak provést testování A/B pro vrstvu přístupu k datům, najdete v tématu [Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-overview).
