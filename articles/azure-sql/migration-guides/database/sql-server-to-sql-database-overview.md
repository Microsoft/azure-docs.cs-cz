---
title: 'SQL Server Azure SQL Database: Přehled migrace'
description: Přečtěte si o různých nástrojích a možnostech, které jsou k dispozici pro migraci SQL Serverch databází do Azure SQL Database.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: ecb9d1e25647e197614817116921140f0d7fbdf4
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024961"
---
# <a name="migration-overview-sql-server-to-azure-sql-database"></a>Přehled migrace: SQL Server pro Azure SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Přečtěte si o různých možnostech migrace a důležitých informacích o migraci SQL Server do Azure SQL Database. 

Můžete migrovat SQL Server spuštěná místně nebo na: 

- SQL Server na virtuálních počítačích  
- Amazon Web Services (AWS) EC2 
- Služba Amazon relačních databází (AWS RDS) 
- Výpočetní modul (Google Cloud Platform – GCP)  
- Cloud SQL pro SQL Server (Google Cloud Platform – GCP) 

Další příručky k migraci najdete v tématu [migrace databáze](https://docs.microsoft.com/data-migration). 

## <a name="overview"></a>Přehled

[Azure SQL Database](../../database/sql-database-paas-overview.md) je doporučenou možností cíle pro SQL Server úlohy, které vyžadují plně spravovanou platformu jako službu (PaaS). SQL Database zpracovává většinu funkcí správy databáze společně s vysokou dostupností, inteligentním zpracováním dotazů, škálovatelností a funkcemi výkonu, které jsou integrované pro různé typy aplikací. 

SQL Database poskytuje flexibilitu s více [modely nasazení](../../database/sql-database-paas-overview.md#deployment-models) a [úrovněmi služeb](../../database/service-tiers-vcore.md#service-tiers) , které nabízí pro různé typy aplikací nebo úloh.

Jednou z klíčových výhod migrace na SQL Database je, že modernizovat aplikaci můžete využívat pomocí možností PaaS a eliminovat jakoukoli závislost na technických součástech, které jsou vymezeny na úrovni instance, jako jsou například úlohy agenta SQL.

Můžete také ušetřit náklady migrací SQL Server místních licencí do Azure SQL Database pomocí [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) pro SQL Server byste si měli vybrat [nákupní model založený na Vcore](../../database/service-tiers-vcore.md).

Tato příručka se zaměřuje na vysvětlení možností migrace a důležitých informací při přípravě na migraci SQL Serverch databází do Azure SQL Database.  

## <a name="considerations"></a>Požadavky 

Klíčové faktory, které je potřeba vzít v úvahu při hodnocení možností migrace, závisí na: 

- Počet serverů a databází
- Velikost databází
- Přijatelné provozní výpadky během migrace 

Možnosti migrace uvedené v této příručce mají tyto faktory v úvahu. Při migraci logických dat na Azure SQL Database může čas migrace záviset na počtu objektů v databázi a na velikosti databáze. 

K dispozici jsou různé nástroje pro různé úlohy a předvolby uživatele. Některé nástroje lze použít k rychlé migraci izolované databáze pomocí nástroje založeného na uživatelském rozhraní, zatímco jiné nástroje mohou migrovat více databází, které lze automatizovat pro zpracování migrace ve velkém měřítku. 

## <a name="choose-appropriate-target"></a>Zvolit vhodný cíl

Vezměte v úvahu obecné pokyny, které vám pomohou vybrat správný model nasazení a vrstvu služby Azure SQL Database. Během nasazování můžete zvolit výpočetní prostředky a prostředky úložiště a pak [je později změnit pomocí Azure Portal](../../database/scale-resources.md) bez výpadků aplikace.


**Modely nasazení**: Seznámení s úlohou vaší aplikace a vzorem využití pro rozhodování mezi izolovanou databází nebo elastickým fondem. 

- [Jediná databáze](../../database/single-database-overview.md) představuje plně spravovanou databázi vhodnou pro většinu moderních cloudových aplikací a mikroslužeb.
- [Elastický fond](../../database/elastic-pool-overview.md) je kolekce izolovaných databází se sdílenou sadou prostředků, jako je například procesor nebo paměť, a vhodná pro kombinování databází ve fondu s předvídatelnými vzory využití, které mohou efektivně sdílet stejnou sadu prostředků.

**Nákupy modelů**: volba mezi modelem nakupování Vcore, DTU nebo bez serveru. 

- [Model Vcore](../../database/service-tiers-vcore.md) vám umožňuje vybrat počet virtuální jádra pro vaše Azure SQL Database, což usnadňuje volbu při překladu z místního SQL Server. Toto je jediná možnost, která podporuje ukládání licencí s [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). 
- [Model DTU](../../database/service-tiers-dtu.md) vyabstrakce základní výpočetní prostředky, paměť a vstupně-výstupní prostředky, aby bylo možné zajistit míchanou DTU. 
- [Model bez serveru](../../database/serverless-tier-overview.md) je určený pro úlohy, které vyžadují automatické škálování na vyžádání s využitím výpočetních prostředků, které se účtují za sekundu. Výpočetní vrstva bez serveru automaticky pozastaví databáze během neaktivních období (kde se účtuje jenom úložiště) a automaticky obnoví databáze při návratu aktivity. 

**Úrovně služeb**: vyberte tři úrovně služeb navržené pro různé typy aplikací.

- [Pro obecné účely/standardní úroveň služeb](../../database/service-tier-general-purpose.md) nabízí vyváženou možnost rozpočtu s výpočetním prostředím a úložištěm vhodným pro doručování středních a úložných aplikací, a to s využitím redundance integrovaných ve vrstvě úložiště, která umožňuje zotavení po selhání. Navrženo pro většinu databázových úloh. 
- [Pro důležité obchodní informace/Premium Service úrovně](../../database/service-tier-business-critical.md) jsou určené pro vysoce vrstvené aplikace, které vyžadují vysoké sazby za transakce, nízkou latenci a vysokou úroveň odolnosti u sekundárních replik, které jsou k dispozici pro převzetí služeb při selhání, i pro přesměrování zatížení.
- [Úroveň služby s škálovatelným škálováním](../../database/service-tier-hyperscale.md) je pro databáze, které mají rostoucí objemy dat a potřebují automaticky škálovat až 100 až TB velikosti databáze. Určeno pro velmi velké databáze. 

> [!IMPORTANT]
> [Rychlost transakčního protokolu se řídí](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) v Azure SQL Database k omezení vysoké sazby pro přijímání. Během migrace může být potřeba škálovat prostředky cílové databáze (virtuální jádra/DTU), aby se usnadnilo zatížení procesoru nebo propustnosti. Vyberte správnou velikost cílové databáze, ale v případě potřeby Naplánujte škálování prostředků v případě migrace. 


### <a name="sql-server-on-azure-vm-alternative"></a>Alternativa SQL Server na virtuálním počítači Azure

Vaše firma může mít požadavky, které zajistí [SQL Server v Azure Virtual Machines](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) vhodnějším cílem než Azure SQL Database. 

Pokud platí následující podmínky pro vaši firmu, zvažte místo toho přechod na SQL Server na VIRTUÁLNÍm počítači Azure: 

- Pokud požadujete přímý přístup k operačnímu systému nebo systému souborů, například k instalaci agentů třetích stran nebo vlastních agentů na stejný virtuální počítač s SQL Server. 
- Pokud máte striktní závislost na funkcích, které stále nejsou podporované, jako jsou FileStream/soubor, základ a transakce mezi instancemi. 
- Pokud je nezbytně nutné zůstat v určité verzi SQL Server (například 2012). 
- Pokud jsou vaše požadavky na výpočetní výkon mnohem nižší než nabídky spravované instance (jeden vCore, například instance) a konsolidace databáze není přijatelnou možností. 


## <a name="migration-tools"></a>Nástroje pro migraci 

Doporučenými nástroji pro migraci jsou Data Migration Assistant a Azure Database Migration Service. K dispozici jsou také další možnosti alternativních možností migrace. 

### <a name="recommended-tools"></a>Doporučené nástroje

V následující tabulce jsou uvedené doporučené nástroje pro migraci: 

|Technologie | Popis|
|---------|---------|
| [Azure Migrate](/azure/migrate/how-to-create-azure-sql-assessment) | Azure Migrate pro Azure SQL vám umožní zjišťovat a hodnotit vaše datové úložiště SQL ve velkém měřítku, pokud je na VMware, a poskytuje doporučení pro nasazení SQL Azure, cílovou velikost a měsíční odhady. | 
|[Data Migration Assistant (DMA)](/sql/dma/dma-migrateonpremsqltosqldb)|Data Migration Assistant je desktopový nástroj, který poskytuje bezproblémový odhad SQL Server a migrace do Azure SQL Database (jak schéma, tak data). Nástroj se dá nainstalovat na místní server nebo do místního počítače, který má připojení ke svým zdrojovým databázím. Proces migrace je logický pohyb dat mezi objekty ve zdrojové a cílové databázi. </br> – Migrace samostatných databází (schématu i dat)|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)|Služba Azure první strany, která může migrovat vaše databáze SQL Server, aby Azure SQL Database pomocí Azure Portal nebo automatizovaného prostředí PowerShell. Azure DMS vyžaduje, abyste při zřizování vybrali upřednostňovanou službu Azure Virtual Network (VNet), abyste měli jistotu, že máte připojení ke zdrojovým databázím SQL Server. </br> – Migrujte jednotlivé databáze nebo škálování. |
| | |


### <a name="alternative-tools"></a>Alternativní nástroje

V následující tabulce jsou uvedeny alternativní nástroje pro migraci: 

|Technologie |Popis  |
|---------|---------|
|[Transakční replikace](../../database/replication-to-sql-database.md)|Replikujte data ze zdrojových databázových tabulek SQL Server do SQL Database tím, že poskytnete možnost migrace typu předplatitele vydavatele a zachováte konzistenci transakcí. Přírůstkové změny dat se šíří u odběratelů, kteří se k nim vyskytují na vydavatelích.|
|[Import služby export/BACPAC](../../database/database-import.md)|[BacPac](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) je soubor Windows s `.bacpac` příponou, která zapouzdřuje schéma a data databáze. BACPAC lze použít k exportu dat ze zdrojových SQL Server a k importu dat do Azure SQL Database. Soubor BACPAC se dá importovat do nového Azure SQL Database pomocí Azure Portal. </br></br> Pro účely škálování a výkonu velkých nebo velkých databází byste měli zvážit použití nástroje příkazového řádku [SqlPackage](../../database/database-import.md#using-sqlpackage) k exportu a importu databází.|
|[Hromadná kopie](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|[Nástroj program pro hromadné kopírování (BCP)](/sql/tools/bcp-utility) kopíruje data z instance SQL Server do datového souboru. Pomocí nástroje BCP exportujte data ze zdroje a importujte datový soubor do cílového SQL Database. </br></br> Pro rychlé operace hromadného kopírování pro přesun dat na Azure SQL Database se dá pomocí [nástroje chytrého kopírování](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) použít k maximalizaci rychlosti přenosu využití paralelních úloh kopírování.|
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-database.md)|[Aktivita kopírování](../../../data-factory/copy-activity-overview.md) v Azure Data Factory migruje data ze zdrojových databází SQL Server na SQL Database pomocí integrovaných konektorů a [Integration runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> ADF podporuje široké spektrum [konektorů](../../../data-factory/connector-overview.md) pro přesun dat z SQL Server zdrojů do SQL Database.|
|[Synchronizace dat SQL](../../database/sql-data-sync-data-sql-server-sql-database.md)|Synchronizace dat SQL je služba založená na Azure SQL Database, která umožňuje synchronizovat data, která jste vybrali obousměrně napříč více databázemi v místním prostředí i v cloudu.</br>Synchronizace dat je užitečná v případech, kdy je potřeba udržovat data v různých databázích ve Azure SQL Database nebo SQL Server.|
| | |

## <a name="compare-migration-options"></a>Porovnat možnosti migrace

Porovnejte možnosti migrace a vyberte cestu odpovídající vašim obchodním potřebám. 

### <a name="recommended-options"></a>Doporučené možnosti

Následující tabulka porovnává Doporučené možnosti migrace: 

|Možnost migrace  |Kdy je použít  |Požadavky  |
|---------|---------|---------|
|[Data Migration Assistant (DMA)](/sql/dma/dma-migrateonpremsqltosqldb) | – Migrujte jednotlivé databáze (schéma i data).  </br> – Může během procesu migrace dat podvyhovovat výpadkům. </br> </br> Podporované zdroje: </br> -SQL Server (2005-2019) místní nebo Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP COMPUTE SQL Server virtuální počítač | – Aktivita migrace provádí přesun dat mezi databázovými objekty (ze zdroje do cíle), a proto se doporučuje spustit v době mimo špičku. </br> -DMA hlásí stav migrace na databázový objekt, včetně počtu migrovaných řádků.  </br> – Pro velké migrace (počet databází/velikost databáze) použijte níže uvedenou Azure Database Migration Service.|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)| – Migrujte jednotlivé databáze nebo škálování. </br> – Může během procesu migrace pojmout výpadky. </br> </br> Podporované zdroje: </br> -SQL Server (2005-2019) místní nebo Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP COMPUTE SQL Server virtuální počítač | – Migrace ve velkém měřítku může být automatizovaná přes [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md). </br> -Doba k dokončení migrace závisí na velikosti databáze a počtu objektů v databázi. </br> – Vyžaduje, aby byla zdrojová databáze nastavena jen pro čtení. |
| | | |

### <a name="alternative-options"></a>Alternativní možnosti

Následující tabulka porovnává alternativní možnosti migrace: 

|Metoda/technologie |Kdy je použít    |Požadavky  |
|---------|---------|---------|
|[Transakční replikace](../../database/replication-to-sql-database.md)| – Migrace provede průběžně publikovanými změnami ze zdrojových databázových tabulek do cílových SQL Database tabulek. </br> – Úplná nebo částečná migrace databází z vybraných tabulek (podmnožina databáze).  </br> </br> Podporované zdroje: </br> - [SQL Server (2016 – 2019) s některými omezeními](/sql/relational-databases/replication/replication-backward-compatibility) </br> – AWS EC2  </br> – GCP COMPUTE SQL Server virtuální počítač  | – Instalace je poměrně složitá v porovnání s dalšími možnostmi migrace.   </br> – Poskytuje možnost průběžné replikace pro migraci dat (aniž byste museli přenášet databáze do režimu offline).  </br> -Transakční replikace má několik omezení, která je potřeba vzít v úvahu při nastavování vydavatele na zdrojovém SQL Server. Další informace najdete v tématu [omezení pro publikování objektů](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) . </br>– Je možné [monitorovat aktivitu replikace](/sql/relational-databases/replication/monitor/monitoring-replication).    |
|[Import služby export/BACPAC](../../database/database-import.md)| – Migrujte jednotlivé databáze obchodních aplikací. </br>– Hodí se pro menší databáze.  </br>  – Nevyžaduje samostatnou službu nebo nástroj pro migraci. </br> </br> Podporované zdroje: </br> -SQL Server (2005-2019) místní nebo Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP COMPUTE SQL Server virtuální počítač  |  – Vyžaduje prostoje, protože data je třeba exportovat ve zdroji a importovat do cílového umístění.   </br> – Formáty souborů a datové typy používané při exportu a importu musí být konzistentní se schématy tabulek, aby se předešlo chybám zkrácení/neshody datových typů.  </br> – Doba potřebná k exportu databáze s velkým počtem objektů může být výrazně vyšší.       |
|[Hromadná kopie](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| – Migrujte úplné nebo částečné migrace dat. </br> – Může vyhovovat výpadkům. </br> </br> Podporované zdroje: </br> -SQL Server (2005-2019) místní nebo Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP COMPUTE SQL Server virtuální počítač   | – Vyžaduje prostoje při exportu dat ze zdroje a importu do cíle. </br> – Formáty souborů a datové typy používané při exportu/importu musí být konzistentní se schématy tabulek. |
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-database.md)| – Migrujte a Transformujte data ze zdrojových SQL Serverch databází. </br> – Slučují se data z více zdrojů dat, aby se Azure SQL Database typicky pro úlohy Business Intelligence (BI).  |  – Vyžaduje vytvoření kanálů přesunu dat v ADF pro přesun dat ze zdroje do cíle.   </br> - [Náklady](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) jsou důležitým aspektem a vycházejí z aktivačních událostí kanálu, spuštění aktivit, doby trvání přesunu dat atd. |
|[Synchronizace dat SQL](../../database/sql-data-sync-data-sql-server-sql-database.md)| -Synchronizovat data mezi zdrojovou a cílovou databází.</br> – Vhodné ke spuštění průběžné synchronizace mezi Azure SQL Database a místními SQL Servery v obousměrném toku. | -Azure SQL Database musí být databáze centra pro synchronizaci s Prem SQL Server databáze jako členské databáze.</br> – Ve srovnání s transakční replikací Synchronizace dat SQL podporuje obousměrnou synchronizaci dat mezi místními a Azure SQL Database. </br> – Může mít vyšší dopad na výkon v závislosti na zatížení.|
| | | |

## <a name="feature-interoperability"></a>Interoperabilita funkcí 

Při migraci úloh, které spoléhají na jiné funkce SQL Server, jsou potřeba další okolnosti.

#### <a name="sql-server-integration-services"></a>Služba SSIS (SQL Server Integration Services)
Migrujte balíčky služba SSIS (SQL Server Integration Services) (SSIS) do Azure tím, že je znovu nasadíte do Azure-SSIS runtime v [Azure Data Factory](../../../data-factory/introduction.md). Azure Data Factory [podporuje migraci balíčků SSIS](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) poskytnutím modulu runtime vytvořeného pro spouštění balíčků SSIS v Azure. Alternativně můžete také přepsat logickou logiku SSIS ETL nativně v ADF pomocí [toku](../../../data-factory/concepts-data-flow-overview.md)dat.


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
Migrace sestav SQL Server Reporting Services (SSRS) na stránkované sestavy v Power BI. Použijte [Nástroj pro migraci RDL](https://github.com/microsoft/RdlMigration) , který vám umožní připravit a migrovat sestavy. Tento nástroj vyvinul Microsoft, aby zákazníkům usnadnil migraci sestav RDL ze serverů SSRS do Power BI. Je k dispozici na GitHubu a dokumentuje kompletní návod k migračnímu scénáři. 

#### <a name="high-availability"></a>Vysoká dostupnost
Ruční nastavení SQL Server funkcí vysoké dostupnosti, jako je vždycky v instancích clusteru s podporou převzetí služeb při selhání a skupiny dostupnosti Always On, se na cílovém Azure SQL Database zastaralá, protože architektura vysoké dostupnosti je už integrovaná do [pro obecné účely (standardní model dostupnosti)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) i [pro důležité obchodní informace (model dostupnosti Premium)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) SQL Database. Úroveň služby Pro důležité obchodní informace/Premium také poskytuje škálování na více instancí, které umožňuje připojení k jednomu ze sekundárních uzlů pro účely jen pro čtení. 

Mimo architekturu s vysokou dostupností, která je součástí SQL Database, se nachází také funkce [skupiny automatického převzetí služeb při selhání](../../database/auto-failover-group-overview.md) , která umožňuje spravovat replikaci a převzetí služeb při selhání databáze ve spravované instanci do jiné oblasti. 

#### <a name="sql-agent-jobs"></a>Úlohy agenta SQL
Úlohy agenta SQL nejsou přímo podporovány v Azure SQL Database a bude nutné je nasadit do [úloh elastic Database (Preview)](../../database/job-automation-overview.md).

#### <a name="logins-and-groups"></a>Přihlášení a skupiny
Přesune přihlášení SQL ze zdrojového SQL Server a Azure SQL Database pomocí Database Migration Service (DMS) v offline režimu.  V **Průvodci migrací** použijte okno **Vybraná přihlášení** a migrujte přihlášení do cílového SQL Database. 

Uživatele a skupiny systému Windows je možné migrovat také pomocí DMS tím, že na stránce konfigurace DMS povolíte odpovídající přepínací tlačítko. 

Alternativně můžete použít [Nástroj PowerShell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) , který je speciálně navržený architekty migrace dat Microsoftu. Nástroj používá PowerShell k vytvoření skriptu Transact-SQL (T-SQL) pro opětovné vytvoření přihlašovacích údajů a výběru uživatelů databáze ze zdroje do cíle. Nástroj automaticky mapuje účty služby Windows AD na účty Azure AD a může provádět vyhledávání UPN pro každé přihlášení ke zdrojové službě Active Directory. Nástroj skripty vlastní server a databázová role a také členství v rolích, databázová role a uživatelská oprávnění. Obsažené databáze se zatím nepodporují a jsou spouštěny jenom podmnožina možných SQL Server oprávnění. 


#### <a name="system-databases"></a>Systémové databáze
V případě Azure SQL Database jsou k dispozici pouze [Hlavní](/sql/relational-databases/databases/master-database) systémové databáze a databáze tempdb. Další informace najdete v tématu [databáze tempdb v Azure SQL Database](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="leverage-advanced-features"></a>Využití pokročilých funkcí 

Nezapomeňte využít výhod pokročilých cloudových funkcí, které nabízí SQL Database. Například už se nemusíte starat o správu záloh, protože služba to udělá za vás. Můžete obnovit k jakémukoli [bodu v čase v rámci doby uchování](../../database/recovery-using-backups.md#point-in-time-restore). 

Pokud chcete posílit zabezpečení, zvažte použití [Azure Active Directory ověřování](../../database/authentication-aad-overview.md), [auditování](../../database/auditing-overview.md), [detekce hrozeb](../../database/azure-defender-for-sql.md), [zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security)a [dynamického maskování dat](/sql/relational-databases/security/dynamic-data-masking).

Kromě pokročilých funkcí správy a zabezpečení SQL Database poskytuje sadu pokročilých nástrojů, které vám pomůžou [monitorovat a ladit vaše úlohy](../../database/monitor-tune-overview.md). [Azure SQL Analytics (Preview)](../../../azure-monitor/insights/azure-sql.md) je pokročilé řešení monitorování cloudu pro monitorování výkonu všech vašich databází v Azure SQL Database škálované a napříč několika předplatnými v jednom zobrazení. Azure SQL Analytics shromažďuje a vizualizuje klíčové metriky výkonu pomocí integrovaných inteligentních funkcí pro řešení potíží s výkonem.

[Automatické ladění](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   nepřetržitě monitoruje výkon statistik plánu spuštění SQL a automaticky opravuje zjištěné problémy s výkonem. 


## <a name="migration-assets"></a>Prostředky migrace 

Další pomoc najdete v následujících materiálech, které byly vyvinuty pro projekty z reálného světa migrace.

|Prostředek  |Description  |
|---------|---------|
|[Model a nástroj pro vyhodnocení datových úloh](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Tento nástroj poskytuje navrženou cílovou platformu "nejlépe vyhovující", připravenost na Cloud a úroveň nápravy aplikace nebo databáze pro danou úlohu. Nabízí jednoduché výpočetní operace s jedním kliknutím a generování sestav, které pomáhají zrychlit vyhodnocení velkých majetku poskytnutím automatizovaného a sjednoceného rozhodovacího procesu platformy.|
|[Nástroj DBLoader](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|DBLoader lze použít k načtení dat z textových souborů s oddělovači do SQL Server. Tento nástroj konzoly Windows používá rozhraní SQL Server nativního BulkLoad klienta, které funguje na všech verzích SQL Server, včetně Azure SQL Database.|
|[Hromadné vytváření databází pomocí PowerShellu](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|To zahrnuje sadu tří skriptů PowerShellu, které vytvářejí skupinu prostředků (create_rg.ps1), [logický Server v Azure](../../database/logical-servers.md) (create_sqlserver.ps1) a Azure SQL Database (create_sqldb.ps1). Skripty obsahují možnosti smyčky, takže můžete iterovat a vytvářet tolik serverů a databází, kolik je potřeba.|
|[Hromadné nasazení schématu pomocí MSSQL-Scripter & PowerShellu](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|Tento prostředek vytvoří skupinu prostředků, jeden nebo několik [logických serverů v Azure](../../database/logical-servers.md) pro hostování Azure SQL Database, exportuje každé schéma z místního SQL Server (nebo několik SQL serverů (2005 +) a importuje ho do Azure SQL Database.|
|[Převod úloh agenta SQL Server na úlohy Elastic Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|Tento skript migruje úlohy agenta zdroje SQL Server na úlohy služby Elastic Database.|
|[Odeslat poštu z Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|To poskytuje řešení jako alternativu k funkci SendMail, která je k dispozici v místním SQL Server. Řešení používá Azure Functions a službu Azure SendGrid k posílání e-mailů z Azure SQL Database.|
|[Nástroj pro přesun místních SQL Server přihlášení do Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Skript PowerShellu, který vytvoří skript příkazového řádku T-SQL pro opětovné vytvoření přihlašovacích údajů a výběr uživatelů databáze z místních SQL Server k Azure SQL Database. Nástroj umožňuje automatické mapování účtů služby Windows AD na účty Azure AD a volitelně také migraci SQL Server nativních přihlášení.|
|[Automatizace shromažďování dat PerfMon pomocí programu Logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Nástroj, který shromažďuje PerMon data pro pochopení výkonu směrného plánu a pomáhá při migraci na cíle migrace. Tento nástroj používá logman.exe k vytvoření příkazu, který bude vytvářet, spouštět, zastavovat a odstraňovat čítače výkonu nastavené na vzdáleném SQL Server|
|[Dokument White Paper – migrace databáze do služby Azure SQL DB pomocí BACPAC](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|Tento dokument white paper poskytuje pokyny a postupy, které vám pomůžou zrychlit migrace z SQL Server Azure SQL Database pomocí souborů BACPAC.|

Tyto prostředky byly vyvinuty jako součást programu data SQL expertem, který je financován technickým týmem Azure Data Group. Základní Chartou programu data SQL expertem je odblokování a urychlení komplexní modernizace a konkurenční možnosti migrace datových platforem na datovou platformu Azure od Microsoftu. Pokud si myslíte, že by vaše organizace mohla zajímat účast v programu data SQL expertem, obraťte se prosím na svůj tým a požádejte ho, aby podal jmenování.


## <a name="next-steps"></a>Další kroky

Pokud chcete zahájit migraci SQL Server do SQL Database, přečtěte si [Průvodce migrací SQL Server na Azure SQL Database](sql-server-to-sql-database-guide.md).

- Matrici služeb a nástrojů společnosti Microsoft, které jsou k dispozici, aby vám pomohla při různých scénářích databáze a migrace dat i v speciálních úlohách, najdete v tématu [služba a nástroje pro migraci dat](../../../dms/dms-tools-matrix.md).

- Další informace o SQL Database najdete v těchto tématech:
   - [Přehled Azure SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkulačka celkových nákladů na vlastnictví Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Další informace o cyklu rozhraní a přijetí pro migrace do cloudu najdete v tématu.
   -  [Cloud Adoption Framework pro Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Osvědčené postupy pro výpočet nákladů a úloh migrace do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Postup vyhodnocení vrstvy přístupu k aplikaci najdete v tématu [sada Data Access Migration Toolkit (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) .
- Podrobnosti o tom, jak provádět testování vrstvy přístupu k datům A/B, najdete [Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-overview).