---
title: SQL Server do spravované instance SQL – Průvodce migrací
description: Podle tohoto průvodce migrujte vaše databáze SQL Server do spravované instance Azure SQL.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 5c20fbbe25b51160f42f233d30c39ccaec0f5cac
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026040"
---
# <a name="migration-guide-sql-server-to-sql-managed-instance"></a>Průvodce migrací: SQL Server do spravované instance SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Tato příručka vám pomůže s migrací instance SQL Server do spravované instance Azure SQL. 

Můžete migrovat SQL Server spuštěná místně nebo na: 

- SQL Server na virtuálních počítačích  
- Amazon Web Services (AWS) EC2 
- Služba Amazon relačních databází (AWS RDS) 
- Výpočetní modul (Google Cloud Platform – GCP)  
- Cloud SQL pro SQL Server (Google Cloud Platform – GCP) 

Další informace o migraci najdete v tématu [Přehled migrace](sql-server-to-managed-instance-overview.md). Další scénáře najdete v [Průvodci migrací databáze](https://datamigration.microsoft.com/).

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-flow-small.png" alt-text="Tok procesu migrace":::

## <a name="prerequisites"></a>Požadavky 

Pokud chcete migrovat SQL Server do spravované instance Azure SQL, nezapomeňte projít následující požadavky: 

- Vyberte [metodu migrace](sql-server-to-managed-instance-overview.md#compare-migration-options) a odpovídající nástroje, které jsou potřeba pro zvolenou metodu.
- Instalace [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) na počítači, který se může připojit ke zdrojovému SQL Server


## <a name="pre-migration"></a>Před migrací

Až ověříte, že je vaše zdrojové prostředí podporované, začněte s fází před migrací. Seznamte se se všemi existujícími zdroji dat, vyhodnoťte proveditelnost migrace a Identifikujte případné blokující problémy, které by mohly bránit vaší migraci.  

### <a name="discover"></a>Zjišťování

Ve fázi zjišťování zkontrolujte síť a identifikujte všechny SQL Server instance a funkce používané vaší organizací. 

Použijte [Azure Migrate](../../../migrate/migrate-services-overview.md) k vyhodnocení vhodnosti migrace místních serverů, provádění určení velikosti na základě výkonu a poskytování odhadů nákladů na jejich spuštění v Azure. 

Případně můžete pomocí sady [nástrojů Microsoft Assessment and Planning Toolkit ("mapová sada")](https://www.microsoft.com/download/details.aspx?id=7826) posoudit svou aktuální infrastrukturu IT. Sada nástrojů poskytuje výkonný nástroj pro inventarizaci, posuzování a vytváření sestav, který zjednodušuje proces plánování migrace. 

Další informace o nástrojích, které jsou k dispozici pro fázi zjišťování, najdete v tématu [služby a nástroje dostupné pro scénáře migrace dat](../../../dms/dms-tools-matrix.md). 

### <a name="assess"></a>Posouzení 

Po zjištění zdrojů dat vyhodnoťte všechny místní instance SQL Server, které se dají migrovat do spravované instance Azure SQL, a Identifikujte tak blokování migrace nebo problémy s kompatibilitou. 

K vyhodnocení databází pro získání můžete použít Data Migration Assistant (verze 4,1 a novější): 

- [Doporučení pro cíle Azure](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Doporučení k Azure SKU](/sql/dma/dma-sku-recommend-sql-db)

Pokud chcete posoudit své prostředí pomocí vyhodnocení migrace databáze, postupujte podle těchto kroků: 

1. Otevřete [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). 
1. Vyberte **soubor** a klikněte na tlačítko **nové posouzení**. 
1. Zadejte název projektu, vyberte jako typ zdrojového serveru možnost SQL Server a jako typ cílového serveru vyberte možnost spravovaná instance Azure SQL. 
1. Vyberte typy sestav hodnocení, které chcete vygenerovat. Například kompatibilita databáze a parita funkcí. Na základě typu posouzení se oprávnění požadovaná na zdrojovém SQL Server můžou lišit.  Před spuštěním posouzení zvýrazní rozhraní DMA oprávnění požadovaná pro vybraný poradce.
    - Kategorie **parity funkcí** poskytuje komplexní sadu doporučení, alternativy dostupné v Azure a zmírnění kroků, které vám pomůžou při plánování projektu migrace. (vyžaduje se oprávnění správce systému.)
    - Kategorie **problémy s kompatibilitou** identifikuje částečně podporované nebo nepodporované problémy s kompatibilitou funkcí, které by mohly blokovat migraci, a také doporučení pro jejich řešení ( `CONNECT SQL` , `VIEW SERVER STATE` a `VIEW ANY DEFINITION` požadovaná oprávnění).
1. Zadejte podrobnosti o zdrojovém připojení pro vaši SQL Server a připojte se ke zdrojové databázi.
1. Vyberte **Spustit posouzení**. 
1. Po dokončení procesu vyberte a zkontrolujte zprávy posouzení pro blokování migrace a problémy s paritou funkcí. Sestavu posouzení je taky možné exportovat do souboru, který se dá sdílet s ostatními týmy nebo zaměstnanci ve vaší organizaci. 
1. Určete úroveň kompatibility databáze, která minimalizuje úsilí po migraci.  
1. Identifikujte nejlepší skladovou jednotku Azure SQL Managed instance pro vaše místní úlohy. 

Další informace najdete v tématu věnovaném [SQL Server hodnocení migrace pomocí Data Migration Assistant](/sql/dma/dma-assesssqlonprem).

Pokud spravovaná instance SQL není vhodným cílem pro vaše úlohy, SQL Server na virtuálních počítačích Azure může být pro vaši firmu životaschopným alternativním cílem. 

#### <a name="scaled-assessments-and-analysis"></a>Měření a analýza škálované

Data Migration Assistant podporuje provádění hodnocení a konsolidaci hodnotících sestav pro účely analýzy. Pokud máte více serverů a databází, které je potřeba posoudit a analyzovat ve velkém měřítku, aby poskytovaly širší pohled na datovou oblast, klikněte na následující odkazy a získejte další informace.

- [Provádění hodnocení s měřítkem pomocí PowerShellu](/sql/dma/dma-consolidatereports)
- [Analýza sestav hodnocení pomocí Power BI](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
>Spouštění hodnocení ve velkém měřítku pro více databází je také možné automatizovat pomocí [nástroje příkazového řádku DMA](/sql/dma/dma-commandline) , který také umožňuje nahrávání výsledků do [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) pro další analýzy a připravenost na cíle.

### <a name="create-a-performance-baseline"></a>Vytvoření standardních hodnot výkonu

Pokud potřebujete porovnat výkon úlohy na spravované instanci SQL s původní úlohou běžící na SQL Server, vytvořte směrný plán výkonu, který se použije pro porovnání. Další informace najdete v tématu [směrný plán výkonu](sql-server-to-managed-instance-performance-baseline.md) . 

### <a name="create-sql-managed-instance"></a>Vytvoření spravované instance SQL 

Na základě informací ve fázi zjišťování a vyhodnocení vytvořte správnou velikost cílové spravované instance SQL. Můžete k tomu použít [Azure Portal](../../managed-instance/instance-create-quickstart.md), [PowerShell](../../managed-instance/scripts/create-configure-managed-instance-powershell.md)nebo [šablonu Azure Resource Manager (ARM)](/azure/azure-sql/managed-instance/create-template-quickstart). 


## <a name="migrate"></a>Migrace

Po dokončení úloh přidružených ke fázi před migrací jste připraveni provést migraci schématu a dat. 

Migrujte svá data pomocí zvolené [metody migrace](sql-server-to-managed-instance-overview.md#compare-migration-options). 

Tato příručka popisuje dvě nejoblíbenější možnosti – Azure Database Migration Service (DMS) a nativní zálohování a obnovení. 

### <a name="database-migration-service"></a>Database Migration Service

Pokud chcete provádět migrace pomocí DMS, postupujte podle následujících kroků:

1. Pokud tento postup provádíte poprvé, zaregistrujte poskytovatele prostředků **Microsoft. datamigration** v rámci vašeho předplatného.
1. Vytvořte instanci Azure Database Migration Service v požadovaném umístění (nejlépe ve stejné oblasti jako vaše cílová spravovaná instance Azure SQL) a vyberte existující virtuální síť, nebo vytvořte novou pro hostování instance DMS.
1. Po vytvoření instance DMS vytvořte nový projekt migrace a zadejte jako **SQL Server** typ zdrojového serveru a jako typ cílového serveru **Azure SQL Database spravovanou instanci**. Vyberte typ aktivity v okně Vytvoření projektu – online nebo offline migrace dat. 
1.  Na stránce Podrobnosti o **zdroji migrace** zadejte podrobnosti o zdrojovém SQL Server a podrobnosti o cílové instanci Azure SQL na stránce Podrobnosti o **cíli migrace** . Vyberte **Další**.
1. Vyberte databázi, kterou chcete migrovat. 
1. Zadejte nastavení konfigurace, abyste určili **síťovou sdílenou složku SMB** , která obsahuje soubory zálohy vaší databáze. Použijte přihlašovací údaje uživatele Windows s operačním systémem DMS, který má přístup ke sdílené síťové složce. Zadejte **Podrobnosti účtu Azure Storage**. 
1. Zkontrolujte souhrn migrace a klikněte na **Spustit migraci**. Pak můžete sledovat aktivitu migrace a kontrolovat průběh migrace databáze.
1. Po obnovení databáze klikněte na tlačítko **Spustit přímou migraci**. Proces migrace zkopíruje zálohu protokolu po jeho zpřístupnění ve sdílené síťové složce SMB a obnoví ho v cíli. 
1. Zastavte veškerý příchozí provoz do zdrojové databáze a aktualizujte připojovací řetězec na novou databázi spravované instance Azure SQL. 

Podrobný kurz této možnosti migrace najdete v článku [migrace SQL Server do spravované instance Azure SQL online pomocí DMS](/azure/dms/tutorial-sql-server-managed-instance-online). 
   


### <a name="backup-and-restore"></a>Zálohování a obnovení 

Jednou z klíčových funkcí spravované instance Azure SQL je zajištění rychlé a snadné migrace databáze nativním obnovením souborů zálohování databáze ( `.bak` ) uložených v systému [Azure Storage](https://azure.microsoft.com/services/storage/). Zálohování a obnovení je asynchronní operace založená na velikosti vaší databáze. 

Následující diagram poskytuje podrobný přehled procesu:

![Diagram zobrazuje SQL Server se šipkou, která je označena jako zálohování nebo nahrání, do přetékání adres URL do Azure Storage a druhá šipka s označením obnovení z adresy URL z Azure Storage do spravované instance SQL.](./media/sql-server-to-managed-instance-overview/migration-restore.png)

> [!NOTE]
> Čas, kdy se má zálohování provést, nahrát ho do služby Azure Storage a provést nativní operaci obnovení do spravované instance Azure SQL na základě velikosti databáze. Přizpůsobte dostatečné výpadky pro uspokojení operace pro velké databáze. 


K migraci pomocí zálohování a obnovení použijte následující postup: 

1. Zálohujte databázi do Azure Blob Storage. Například použijte příkaz [Backup na adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) v [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). [Nástroj Microsoft Azure](https://go.microsoft.com/fwlink/?LinkID=324399) použijte k podpoře databází starších než SQL Server 2012 SP1 CU2. 
1. Připojte se ke spravované instanci SQL Azure pomocí SQL Server Management Studio. 
1. Vytvořte přihlašovací údaje pomocí sdíleného přístupového podpisu pro přístup k účtu služby Azure Blob Storage pomocí záloh databáze. Například:

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```
1. Obnovte zálohu z kontejneru objektů BLOB služby Azure Storage. Například: 

    ```sql
   RESTORE DATABASE [TargetDatabaseName] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

1. Až se obnovení dokončí, zobrazte databázi v **Průzkumník objektů** v rámci SQL Server Management Studio. 

Další informace o této možnosti migrace najdete v tématu [obnovení databáze do spravované instance Azure SQL pomocí SSMS](https://docs.microsoft.com/azure/azure-sql/managed-instance/restore-sample-database-quickstart).

> [!NOTE]
> Operace obnovení databáze je asynchronní a je opakovaná. Při přerušení připojení nebo vypršení časového limitu může dojít k chybě v SQL Server Management Studio. Azure SQL Database se nadále snaží obnovit databázi na pozadí a průběh obnovy můžete sledovat pomocí zobrazení [Sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) a [Sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) .



## <a name="data-sync-and-cutover"></a>Synchronizace dat a přímou migraci

Při použití možností migrace, které průběžně replikují nebo synchronizují změny dat ze zdroje do cíle, se zdrojová data a schéma můžou měnit a přenášet od cíle. Při synchronizaci dat se ujistěte, že jsou všechny změny ve zdroji zachyceny a aplikovány na cíl během procesu migrace. 

Po ověření, že jsou data na zdrojovém i cílovém stejné, můžete přímou migraci ze zdroje do cílového prostředí. Je důležité naplánovat proces přímou migraci s využitím obchodních nebo aplikačních týmů, aby bylo zajištěno minimální přerušení během přímou migraci, které nemá vliv na kontinuitu podnikových procesů. 

> [!IMPORTANT]
> Podrobnosti o konkrétních krocích spojených s prováděním přímou migraci jako součást migrace pomocí DMS najdete v tématu [provádění migrace přímou migraci](../../../dms/tutorial-sql-server-managed-instance-online.md#performing-migration-cutover).


## <a name="post-migration"></a>Po migraci

Po úspěšném dokončení fáze migrace Projděte řadu úkolů po migraci, abyste zajistili, že všechno funguje hladce a efektivně. 

Fáze po migraci je zásadní pro sjednocení problémů s přesností dat a ověření úplnosti a také řešení potíží s výkonem s úlohou. 

### <a name="remediate-applications"></a>Opravit aplikace 

Po migraci dat do cílového prostředí všechny aplikace, které dříve využily zdroj, musí začít spotřebovávat cíl. Výsledkem bude, že v některých případech bude nutné v aplikacích provádět změny.

### <a name="perform-tests"></a>Provést testy

Testovací přístup pro migraci databáze se skládá z následujících aktivit:

1. **Vývoj ověřovacích testů**: k otestování migrace databáze je nutné použít dotazy SQL. Je nutné vytvořit ověřovací dotazy ke spuštění proti zdrojové i cílové databázi. Dotazy na ověřování by se měly pokrývat s definovaným oborem.
1. **Nastavení testovacího prostředí**: testovací prostředí by mělo obsahovat kopii zdrojové databáze a cílovou databázi. Nezapomeňte izolovat testovací prostředí.
1. **Spustit ověřovací testy**: Spusťte ověřovací testy proti zdroji a cíli a pak Analyzujte výsledky.
1. **Spustit testy výkonu**: spustit test výkonnosti proti zdroji a cíli a pak analyzovat a porovnat výsledky.

   > [!NOTE]
   > Pro pomoc s vývojem a prováděním ověřovacích testů po migraci zvažte řešení kvality dat dostupné od partnera [QuerySurge](https://www.querysurge.com/company/partners/microsoft). 



## <a name="leverage-advanced-features"></a>Využití pokročilých funkcí 

Nezapomeňte využít výhod pokročilých cloudových funkcí, které nabízí spravovaná instance SQL, jako je [integrovaná vysoká dostupnost](../../database/high-availability-sla.md), [detekce hrozeb](../../database/advanced-data-security.md)a [monitorování a optimalizace vašich úloh](../../database/monitor-tune-overview.md). 

[Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md) umožňuje centralizovaným způsobem monitorovat velkou sadu spravovaných instancí.

Některé funkce SQL Server jsou dostupné až po změně [úrovně kompatibility databáze](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) na nejnovější úroveň kompatibility (150). 


## <a name="next-steps"></a>Další kroky

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
