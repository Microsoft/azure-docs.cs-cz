---
title: Při migraci do Azure SQL Database spravované Instance postupujte DMS | Microsoft Docs
description: Zjistěte, k migraci z místního serveru SQL do Azure SQL Database spravované Instance pomocí služby Azure databáze migrace.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/07/2018
ms.openlocfilehash: 86af0101d84fe9cd44211a931567a85d7b5166e0
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261606"
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-using-dms"></a>Migrace systému SQL Server do Azure SQL Database spravované instanci pomocí DMS
Můžete používat službu Azure databáze migrace k migraci databáze z místní instance systému SQL Server do [Azure SQL Database spravované Instance](../sql-database/sql-database-managed-instance.md). Pro další metody, které mohou vyžadovat některé úsilí, najdete v článku [migrace instance systému SQL Server do Azure SQL Database spravované Instance](../sql-database/sql-database-managed-instance-migrate.md).

> [!IMPORTANT]
> Migrace projektů ze serveru SQL Server k provádění příkazu spravované Instance databáze SQL Azure jsou ve verzi preview a podléhají [doplňkové podmínky použití pro Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V tomto kurzu, migrovat **Adventureworks2012** databáze z místní instance systému SQL Server do Azure SQL Database spravované Instance pomocí služby Azure databáze migrace.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření instance služby migrace databáze Azure.
> * Vytvoření projektu migrace pomocí služby Azure databáze migrace.
> * Spusťte migrace.
> * Monitorujte migraci.
> * Stáhněte si sestavu migraci.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto kurzu potřebujete:

- Vytvoření virtuální sítě pro službu Azure databáze migrace pomocí modelu nasazení Azure Resource Manager, které poskytuje připojení site-to-site k vaší místní zdrojové servery pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Další síťové topologie pro Azure SQL DB spravované Instance migrace pomocí služby Azure databáze migrace](https://aka.ms/dmsnetworkformi).
- Ujistěte se, že vaše Azure virtuální síť (VNET) skupinu zabezpečení sítě pravidla proveďte bloku následující komunikace porty 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování provozu NSG virtuální síť Azure, najdete v článku [filtrování provozu sítě přenosů se skupinami zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurace vaší [brány Windows Firewall pro přístup k databázovému stroji zdroj](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otevřete vaší brány Windows Firewall a povolit službu Azure databáze migrace k přístupu ke zdroji systému SQL Server, který ve výchozím nastavení je TCP port 1433.
- Pokud používáte více pojmenované instance systému SQL Server pomocí dynamické porty, budete pravděpodobně chtít povolit službu SQL Browser a povolení přístupu k portu UDP 1434 přes vaší brány firewall tak, aby služba migrace databáze Azure může připojit k pojmenovaná instance na svůj zdroj Server.
- Pokud používáte zařízení brány firewall před zdrojové databáze, musíte přidat pravidla firewallu povolující službu Azure databáze migrace pro přístup k databází zdroje pro migraci, stejně jako soubory přes SMB port 445.
- Vytvoření Instance spravované databáze Azure SQL pomocí následujících podrobností v článku [vytvoření spravované Instance databáze Azure SQL na portálu Azure](https://aka.ms/sqldbmi).
- Zajistěte, aby přihlášení použít k připojení systému SQL Server zdrojové a cílové spravované Instance členové role serveru sysadmin.
- Vytvoření sdílené síťové složky, službu Azure databáze migrace můžete použít k zálohování zdrojové databáze.
- Ujistěte se, že účtu služby, zdroj je instance systému SQL Server oprávnění k zápisu ve sdílené síťové složce, kterou jste vytvořili a že účet počítače pro zdrojový server má přístup pro čtení a zápis k téže sdílené složky.
- Ujistěte se, poznamenejte uživatelem systému Windows (a heslo), který má oprávnění Úplné řízení na sdílené síťové složce, kterou jste vytvořili. Služba Azure databáze migrace zosobňuje přihlašovací údaje uživatele pro nahrání záložní soubory do kontejneru úložiště Azure pro operaci obnovení.
- Vytvořte kontejner objektů blob a načíst jeho identifikátoru URI SAS pomocí kroků v článku [prostředků spravovat Azure Blob Storage pomocí Storage Exploreru](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container), je nutné vybrat v okně zásady při všechna oprávnění (pro čtení, zápisu, odstranit, seznamu) vytváření identifikátor URI SAS. Tento podrobností poskytuje služba migrace databáze Azure přístup k vaší kontejneru účtu úložiště pro nahrávání záložní soubory použít k migraci databáze Azure SQL Database spravované Instance

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k portálu Azure, vyberte **všechny služby**a potom vyberte **odběry**.

    ![Zobrazit portálu odběrů](media\tutorial-sql-server-to-managed-instance\portal-select-subscriptions.png)        

2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Azure Database Migration Service, a pak vyberte **Poskytovatelé prostředků**.

    ![Zobrazit zprostředkovatelé prostředků](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

3. Vyhledávání pro migraci a potom napravo od **Microsoft.DataMigration**, vyberte **zaregistrovat**.

    ![Registrace poskytovatele prostředků](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Vytvoření instance služby migrace databáze Azure

1. Na portálu Azure vyberte + **vytvořit prostředek**, vyhledejte **služba migrace databáze Azure**a potom vyberte **služba migrace databáze Azure** z rozevíracího seznamu seznam.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

2. Na **služba migrace databáze Azure** obrazovku, vyberte **vytvořit**.

    ![Vytvoření instance služby migrace databáze Azure](media\tutorial-sql-server-to-managed-instance\dms-create1.png)

3. Na **vytvořit službu migrace** obrazovky, zadejte název pro tuto službu, předplatné a skupiny nový nebo existující prostředek.

4. Vyberte existující virtuální síť (VNET) nebo ji vytvořte.
 
    Virtuální sítě poskytuje službu Azure databáze migrace s přístupem k systému SQL Server zdrojové a cílové Instance spravované databáze Azure SQL.

    Další informace o tom, jak vytvořit síť VNET na portálu Azure, najdete v článku [vytvoření virtuální sítě pomocí portálu Azure](https://aka.ms/DMSVnet).

    Další podrobnosti najdete v článku [síťové topologie pro Azure SQL DB spravované Instance migrace pomocí služby Azure databáze migrace](https://aka.ms/dmsnetworkformi).

5. Vyberte cenovou úroveň.

    Další informace o náklady a cenových úrovní najdete v tématu [stránce s cenami](https://aka.ms/dms-pricing).
   
    ![Vytvořte službu DMS](media\tutorial-sql-server-to-managed-instance\dms-create-service1.png)

6.  Vyberte **vytvořit** vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby najít na portálu Azure, otevřete ho a pak vytvořte nový projekt migrace.

1. Na portálu Azure vyberte **všechny služby**, vyhledejte službu migrace databáze Azure a pak vyberte **služby migrace databáze Azure**.

    ![Vyhledejte všechny instance služby Azure databáze migrace](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. Na **služba migrace databáze Azure obrazovky**, vyhledávání pro název instance, že jste vytvořili a pak vyberte instanci.
 
3. Vyberte + **nový projekt migrace**.

4. Na **nový projekt migrace** obrazovky, zadejte název projektu, ve **serveru typ zdroje** textového pole, vyberte **systému SQL Server**a potom v **cíl Typ serveru** textového pole, vyberte **Azure SQL Database spravované Instance**.

   ![Vytvoření projektu DMS](media\tutorial-sql-server-to-managed-instance\dms-create-project1.png)

5. Vyberte **vytvořit** a vytvořte tak projekt.

## <a name="specify-source-details"></a>Zadejte podrobnosti zdroje

1. Na **zdroje podrobnosti** obrazovky, zadat podrobnosti připojení ke zdroji systému SQL Server.

2. Pokud jste nenainstalovali důvěryhodný certifikát na serveru, vyberte **certifikátu serveru důvěryhodnosti** zaškrtávací políčko.

    Když není nainstalován důvěryhodný certifikát, SQL Server vygeneruje certifikát podepsaný svým držitelem při spuštění instance. Tento certifikát se používá k šifrování přihlašovacích údajů pro připojení klientů.

    > [!CAUTION]
    > Připojení SSL, které jsou šifrované pomocí certifikát podepsaný svým držitelem neposkytuje silné zabezpečení. Jsou náchylné k útokům man-in-the-middle. Neměli byste tedy spoléhat na certifikáty podepsané svým držitelem v produkčním prostředí pomocí SSL nebo na serverech, které jsou připojené k Internetu.

   ![Podrobnosti zdroje](media\tutorial-sql-server-to-managed-instance\dms-source-details1.png)

3. Vyberte **Uložit**.

4. Na **vybrat zdrojové databáze** obrazovky, vyberte **Adventureworks2012** databáze pro migraci.

   ![Vyberte zdrojové databáze](media\tutorial-sql-server-to-managed-instance\dms-source-database1.png)

5. Vyberte **Uložit**.

## <a name="specify-target-details"></a>Zadejte podrobnosti cíl

1.  Na **cíle podrobnosti** obrazovky, zadat podrobnosti připojení pro cíl, který je předem zřízená Azure SQL spravované instanci databáze ke kterému **AdventureWorks2012** nebude databáze migrovat.

    Pokud jste ještě nezřídili spravované Instance databáze Azure SQL, vyberte **ne** pro odkaz můžete zřídit instance. Můžete dál pokračujte vytvoření projektu a pak se vraťte na tuto konkrétní projekt k provedení migrace při spravované Instance databáze Azure SQL je připraven.   
 
       ![Vyberte cíl](media\tutorial-sql-server-to-managed-instance\dms-target-details1.png)

2.  Vyberte **Uložit**.

3.  Na **souhrn projektu** obrazovce zkontrolujte a zkontrolujte podrobnosti o přidružený k projektu migrace.
 
    ![Souhrn migrace projektu](media\tutorial-sql-server-to-managed-instance\dms-project-summary1.png)

4.  Vyberte **Uložit**.   

## <a name="run-the-migration"></a>Spustit migrace

1.  Vyberte nedávno uložený projekt, vyberte + **nová aktivita**a potom vyberte **spuštění migrace**.

    ![Vytvořit novou aktivitu](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity1.png)

2.  Po zobrazení výzvy zadejte přihlašovací údaje zdrojové a cílové servery a potom vyberte **Uložit**.

3.  Na **vybrat zdrojové databáze** vyberte zdrojové databáze, kterou chcete migrovat.

    ![Vyberte zdrojové databáze](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases1.png)

4.  Vyberte **Uložit**a pak na **nakonfigurovat nastavení migrace** obrazovky, zadejte následující podrobnosti:

    | | |
    |--------|---------|
    |**Síťové umístění sdílené složky** | Místní síti sdílet, že služba Azure databáze migrace může trvat zdroj zálohy databází na. Účtu služby, zdroj instance systému SQL Server musí mít oprávnění k zápisu v této sdílené síťové složce. Zadejte plně kvalifikovaný název domény nebo IP adresy serveru ve sdílené síťové složce, například "\\\servername.domainname.com\backupfolder' nebo '\\\IP address\backupfolder'.|
    |**Uživatelské jméno** | Uživatelské jméno systému windows, můžete službu Azure databáze migrace zosobnění a nahrát záložní soubory do kontejneru úložiště Azure pro operaci obnovení. |
    |**Heslo** | Heslo pro uživatele. |
    |**Nastavení účtu úložiště** | Identifikátor URI SAS, který zajišťuje, že služba migrace databáze Azure přístup k vaší účet kontejneru úložiště, do které služba odesílá zálohování souborů a který se používá k migraci databází, které mají spravované Instance serveru Azure SQL Database. [Zjistěte, jak získat identifikátor URI SAS pro kontejner objektů blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![Nakonfigurovat nastavení migrace](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings1.png)

5.  Vyberte **Uložit**a pak na **migrace Souhrn** obrazovce **název aktivity** textové pole, zadejte název aktivity migrace.

6. Rozbalte **možnost ověření** části zobrazíte **zvolte možnost ověření** obrazovky, určete, zda chcete ověřit, databázi migrované správnost dotazu a potom vyberte **uložit** .  

    ![Souhrn migrace](media\tutorial-sql-server-to-managed-instance\dms-migration-summary1.png)

7. Vyberte **spuštění migrace**.

    Zobrazí se okno aktivity migrace a stav aktivity je **čekající**.

   ![Aktivity související s migrací čekající na vyřízení](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-pending.png)

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivity migrace vyberte **aktualizovat** aktualizovat zobrazení, dokud se nezobrazí, že je migrace stavu jako **dokončeno**.
 
   ![Migrace aktivita dokončena](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-finished.png)

2. Po dokončení migrace, vyberte **stáhnout sestavu** získat sestavu se seznamem podrobnosti přidružené k procesu migrace.
 
3. Ověřte, že cílová databáze v cílovém prostředí Azure spravované Instance databáze SQL.

## <a name="next-steps"></a>Další postup

- Kurz ukazuje, jak migrovat databázi k instanci spravované pomocí příkazu RESTORE T-SQL, najdete v části [obnovit zálohu do Instance spravované pomocí příkazu restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Informace o instanci spravované najdete v tématu [co je Instance spravované](../sql-database/sql-database-managed-instance.md).
- Informace o připojení k instanci spravované aplikace najdete v tématu [připojit aplikace](../sql-database/sql-database-managed-instance-connect-app.md).
