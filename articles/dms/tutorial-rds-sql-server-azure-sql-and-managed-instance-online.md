---
title: 'Kurz: Migrace serveru RDS SQL Server online do databáze SQL'
titleSuffix: Azure Database Migration Service
description: Naučte se provádět online migraci z RDS SQL Server do jedné databáze Azure SQL Database nebo spravované instance pomocí služby Migrace databáze Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 8d538deca610fd9981d401d28b6bea1c31c6d4c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298871"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-database-managed-instance-online-using-dms"></a>Kurz: Migrace RDS SQL Serveru do Azure SQL Database nebo instance spravované azure SQL database online pomocí DMS
Službu migrace databáze Azure můžete použít k migraci databází z instance RDS SQL Server do [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/) nebo instance spravované azure SQL [database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) s minimálními prostoji. V tomto kurzu migrujete databázi **Adventureworks2012** obnovenou do instance RDS SQL Server SQL Serveru SQL Serveru 2012 (nebo novější) do Azure SQL Database nebo spravované instance Azure SQL Database pomocí služby Migrace databáze Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvořte instanci Azure SQL Database nebo spravované instance Azure SQL Database. 
> * Migrace ukázkového schématu pomocí nástroje Data Migration Assistant
> * Vytvoření instance služby Azure Database Migration Service
> * Vytvoření projektu migrace pomocí služby Azure Database Migration Service
> * Spuštění migrace
> * Monitorování migrace
> * Stažení sestavy migrace

