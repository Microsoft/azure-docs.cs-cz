---
title: 'Kurz: migrace služby RDS SQL Server Online na SQL Database'
titleSuffix: Azure Database Migration Service
description: Naučte se provádět online migraci ze služby RDS SQL Server Azure SQL Database izolovanou databázi nebo spravované instanci pomocí Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 484598c7543e146618b6d2ab1f12bdf13710946b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091372"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-managed-instance-online-using-dms"></a>Kurz: migrace SQL Server RDS do Azure SQL Database nebo spravované instance Azure SQL online pomocí DMS

Pomocí Azure Database Migration Service můžete migrovat databáze z instance služby RDS SQL Server na [Azure SQL Database](/azure/sql-database/) nebo na [SPRAVOVANOU instanci Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) s minimálními výpadky. V tomto kurzu migrujete databázi **Adventureworks2012** obnovenou do instance služby RDS SQL Server SQL Server 2012 (nebo novější) na SQL Database nebo na SPRAVOVANOU instanci SQL pomocí Azure Database Migration Service.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoří databázi v Azure SQL Database nebo v spravované instanci SQL. 
> * Migrace ukázkového schématu pomocí nástroje Data Migration Assistant
> * Vytvoření instance služby Azure Database Migration Service
> * Vytvoření projektu migrace pomocí služby Azure Database Migration Service
> * Spuštění migrace
> * Monitorujte migraci.
> * Stažení sestavy migrace

> [!NOTE]
> Použití Azure Database Migration Service k provedení online migrace vyžaduje vytvoření instance založené na cenové úrovni Premium. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/database-migration/) Azure Database Migration Service.

> [!IMPORTANT]
> Aby migrace proběhla optimálním způsobem, doporučuje Microsoft vytvořit instanci služby Azure Database Migration Service ve stejné oblasti Azure, ve které je cílová databáze. Přenášení dat mezi oblastmi geografickými lokalitami může zpomalit proces migrace a způsobit chyby.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje online migraci ze služby RDS SQL Server do SQL Database nebo spravované instance SQL.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

* Vytvořte [databázi služby RDS SQL Server](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html).
* [Vytvořte databázi v Azure SQL Database Azure Portal](../azure-sql/database/single-database-create-quickstart.md) nebo [vytvořte databázi ve spravované instanci SQL](../azure-sql/managed-instance/instance-create-quickstart.md)a pak vytvořte prázdnou databázi s názvem **AdventureWorks2012**. 
* Stáhněte a nainstalujte nástroj [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) verze 3.3 nebo novější.
* Vytvořte Microsoft Azure Virtual Network pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manager. Pokud migrujete do spravované instance SQL, ujistěte se, že jste instanci DMS vytvořili ve stejné virtuální síti, která se používá pro spravovanou instanci SQL, ale v jiné podsíti.  Pokud použijete jinou virtuální síť pro DMS, budete muset vytvořit partnerský vztah virtuální sítě mezi oběma virtuálními sítěmi. Další informace o vytváření virtuálních sítí najdete v [dokumentaci k Virtual Network](../virtual-network/index.yml)a zejména v článcích rychlý Start s podrobnými údaji.

    > [!NOTE]
    > Pokud při instalaci virtuální sítě používáte ExpressRoute s partnerským vztahem k síti Microsoftu, přidejte do podsítě, ve které se služba zřídí, tyto [koncové body](../virtual-network/virtual-network-service-endpoints-overview.md) služby:
    >
    > * Koncový bod cílové databáze (například koncový bod SQL, Cosmos DB koncový bod atd.)
    > * Koncový bod úložiště
    > * Koncový bod služby Service Bus
    >
    > Tato konfigurace je nezbytná, protože Azure Database Migration Service nemá připojení k Internetu. 

