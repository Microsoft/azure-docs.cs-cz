---
title: 'Kurz: Azure Database Migration Service můžete provést online migraci vzdálené plochy SQL serveru do Azure SQL Database nebo Azure SQL Database Managed Instance | Dokumentace Microsoftu'
description: Zjistěte, jak provést online migrace z místního SQL serveru vzdálené plochy do Azure SQL Database nebo Azure SQL Database Managed Instance s využitím Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 02/11/2019
ms.openlocfilehash: 00291cbcb23a3bcff320d391e56ff210c0a24af0
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56007902"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-online-using-dms"></a>Kurz: Migrace vzdálené plochy SQL serveru do Azure SQL Database pomocí DMS online
Azure Database Migration Service můžete použít k migraci databází z instance SQL Server služby Vzdálená plocha v místním [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/) nebo [Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) s minimálními výpadky . V tomto kurzu, migrujete **Adventureworks2012** databázi obnovit do instance SQL serveru vzdálené plochy (SQL Server 2012 nebo novější) ke službě Azure SQL Database / spravované Instance s využitím Azure Database Migration Service.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření instance Azure SQL Database nebo databáze v Azure SQL Database Managed Instance. 
> * Migrace ukázkového schématu pomocí nástroje Data Migration Assistant
> * Vytvoření instance služby Azure Database Migration Service
> * Vytvoření projektu migrace pomocí služby Azure Database Migration Service
> * Spuštění migrace
> * Monitorování migrace
> * Stažení sestavy migrace

