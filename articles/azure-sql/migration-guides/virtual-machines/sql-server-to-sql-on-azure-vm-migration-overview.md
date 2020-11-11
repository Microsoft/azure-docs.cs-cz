---
title: SQL Server SQL Server na virtuálním počítači Azure (Přehled migrace)
description: Přečtěte si o různých strategiích migrace, pokud chcete SQL Server migrovat do SQL Server na virtuálních počítačích Azure.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: ''
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 134685f56de569b39e28165d7ed25868300810f5
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496906"
---
# <a name="migration-overview-sql-server-to-sql-server-on-azure-vms"></a>Přehled migrace: SQL Server pro SQL Server na virtuálních počítačích Azure
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Přečtěte si o různých strategiích migrace za účelem migrace SQL Server na SQL Server na Azure Virtual Machines (virtuálních počítačích). 

Můžete migrovat SQL Server spuštěná místně nebo na:

- SQL Server na virtuálních počítačích  
- Amazon Web Services (AWS) EC2 
- Služba Amazon relačních databází (AWS RDS) 
- Výpočetní modul (Google Cloud Platform – GCP)

Další scénáře najdete v [Průvodci migrací databáze](https://datamigration.microsoft.com/). 

## <a name="overview"></a>Přehled

Migrujte na [SQL Server na Azure Virtual Machines (virtuálních počítačů)](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview) , pokud chcete používat známé SQL Server prostředí s operačním systémem, a chcete využít výhod funkcí poskytovaných cloudem, jako je integrovaná virtuální počítač s vysokou dostupností, [automatizované zálohování](../../virtual-machines/windows/automated-backup.md)a [automatizované opravy](../../virtual-machines/windows/automated-patching.md). 

Získáním vlastní licence s [modelem zvýhodněné hybridní využití Azure licencování](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md) můžete ušetřit náklady tím, že získáte [bezplatné aktualizace zabezpečení](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)SQL Server 2008 a SQL Server 2008 R2. 


## <a name="choosing-appropriate-target"></a>Výběr vhodného cíle

Azure Virtual Machines běžet v mnoha různých oblastech Azure a nabízí také nejrůznější [velikosti počítačů](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) a [Možnosti úložiště](https://docs.microsoft.com/azure/virtual-machines/disks-types). Při určování správné velikosti virtuálního počítače a úložiště pro SQL Server úlohy, přečtěte si [pokyny pro výkon SQL Server v Azure Virtual Machines.](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices#vm-size-guidance) K určení velikosti virtuálního počítače a požadavků na úložiště pro vaše zatížení. doporučuje se, aby byly velikosti prostřednictvím Performance-Based [Azure Migrate posouzení](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#types-of-assessments). Pokud tato možnost není dostupná, přečtěte si následující článek o vytvoření vlastního [směrného plánu pro výkon](https://azure.microsoft.com/services/virtual-machines/sql-server/).

Je taky potřeba zvážit správnou instalaci a konfiguraci SQL Server na virtuálním počítači. Doporučuje se použít [galerii imagí virtuálních počítačů Azure SQL](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/create-sql-vm-portal) , protože umožňuje vytvořit virtuální počítač s SQL Server se správnou verzí, edicí a operačním systémem. Tím se také zaregistruje virtuální počítač Azure s SQL Server [poskytovatelem prostředků](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/create-sql-vm-portal) automaticky a povolí se funkce, jako je automatické zálohování a automatizované opravy.

## <a name="migration-strategies"></a>Strategie migrace

Existují dvě strategie migrace, které slouží k migraci uživatelských databází do instance SQL Server na virtuálních počítačích Azure: **migrace** a **navýšení a posunutí**. 

Vhodný přístup k vašemu podniku obvykle závisí na následujících faktorech: 

- Velikost a škálování migrace
- Rychlost migrace
- Podpora aplikací pro změnu kódu
- Je potřeba změnit SQL Server verzi, operační systém nebo obojí.
- Životní cyklus podpory vašich stávajících produktů
- Okno pro výpadek aplikace během migrace

Následující tabulka popisuje rozdíly ve dvou strategiích migrace:
<br />

| **Strategie migrace** | **Popis** | **Kdy je použít** |
| --- | --- | --- |
| **Přezvednutí & Shift** | Pomocí strategie migrace navýšení a posunutí můžete celý fyzický nebo virtuální SQL Server z jeho aktuálního umístění přesunout do instance SQL Server na VIRTUÁLNÍm počítači Azure bez jakýchkoli změn v operačním systému nebo verze SQL Server. Chcete-li dokončit migraci výtahu a posunutí, přečtěte si téma [Azure Migrate](../../../migrate/migrate-services-overview.md). <br /><br /> Zdrojový server zůstává online a služby, i když zdrojový a cílový server synchronizuje data a umožňuje téměř bezproblémové migrace. | Používejte pro vysoce rozsáhlé migrace, a to i v případě scénářů, jako je třeba ukončení datového centra. <br /><br /> Minimální a žádné změny kódu, které jsou potřeba pro uživatelské databáze nebo aplikace SQL, umožňují rychlejší celkové migrace. <br /><br />Migrace služeb Business Intelligence, jako jsou  [SSIS](/sql/integration-services/sql-server-integration-services), [SSRS](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports)a [SSAS](/analysis-services/analysis-services-overview), nevyžaduje žádné další kroky. |
|**Migrate** | Pokud chcete upgradovat cílovou SQL Server nebo verzi operačního systému, použijte strategii migrace. <br /> <br /> Vyberte virtuální počítač Azure z Azure Marketplace nebo připravený SQL Server Image, která odpovídá zdrojové SQL Server verzi. | Použijte v případě, že máte požadavek nebo si přejete použít funkce dostupné v novějších verzích SQL Server, nebo pokud je potřeba upgradovat starší verze SQL Server nebo operačních systémů, které už nejsou podporovány.  <br /> <br /> Může vyžadovat, aby se některé aplikace nebo uživatelské databáze změnily na podporu SQL Server upgradu. <br /><br />Při migraci služeb [Business Intelligence](#business-intelligence) v oboru migrace můžou být potřeba další informace. |


## <a name="lift-and-shift"></a>Metoda „lift and shift“  

V následující tabulce najdete podrobnosti o dostupné metodě pro migrační strategii migrace **a posunutí** pro migraci databáze SQL Server do SQL Server na virtuálních počítačích Azure:
<br />

|**Metoda** | **Minimální zdrojová verze** | **Minimální cílová verze** | **Omezení velikosti zdrojové zálohy** |  **Poznámky** |
| --- | --- | --- | --- | --- |
| [Azure Migrate](../../../migrate/index.yml) | SQL Server 2008 SP4| SQL Server 2008 SP4| [Limit úložiště virtuálních počítačů Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  Existující SQL Server k přesunu jako instance SQL Server na virtuálním počítači Azure. Může škálovat úlohy migrace až na 35 000 virtuálních počítačů. <br /><br /> Zdrojový server (y) zůstávají online a obsluhují požadavky během synchronizace dat serveru a minimalizují prostoje. <br /><br /> **Automatizace & skriptování** : [Azure Site Recovery skriptů](../../../migrate/how-to-migrate-at-scale.md) a [příkladem migrace na škálované a naplánování pro Azure](/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)|

## <a name="migrate"></a>Migrate  

Vzhledem k usnadnění instalace je doporučený postup pro migraci v místním prostředí SQL Server [zálohování](/sql/t-sql/statements/backup-transact-sql) a následně zkopírování souboru do Azure. Tato metoda podporuje větší databáze (>1 TB) pro všechny verze SQL Server počínaje 2008 a větším zálohováním databáze (>1 TB). Pro databáze od SQL Server 2014, které jsou menší než 1 TB, ale mají dobré připojení k Azure, je lepší přístup [SQL Server zálohování na adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) . 

Při migraci SQL Server databází do instance služby SQL Server na virtuálních počítačích Azure je důležité zvolit přístup, který bude vyhovovat tomu, co se přímou migraci na cílový server, protože to ovlivní okno prostoje aplikace.

Následující tabulka podrobně popisuje všechny dostupné metody migrace databáze SQL Server do SQL Server na virtuálních počítačích Azure:
<br />

|**Metoda** | **Minimální zdrojová verze** | **Minimální cílová verze** | **Omezení velikosti zdrojové zálohy** | **Poznámky** |
| --- | --- | --- | --- | --- |
| **[Zálohování do souboru](/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore)** | SQL Server 2008 SP4 | SQL Server 2008 SP4| [Limit úložiště virtuálních počítačů Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  Toto je jednoduchá a dobře Testovaná metoda pro přesouvání databází napříč počítači. K minimalizaci velikosti záložního přenosu použijte kompresi. <br /><br /> **Automatizace & skriptování** : [Transact-SQL (T-SQL)](/sql/t-sql/statements/backup-transact-sql) a [AzCopy do úložiště objektů BLOB](../../../storage/common/storage-use-azcopy-v10.md)  |
| **[Záloha na adresu URL](/windows/migrate-to-vm-from-sql-server.md#backup-to-url-and-restore-from-url)** | SQL Server 2012 SP1 CU2 | SQL Server 2012 SP1 CU2| 12,8 TB pro SQL Server 2016, jinak 1 TB | Alternativní způsob, jak přesunout záložní soubor do virtuálního počítače pomocí služby Azure Storage. K minimalizaci velikosti záložního přenosu použijte kompresi. <br /><br /> **Automatizace & skriptování** :  [plán T-SQL nebo údržba](/sql/relational-databases/backup-restore/sql-server-backup-to-url) |
| **[Pomocník s migrací databáze (DMA)](/sql/dma/dma-overview)** | SQL Server 2005| SQL Server 2008 SP4| [Limit úložiště virtuálních počítačů Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  [DMA](/sql/dma/dma-overview) posuzuje SQL Server v místním prostředí a pak hladce upgraduje na novější verze SQL Server nebo SQL Server migruje na virtuální počítače azure, Azure SQL Database nebo Azure SQL Managed instance. <br /><br /> Neměl by se používat v uživatelských databázích s podporou FILESTREAM.<br /><br /> Přímý přístup do paměti (DMA) zahrnuje i možnost migrace [přihlašovacích údajů SQL a Windows](/sql/dma/dma-migrateserverlogins) a vyhodnocení [balíčků SSIS](/sql/dma/dma-assess-ssis). <br /><br /> **Automatizace & skriptování** : [rozhraní příkazového řádku](/sql/dma/dma-commandline) |
| **[Odpojit a připojit](/windows/migrate-to-vm-from-sql-server.md#detach-and-attach-from-a-url)** | SQL Server 2008 SP4 | SQL Server 2014 | [Limit úložiště virtuálních počítačů Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Tuto metodu použijte, když plánujete [ukládat tyto soubory pomocí služby Azure Blob Storage](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) a připojit je k instanci SQL Server na virtuálním počítači Azure, zvláště užitečné u velmi rozsáhlých databází nebo v případě, že je čas zálohování a obnovení příliš dlouhý. <br /><br /> **Automatizace & skriptování** :  [T-SQL](/sql/relational-databases/databases/detach-a-database#TsqlProcedure) a [AzCopy do úložiště objektů BLOB](../../../storage/common/storage-use-azcopy-v10.md)|
|**[Přesouvání protokolu](/sql/database-engine/log-shipping/about-log-shipping-sql-server)** | SQL Server 2008 SP4 (jenom Windows) | SQL Server 2008 SP4 (jenom Windows) | [Limit úložiště virtuálních počítačů Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Přenos protokolů replikuje transakční soubory protokolu z místního prostředí do instance SQL Server na virtuálním počítači Azure. <br /><br /> To zajišťuje minimální výpadky během převzetí služeb při selhání a má méně režijních nákladů na konfiguraci než nastavení skupiny dostupnosti Always On. <br /><br /> **Automatizace & skriptování** : [T-SQL](/sql/database-engine/log-shipping/log-shipping-tables-and-stored-procedures)  |
| **[Distribuovaná skupina dostupnosti](/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)** | SQL Server 2016| SQL Server 2016 | [Limit úložiště virtuálních počítačů Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  [Distribuovaná Skupina dostupnosti](/sql/database-engine/availability-groups/windows/distributed-availability-groups) je speciální typ skupiny dostupnosti, která zahrnuje dvě samostatné skupiny dostupnosti. Skupiny dostupnosti, které se účastní distribuované skupiny dostupnosti, nemusí být ve stejném umístění a zahrnovat podporu mezi doménami. <br /><br /> Tato metoda minimalizuje výpadek, používá se, pokud máte skupinu dostupnosti nakonfigurovanou místně. <br /><br /> **Automatizace & skriptování** : [T-SQL](/sql/t-sql/statements/alter-availability-group-transact-sql)  |
| | | | | |

&nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;
 
> [!TIP]
> Přenos velkých objemů dat s omezeným počtem možností sítě najdete v tématu [přenos velkých objemů dat s omezeným připojením](../../../storage/common/storage-solution-large-dataset-low-network.md).
> 

### <a name="considerations"></a>Požadavky

V následujícím seznamu jsou klíčové body, které je potřeba vzít v úvahu při kontrole metod migrace:

- Pro optimální výkon přenosu dat migrujte databáze a soubory do instance SQL Server na virtuálním počítači Azure pomocí komprimovaného záložního souboru. U větších databází kromě komprese [rozdělte záložní soubor na menší soubory](/sql/relational-databases/backup-restore/back-up-files-and-filegroups-sql-server) pro zvýšení výkonu při zálohování a přenosu. 
- Pokud migrujete z SQL Server 2014 nebo vyšší, zvažte [šifrování záloh](/sql/relational-databases/backup-restore/backup-encryption) , abyste chránili data během přenosu v síti.
- Pokud chcete během migrace databáze minimalizovat prostoje, použijte možnost Skupina dostupnosti Always On. 
- Chcete-li minimalizovat prostoje bez režie konfigurace skupiny dostupnosti, použijte možnost přesouvání protokolu. 
- V případě omezeného počtu možností sítě použijte offline metody migrace, jako je zálohování a obnovení, nebo [služby přenosu disků](../../../storage/common/storage-solution-large-dataset-low-network.md) dostupné v Azure.
- Pokud chcete také změnit verzi SQL Server v SQL Server na virtuálním počítači Azure, přečtěte si téma [Změna verze SQL Server Edition](../../virtual-machines/windows/change-sql-server-edition.md).

## <a name="business-intelligence"></a>Business Intelligence 

Při migraci SQL Server služeb Business Intelligence mimo rozsah migrace uživatelských databází může docházet k dalším hlediskům. 

Mezi tyto služby patří:

- [**Služba SSIS (SQL Server Integration Services)**](/sql/integration-services/install-windows/upgrade-integration-services)
- [**SQL Server Reporting Services (SSRS)**](/sql/reporting-services/install-windows/upgrade-and-migrate-reporting-services)
- [**SQL Server Analysis Services (SSAS)**](/sql/database-engine/install-windows/upgrade-analysis-services)

## <a name="supported-versions"></a>Podporované verze

Při přípravě na migraci SQL Server databází SQL Server na virtuálních počítačích Azure nezapomeňte zvážit podporované verze SQL Server. Seznam aktuálních podporovaných SQL Server verzí na virtuálních počítačích Azure najdete v [SQL Server na virtuálních počítačích Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms).


## <a name="next-steps"></a>Další kroky

Pokud chcete začít migrovat databáze SQL Server a SQL Server na virtuálních počítačích Azure, Projděte si [příručku pro migraci jednotlivých databází](sql-server-to-sql-on-azure-vm-individual-databases-guide.md). 

- Matrici služeb a nástrojů společnosti Microsoft, které jsou k dispozici, aby vám pomohla při různých scénářích databáze a migrace dat a speciálních úlohách, najdete v článku [služba a nástroje pro migraci dat.](../../../dms/dms-tools-matrix.md)

- Další informace o Azure SQL najdete tady:
   - [Možnosti nasazení](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server na virtuálních počítačích Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Kalkulačka celkových nákladů na vlastnictví Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Další informace o cyklu rozhraní a přijetí pro migrace do cloudu najdete v tématu.
   -  [Cloud Adoption Framework pro Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Osvědčené postupy pro výpočet nákladů a úloh migrace do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Informace o licencování najdete v tématu.
   - [Využijte vlastní licenci s Zvýhodněné hybridní využití Azure](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Získejte bezplatnou rozšířenou podporu pro SQL Server 2008 a SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Postup vyhodnocení vrstvy přístupu k aplikaci najdete v tématu [sada Data Access Migration Toolkit (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) .
- Podrobnosti o tom, jak provádět testování vrstvy přístupu k datům A/B, najdete [Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-overview).