* Zajistěte, aby pravidla skupiny zabezpečení sítě virtuálních sítí neblokovala odchozí port 443 ServiceTag pro ServiceBus, Storage a AzureMonitor. Další podrobnosti o filtrování provozu NSG virtuální sítě najdete v článku [filtrování provozu sítě pomocí skupin zabezpečení sítě](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Nakonfigurujte bránu [Windows Firewall pro přístup k databázovému stroji](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otevřete bránu Windows Firewall a povolte službě Azure Database Migration Service přístup ke zdrojovému SQL Serveru, který ve výchozím nastavení probíhá přes port TCP 1433.
* Pro SQL Database vytvořte [pravidlo brány firewall](../azure-sql/database/firewall-configure.md) na úrovni serveru, které umožní Azure Database Migration Service přístup k cílové databázi. Zadejte rozsah podsítě virtuální sítě, která se používá pro Azure Database Migration Service.
* Zajistěte, aby se přihlašovací údaje použité pro připojení ke zdrojové instanci služby RDS SQL Server přidružil k účtu, který je členem role serveru "processadmin", a členem rolí databáze "db_owner" na všech databázích, které mají být migrovány.
* Ujistěte se, že pověření používaná k připojení k cílové databázi mají oprávnění řídicí databáze v cílové databázi v SQL Database a člen role sysadmin při migraci do databáze ve spravované instanci SQL.
* Zdrojová verze SQL Server VP musí být SQL Server 2012 a vyšší. Návod k určení verze vaší instance SQL Serveru najdete v článku [Určení verze, edice a úrovně aktualizace SQL Serveru a jeho komponent](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
* Povolte funkci Change Data Capture (CDC) v databázi RDS SQL Server a všech uživatelských tabulkách vybraných pro migraci.
    > [!NOTE]
    > Pomocí následujícího skriptu můžete povolit funkci CDC v databázi služby RDS SQL Server.
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > Pomocí níže uvedeného skriptu můžete na všech tabulkách povolit funkci CDC.
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
* Zakáže aktivační procedury databáze v cílové databázi.
    > [!NOTE]
    > Aktivační procedury databáze můžete v cílové databázi najít pomocí následujícího dotazu:
    ```
    Use <Database name>
    go
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    Další informace najdete v článku [DISABLE TRIGGER (Transact-SQL)](/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

## <a name="migrate-the-sample-schema"></a>Migrace ukázkového schématu
K migraci schématu použijte technologii DMA.

> [!NOTE]
> Před vytvořením projektu migrace v DMA se ujistěte, že jste již zřídili databázi v SQL Database nebo spravované instanci SQL, jak je uvedeno v části požadavky. Pro účely tohoto kurzu se předpokládá, že název databáze je **AdventureWorks2012**, ale můžete zadat jakýkoli název, který si přejete.

Chcete-li migrovat schéma **AdventureWorks2012** , proveďte následující kroky:

1. V nástroji Data Migration Assistant vyberte ikonu Nový (+) a pak v části **Typ projektu** vyberte **Migrace**.
2. Zadejte název projektu, v textovém poli **Typ zdrojového serveru** vyberte **SQL Server** a pak v textovém poli **Typ cílového serveru** vyberte **Azure SQL Database**.

    > [!NOTE]
    > V případě typu cílového serveru vyberte **Azure SQL Database** pro migraci do obou Azure SQL Database a i do spravované instance SQL.

3. V části **Rozsah migrace** vyberte **Pouze schéma**.

    Po provedení předchozích kroků by se mělo zobrazit rozhraní DMA, jak je znázorněno na následujícím obrázku:

    ![Vytvoření projektu nástroje Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4. Vyberte **Vytvořit** a vytvořte projekt.
5. V DMA zadejte podrobnosti o připojení ke zdrojovému SQL Serveru, vyberte **Připojit** a pak vyberte databázi **AdventureWorks2012**.

    ![Podrobnosti o připojení ke zdroji v nástroji Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6. Vyberte **Další**, v části **připojit k cílovému serveru** zadejte podrobnosti o cílovém připojení pro databázi v SQL Database nebo spravované instanci SQL, vyberte **připojit** a pak vyberte databázi **AdventureWorksAzure** , kterou jste předem zřídili.

    ![Podrobnosti o připojení k cíli v nástroji Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7. Kliknutím na tlačítko **Další** přejdete na obrazovku **Vybrat objekty** , na které můžete zadat objekty schématu v databázi **AdventureWorks2012** , kterou je nutné nasadit.

    Ve výchozím nastavení jsou vybrané všechny objekty.

    ![Generování skriptů SQL](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8. Výběrem možnosti **Vygenerovat skript SQL** vytvořte skripty SQL a pak zkontrolujte, jestli skripty neobsahují chyby.

    ![Skript schématu](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9. Vyberte **nasadit schéma** pro nasazení schématu a po nasazení schématu zkontrolujte cíl pro všechny anomálie.

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

    Virtuální síť poskytuje Azure Database Migration Service s přístupem ke zdrojovému SQL Server a cílovému SQL Database nebo spravované instanci SQL.

    Další informace o tom, jak vytvořit virtuální síť v Azure Portal, najdete v článku [vytvoření virtuální sítě pomocí Azure Portal](../virtual-network/quick-create-portal.md).

6. Vyberte cenovou úroveň; v případě této online migrace nezapomeňte vybrat cenovou úroveň Premium.

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
4. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli **typ zdrojového serveru** vyberte **AWS VP pro SQL Server** v textovém poli **typ cílového serveru** vyberte možnost **Azure SQL Database**.

    > [!NOTE]
    > V případě typu cílového serveru vyberte **Azure SQL Database** pro migraci do obou SQL Database a i do spravované instance SQL.

5. V části **Zvolte typ aktivity** vyberte možnost **migrace online dat**.

    > [!IMPORTANT]
    > Ujistěte se, že jste vybrali **online migraci dat**; offline migrace nejsou v tomto scénáři podporovány.

    ![Vytvoření projektu Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > Alternativně můžete zvolit **vytvořit projekt pouze** k vytvoření projektu migrace a provést migraci později.

6. Vyberte **Uložit**.

7. Vyberte **Vytvořit a spustit aktivitu** a vytvořte projekt a spusťte aktivitu migrace.

    ![Vytvoření a spuštění aktivity služby Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Podrobnosti zdroje migrace** zadejte podrobnosti o připojení ke zdrojové instanci SQL Serveru.

    Jako název zdrojové instance SQL Serveru nezapomeňte použít plně kvalifikovaný název domény.

2. Pokud jste na svém zdrojovém serveru nenainstalovali důvěryhodný certifikát, zaškrtněte políčko **Důvěřovat certifikátu serveru**.

    Pokud není nainstalovaný důvěryhodný certifikát, SQL Server při spuštění instance vygeneruje certifikát podepsaný svým držitelem. Tento certifikát slouží k šifrování přihlašovacích údajů pro připojení klientů.

    > [!CAUTION]
    > Připojení TLS zašifrovaná pomocí certifikátu podepsaného svým držitelem neposkytují silné zabezpečení. Jsou náchylná na útoky, kdy se útočníci vydávají za prostředníky. Neměli byste spoléhat na protokol TLS pomocí certifikátů podepsaných držitelem v produkčním prostředí nebo na serverech, které jsou připojené k Internetu.

   ![Podrobnosti zdroje](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Vyberte **Uložit** a na obrazovce **Podrobnosti cíle migrace** zadejte podrobnosti o připojení pro cílovou databázi v Azure.

    ![Výběr cíle](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. Vyberte **Uložit** a pak na obrazovce **Mapovat na cílové databáze** namapujte zdrojovou a cílovou databázi pro migraci.

    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, služba Azure Database Migration Service ve výchozím nastavení vybere cílovou databázi.

    ![Mapování na cílové databáze](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. Vyberte **Uložit**, na obrazovce **Vybrat tabulky** rozbalte seznam tabulek a zkontrolujte seznam ovlivněných polí.

    Azure Database Migration Service automaticky vybere všechny prázdné zdrojové tabulky, které existují v cílové databázi. Chcete-li znovu migrovat tabulky, které již obsahují data, je nutné explicitně vybrat tabulky na této obrazovce.

    ![Výběr tabulek](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4. Po nastavení následujících **Nastavení rozšířeného online migrace** vyberte **Save (Uložit**).

    | Nastavení | Popis |
    | ------------- | ------------- |
    | **Maximální počet tabulek, které se mají načítat paralelně** | Určuje počet tabulek, které se během migrace paralelně spustí. Výchozí hodnota je 5, ale je možné ji nastavit na optimální hodnotu pro splnění konkrétních potřeb migrace v závislosti na všech migracích ověření koncepce. |
    | **Když je zdrojová tabulka zkrácená** | Určuje, zda DMS během migrace zkrátí cílovou tabulku. Toto nastavení může být užitečné, když se jedna nebo víc tabulek zkrátí v rámci procesu migrace. |
    | **Konfigurace nastavení pro data pro velké objekty (LOB)** | Určuje, zda DMS migruje neomezená obchodní data nebo omezuje data LOB migrovaná na konkrétní velikost.  V případě, že je u migrovaných dat LOB povolený limit, všechna data LOB nad rámec tohoto limitu se zkrátí. Pro produkční migrace se doporučuje vybrat možnost **Povolení neomezené velikosti LOB** , aby se zabránilo ztrátě dat. Když zadáte, že povolíte neomezenou velikost LOB, zaškrtněte políčko **migrovat data LOB v jednom bloku, pokud je velikost objektu LOB menší než (KB)** , aby se zlepšil výkon. |

    ![Nastavení upřesňujících nastavení online migrace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

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
4. Jakmile se zobrazí stav migrace databáze **dokončeno**, připojte aplikace k nové cílové databázi.

    ![Stav aktivity – Dokončeno](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>Další kroky

* Informace o známých problémech a omezeních při provádění online migrací do Azure najdete v článku [známé problémy a řešení pro online migrace](known-issues-azure-sql-online.md).
* Informace o Database Migration Service najdete v článku [co je Database Migration Service?](./dms-overview.md).
* Informace o SQL Database najdete v článku [co je služba SQL Database?](../azure-sql/database/sql-database-paas-overview.md).
* Informace o spravovaných instancích SQL najdete v článku [co je Managed instance SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).