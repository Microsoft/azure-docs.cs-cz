---
title: 'SQL Server ke spravované instanci SQL: Přehled migrace'
description: Seznamte se s nástroji a možnostmi, které jsou k dispozici pro migraci SQL Serverch databází do spravované instance Azure SQL.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 02/18/2020
ms.openlocfilehash: 0a3fd1b492d19e241d89cc5477891c7c836e4640
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078974"
---
# <a name="migration-overview-sql-server-to-azure-sql-managed-instance"></a>Přehled migrace: SQL Server do spravované instance Azure SQL
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

Přečtěte si o možnostech a faktorech migrace databází SQL Server do spravované instance Azure SQL. 

Můžete migrovat SQL Server databáze spuštěné místně nebo na: 

- SQL Server v Azure Virtual Machines.  
- Cloud COMPUTE AWS (EC2) pro elastické výpočetní prostředky (). Amazon Web Services 
- Služba AWS relačních databází (RDS). 
- Výpočetní modul v Google Cloud Platform (GCP).  
- Cloudový SQL pro SQL Server v GCP. 

Další příručky k migraci najdete v tématu [migrace databáze](https://docs.microsoft.com/data-migration). 

## <a name="overview"></a>Přehled

[Spravovaná instance Azure SQL](../../managed-instance/sql-managed-instance-paas-overview.md) je doporučovanou možností cíle pro SQL Server úlohy, které vyžadují plně spravovanou službu, aniž by bylo nutné spravovat virtuální počítače nebo jejich operační systémy. Spravovaná instance SQL umožňuje přesunout vaše místní aplikace do Azure s minimálními změnami aplikace nebo databáze. Nabízí úplnou izolaci instancí s podporou nativní virtuální sítě. 

## <a name="considerations"></a>Požadavky 

Mezi klíčové faktory, které je potřeba vzít v úvahu při hodnocení možností migrace, patří: 
- Počet serverů a databází
- Velikost databází
- Přijatelné provozní výpadky během migrace 

Jednou z klíčových výhod migrace databází SQL Server do spravované instance SQL je, že se můžete rozhodnout migrovat celou instanci nebo jenom podmnožinu jednotlivých databází. Pečlivě se zamyslete nad tím, že zadáte do procesu migrace následující: 
- Všechny databáze, které je třeba společně umístit do stejné instance 
- Objekty na úrovni instance vyžadované pro vaši aplikaci, včetně přihlašovacích údajů, přihlašovacích údajů, úloh a operátorů SQL agenta a triggerů na úrovni serveru 

> [!NOTE]
> Spravovaná instance Azure SQL garantuje 99,99% dostupnost, i v kritických scénářích. Režie způsobená některými funkcemi ve spravované instanci SQL se nedá zakázat. Další informace najdete v tématu [klíčové příčiny rozdílů výkonu mezi spravovanou instancí SQL a](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) položkou SQL Server blogu. 


## <a name="choose-an-appropriate-target"></a>Zvolit vhodný cíl

Následující obecné pokyny vám pomůžou zvolit správnou úroveň služby a charakteristiky spravované instance SQL, aby se shodovaly s vaším [směrným plánem výkonu](sql-server-to-managed-instance-performance-baseline.md): 

- Pomocí směrného plánu využití CPU zřídíte spravovanou instanci, která odpovídá počtu jader, které vaše instance SQL Server používá. Může být potřeba škálovat prostředky tak, aby odpovídaly [charakteristikám generování hardwaru](../../managed-instance/resource-limits.md#hardware-generation-characteristics). 
- Pomocí směrného plánu využití paměti můžete zvolit [možnost Vcore](../../managed-instance/resource-limits.md#service-tier-characteristics) , která odpovídajícím způsobem odpovídá vašemu přidělení paměti. 
- Použijte latenci I/O základny subsystému souborů k výběru mezi Pro obecné účely (latence větší než 5 ms) a Pro důležité obchodní informace (latence menší než 3 MS) úrovně služeb. 
- Pomocí propustnosti základní hodnoty můžete předem přidělit velikost dat a souborů protokolu, abyste dosáhli očekávaného vstupně-výstupního výkonu. 

Můžete zvolit výpočetní prostředky a prostředky úložiště během nasazování a potom [je později změnit pomocí Azure Portal](../../database/scale-resources.md), aniž byste museli zaniknout do výpadku aplikace. 

> [!IMPORTANT]
> Jakékoli nesrovnalosti v [požadavcích virtuální sítě pro spravované instance](../../managed-instance/connectivity-architecture-overview.md#network-requirements) vám můžou zabránit v vytváření nových instancí nebo používání stávajících instancí. Přečtěte si další informace o [vytváření nových](../../managed-instance/virtual-network-subnet-create-arm-template.md)   a [konfigurování stávajících](../../managed-instance/vnet-existing-add-subnet.md)   sítí. 

Další klíčovým aspektem výběru cílové úrovně služby ve spravované instanci Azure SQL (Pro obecné účely oproti Pro důležité obchodní informace) je dostupnost určitých funkcí, jako je In-Memory OLTP, které jsou k dispozici pouze na Pro důležité obchodní informace úrovni. 

### <a name="sql-server-vm-alternative"></a>Alternativní virtuální počítač SQL Server

Vaše firma může mít požadavky, které zajistí [SQL Server v Azure Virtual Machines](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) lépe vhodným cílem, než je spravovaná instance Azure SQL. 

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
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-managed-instance.md)  | Tato služba Azure podporuje migraci v režimu offline pro aplikace, které můžou během procesu migrace dovolit výpadky. Na rozdíl od nepřetržité migrace v online režimu spustí migrace offline režimu jednorázové obnovení úplné zálohy databáze ze zdroje do cíle. | 
|[Nativní zálohování a obnovení](../../managed-instance/restore-sample-database-quickstart.md) | Spravovaná instance SQL podporuje obnovení nativních zálohování databáze SQL Server (soubory. bak). Je to nejjednodušší možnost migrace pro zákazníky, kteří můžou poskytnout úplné zálohy databáze Azure Storage. V [části o prostředcích migrace](#migration-assets) dále v tomto článku jsou podporovány a popsány také úplné a rozdílové zálohy.| 
|[Služba opětovného přehrání protokolu](../../managed-instance/log-replay-service-migrate.md) | Tato cloudová služba je povolená pro spravovanou instanci SQL na základě SQL Server technologie pro přenos protokolů. Je to možnost migrace pro zákazníky, kteří můžou do Azure Storage poskytnout úplné, rozdílové a zaprotokolované zálohy databáze. Služba opětovného přehrání protokolu se používá k obnovení záložních souborů z Azure Blob Storage do spravované instance SQL.| 
| | |

V následující tabulce jsou uvedeny alternativní nástroje pro migraci: 

|**Technologie** |**Popis**  |
|---------|---------|
|[Transakční replikace](../../managed-instance/replication-transactional-overview.md) | Umožňuje replikovat data ze zdrojových databázových tabulek SQL Server do spravované instance SQL poskytnutím možnosti migrace typu předplatitele vydavatele při zachování konzistentní transakční konzistence. | 
|[Hromadná kopie](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| [Nástroj program pro hromadné kopírování (BCP)](/sql/tools/bcp-utility) kopíruje data z instance SQL Server do datového souboru. Pomocí nástroje exportujte data ze zdroje a importujte datový soubor do cílové spravované instance SQL. </br></br> U vysokorychlostních operací hromadného kopírování k přesunu dat do spravované instance Azure SQL můžete použít nástroj pro [inteligentní hromadné kopírování](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) k maximalizaci rychlosti přenosu tím, že budete využívat paralelní kopírování úloh. | 
|[Průvodce exportem a BACPAC importu](../../database/database-import.md?tabs=azure-powershell)| [BacPac](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) je soubor Windows s příponou. BacPac, který zapouzdřuje schéma a data databáze. Pomocí BACPAC můžete exportovat data ze zdroje SQL Server a importovat data zpátky do spravované instance Azure SQL. |  
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)|  [Aktivita kopírování](../../../data-factory/copy-activity-overview.md) v Azure Data Factory migruje data ze zdrojových databází SQL Server do spravované instance SQL pomocí integrovaných konektorů a [prostředí Integration runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> Data Factory podporuje široké spektrum [konektorů](../../../data-factory/connector-overview.md) pro přesun dat z SQL Server zdrojů do spravované instance SQL. |

## <a name="compare-migration-options"></a>Porovnat možnosti migrace

Porovnejte možnosti migrace a vyberte cestu, která je vhodná pro vaše obchodní potřeby. 

Následující tabulka porovnává možnosti migrace, které doporučujeme: 

|Možnost migrace  |Kdy je použít  |Požadavky  |
|---------|---------|---------|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-managed-instance.md) | – Migrujte jednotlivé databáze nebo více databází se škálováním. </br> – Může během procesu migrace pojmout výpadky. </br> </br> Podporované zdroje: </br> – SQL Server (2005 až 2019) místní nebo Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP COMPUTE SQL Server virtuální počítač |  – Migrace ve velkém měřítku může být automatizovaná přes [PowerShell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). </br> -Doba k dokončení migrace závisí na velikosti databáze a je ovlivněná časem zálohování a obnovení. </br> – Může být vyžadováno dostatečné výpadky. |
|[Nativní zálohování a obnovení](../../managed-instance/restore-sample-database-quickstart.md) | – Migrujte jednotlivé databáze obchodních aplikací.  </br> – Rychlá a snadná migrace bez samostatné služby nebo nástroje pro migraci  </br> </br> Podporované zdroje: </br> – SQL Server (2005 až 2019) místní nebo Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP COMPUTE SQL Server virtuální počítač | -Záloha databáze používá více vláken pro optimalizaci přenosu dat do služby Azure Blob Storage, ale šířka pásma a velikost databáze může ovlivnit přenosovou rychlost. </br> -Výpadky by měly pojmout dobu potřebnou k provedení úplného zálohování a obnovení (což je velikost operace s daty).| 
|[Služba opětovného přehrání protokolu](../../managed-instance/log-replay-service-migrate.md) | – Migrujte jednotlivé databáze obchodních aplikací.  </br> – Pro migrace databáze je potřeba více ovládacích prvků.  </br> </br> Podporované zdroje: </br> – SQL Server (2008 až 2019) místní nebo Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP COMPUTE SQL Server virtuální počítač | – Migrace zahrnuje vytvoření úplných záloh databáze při SQL Server a zkopírování záložních souborů do Azure Blob Storage. Služba opětovného přehrání protokolu se používá k obnovení záložních souborů z Azure Blob Storage do spravované instance SQL. </br> – Databáze obnovované během procesu migrace budou v režimu obnovení a nelze je použít ke čtení nebo zápisu do dokončení procesu.| 
| | | |

Následující tabulka porovnává alternativní možnosti migrace: 

|Metoda nebo technologie |Kdy je použít |Požadavky  |
|---------|---------|---------|
|[Transakční replikace](../../managed-instance/replication-transactional-overview.md) | – Migrujete průběžně po publikování změn z tabulek zdrojové databáze do cílové tabulky databáze spravované instance SQL. </br> – Proveďte úplné nebo částečné migrace databází z vybraných tabulek (podmnožina databáze).  </br> </br> Podporované zdroje: </br> -SQL Server (2012 až 2019) s některými omezeními </br> – AWS EC2  </br> – GCP COMPUTE SQL Server virtuální počítač | </br> – Instalace je poměrně složitá v porovnání s dalšími možnostmi migrace.   </br> – Poskytuje možnost průběžné replikace pro migraci dat (aniž byste museli přenášet databáze do režimu offline).</br> – Transakční replikace má při nastavování vydavatele na zdrojové instanci SQL Server omezení. Další informace najdete v tématu [omezení pro publikování objektů](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) .  </br> – K dispozici je možnost [monitorování aktivity replikace](/sql/relational-databases/replication/monitor/monitoring-replication) .    |
|[Hromadná kopie](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| – Proveďte úplné nebo částečné migrace dat. </br> – Může vyhovovat výpadkům. </br> </br> Podporované zdroje: </br> – SQL Server (2005 až 2019) místní nebo Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP COMPUTE SQL Server virtuální počítač   | – Vyžaduje prostoje při exportu dat ze zdroje a importu do cíle. </br> – Formáty souborů a datové typy používané při exportu nebo importu musí být konzistentní se schématy tabulek. |
|[Průvodce exportem a BACPAC importu](../../database/database-import.md)| – Migrujte jednotlivé databáze obchodních aplikací. </br>– Hodí se pro menší databáze.  </br>  Nevyžaduje samostatnou službu nebo nástroj pro migraci. </br> </br> Podporované zdroje: </br> – SQL Server (2005 až 2019) místní nebo Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP COMPUTE SQL Server virtuální počítač  |   </br> – Vyžaduje prostoje, protože data musí být exportována ve zdroji a importována v cílovém umístění.   </br> – Formáty souborů a datové typy používané při exportu nebo importu musí být konzistentní se schématy tabulek, aby nedošlo k chybám při zkracování nebo neshodě typů dat. </br> – Doba potřebná k exportu databáze s velkým počtem objektů může být výrazně vyšší. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)| – Migrujte nebo Transformujte data ze zdrojových SQL Server databází.</br> – Sloučení dat z více zdrojů dat do spravované instance Azure SQL je obvykle pro úlohy služby business intelligence (BI).   </br> – Vyžaduje vytvoření kanálů přesunu dat v Data Factory k přesunu dat ze zdroje do cíle.   </br> - [Náklady](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) jsou důležitým aspektem a jsou založené na faktorech, jako jsou triggery kanálu, spuštění aktivit a doba trvání přesunu dat. |
| | | |

## <a name="feature-interoperability"></a>Interoperabilita funkcí 

Při migraci úloh, které spoléhají na jiné funkce SQL Server, jsou potřeba další informace. 

### <a name="sql-server-integration-services"></a>Služba SSIS (SQL Server Integration Services)

Migrujte balíčky služba SSIS (SQL Server Integration Services) (SSIS) a projekty v SSISDB do spravované instance Azure SQL pomocí [Azure Database Migration Service](../../../dms/how-to-migrate-ssis-packages-managed-instance.md). 

Pro migraci se podporují jenom balíčky SSIS v SSISDB, které začínají na SQL Server 2012. Před migrací převeďte starší balíčky SSIS. Další informace najdete v [kurzu převod projektu](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model) . 


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

Sestavy SQL Server Reporting Services (SSRS) můžete migrovat na stránkované sestavy v Power BI. Použijte [Nástroj pro migraci RDL](https://github.com/microsoft/RdlMigration) , který vám umožní připravit a migrovat sestavy. Společnost Microsoft vyvinula tento nástroj, který zákazníkům umožňuje migrovat sestavy Report Definition Language (RDL) ze svých serverů SSRS na Power BI. Je k dispozici na GitHubu a dokumentuje podrobný návod k migračnímu scénáři. 

### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

Služba Analysis Services serveru SQL tabulkové modely z SQL Server 2012 a novějších se dají migrovat do Azure Analysis Services, což je model nasazení Platform as a Service (PaaS) pro Analysis Services tabelární model v Azure. Další informace o migraci místních modelů a Azure Analysis Services v [tomto výukovém kurzu najdete v tomto kurzu](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/).

Alternativně můžete zvážit migraci místních Analysis Services tabelárních modelů do [Power BI Premium pomocí nových koncových bodů pro čtení a zápis XMLA](/power-bi/admin/service-premium-connect-tools). 

### <a name="high-availability"></a>Vysoká dostupnost

U instancí clusteru s podporou převzetí služeb při selhání se vždy vySQL Server funkce vysoké dostupnosti a skupiny dostupnosti Always On se v cílové spravované instanci SQL stanou zastaralými. Architektura s vysokou dostupností je už integrovaná v [pro obecné účely (standardní model dostupnosti)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) a úrovně služeb [pro důležité obchodní informace (model dostupnosti Premium)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) pro spravovanou instanci SQL. Model dostupnosti Premium také poskytuje škálování na více instancí, které umožňuje připojení k jednomu ze sekundárních uzlů pro účely jen pro čtení.     

Kromě architektury s vysokou dostupností, která je součástí spravované instance SQL, umožňuje funkce [skupin s automatickým převzetím služeb při selhání](../../database/auto-failover-group-overview.md) spravovat replikaci a převzetí služeb při selhání databáze ve spravované instanci do jiné oblasti. 

### <a name="sql-agent-jobs"></a>Úlohy agenta SQL

K migraci [úloh agenta SQL](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)použijte možnost offline Azure Database Migration Service. Jinak skriptujte úlohy v jazyce Transact-SQL (T-SQL) pomocí SQL Server Management Studio a pak je ručně znovu vytvořte v cílové instanci SQL Managed. 

> [!IMPORTANT]
> V současné době Azure Database Migration Service podporuje pouze úlohy, které mají podsystém T-SQL. Úlohy s SSIS kroky balíčku se musí migrovat ručně. 

### <a name="logins-and-groups"></a>Přihlášení a skupiny

Umožňuje přesunout přihlašovací údaje SQL ze zdroje SQL Server do spravované instance Azure SQL pomocí Database Migration Service v offline režimu.  V Průvodci migrací použijte podokno [Vybrat přihlášení](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins) k migraci přihlášení do cílové spravované instance SQL. 

Ve výchozím nastavení Azure Database Migration Service podporuje migraci pouze přihlášení SQL. Migraci přihlašovacích údajů systému Windows ale můžete povolit pomocí:

- Ujistěte se, že cílová instance SQL Managed má Azure Active Directory (Azure AD) přístup pro čtení. Uživatel, který má roli globálního správce, může nakonfigurovat tento přístup prostřednictvím Azure Portal.
- Konfigurace Azure Database Migration Service, aby se povolily migrace přihlášení uživatelů nebo skupin systému Windows Tuto stránku nastavíte prostřednictvím Azure Portal na stránce **Konfigurace** . Po povolení tohoto nastavení restartujte službu, aby se změny projevily.

Po restartování služby se v seznamu přihlašovacích údajů, které jsou k migraci k dispozici, zobrazí uživatelská jména nebo uživatelé systému Windows. U všech přihlášení uživatelů nebo skupin systému Windows, které migrujete, budete vyzváni k zadání přidruženého názvu domény. Uživatelské účty služeb (účty se službou Domain Name NT) a virtuální uživatelské účty (účty s názvem domény služba NT) nejsou podporovány. Další informace najdete v tématu [Postup migrace uživatelů a skupin Windows v instanci SQL Server do spravované instance Azure SQL pomocí T-SQL](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md).

Alternativně můžete použít [Nástroj PowerShell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) , který je speciálně navržený architekty migrace dat Microsoftu. Nástroj používá PowerShell k vytvoření skriptu T-SQL pro opětovné vytvoření přihlašovacích údajů a výběru uživatelů databáze ze zdroje do cíle. 

Nástroj PowerShell automaticky mapuje účty služby Active Directory systému Windows Server na účty služby Azure AD a může provádět hledání hlavního názvu uživatele (UPN) pro každé přihlášení ke zdrojové instanci služby Active Directory. Nástroj skripty vlastní server a databázová role společně s členstvím rolí a uživatelskými oprávněními. Databáze s omezením nejsou zatím podporovány a jsou spouštěny pouze podmnožina možných SQL Server oprávnění. 

### <a name="encryption"></a>Šifrování

Pokud migrujete databáze chráněné  [transparentní šifrování dat](../../database/transparent-data-encryption-tde-overview.md)   do spravované instance pomocí možnosti nativní obnovení, [migrujte odpovídající certifikát](../../managed-instance/tde-certificate-migrate.md) ze zdrojové SQL Server instance do cílové spravované instance SQL *před* obnovením databáze. 

### <a name="system-databases"></a>Systémové databáze

Obnovení systémových databází se nepodporuje. Chcete-li migrovat objekty na úrovni instance (uložené v hlavních databázích a databázi msdb), zapněte je pomocí T-SQL a pak je znovu vytvořte v cílové spravované instanci. 

### <a name="in-memory-oltp-memory-optimized-tables"></a>In-Memory OLTP (paměťově optimalizované tabulky)

SQL Server poskytuje možnost In-Memory OLTP. Umožňuje využití paměťově optimalizovaných tabulek, paměťově optimalizovaných typů tabulek a nativně kompilovaných modulů SQL ke spouštění úloh, které mají požadavky na vysokou propustnost a nízkou latenci pro zpracování transakcí. 

> [!IMPORTANT]
> In-Memory OLTP se podporuje jenom v Pro důležité obchodní informace úrovni ve spravované instanci Azure SQL. Není podporován na úrovni Pro obecné účely.

Pokud máte v místní instanci SQL Server paměťově optimalizované tabulky nebo paměťově optimalizované typy tabulek a chcete migrovat na spravovanou instanci Azure SQL, měli byste provést jednu z těchto akcí:

- Vyberte vrstvu Pro důležité obchodní informace pro vaši cílovou spravovanou instanci SQL, která podporuje In-Memory OLTP.
- Pokud chcete migrovat do Pro obecné účely úrovně ve spravované instanci Azure SQL, odeberte paměťově optimalizované tabulky, paměťově optimalizované typy tabulek a nativně zkompilované moduly SQL, které komunikují s paměťově optimalizovanými objekty před migrací databází. K určení všech objektů, které je třeba před migrací do Pro obecné účely úrovně odebrat, můžete použít následující dotaz T-SQL:

   ```tsql
   SELECT * FROM sys.tables WHERE is_memory_optimized=1
   SELECT * FROM sys.table_types WHERE is_memory_optimized=1
   SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
   ```

Další informace o technologiích v paměti najdete v tématu [optimalizace výkonu pomocí technologií v paměti v Azure SQL Database a spravované instance Azure SQL](../../in-memory-oltp-overview.md).

## <a name="advanced-features"></a>Pokročilé funkce 

Nezapomeňte využít výhod pokročilých cloudových funkcí ve spravované instanci SQL. Například nemusíte se starat o správu záloh, protože služba to dělá za vás. Můžete obnovit k jakémukoli [bodu v čase v rámci doby uchování](../../database/recovery-using-backups.md#point-in-time-restore). Navíc se nemusíte starat o nastavení vysoké dostupnosti, protože [je integrovaná vysoká dostupnost](../../database/high-availability-sla.md). 

Pokud chcete posílit zabezpečení, zvažte použití [ověřování Azure AD](../../database/authentication-aad-overview.md), [auditování](../../managed-instance/auditing-configure.md), [detekce hrozeb](../../database/azure-defender-for-sql.md), [zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security)a [dynamického maskování dat](/sql/relational-databases/security/dynamic-data-masking).

Kromě pokročilých funkcí správy a zabezpečení poskytuje spravovaná instance SQL pokročilé nástroje, které vám pomůžou [monitorovat a ladit vaše úlohy](../../database/monitor-tune-overview.md). [Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md) umožňuje centralizovaným způsobem monitorovat velkou sadu spravovaných instancí.  [Automatické ladění](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   ve spravovaných instancích nepřetržitě monitoruje výkon při provádění plánu SQL a automaticky opravuje zjištěné problémy s výkonem. 

Některé funkce jsou dostupné až po změně [úrovně kompatibility databáze](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) na nejnovější úroveň kompatibility (150). 

## <a name="migration-assets"></a>Prostředky migrace 

Další pomoc najdete v následujících materiálech, které byly vyvinuty pro projekty migrace z reálného světa.

|Prostředek  |Description  |
|---------|---------|
|[Model a nástroj pro vyhodnocení datových úloh](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Tento nástroj nabízí navrženou cílovou platformu "nejlépe vyhovující", připravenost na Cloud a úroveň nápravy aplikace nebo databáze pro zatížení. Nabízí jednoduché výpočetní operace s jedním kliknutím a generování sestav, které pomáhají zrychlit vyhodnocení rozsáhlých nemovitostí poskytnutím automatizovaného a jednotného rozhodovacího procesu pro cílové platformy.|
|[Nástroj DBLoader](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|Pomocí DBLoader můžete načíst data z textových souborů s oddělovači do SQL Server. Tento nástroj konzoly Windows používá SQL Server nativním rozhraním hromadného načtení klienta. Rozhraní funguje na všech verzích SQL Server společně se službou Azure SQL Managed instance.|
|[Nástroj pro přesun místních SQL Server přihlášení do spravované instance Azure SQL](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|PowerShellový skript může vytvořit příkaz skriptu T-SQL pro opětovné vytvoření přihlašovacích údajů a vybrat uživatele databáze z místního SQL Server do spravované instance Azure SQL. Nástroj umožňuje automatické mapování účtů služby Windows Server Active Directory na účty Azure AD a volitelně také migraci SQL Server nativních přihlášení.|
|[Automatizace shromažďování dat Perfmon pomocí programu Logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Nástroj Logman můžete použít ke shromažďování dat o výkonu (které vám pomůžou pochopit základní výkon) a získat doporučení pro cíle migrace. Tento nástroj používá logman.exe k vytvoření příkazu, který bude vytvářet, spouštět, zastavovat a odstraňovat čítače výkonu nastavené na vzdáleném SQL Server instanci.|
|[Migrace databáze do spravované instance Azure SQL obnovením úplného a rozdílového zálohování](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|Tento dokument white paper obsahuje pokyny a postupy, které vám pomůžou zrychlit migrace z SQL Server do Azure SQL Managed instance, pokud máte jenom úplné a rozdílové zálohy (a nemusíte mít možnost zálohování protokolu).|

Data tým SQL Engineering vyvinuli tyto prostředky. Základní Chart týmu je odblokování a urychlení komplexní modernizace pro projekty migrace datové platformy na datovou platformu Azure od Microsoftu.


## <a name="next-steps"></a>Další kroky

- Pokud chcete začít migrovat databáze SQL Server do spravované instance Azure SQL, přečtěte si [příručku SQL Server na Azure SQL Managed instance Průvodce migrací](sql-server-to-managed-instance-guide.md).

- Matrici služeb a nástrojů, které vám pomůžou s scénáři migrace databáze a dat a také speciálními úkoly, najdete v tématu [služby a nástroje pro migraci dat](../../../dms/dms-tools-matrix.md).

- Další informace o spravované instanci Azure SQL najdete tady:
   - [Úrovně služeb ve spravované instanci Azure SQL](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Rozdíly mezi SQL Server a spravovanou instancí Azure SQL](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Kalkulačka celkových nákladů na vlastnictví Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Další informace o cyklu rozhraní a přijetí pro migrace do cloudu najdete tady:
   -  [Cloud Adoption Framework pro Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Osvědčené postupy pro určování nákladů a velikostí úloh migrovaných do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Chcete-li vyhodnotit vrstvu přístupu aplikace, přečtěte si část [Data Access Migration Toolkit (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).

- Podrobnosti o tom, jak provést testování A/B na vrstvě přístupu k datům, najdete v tématu [Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-overview).