> [!NOTE]
> Použití Azure Database Migration Service online migrace vyžaduje vytvoření instance založené na cenovou úroveň Premium. Další informace najdete v tématu Azure Database Migration Service [ceny](https://azure.microsoft.com/pricing/details/database-migration/) stránky.

> [!IMPORTANT]
> Aby migrace proběhla optimálním způsobem, doporučuje Microsoft vytvořit instanci služby Azure Database Migration Service ve stejné oblasti Azure, ve které je cílová databáze. Přenášení dat mezi oblastmi geografickými lokalitami může zpomalit proces migrace a způsobit chyby.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje online migrace ze vzdálené plochy SQL serveru do Azure SQL Database nebo Azure SQL Database Managed Instance.

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu je potřeba provést následující:

- Vytvoření [databáze SQL Server služby Vzdálená plocha](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html).
- Vytvoření instance Azure SQL Database, což uděláte pomocí následujících podrobností v článku [vytvořit databázi Azure SQL na webu Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).

    > [!NOTE]
    > Pokud provádíte migraci do Azure SQL Database Managed Instance, postupujte podle podrobností v článku [vytvořit Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)a pak vytvořte prázdnou databázi s názvem **AdventureWorks2012** . 
 
- Stáhněte a nainstalujte nástroj [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) verze 3.3 nebo novější.
- Vytvoření Azure Virtual Network (VNET) pro službu Azure Database Migration Service pomocí modelu nasazení Azure Resource Manageru. Pokud migrujete do Azure SQL Database Managed Instance, ujistěte se, že k vytvoření DMS instance ve stejné virtuální síti používá pro Azure SQL Database Managed Instance, ale v jiné podsíti.  Případně pokud používáte jiné virtuální sítě pro systém DMS, musíte vytvořit Partnerský vztah mezi dvěma virtuálními sítěmi.
- Ujistěte se, že pravidla skupiny zabezpečení sítě Azure VNET není blokovat následující komunikační porty 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování provozu pomocí skupiny zabezpečení virtuální sítě Azure najdete v článku [Filtrování provozu sítě s použitím skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Nakonfigurujte bránu [Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otevřete bránu Windows Firewall a povolte službě Azure Database Migration Service přístup ke zdrojovému SQL Serveru, který ve výchozím nastavení probíhá přes port TCP 1433.
- Vytvořte pro server služby Azure SQL Database [pravidlo brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na úrovni serveru, které službě Azure Database Migration Service povolí přístup k cílovým databázím. Zadejte rozsah podsítí virtuální sítě použité pro službu Azure Database Migration Service.
- Ujistěte se, že pověření použitá k připojení ke zdrojové instanci SQL Server služby Vzdálená plocha jsou přidružené k účtu, který je členem role serveru "Processadmin" a členem role databáze "db_owner" na všechny databáze, které se mají migrovat.
- Zajistěte, aby oprávnění CONTROL DATABASE na cílové databáze Azure SQL a členem sysadmin role v pověření používaná k připojení k instanci Azure SQL Database cílové Pokud migraci do Azure SQL Database Managed Instance.
- Verze SQL Server služby Vzdálená plocha zdroje musí být SQL Server 2012 a vyšší. Návod k určení verze vaší instance SQL Serveru najdete v článku [Určení verze, edice a úrovně aktualizace SQL Serveru a jeho komponent](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
- Povolte funkci Change Data Capture (CDC) v databázi SQL Server služby Vzdálená plocha a všechny tabulky uživatelů vybrána pro migraci.
    > [!NOTE]
    > Můžete použít níže uvedený skript pro povolte funkci CDC na databázi SQL Server služby Vzdálená plocha.
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > Níže uvedený skript můžete použít k povolení funkce CDC ve všech tabulkách.
    ```
    use <Database name>
    go
    exec sys.sp_cdc_enable_table 
    @source_schema = N'Schema name', 
    @source_name = N'table name', 
    @role_name = NULL, 
    @supports_net_changes = 1 --for PK table 1, non PK tables 0
    GO
    ```
- Zakažte pro cílovou službu Azure SQL Database triggery databáze.
    > [!NOTE]
    > Triggery databáze pro cílovou službu Azure SQL Database můžete vyhledat pomocí následujícího dotazu:
    ```
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    Další informace najdete v článku [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

## <a name="migrate-the-sample-schema"></a>Migrace ukázkového schématu
Migrace schématu do služby Azure SQL Database pomocí DMA.

> [!NOTE]
> Před vytvořením projektu migrace v DMA se ujistěte, že už máte zřízenou službu Azure SQL Database, jak je uvedeno v požadavcích. Pro účely tohoto kurzu, název služby Azure SQL Database je považován za **AdventureWorks2012**, ale můžete zadat jakýkoli název, který chcete.

Pokud chcete migrovat schéma **AdventureWorks2012** do služby Azure SQL Database, proveďte následující kroky:

1.  V nástroji Data Migration Assistant vyberte ikonu Nový (+) a pak v části **Typ projektu** vyberte **Migrace**.
2.  Zadejte název projektu, v textovém poli **Typ zdrojového serveru** vyberte **SQL Server** a pak v textovém poli **Typ cílového serveru** vyberte **Azure SQL Database**.
3.  V části **Rozsah migrace** vyberte **Pouze schéma**.

    Po provedení předchozích kroků by se mělo zobrazit rozhraní DMA, jak je znázorněno na následujícím obrázku:
    
    ![Vytvoření projektu nástroje Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4.  Vyberte **Vytvořit** a vytvořte projekt.
5.  V DMA zadejte podrobnosti o připojení ke zdrojovému SQL Serveru, vyberte **Připojit** a pak vyberte databázi **AdventureWorks2012**.

    ![Podrobnosti o připojení ke zdroji v nástroji Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6.  Vyberte **Další**v části **připojit k cílovému serveru**, zadat podrobnosti připojení cíl pro službu Azure SQL database, vyberte **připojit**a pak vyberte **AdventureWorksAzure** database předběžně zřizovaných ve službě Azure SQL Database.

    ![Podrobnosti o připojení k cíli v nástroji Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7.  Vyberte **Další** a přejděte na obrazovku **Vybrat objekty**, na které můžete určit objekty schématu v databázi **AdventureWorks2012**, které je potřeba nasadit do služby Azure SQL Database.

    Ve výchozím nastavení jsou vybrané všechny objekty.

    ![Generování skriptů SQL](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8.  Výběrem možnosti **Vygenerovat skript SQL** vytvořte skripty SQL a pak zkontrolujte, jestli skripty neobsahují chyby.

    ![Skript schématu](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9.  Vyberte **Nasadit schéma** a nasaďte schéma do služby Azure SQL Database. Po nasazení schématu zkontrolujte případné anomálie na cílovém serveru.

    ![Nasazení schématu](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration
1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.
 
   ![Zobrazení předplatných na portálu](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-select-subscription1.png)
       
2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Azure Database Migration Service, a pak vyberte **Poskytovatelé prostředků**.
 
    ![Zobrazení poskytovatelů prostředků](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)
    
3.  Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.
 
    ![Registrace poskytovatele prostředků](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Vytvoření instance
1.  Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-marketplace.png)

2.  Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.
 
    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3.  Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete vytvořit instanci služby Azure Database Migration Service. 

5. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje službě Azure Database Migration Service přístup ke zdrojovému SQL Serveru a cílové instanci služby Azure SQL Database.

    Další informace o vytvoření virtuální sítě na webu Azure Portal najdete v článku [Vytvoření virtuální sítě pomocí webu Azure Portal](https://aka.ms/DMSVnet).

6. Vyberte cenovou úroveň; pro tuto online migraci je potřeba vybrat možnost cenové úrovně Premium.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

     ![Konfigurace nastavení instance služby Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-settings3.png)

7.  Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace
Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.
 
      ![Vyhledání všech instancí služby Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-search.png)

2. Na obrazovce **Služby Azure Database Migration Service** vyhledejte název instance služby Azure Database Migration Service, kterou jste vytvořili, a vyberte ji.
 
     ![Vyhledání instance služby Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-instance-search.png)
 
3. Vyberte **+ Nový projekt migrace**.
4. Na **nový projekt migrace** obrazovky, zadejte název projektu, v **serveru typ zdroje** textového pole, vyberte **AWS RDS pro SQL Server**v  **Cílový typ serveru** textového pole, vyberte **Azure SQL Database**.
5. V **zvolte typ aktivity** vyberte **Online data migrace**.

    ![Vytvoření projektu Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > Alternativně můžete zvolit možnost **Vytvořit pouze projekt** a vytvořit teď projekt migrace, ale samotnou migraci provést později.

6. Vyberte **Uložit**.

7. Vyberte **Vytvořit a spustit aktivitu** a vytvořte projekt a spusťte aktivitu migrace.

    ![Vytvoření a spuštění aktivity služby Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)
 
## <a name="specify-source-details"></a>Zadání podrobností o zdroji
1. Na obrazovce **Podrobnosti zdroje migrace** zadejte podrobnosti o připojení ke zdrojové instanci SQL Serveru.
 
    Jako název zdrojové instance SQL Serveru nezapomeňte použít plně kvalifikovaný název domény.

2. Pokud jste na svém zdrojovém serveru nenainstalovali důvěryhodný certifikát, zaškrtněte políčko **Důvěřovat certifikátu serveru**.

    Pokud není nainstalovaný důvěryhodný certifikát, SQL Server při spuštění instance vygeneruje certifikát podepsaný svým držitelem. Tento certifikát slouží k šifrování přihlašovacích údajů pro připojení klientů.

    > [!CAUTION]
    > Připojení SSL šifrovaná pomocí certifikátu podepsaného svým držitelem neposkytují silné zabezpečení. Jsou náchylná na útoky, kdy se útočníci vydávají za prostředníky. Na připojení SSL s použitím certifikátů podepsaných svým držitelem byste neměli spoléhat v produkčním prostředí ani na serverech připojených k internetu.

   ![Podrobnosti zdroje](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>Zadání podrobností o cíli
1. Vyberte **Uložit** a pak na obrazovce **Podrobnosti cíle migrace** zadejte podrobnosti o připojení k cílovému serveru služby Azure SQL Database. Tím je předem zřízená služba Azure SQL Database, do které se pomocí DMA nasadilo schéma **AdventureWorks2012**.

    ![Výběr cíle](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. Vyberte **Uložit** a pak na obrazovce **Mapovat na cílové databáze** namapujte zdrojovou a cílovou databázi pro migraci.

    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, služba Azure Database Migration Service ve výchozím nastavení vybere cílovou databázi.

    ![Mapování na cílové databáze](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. Vyberte **Uložit**, na obrazovce **Vybrat tabulky** rozbalte seznam tabulek a zkontrolujte seznam ovlivněných polí.

    Azure Database Migration Service automaticky vybere všechny prázdné zdrojové tabulky, které existují v cílové instanci služby Azure SQL Database. Pokud chcete přenést tabulek, které již obsahují data, musíte explicitně vybrat tabulky na této obrazovce.

    ![Výběr tabulek](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4.  Vyberte **Uložit**, po nastavení následující **upřesňující nastavení online migrace**.
    
    | Nastavení | Popis |
    | ------------- | ------------- |
    | **Maximální počet tabulek, načtení paralelně** | Určuje počet tabulek, které DMS spustí paralelně během migrace. Výchozí hodnota je 5, ale může být nastavena na optimální hodnotu podle potřeb konkrétního migrace založené na všechny migrace POC. |
    | **Když je oříznutá. zdrojová tabulka** | Určuje, zda DMS zkrátí cílová tabulka během migrace. To může být užitečné, pokud jeden nebo více tabulek se zkrátí jako součást procesu migrace. |
    | **Konfigurace nastavení pro velké objekty (LOB) dat.** | Určuje, zda DMS migruje neomezená data LOB nebo omezení obchodní data migrovat do určité velikosti.  Když je, že omezení dat LOB migrovat, žádná obchodní data nad rámec tohoto limitu je zkrácen. Pro migraci produkčního prostředí, doporučujeme vybrat **Povolit neomezené velikosti LOB** se tak ztrátě dat. Při zadávání Povolit neomezené velikosti LOB, vyberte **zadat migraci obchodní data v jeden blok při LOB velikost je menší než (KB)** zaškrtávací políčko ke zlepšení výkonu. |
    
    ![Nastavení pokročilých online migrace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

5.  Vyberte **Uložit**, na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace a pak zkontrolujte souhrnné informace a ujistěte se, že podrobnosti zdroje a cíle odpovídají dříve zadaným informacím.

    ![Shrnutí migrace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-migration-summary.png)

## <a name="run-the-migration"></a>Spuštění migrace
- Vyberte **Spustit migraci**.

    Zobrazí se okno aktivity migrace a **Stav** aktivity bude **Inicializace**.

    ![Stav aktivity – Inicializace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Monitorování migrace
1. Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Spuštěno**.

2. Kliknutím na konkrétní databázi zobrazíte stav migrace pro operace **Úplné načtení dat** a **Přírůstková synchronizace dat**.

    ![Stav aktivity – Probíhá](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Provedení přímé migrace
Po dokončení počátečního úplného načtení se databáze označí jako **Připraveno k přímé migraci**.

1. Jakmile budete připraveni dokončit migraci databází, vyberte **Spustit přímou migraci**.

    ![Spuštění přímé migrace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-start-cutover.png)
 
2.  Ujistěte se, že jste zastavili všechny příchozí transakce do zdrojové databáze a počkejte, dokud se v čítači **Probíhající změny** nezobrazí **0**.
3.  Vyberte **Potvrdit** a pak **Použít**.
4. Když se stav migrace databází změní na **Dokončeno**, připojte své aplikace k nové cílové službě Azure SQL Database.
 
    ![Stav aktivity – Dokončeno](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>Další postup
- Informace o známých problémech a omezeních při provádění online migrace do Azure SQL DatabaseL, najdete v článku [– známé problémy a řešení pro Azure SQL Database online migrace](known-issues-azure-sql-online.md).
- Informace o službě Azure Database Migration Service najdete v článku [Co je Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
- Informace o službě Azure SQL Database, najdete v článku [novinky službě Azure SQL Database?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
- Informace o Azure SQL Database Managed Instance, naleznete na stránce [Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index).