> [!NOTE]
> Použití služby migrace databáze Azure k provedení migrace online vyžaduje vytvoření instance založené na cenové úrovni Premium. Další informace najdete na stránce [s cenami](https://azure.microsoft.com/pricing/details/database-migration/) služby Azure Database Migration Service.

> [!IMPORTANT]
> Aby migrace proběhla optimálním způsobem, doporučuje Microsoft vytvořit instanci služby Azure Database Migration Service ve stejné oblasti Azure, ve které je cílová databáze. Přenášení dat mezi oblastmi geografickými lokalitami může zpomalit proces migrace a způsobit chyby.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje online migraci z RDS SQL Server do Azure SQL Database nebo spravované instance Azure SQL Database.

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu je potřeba provést následující:

* Vytvořte [databázi serveru RDS SQL Server](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html).
* Vytvořte instanci Azure SQL Database, kterou uděláte podle podrobností v článku [Vytvoření databáze Azure SQL na webu Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).

    > [!NOTE]
    > Pokud migrujete na spravovanou instanci Azure SQL Database, postupujte podle podrobností v článku [Vytvoření instance spravované službou Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)a pak vytvořte prázdnou databázi s názvem **AdventureWorks2012**. 
 
* Stáhněte a nainstalujte nástroj [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) verze 3.3 nebo novější.
* Vytvořte službu migrace databáze Microsoft Azure pro Azure pomocí modelu nasazení Azure Resource Manageru. Pokud migrujete na spravovanou instanci Azure SQL Database, nezapomeňte vytvořit instanci DMS ve stejné virtuální síti, která se používá pro spravovanou instanci Azure SQL Database, ale v jiné podsíti.  Případně pokud používáte jinou virtuální síť pro DMS, budete muset vytvořit partnerský vztah virtuální sítě mezi dvěma virtuálními sítěmi. Další informace o vytvoření virtuální sítě naleznete v [dokumentaci k virtuální síti](https://docs.microsoft.com/azure/virtual-network/)a zejména v článcích rychlého startu s podrobnými podrobnostmi.

    > [!NOTE]
    > Pokud během instalace virtuální sítě používáte ExpressRoute s partnerským vztahem k síti společnosti Microsoft, přidejte do podsítě, ve které bude služba zřízena, následující [koncové body služby:](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)
    >
    > * Cílový koncový bod databáze (například koncový bod SQL, koncový bod Cosmos DB a tak dále)
    > * Koncový bod úložiště
    > * Koncový bod sběrnice
    >
    > Tato konfigurace je nezbytná, protože služba migrace databáze Azure nemá připojení k internetu. 

* Ujistěte se, že pravidla skupiny zabezpečení sítě virtuální sítě neblokují následující příchozí komunikační porty služby Azure Database Migration Service: 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování přenosů skupin nsg ve virtuální síti naleznete v článku [Filtrování síťového provozu se skupinami zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Nakonfigurujte bránu [Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otevřete bránu Windows Firewall a povolte službě Azure Database Migration Service přístup ke zdrojovému SQL Serveru, který ve výchozím nastavení probíhá přes port TCP 1433.
* Vytvořte pro server služby Azure SQL Database [pravidlo brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na úrovni serveru, které službě Azure Database Migration Service povolí přístup k cílovým databázím. Zadejte rozsah podsítí virtuální sítě používané pro službu migrace databáze Azure.
* Ujistěte se, že pověření použitá pro připojení ke zdrojové instanci rds sql serveru jsou přidružena k účtu, který je členem role serveru "Processadmin" a členem databázových rolí "db_owner" ve všech databázích, které mají být migrovány.
* Ujistěte se, že přihlašovací údaje použité pro připojení k cílové instanci Azure SQL Database mají oprávnění DATABÁZE CONTROL v cílových databázích Azure SQL a člena role sysadmin, pokud se migrují do spravované instance Azure SQL Database.
* Zdrojová verze serveru RDS SQL Server musí být SQL Server 2012 a vyšší. Návod k určení verze vaší instance SQL Serveru najdete v článku [Určení verze, edice a úrovně aktualizace SQL Serveru a jeho komponent](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
* Povolte sběr dat změny (CDC) v databázi serveru RDS SQL Server a všechny uživatelské tabulky vybrané pro migraci.
    > [!NOTE]
    > Pomocí níže uvedeného skriptu můžete povolit cdc v databázi serveru RDS SQL Server.
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > Pomocí níže uvedeného skriptu můžete povolit CDC ve všech tabulkách.
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
* Zakažte pro cílovou službu Azure SQL Database triggery databáze.
    > [!NOTE]
    > Triggery databáze pro cílovou službu Azure SQL Database můžete vyhledat pomocí následujícího dotazu:
    ```
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    Další informace najdete v článku [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

## <a name="migrate-the-sample-schema"></a>Migrace ukázkového schématu
Pomocí dma migrovat schéma do Azure SQL Database.

> [!NOTE]
> Před vytvořením projektu migrace v DMA se ujistěte, že už máte zřízenou databázi Azure SQL, jak je uvedeno v požadavcích. Pro účely tohoto kurzu se předpokládá, že název azure SQL database **adventureworks2012**, ale můžete zadat libovolné jméno, které chcete.

Pokud chcete migrovat schéma **AdventureWorks2012** do služby Azure SQL Database, proveďte následující kroky:

1. V nástroji Data Migration Assistant vyberte ikonu Nový (+) a pak v části **Typ projektu** vyberte **Migrace**.
2. Zadejte název projektu, v textovém poli **Typ zdrojového serveru** vyberte **SQL Server** a pak v textovém poli **Typ cílového serveru** vyberte **Azure SQL Database**.
3. V části **Rozsah migrace** vyberte **Pouze schéma**.

    Po provedení předchozích kroků by se mělo zobrazit rozhraní DMA, jak je znázorněno na následujícím obrázku:

    ![Vytvoření projektu nástroje Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4. Vyberte **Vytvořit** a vytvořte projekt.
5. V DMA zadejte podrobnosti o připojení ke zdrojovému SQL Serveru, vyberte **Připojit** a pak vyberte databázi **AdventureWorks2012**.

    ![Podrobnosti o připojení ke zdroji v nástroji Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6. Vyberte **Další**, v části **Připojit k cílovému serveru**zadejte podrobnosti cílového připojení pro databázi Azure SQL, vyberte **Připojit**a pak vyberte databázi **AdventureWorksAzure,** kterou jste předem zřídit i v Azure SQL Database.

    ![Podrobnosti o připojení k cíli v nástroji Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7. Vyberte **Další** a přejděte na obrazovku **Vybrat objekty**, na které můžete určit objekty schématu v databázi **AdventureWorks2012**, které je potřeba nasadit do služby Azure SQL Database.

    Ve výchozím nastavení jsou vybrané všechny objekty.

    ![Generování skriptů SQL](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8. Výběrem možnosti **Vygenerovat skript SQL** vytvořte skripty SQL a pak zkontrolujte, jestli skripty neobsahují chyby.

    ![Skript schématu](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9. Vyberte **Nasadit schéma** a nasaďte schéma do služby Azure SQL Database. Po nasazení schématu zkontrolujte případné anomálie na cílovém serveru.

    ![Nasazení schématu](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

   ![Zobrazení předplatných na portálu](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-select-subscription1.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Azure Database Migration Service, a pak vyberte **Poskytovatelé prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Vytvoření instance

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete vytvořit instanci služby Azure Database Migration Service. 

5. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje službě Migrace databáze Azure přístup ke zdrojovému serveru SQL Server a cílové instanci Azure SQL Database.

    Další informace o tom, jak vytvořit virtuální síť na webu Azure Portal, najdete v článku [Vytvoření virtuální sítě pomocí portálu Azure](https://aka.ms/DMSVnet).

6. Vyberte cenovou úroveň; Pro tuto online migraci nezapomeňte vybrat cenovou úroveň Premium.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

     ![Konfigurace nastavení instance služby Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-settings3.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

      ![Vyhledání všech instancí služby Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-search.png)

2. Na obrazovce **Služby Azure Database Migration Service** vyhledejte název instance služby Azure Database Migration Service, kterou jste vytvořili, a vyberte ji.

     ![Vyhledání instance služby Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-instance-search.png)

3. Vyberte **+ Nový projekt migrace**.
4. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli Typ **zdrojového serveru** vyberte **aws RDS pro SQL Server**, v textovém poli Typ **cílového serveru** vyberte Azure SQL **Database**.

    > [!NOTE]
    > Pro typ cílového serveru vyberte **Azure SQL Database** pro migraci do databáze singleton Azure SQL Database a také do instance spravované službou Azure SQL Database.

5. V části **Zvolit typ aktivity** vyberte **Online migraci dat**.

    > [!IMPORTANT]
    > Nezapomeňte vybrat **online migraci dat**. offline migrace nejsou pro tento scénář podporovány.

    ![Vytvoření projektu Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > Alternativně můžete zvolit **Vytvořit projekt pouze** k vytvoření projektu migrace nyní a provést migraci později.

6. Vyberte **Uložit**.

7. Vyberte **Vytvořit a spustit aktivitu** a vytvořte projekt a spusťte aktivitu migrace.

    ![Vytvoření a spuštění aktivity služby Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Podrobnosti zdroje migrace** zadejte podrobnosti o připojení ke zdrojové instanci SQL Serveru.

    Jako název zdrojové instance SQL Serveru nezapomeňte použít plně kvalifikovaný název domény.

2. Pokud jste na svém zdrojovém serveru nenainstalovali důvěryhodný certifikát, zaškrtněte políčko **Důvěřovat certifikátu serveru**.

    Pokud není nainstalovaný důvěryhodný certifikát, SQL Server při spuštění instance vygeneruje certifikát podepsaný svým držitelem. Tento certifikát slouží k šifrování přihlašovacích údajů pro připojení klientů.

    > [!CAUTION]
    > Připojení TLS, která jsou šifrována pomocí certifikátu podepsaného svým držitelem, neposkytují silné zabezpečení. Jsou náchylná na útoky, kdy se útočníci vydávají za prostředníky. Na protokol TLS byste neměli spoléhat pomocí certifikátů podepsaných svým držitelem v produkčním prostředí nebo na serverech, které jsou připojeny k Internetu.

   ![Podrobnosti zdroje](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Vyberte **Uložit** a pak na obrazovce **Podrobnosti cíle migrace** zadejte podrobnosti o připojení k cílovému serveru služby Azure SQL Database. Tím je předem zřízená služba Azure SQL Database, do které se pomocí DMA nasadilo schéma **AdventureWorks2012**.

    ![Výběr cíle](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. Vyberte **Uložit** a pak na obrazovce **Mapovat na cílové databáze** namapujte zdrojovou a cílovou databázi pro migraci.

    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, služba Azure Database Migration Service ve výchozím nastavení vybere cílovou databázi.

    ![Mapování na cílové databáze](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. Vyberte **Uložit**, na obrazovce **Vybrat tabulky** rozbalte seznam tabulek a zkontrolujte seznam ovlivněných polí.

    Azure Database Migration Service automaticky vybere všechny prázdné zdrojové tabulky, které existují v cílové instanci služby Azure SQL Database. Pokud chcete znovu emigrovat tabulky, které již obsahují data, musíte explicitně vybrat tabulky na této obrazovce.

    ![Výběr tabulek](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4. Po nastavení následujícího **nastavení rozšířené migrace online**vyberte **možnost Uložit**.

    | Nastavení | Popis |
    | ------------- | ------------- |
    | **Maximální počet stolů, které se mají načíst paralelně** | Určuje počet tabulek, které dms provádí paralelně během migrace. Výchozí hodnota je 5, ale lze ji nastavit na optimální hodnotu, aby vyhovovala specifickým potřebám migrace na základě všech migrací POC. |
    | **Při zkrácení zdrojové tabulky** | Určuje, zda systém DMS během migrace zkrátí cílovou tabulku. Toto nastavení může být užitečné, pokud je jedna nebo více tabulek zkráceno jako součást procesu migrace. |
    | **Konfigurace nastavení dat velkých objektů (LOB)** | Určuje, zda dms migruje neomezená obchodní data nebo omezuje přenesená obchodní data na určitou velikost.  Pokud je limit na degrovaná data migrovaná, všechna obchodní data nad rámec tohoto limitu je zkrácena. Pro produkční migrace se doporučuje vybrat **povolit neomezenou velikost LOB,** abyste zabránili ztrátě dat. Při zadávání, chcete-li povolit neomezenou velikost LOB, **zaškrtněte políčko Migrovat obchodní data v jednom bloku, pokud je zadaný formát LOB menší než (KB),** abyste zvýšili výkon. |

    ![Nastavení upřesňujících nastavení migrace online](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

5. Vyberte **Uložit**, na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace a pak zkontrolujte souhrnné informace a ujistěte se, že podrobnosti zdroje a cíle odpovídají dříve zadaným informacím.

    ![Shrnutí migrace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-migration-summary.png)

## <a name="run-the-migration"></a>Spuštění migrace

* Vyberte **Spustit migraci**.

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

2. Ujistěte se, že jste zastavili všechny příchozí transakce do zdrojové databáze a počkejte, dokud se v čítači **Probíhající změny** nezobrazí **0**.
3. Vyberte **Potvrdit** a pak **Použít**.
4. Když se stav migrace databází změní na **Dokončeno**, připojte své aplikace k nové cílové službě Azure SQL Database.

    ![Stav aktivity – Dokončeno](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>Další kroky

* Informace o známých problémech a omezeních při provádění online migrací do Azure SQL DatabaseL najdete v článku [Známé problémy a řešení migrace online databáze Azure SQL Database](known-issues-azure-sql-online.md).
* Informace o službě Azure Database Migration Service najdete v článku [Co je Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Informace o Azure SQL Database najdete v článku [Co je služba Azure SQL Database?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
* Informace o spravovaných instancích Azure SQL Database najdete na stránce [Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index).
