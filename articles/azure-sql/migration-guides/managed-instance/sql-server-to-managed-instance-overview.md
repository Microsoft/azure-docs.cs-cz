---
title: 'SQL Server ke spravované instanci Azure SQL: Přehled migrace'
description: Přečtěte si o různých nástrojích a možnostech, které jsou k dispozici pro migraci SQL Serverch databází do spravované instance Azure SQL.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 02/18/2020
ms.openlocfilehash: a3876b63e9dc41a22ac6e95b31f34665a0d0bdd8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642345"
---
# <a name="migration-overview-sql-server-to-azure-sql-managed-instance"></a>Přehled migrace: SQL Server do spravované instance Azure SQL
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

Přečtěte si o různých možnostech migrace a důležitých informacích o migraci SQL Server do spravované instance Azure SQL. 

Můžete migrovat SQL Server spuštěná místně nebo na: 

- SQL Server na virtuálních počítačích  
- Amazon Web Services (AWS) EC2 
- Služba Amazon relačních databází (AWS RDS) 
- Výpočetní modul (Google Cloud Platform – GCP)  
- Cloud SQL pro SQL Server (Google Cloud Platform – GCP) 

Další příručky k migraci najdete v tématu [migrace databáze](https://docs.microsoft.com/data-migration). 

## <a name="overview"></a>Přehled

[Spravovaná instance Azure SQL](../../managed-instance/sql-managed-instance-paas-overview.md) je doporučovanou možností cíle pro SQL Server úlohy, které vyžadují plně spravovanou službu, aniž by bylo nutné spravovat virtuální počítače nebo jejich operační systémy. Spravovaná instance SQL umožňuje napředt a přesunout vaše místní aplikace do Azure s minimálními změnami aplikace nebo databáze a zároveň dokončit izolaci instancí s nativní podporou virtuální sítě (VNet). 

## <a name="considerations"></a>Požadavky 

Klíčové faktory, které je potřeba vzít v úvahu při hodnocení možností migrace, závisí na: 
- Počet serverů a databází
- Velikost databází
- Přijatelné provozní výpadky během migrace 

Jednou z klíčových výhod migrace SQL serverů do spravované instance SQL je, že se můžete rozhodnout migrovat celou instanci nebo jenom podmnožinu jednotlivých databází. Pečlivě se zamyslete nad tím, že zadáte do procesu migrace následující: 
- Všechny databáze, které je třeba společně umístit do stejné instance 
- Objekty na úrovni instance vyžadované pro vaši aplikaci, včetně přihlašovacích údajů, přihlašovacích údajů, úloh a operátorů SQL agenta a triggerů na úrovni serveru. 

> [!NOTE]
> Spravovaná instance Azure SQL garantuje 99,99% dostupnost i v kritických scénářích, takže režijní náklady způsobené některými funkcemi v SQL MI nejde zakázat. Další informace najdete v tématu [hlavní příčiny, které můžou způsobit jiný výkon na SQL Server a blogu spravované instance SQL Azure](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) . 


## <a name="choose-appropriate-target"></a>Zvolit vhodný cíl

Některé obecné pokyny, které vám pomůžou zvolit správnou úroveň služby a charakteristiky spravované instance SQL, aby se shodovaly s vaším [směrným plánem výkonu](sql-server-to-managed-instance-performance-baseline.md): 

- Pomocí směrného plánu využití CPU zřídíte spravovanou instanci, která odpovídá počtu jader, které vaše instance SQL Server používá. Může být potřeba škálovat prostředky tak, aby odpovídaly [charakteristikám generování hardwaru](../../managed-instance/resource-limits.md#hardware-generation-characteristics). 
- Pomocí směrného plánu využití paměti můžete zvolit [možnost Vcore](../../managed-instance/resource-limits.md#service-tier-characteristics) , která odpovídajícím způsobem odpovídá vašemu přidělení paměti. 
- Použijte latenci v/v podsystému souborů k výběru mezi Pro obecné účely (latence větší než 5 ms) a Pro důležité obchodní informace (latence méně než 3 MS) úrovně služeb. 
- Pomocí propustnosti základní hodnoty můžete předem přidělit velikost dat a souborů protokolu, abyste dosáhli očekávaného vstupně-výstupních operací. 

Můžete zvolit výpočetní prostředky a prostředky úložiště během nasazování a pak [je po použití Azure Portal změnit](../../database/scale-resources.md) , aniž by došlo k výpadku vaší aplikace. 

> [!IMPORTANT]
> Jakékoli nesrovnalosti v [požadavcích virtuální sítě spravované instance](../../managed-instance/connectivity-architecture-overview.md#network-requirements) vám můžou zabránit v vytváření nových instancí nebo používání stávajících instancí. Přečtěte si další informace o [vytváření nových](../../managed-instance/virtual-network-subnet-create-arm-template.md)   a [konfigurování stávajících](../../managed-instance/vnet-existing-add-subnet.md)   sítí. 

Další klíčovým aspektem výběru cílové úrovně služby ve spravované instanci Azure SQL (Pro obecné účely vs Pro důležité obchodní informace) je dostupnost určitých funkcí, jako je In-Memory OLTP, která je dostupná jenom na Pro důležité obchodní informace úrovni. 

### <a name="sql-server-vm-alternative"></a>Alternativní virtuální počítač SQL Server

Vaše firma může mít požadavky, které SQL Server na virtuálních počítačích Azure a lépe vhodný cíl než spravovaná instance Azure SQL. 

Pokud platí následující podmínky pro vaši firmu, zvažte přechod na SQL Server virtuálního počítače: 

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
| [Azure Migrate](../../../migrate/how-to-create-azure-sql-assessment.md) | Azure Migrate pro Azure SQL vám umožní zjišťovat a hodnotit vaše datové úložiště SQL ve velkém měřítku, pokud je na VMware, a poskytuje doporučení pro nasazení SQL Azure, cílovou velikost a měsíční odhady. | 
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md)  | Služba Azure první strany, která podporuje migraci v režimu offline pro aplikace, které můžou během procesu migrace dovolit výpadky. Na rozdíl od nepřetržité migrace v online režimu spustí migrace offline režimu jednorázové obnovení úplné zálohy databáze ze zdroje do cíle. | 
|[Nativní zálohování a obnovení](../../managed-instance/restore-sample-database-quickstart.md) | Spravovaná instance SQL podporuje obnovení nativních záloh databáze SQL Server (soubory. bak), což usnadňuje možnost migrace pro zákazníky, kteří můžou poskytovat úplné zálohy databází do Azure Storage. Úplné a rozdílové zálohy se také podporují a zdokumentují v [části assety migrace](#migration-assets) dále v tomto článku.| 
|[Služba pro opětovné přehrání protokolů (LRS)](../../managed-instance/log-replay-service-migrate.md) | Toto je cloudová služba povolená pro spravovanou instanci založenou na technologii SQL Server přenosů protokolů. díky tomu je možnost migrace pro zákazníky, kteří můžou poskytovat úplné, rozdílové a zaprotokolované zálohy databáze do služby Azure Storage. LRS se používá k obnovení záložních souborů z Azure Blob Storage do spravované instance SQL.| 
| | |

### <a name="alternative-tools"></a>Alternativní nástroje

V následující tabulce jsou uvedeny alternativní nástroje pro migraci: 

|**Technologie** |**Popis**  |
|---------|---------|
|[Transakční replikace](../../managed-instance/replication-transactional-overview.md) | Umožňuje replikovat data ze zdrojových databázových tabulek SQL Server do spravované instance SQL poskytnutím možnosti migrace typu předplatitele vydavatele při zachování konzistentní transakční konzistence. | 
|[Hromadná kopie](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| [Nástroj program pro hromadné kopírování (BCP)](/sql/tools/bcp-utility) kopíruje data z instance SQL Server do datového souboru. Pomocí nástroje BCP exportujte data ze zdroje a importujte datový soubor do cílové spravované instance SQL.</br></br> Pro rychlé operace hromadného kopírování pro přesun dat na Azure SQL Database se dá použít [Nástroj pro inteligentní kopírování](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) , který umožňuje maximalizovat rychlost přenosu tím, že využívá úlohy paralelního kopírování. | 
|[Průvodce exportem a BACPAC importu](../../database/database-import.md?tabs=azure-powershell)| [BacPac](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) je soubor Windows s `.bacpac` příponou, která zapouzdřuje schéma a data databáze. BACPAC se dá použít k exportu dat ze zdrojového SQL Server a k importu tohoto souboru zpátky do spravované instance Azure SQL.  |  
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-managed-instance.md)| [Aktivita kopírování](../../../data-factory/copy-activity-overview.md) v Azure Data Factory migruje data ze zdrojového SQL Server databáze do spravované instance SQL pomocí integrovaných konektorů a [Integration runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> ADF podporuje široké spektrum [konektorů](../../../data-factory/connector-overview.md) pro přesun dat z SQL Server zdrojů do spravované instance SQL. |
| | |

## <a name="compare-migration-options"></a>Porovnat možnosti migrace

Porovnejte možnosti migrace a vyberte cestu odpovídající vašim obchodním potřebám. 

### <a name="recommended-options"></a>Doporučené možnosti

Následující tabulka porovnává Doporučené možnosti migrace: 

|Možnost migrace  |Kdy je použít  |Požadavky  |
|---------|---------|---------|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md) | – Migrujte jednotlivé databáze nebo více databází se škálováním. </br> – Může během procesu migrace pojmout výpadky. </br> </br> Podporované zdroje: </br> -SQL Server (2005-2019) místní nebo Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP COMPUTE SQL Server virtuální počítač |  – Migrace ve velkém měřítku může být automatizovaná přes [PowerShell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). </br> -Doba k dokončení migrace závisí na velikosti databáze a ovlivněná časem zálohování a obnovení. </br> – Může být vyžadováno dostatečné výpadky. |
|[Nativní zálohování a obnovení](../../managed-instance/restore-sample-database-quickstart.md) | – Migrujte jednotlivé databáze obchodních aplikací.  </br> – Rychlá a snadná migrace bez samostatné služby nebo nástroje pro migraci  </br> </br> Podporované zdroje: </br> -SQL Server (2005-2019) místní nebo Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP COMPUTE SQL Server virtuální počítač | -Záloha databáze používá více vláken pro optimalizaci přenosu dat do služby Azure Blob Storage, ale šířka pásma a velikost databáze ISV může ovlivnit přenosovou rychlost. </br> -Výpadky by měly pojmout dobu potřebnou k provedení úplného zálohování a obnovení (což je velikost operace s daty).| 
|[Služba pro opětovné přehrání protokolů (LRS)](../../managed-instance/log-replay-service-migrate.md) | – Migrujte jednotlivé databáze obchodních aplikací.  </br> – Pro migrace databáze je potřeba více ovládacích prvků.  </br> </br> Podporované zdroje: </br> -SQL Server (2008-2019) místní nebo Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP COMPUTE SQL Server virtuální počítač | – Migrace zahrnuje vytvoření úplných záloh databáze při SQL Server a zkopírování záložních souborů do Azure Blob Storage. LRS se používá k obnovení záložních souborů z Azure Blob Storage do spravované instance SQL. </br> – Databáze obnovované během procesu migrace budou v režimu obnovení a nelze je použít ke čtení nebo zápisu do dokončení procesu.| 
| | | |

### <a name="alternative-options"></a>Alternativní možnosti

Následující tabulka porovnává alternativní možnosti migrace: 

|Metoda/technologie |Kdy je použít |Požadavky  |
|---------|---------|---------|
|[Transakční replikace](../../managed-instance/replication-transactional-overview.md) | – Migrujete průběžně po publikování změn z tabulek zdrojové databáze do cílové tabulky databáze spravované instance SQL. </br> – Úplná nebo částečná migrace databází z vybraných tabulek (podmnožina databáze).  </br> </br> Podporované zdroje: </br> -SQL Server (2012-2019) s některými omezeními </br> – AWS EC2  </br> – GCP COMPUTE SQL Server virtuální počítač | </br> – Instalace je poměrně složitá v porovnání s dalšími možnostmi migrace.   </br> – Poskytuje možnost průběžné replikace pro migraci dat (aniž byste museli přenášet databáze do režimu offline).</br> -Transakční replikace má několik omezení, která je potřeba vzít v úvahu při nastavování vydavatele na zdrojovém SQL Server. Další informace najdete v tématu [omezení pro publikování objektů](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) .  </br> – K dispozici je možnost [monitorování aktivity replikace](/sql/relational-databases/replication/monitor/monitoring-replication) .    |
|[Hromadná kopie](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| – Migrace úplných nebo částečných migrací dat. </br> – Může vyhovovat výpadkům. </br> </br> Podporované zdroje: </br> -SQL Server (2005-2019) místní nebo Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP COMPUTE SQL Server virtuální počítač   | – Vyžaduje prostoje při exportu dat ze zdroje a importu do cíle. </br> – Formáty souborů a datové typy používané při exportu/importu musí být konzistentní se schématy tabulek. |
|[Průvodce exportem a BACPAC importu](../../database/database-import.md)| – Migrujte jednotlivé databáze obchodních aplikací. </br>– Hodí se pro menší databáze.  </br>  Nevyžaduje samostatnou službu nebo nástroj pro migraci. </br> </br> Podporované zdroje: </br> -SQL Server (2005-2019) místní nebo Azure VM </br> – AWS EC2 </br> – AWS RDS </br> – GCP COMPUTE SQL Server virtuální počítač  |   </br> – Vyžaduje prostoje, protože data je třeba exportovat ve zdroji a importovat do cílového umístění.   </br> – Formáty souborů a datové typy používané při exportu a importu musí být konzistentní se schématy tabulek, aby se předešlo chybám zkrácení/neshody datových typů. </br> – Doba potřebná k exportu databáze s velkým počtem objektů může být výrazně vyšší. |
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-managed-instance.md)| – Migrace a transformace dat ze zdrojových SQL Server databází.</br> – Slučují se data z více zdrojů dat do spravované instance Azure SQL, obvykle pro úlohy Business Intelligence (BI).   </br> – Vyžaduje vytvoření kanálů přesunu dat v ADF pro přesun dat ze zdroje do cíle.   </br> - [Náklady](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) jsou důležitým aspektem a vycházejí z aktivačních událostí kanálu, spuštění aktivit, doby trvání přesunu dat atd. |
| | | |

## <a name="feature-interoperability"></a>Interoperabilita funkcí 

Při migraci úloh, které spoléhají na jiné funkce SQL Server, jsou potřeba další okolnosti. 

#### <a name="sql-server-integration-services"></a>Služba SSIS (SQL Server Integration Services)

Migrujte balíčky služba SSIS (SQL Server Integration Services) (SSIS) a projekty v SSISDB do spravované instance Azure SQL pomocí [Azure Database Migration Service (DMS)](../../../dms/how-to-migrate-ssis-packages-managed-instance.md). 

Pro migraci se podporují jenom balíčky SSIS v SSISDB, které začínají na SQL Server 2012. Před migrací převeďte starší balíčky SSIS. Další informace najdete v [kurzu převod projektu](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model) . 


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

Sestavy SQL Server Reporting Services (SSRS) je možné migrovat na stránkované sestavy v Power BI. Pomocí [Nástroje pro migraci RDL](https://github.com/microsoft/RdlMigration) můžete připravovat a migrovat sestavy. Tento nástroj vyvinul Microsoft, aby zákazníkům usnadnil migraci sestav RDL ze serverů SSRS do Power BI. Je k dispozici na GitHubu a dokumentuje kompletní návod k migračnímu scénáři. 

#### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

Služba Analysis Services serveru SQL tabulkové modely z SQL Server 2012 a novějších se dají migrovat do Azure Analysis Services, což je model nasazení PaaS pro Analysis Services tabelární model v Azure. Další informace o migraci Prem modelů a jejich Azure Analysis Services najdete v tomto [výukovém kurzu](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/).

Případně můžete také zvážit migraci místních Analysis Services tabelárních modelů do [Power BI Premium pomocí nových koncových bodů pro čtení a zápis XMLA](/power-bi/admin/service-premium-connect-tools). 
> [!NOTE]
> Funkce koncových bodů pro čtení a zápis pro Power BI XMLA je aktuálně v Public Preview a neměla by se považovat za produkční úlohy, dokud nebudou tyto funkce všeobecně dostupné.

#### <a name="high-availability"></a>Vysoká dostupnost

Funkce vysoké dostupnosti SQL Server vždy na instancích clusteru s podporou převzetí služeb při selhání a skupiny dostupnosti Always On se na cílové spravované instanci Azure SQL stanou zastaralými, protože architektura vysoké dostupnosti je už integrovaná do obou [pro obecné účely (standardní model dostupnosti)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) a [pro důležité obchodní informace (model dostupnosti Premium)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) spravované instance SQL. Model dostupnosti Premium také poskytuje škálování na více instancí, které umožňuje připojení k jednomu ze sekundárních uzlů pro účely jen pro čtení.     

Mimo architekturu s vysokou dostupností, která je součástí spravované instance SQL, existuje také funkce [skupin s automatickým převzetím služeb při selhání](../../database/auto-failover-group-overview.md) , která umožňuje spravovat replikaci a převzetí služeb při selhání databáze ve spravované instanci do jiné oblasti. 

#### <a name="sql-agent-jobs"></a>Úlohy agenta SQL

K migraci [úloh agenta SQL](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)použijte možnost DMS (offline Azure Database Migration Service). V opačném případě skriptujte úlohy v jazyce Transact-SQL (T-SQL) pomocí SQL Server Management Studio a pak je ručně znovu vytvořte na cílové spravované instanci SQL. 

> [!IMPORTANT]
> Azure DMS v současné době podporuje jenom úlohy s kroky v subsystému T-SQL. Úlohy s SSIS kroky balíčku se musí migrovat ručně. 

#### <a name="logins-and-groups"></a>Přihlášení a skupiny

Přihlášení SQL ze zdrojového SQL Server můžete přesunout do spravované instance Azure SQL pomocí Database Migration Service (DMS) v offline režimu.  V **Průvodci migrací** použijte okno **[Vybrat přihlášení](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins)** a migrujte přihlášení do cílové spravované instance SQL. 

Ve výchozím nastavení Azure Database Migration Service podporuje pouze migraci přihlašovacích údajů SQL. Můžete ale povolit možnost migrovat přihlášení Windows pomocí:

Zajistěte, aby cílová instance SQL Managed instance měla přístup ke čtení Azure AD, který je možné nakonfigurovat prostřednictvím Azure Portal uživatelem s rolí **globálního správce** .
Konfigurace instance Azure Database Migration Service, aby se povolily migrace přihlášení uživatelů nebo skupin systému Windows, které se nastaví přes Azure Portal, na stránce konfigurace. Po povolení tohoto nastavení restartujte službu, aby se změny projevily.

Po restartování služby se zobrazí přihlášení uživatele nebo skupiny systému Windows v seznamu přihlášení, která jsou k dispozici pro migraci. U všech přihlášení uživatelů nebo skupin systému Windows, které migrujete, budete vyzváni k zadání přidruženého názvu domény. Uživatelské účty služby (účet s názvem domény NT AUTHORITY) a virtuální uživatelské účty (název účtu s názvem domény NT) nejsou podporovány.

Další informace najdete v tématu [Postup migrace uživatelů a skupin Windows v instanci SQL Server do spravované instance Azure SQL pomocí T-SQL](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md).

Alternativně můžete použít [Nástroj PowerShell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) , který je speciálně navržený architekty migrace dat Microsoftu. Nástroj používá PowerShell k vytvoření skriptu T-SQL pro opětovné vytvoření přihlašovacích údajů a výběru uživatelů databáze ze zdroje do cíle. Nástroj automaticky mapuje účty služby Windows AD na účty Azure AD a může provádět vyhledávání UPN pro každé přihlášení ke zdrojové službě Active Directory. Nástroj skripty vlastní server a databázová role a také členství v rolích, databázová role a uživatelská oprávnění. Databáze s omezením nejsou aktuálně podporovány a jsou spouštěny pouze podmnožina možných SQL Server oprávnění. 

#### <a name="encryption"></a>Šifrování

Při migraci databází chráněných nástrojem  [transparentní šifrování dat](../../database/transparent-data-encryption-tde-overview.md)   do spravované instance pomocí možnosti nativní obnovení [migrujte odpovídající certifikát](../../managed-instance/tde-certificate-migrate.md) ze zdrojového SQL Server do cílové spravované instance SQL *před* obnovením databáze. 

#### <a name="system-databases"></a>Systémové databáze

Obnovení systémových databází se nepodporuje. Chcete-li migrovat objekty na úrovni instance (uložené v databázích Master nebo msdb), skriptujte je pomocí jazyka Transact-SQL (T-SQL) a pak je znovu vytvořte na cílové spravované instanci. 

#### <a name="in-memory-oltp-memory-optimized-tables"></a>In-Memory OLTP (paměťově optimalizované tabulky)

SQL Server poskytuje In-Memory OLTP schopnost, která umožňuje využití paměťově optimalizovaných tabulek, paměťově optimalizovaných typů tabulek a nativně kompilovaných modulů SQL ke spouštění úloh, které mají požadavky na zpracování s vysokou propustností a nízkou latencí. 

> [!IMPORTANT]
> In-Memory OLTP se podporuje jenom v Pro důležité obchodní informace úrovni ve spravované instanci Azure SQL (a není podporovaná na úrovni Pro obecné účely).

Pokud máte v místních SQL Serverch paměťově optimalizované tabulky nebo paměťově optimalizované typy tabulek a chcete migrovat na spravovanou instanci Azure SQL, měli byste buď:

- Vyberte úroveň Pro důležité obchodní informace pro vaši cílovou spravovanou instanci SQL Azure, která podporuje In-Memory OLTP, nebo
- Pokud chcete migrovat na Pro obecné účely úroveň ve spravované instanci Azure SQL, odeberte paměťově optimalizované tabulky, paměťově optimalizované typy tabulek a nativně zkompilované moduly SQL, které komunikují s paměťově optimalizovanými objekty před migrací databází. K identifikaci všech objektů, které je třeba před migrací do Pro obecné účely úrovně, použít následující dotaz T-SQL:

```tsql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

Další informace o technologiích v paměti najdete v tématu [optimalizace výkonu pomocí technologií v paměti v Azure SQL Database a spravované instance Azure SQL](../../in-memory-oltp-overview.md) .

## <a name="leverage-advanced-features"></a>Využití pokročilých funkcí 

Nezapomeňte využít výhod pokročilých cloudových funkcí nabízených službou SQL Managed instance. Například už se nemusíte starat o správu záloh, protože služba to udělá za vás. Můžete obnovit k jakémukoli [bodu v čase v rámci doby uchování](../../database/recovery-using-backups.md#point-in-time-restore). Kromě toho se nemusíte starat o nastavení vysoké dostupnosti, protože [je integrovaná vysoká dostupnost](../../database/high-availability-sla.md). 

Pokud chcete posílit zabezpečení, zvažte použití [Azure Active Directory ověřování](../../database/authentication-aad-overview.md), [auditování](../../managed-instance/auditing-configure.md), [detekce hrozeb](../../database/azure-defender-for-sql.md), [zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security)a [dynamického maskování dat](/sql/relational-databases/security/dynamic-data-masking).

Kromě pokročilých funkcí správy a zabezpečení poskytuje spravovaná instance SQL sadu pokročilých nástrojů, které vám pomůžou [monitorovat a ladit vaše úlohy](../../database/monitor-tune-overview.md). [Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md) umožňuje centralizovaným způsobem monitorovat velkou sadu spravovaných instancí.  [Automatické ladění](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   ve spravovaných instancích nepřetržitě monitoruje výkon statistik spuštění plánu SQL a automaticky opravuje zjištěné problémy s výkonem. 

Některé funkce jsou dostupné až po změně [úrovně kompatibility databáze](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) na nejnovější úroveň kompatibility (150). 

## <a name="migration-assets"></a>Prostředky migrace 

Další pomoc najdete v následujících materiálech, které byly vyvinuty pro projekty z reálného světa migrace.

|Prostředek  |Description  |
|---------|---------|
|[Model a nástroj pro vyhodnocení datových úloh](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Tento nástroj poskytuje navrženou cílovou platformu "nejlépe vyhovující", připravenost na Cloud a úroveň nápravy aplikace nebo databáze pro danou úlohu. Nabízí jednoduché výpočetní operace s jedním kliknutím a generování sestav, které pomáhají zrychlit vyhodnocení velkých nemovitostí tím, že zajišťují a automatizují a automatizují rozhodovací procesy na základě cílové platformy.|
|[Nástroj DBLoader](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|DBLoader lze použít k načtení dat z textových souborů s oddělovači do SQL Server. Tento nástroj konzoly Windows používá rozhraní SQL Server BulkLoad Native Client, které funguje na všech verzích SQL Server, včetně Azure SQL MI.|
|[Nástroj pro přesun místních SQL Server přihlášení do spravované instance Azure SQL](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Skript PowerShellu, který vytvoří skript příkazového řádku T-SQL pro opětovné vytvoření přihlašovacích údajů a výběr uživatelů databáze z místní SQL Server do spravované instance Azure SQL. Nástroj umožňuje automatické mapování účtů služby Windows AD na účty Azure AD a volitelně také migraci SQL Server nativních přihlášení.|
|[Automatizace shromažďování dat Perfmon pomocí programu Logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Nástroj, který shromažďuje data, aby porozuměl základnímu výkonu, který pomáhá s doporučením cíle migrace. Tento nástroj, který používá logman.exe k vytvoření příkazu, který bude vytvářet, spouštět, zastavovat a odstraňovat čítače výkonu nastavené na vzdáleném SQL Server.|
|[Dokument White Paper – migrace databáze do spravované instance Azure SQL obnovením úplných a rozdílných záloh](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|Tento dokument white paper obsahuje pokyny a postupy, které vám pomůžou zrychlit migrace z SQL Server do spravované instance Azure SQL, pokud máte jenom úplné a rozdílové zálohy (a nemusíte mít možnost zálohování protokolu).|

Data tým SQL Engineering vyvinuli tyto prostředky. Základní Chart týmu je odblokování a urychlení komplexní modernizace pro projekty migrace datové platformy na datovou platformu Azure od Microsoftu.


## <a name="next-steps"></a>Další kroky

Pokud chcete začít migrovat SQL Server do spravované instance Azure SQL, přečtěte si [příručku SQL Server na Azure SQL Managed instance Průvodce migrací](sql-server-to-managed-instance-guide.md).

- Matrici služeb a nástrojů společnosti Microsoft, které jsou k dispozici, aby vám pomohla při různých scénářích databáze a migrace dat i v speciálních úlohách, najdete v tématu [služba a nástroje pro migraci dat](../../../dms/dms-tools-matrix.md).

- Další informace o spravované instanci Azure SQL najdete tady:
   - [Úrovně služeb ve spravované instanci Azure SQL](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Rozdíly mezi SQL Server a spravovanou instancí Azure SQL](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Kalkulačka celkových nákladů na vlastnictví Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Další informace o cyklu rozhraní a přijetí pro migrace do cloudu najdete v tématu.
   -  [Cloud Adoption Framework pro Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Osvědčené postupy pro výpočet nákladů a úloh migrace do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Postup vyhodnocení vrstvy přístupu k aplikaci najdete v tématu [sada Data Access Migration Toolkit (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) .
- Podrobnosti o tom, jak provádět testování vrstvy přístupu k datům A/B, najdete [Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-overview).