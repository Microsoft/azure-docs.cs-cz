---
title: Migrace do Azure SQL Database Managed Instance pomocí DMS | Dokumentace Microsoftu
description: Zjistěte, jak migrovat z místního SQL serveru do Azure SQL Database Managed Instance s využitím Azure Database Migration Service.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 07/12/2018
ms.openlocfilehash: c911b096af6662e11afb4c4262b92c239d252c36
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990223"
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-using-dms"></a>Migrace SQL serveru do Azure SQL Database Managed Instance pomocí DMS
Azure Database Migration Service můžete použít k migraci databází z místní instance systému SQL Server do [Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance.md). Další metody, které mohou vyžadovat některé ruční práce, najdete v článku [migrace instance SQL serveru do Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-migrate.md).

> [!IMPORTANT]
> Projekty migrace z SQL serveru do Azure SQL Database Managed Instance jsou ve verzi preview a podléhá [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V tomto kurzu, migrujete **Adventureworks2012** databáze z místní instance systému SQL Server do Azure SQL Database Managed Instance s využitím Azure Database Migration Service.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření instance služby Azure Database Migration Service.
> * Vytvořte projekt migrace s využitím Azure Database Migration Service.
> * Migraci spustíte.
> * Monitorujte migraci.
> * Stáhněte si sestavu migrace.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto kurzu, budete muset:

- Vytvoření virtuální sítě pro službu Azure Database Migration Service pomocí modelu nasazení Azure Resource Manageru, který poskytuje připojení site-to-site k vašich zdrojových serverů s místními pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Zjistěte síťové topologie pro migrace Azure SQL DB Managed Instance pomocí Azure Database Migration Service](https://aka.ms/dmsnetworkformi).
- Ujistěte se, že vaší virtuální sítě (VNET) skupiny zabezpečení sítě Azure pravidla blokovat následující komunikační porty 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování provozu skupiny zabezpečení sítě Azure VNET najdete v článku [filtrování provozu sítě s použitím skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurace vašeho [brány Windows Firewall pro přístup k databázi zdrojové](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otevřete bránu Windows Firewall a povolit službu Azure Database Migration Service přístup k zdrojového systému SQL Server, který ve výchozím nastavení je TCP port 1433.
- Pokud používáte více pojmenované instance SQL serveru pomocí dynamické porty, které staví na povolte službu SQL Browser a povolit přístup na port UDP 1434 prostřednictvím brány firewall tak, aby službu Azure Database Migration Service můžete připojit k pojmenované instanci na zdroji Server.
- Pokud používáte zařízení brány firewall u zdrojové databáze, budete muset přidat pravidla firewallu povolující službu Azure Database Migration Service přístup k zdrojových databází pro migraci, stejně jako soubory prostřednictvím SMB portu 445.
- Vytvoření Azure SQL Database Managed Instance pomocí následujících podrobností v článku [vytvořit Azure SQL Database Managed Instance na webu Azure Portal](https://aka.ms/sqldbmi).
- Ujistěte se, že přihlašovací údaje používat k připojení zdrojového systému SQL Server a cílit na Managed Instance jsou členové role serveru sysadmin.
- Vytvoření sdílené síťové složky, Azure Database Migration Service můžete použít k zálohování zdrojové databáze.
- Ujistěte se, že účet služby spuštění zdroj obsahuje instanci systému SQL Server oprávnění k zápisu ve sdílené síťové složce, kterou jste vytvořili a že účet počítače pro zdrojový server má přístup pro čtení a zápis ke stejné sdílené složce.
- Poznamenejte Windows uživatele (a heslo), který má oprávnění Úplné řízení na sdílené síťové složce, kterou jste vytvořili. Azure Database Migration Service zosobní přihlašovací údaje uživatele k nahrání záložních souborů do kontejneru úložiště Azure pro operaci obnovení.
- Vytvořit kontejner objektů blob a získat jeho identifikátor URI SAS pomocí kroků v článku [prostředků Správa služby Azure Blob Storage pomocí Storage Exploreru](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container), je nutné vybrat všechna oprávnění (čtení, zápis, odstranění, seznamu) v okně zásad při vytváří se identifikátor URI SAS. Těchto podrobných informací poskytuje Azure Database Migration Service přístup ke kontejneru účtu úložiště pro nahrávání záložní soubory použít k migraci databází do Azure SQL Database Managed Instance

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure portal vyberte **všechny služby**a pak vyberte **předplatná**.

    ![Zobrazení portálu předplatných](media\tutorial-sql-server-to-managed-instance\portal-select-subscriptions.png)        

2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Azure Database Migration Service, a pak vyberte **Poskytovatelé prostředků**.

    ![Zobrazení poskytovatelů prostředků](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

3. Hledání pro migraci a potom napravo od **Microsoft.DataMigration**vyberte **zaregistrovat**.

    ![Registrace poskytovatele prostředků](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Vytvoření instance služby Azure Database Migration Service

1. Na webu Azure Portal, vyberte + **vytvořit prostředek**, vyhledejte **Azure Database Migration Service**a pak vyberte **Azure Database Migration Service** z rozevíracího seznamu seznam.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

2. Na **Azure Database Migration Service** obrazovky, vyberte **vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media\tutorial-sql-server-to-managed-instance\dms-create1.png)

3. Na **vytvořit službu migrace** obrazovky, zadejte název pro službu, předplatné a skupinu nový nebo existující prostředek.

4. Vyberte existující virtuální síť (VNET) nebo vytvořit novou.
 
    Virtuální síť poskytuje Azure Database Migration Service přístup k systému SQL Server zdroje a cíle Azure SQL Database Managed Instance.

    Další informace o tom, jak vytvořit virtuální síť na webu Azure portal najdete v článku [vytvořit virtuální síť pomocí webu Azure portal](https://aka.ms/DMSVnet).

    Další podrobnosti najdete v článku [síťové topologie pro migrace Azure SQL DB Managed Instance pomocí Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

5. Vyberte cenovou úroveň.

    Další informace o nákladech a cenových úrovní najdete v článku [stránce s cenami](https://aka.ms/dms-pricing).
   
    ![Vytvořit službu DMS](media\tutorial-sql-server-to-managed-instance\dms-create-service1.png)

6.  Vyberte **vytvořit** vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvořit projekt migrace

Po vytvoření služby, vyhledejte na webu Azure portal, otevřete ho a vytvořte nový projekt migrace.

1. Na webu Azure Portal, vyberte **všechny služby**, vyhledejte Azure Database Migration Service a pak vyberte **Azure Database Migration Service**.

    ![Vyhledejte všechny instance služby Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. Na **Azure Database Migration Service obrazovky**, vyhledejte název instance, že jste vytvořili a potom vyberte instanci.
 
3. Vyberte + **nový projekt migrace**.

4. Na **nový projekt migrace** obrazovky, zadejte název projektu, v **serveru typ zdroje** textového pole, vyberte **systému SQL Server**a potom v **cíl Typ serveru** textového pole, vyberte **Azure SQL Database Managed Instance**.

   ![Vytvoření projektu DMS](media\tutorial-sql-server-to-managed-instance\dms-create-project1.png)

5. Vyberte **vytvořit** pro vytvoření projektu.

## <a name="specify-source-details"></a>Zadejte podrobnosti zdroje

1. Na **zdrojové podrobnosti** obrazovky, zadat podrobnosti připojení pro zdrojového systému SQL Server.

2. Pokud jste dosud nenainstalovali důvěryhodný certifikát na serveru, vyberte **důvěřovat certifikátu serveru** zaškrtávací políčko.

    Když není nainstalován důvěryhodný certifikát, SQL Server vygeneruje certifikát podepsaný svým držitelem při spuštění instance. Tento certifikát se používá k šifrování přihlašovacích údajů pro připojení klientů.

    > [!CAUTION]
    > Připojení SSL, které jsou šifrované pomocí certifikátu podepsaného svým držitelem neposkytuje silné zabezpečení. Jsou náchylné k útokům man-in-the-middle. Neměli byste tedy spoléhat na SSL pomocí certifikátů podepsaných svým držitelem v produkčním prostředí nebo na serverech, které jsou připojené k Internetu.

   ![Podrobnosti zdroje](media\tutorial-sql-server-to-managed-instance\dms-source-details1.png)

3. Vyberte **Uložit**.

4. Na **vyberte zdrojové databáze** obrazovky, vyberte **Adventureworks2012** databáze pro migraci.

   ![Vyberte zdrojové databáze](media\tutorial-sql-server-to-managed-instance\dms-source-database1.png)

5. Vyberte **Uložit**.

## <a name="specify-target-details"></a>Zadejte podrobnosti o cíli

1.  Na **cílit na podrobnosti** obrazovky, zadat podrobnosti připojení pro cíl, který je předem zřízená Azure SQL Database Managed Instance ke kterému **AdventureWorks2012** databáze má být migrovat.

    Pokud jste ještě nezřídili Azure SQL Database Managed Instance, vyberte **ne** pro odkaz na nápovědu můžete zřídit instanci. Můžete dál pokračujte ve vytváření projektu a, když Azure SQL Database Managed Instance je připravena, vraťte se do tohoto konkrétního projektu k provedení migrace.   
 
       ![Vyberte cíl](media\tutorial-sql-server-to-managed-instance\dms-target-details1.png)

2.  Vyberte **Uložit**.

3.  Na **souhrn projektu** obrazovky, zkontrolujte a zkontrolujte podrobnosti přidružené k projektu migrace.
 
    ![Souhrn projektu migrace](media\tutorial-sql-server-to-managed-instance\dms-project-summary1.png)

4.  Vyberte **Uložit**.   

## <a name="run-the-migration"></a>Spustit migraci

1.  Vyberte projekt, který naposledy uloženy, vyberte + **novou aktivitu**a pak vyberte **spustit migraci**.

    ![Vytvořit novou aktivitu](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity1.png)

2.  Po zobrazení výzvy zadejte přihlašovací údaje zdrojových a cílových serverů a pak vyberte **Uložit**.

3.  Na **vyberte zdrojové databáze** vyberte zdrojových databází, které chcete migrovat.

    ![Vyberte zdrojové databáze](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases2.png)

4.  Vyberte **Uložit**a pak na **vyberte přihlašovací jména** obrazovky, vyberte přihlašovací jména, které chcete migrovat.

    Aktuální verze podporuje jenom migrace přihlášeních SQL.

    ![Vybrat přihlašovací jména](media\tutorial-sql-server-to-managed-instance\dms-select-logins.png)

5. Vyberte **Uložit**a pak na **nakonfigurovat nastavení migrace** obrazovky, zadejte následující podrobnosti:

    | | |
    |--------|---------|
    |**Sdíleného síťového umístění** | Místní síťové sdílené složky, Azure Database Migration Service může umístit zdroj zálohy databáze. Účet služby spuštění zdrojové instanci SQL serveru musí mít oprávnění k zápisu na dané sdílené síťové složky. Zadejte plně kvalifikovaný název domény nebo IP adres serveru ve sdílené síťové složky, například "\\\servername.domainname.com\backupfolder" nebo "\\\IP address\backupfolder".|
    |**Uživatelské jméno** | Uživatelské jméno systému windows, Azure Database Migration Service můžete vydávat se za a nahrání záložních souborů do kontejneru úložiště Azure pro operaci obnovení. |
    |**Heslo** | Heslo pro uživatele. |
    |**Nastavení účtu úložiště** | Identifikátor URI SAS, který poskytuje Azure Database Migration Service přístup k vaší kontejner účtu úložiště, do kterého služba nahraje zálohování souborů a, který se používá k migraci databází do Azure SQL Database Managed Instance. [Zjistěte, jak získat identifikátor URI SAS pro kontejner objektů blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![Nakonfigurujte nastavení migrace](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings2.png)

5.  Vyberte **Uložit**a pak na **shrnutí migrace** obrazovce **název aktivity** textové pole, zadejte název aktivity migrace.

    ![Shrnutí migrace](media\tutorial-sql-server-to-managed-instance\dms-migration-summary2.png)

6. Rozbalte **možnost ověřování** části zobrazíte **vyberte možnost ověřování** obrazovky, určete, jestli chcete ověřit správnost dotazu bude migrovaná databáze a pak vyberte **uložit** .  

7. Vyberte **spustit migraci**.

    Zobrazí se okno aktivity migrace a stav aktivity je **čekající**.

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivita migrace, vyberte **aktualizovat** aktualizovat zobrazení.
 
   ![Probíhá migrace aktivita.](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-in-progress.png)

2. Můžete dále rozšířit kategorie databází a přihlašovacích údajů, které chcete sledovat stav migrace objektů příslušný server.

   ![Probíhá migrace aktivita.](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-monitor.png)

3. Po dokončení migrace, vyberte **stáhnout sestavu** k získání sestavy Podrobnosti spojených s procesem migrace.
 
4. Ověřte, že cílová databáze na cílovém prostředí Azure SQL Database Managed Instance.

## <a name="next-steps"></a>Další postup

- Kurz ukazuje, jak migrovat databázi do Managed Instance pomocí příkazu T-SQL RESTORE, najdete v tématu [obnovení zálohy do spravované Instance pomocí příkazu restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Informace o Managed Instance najdete v tématu [co je Managed Instance](../sql-database/sql-database-managed-instance.md).
- Informace o připojování aplikací k Managed Instance najdete v tématu [připojit aplikace](../sql-database/sql-database-managed-instance-connect-app.md).
