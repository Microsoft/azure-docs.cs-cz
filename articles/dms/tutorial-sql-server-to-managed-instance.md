---
title: "Používat službu Azure databáze migrace k migraci na Azure SQL Database spravované Instance systému SQL Server | Microsoft Docs"
description: "Zjistěte, k migraci z místního serveru SQL do Azure SQL Database spravované Instance pomocí služby Azure databáze migrace."
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: 
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 6a581141a3d262416385d859d2f0e73228fe1734
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance"></a>Migrace systému SQL Server na spravované Instance databáze Azure SQL
Službu Azure databáze migrace můžete použít k migraci databáze z místní instance systému SQL Server do Azure SQL Database. V tomto kurzu, migrovat **Adventureworks2012** databáze z místní instance systému SQL Server do databáze SQL Azure pomocí služby Azure databáze migrace.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření instance služby migrace databáze Azure.
> * Vytvoření projektu migrace pomocí služby Azure databáze migrace.
> * Spusťte migrace.
> * Monitorujte migraci.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto kurzu potřebujete:

- Vytvoření virtuální sítě pro službu Azure databáze migrace pomocí modelu nasazení Azure Resource Manager, které poskytuje připojení site-to-site k vaší místní zdrojové servery pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Další síťové topologie pro Azure SQL DB spravované Instance migrace pomocí služby Azure databáze migrace](https://aka.ms/dmsnetworkformi).
- Ujistěte se, že vaše Azure virtuální síť (VNET) skupinu zabezpečení sítě pravidla proveďte bloku následující komunikace porty 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování provozu NSG virtuální síť Azure, najdete v článku [filtrování provozu sítě přenosů se skupinami zabezpečení sítě](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg).
- Konfigurace vaší [konfigurace brány Windows Firewall pro přístup k databázovému stroji zdroj](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otevřete vaší brány Windows firewall a povolit službu Azure databáze migrace k přístupu ke zdroji systému SQL Server.
- Pokud používáte zařízení brány firewall před zdrojové databáze, musíte přidat pravidla firewallu povolující službu Azure databáze migrace pro přístup k databází zdroje pro migraci, stejně jako soubory přes SMB port 445.
- Vytvořte instanci Azure spravované Instance databáze SQL pomocí následujících podrobností v článku [vytvoření spravované Instance databáze Azure SQL na portálu Azure](https://aka.ms/sqldbmi).
- Zajistěte, aby přihlášení použít k připojení systému SQL Server zdrojové a cílové spravované Instance členové role serveru sysadmin.
- Vytvoření sdílené síťové složky, službu Azure databáze migrace můžete použít k zálohování zdrojové databáze.
- Ujistěte se, zda má účet služby spuštění instance systému SQL Server zdrojové oprávnění k zápisu ve sdílené síťové složce, kterou jste vytvořili.
- Poznamenejte si uživatel systému Windows (a heslo), který má oprávnění Úplné řízení ve sdílené síťové složce, kterou jste vytvořili výše. Služba Azure databáze migrace bude zosobnit přihlašovací údaje uživatele pro nahrání záložní soubory do kontejneru úložiště Azure pro operaci obnovení.
- Vytvořte kontejner objektů blob a načíst jeho identifikátoru URI SAS pomocí kroků v článku [prostředků spravovat Azure Blob Storage pomocí Storage Exploreru (Preview)](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container), je nutné vybrat všechna oprávnění (pro čtení, zápisu, odstranit, seznamu) v okně Zásady Při vytváření identifikátor URI SAS. To bude služba migrace databáze Azure poskytnout přístup k úložiště kontejner účet pro odesílání záložní soubory, které se použijí pro migraci databází, které mají spravované Instance serveru Azure SQL Database

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1.  Přihlaste se k portálu Azure, vyberte **všechny služby**a potom vyberte **odběry**.
![Zobrazit portálu odběrů](media\tutorial-sql-server-to-managed-instance\portal-select-subscription.png)

1.  Vyberte předplatné, ve kterém chcete vytvořit instanci služby Azure Database Migration Service, a pak vyberte **Poskytovatelé prostředků**.
![Zobrazit zprostředkovatelé prostředků](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

1.  Vyhledávání pro migraci a potom napravo od **Microsoft.DataMigration**, vyberte **zaregistrovat**.
![registrace poskytovatele prostředků](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Vytvoření instance

1.  Na portálu Azure vyberte **+ vytvořit prostředek**, vyhledejte **služba migrace databáze Azure**a potom vyberte **služba migrace databáze Azure** z rozevíracího seznamu seznam.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

1.  Na **služba migrace databáze Azure (preview)** obrazovku, vyberte **vytvořit**.

    ![Vytvoření instance služby migrace databáze Azure](media\tutorial-sql-server-to-managed-instance\dms-create.png)

1.  Na **služba migrace databáze** obrazovky, zadejte název pro tuto službu, předplatné, skupinu prostředků, virtuální síť a cenovou úroveň.

    Další informace o náklady a cenových úrovní najdete v tématu [stránce s cenami](https://aka.ms/dms-pricing). *Služba Azure migrace databáze je aktuálně ve verzi preview a vám nebude nic účtováno.*

    **Síť:** vyberte existující nebo vytvořte novou virtuální síť, které služba migrace databáze Azure poskytuje přístup k systému SQL Server zdrojové a cílové Instance spravované databáze Azure SQL. [Další síťové topologie pro Azure SQL DB spravované Instance migrace pomocí služby Azure databáze migrace](https://aka.ms/dmsnetworkformi).

    Další informace o tom, jak vytvořit síť VNET na portálu Azure najdete v tématu [vytvořit virtuální síť s více podsítěmi pomocí portálu Azure](https://aka.ms/DMSVnet).

    ![Vytvořte službu DMS](media\tutorial-sql-server-to-managed-instance\dms-create-service.png)

1.  Vyberte **vytvořit** vytvořte službu.


## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby najít na portálu Azure a otevřete jej.

1.  Vyberte **+ nový projekt migrace**.

1.  Na **nový projekt migrace** obrazovky, zadejte název projektu, ve **serveru typ zdroje** textového pole, vyberte **systému SQL Server**a potom v **cíl Typ serveru** textového pole, vyberte **Azure SQL Database spravované Instance**.

    ![Vytvoření projektu DMS](media\tutorial-sql-server-to-managed-instance\dms-create-project.png)

1.  Vyberte **vytvořit** a vytvořte tak projekt.

## <a name="specify-source-details"></a>Zadejte podrobnosti zdroje

1.  Na **zdroje podrobnosti** obrazovky, zadat podrobnosti připojení ke zdroji systému SQL Server.

    ![Podrobnosti zdroje](media\tutorial-sql-server-to-managed-instance\dms-source-details.png)

1.  Vyberte **Uložit**a pak vyberte **Adventureworks2012** databáze pro migraci.

    ![Vyberte zdrojové databáze](media\tutorial-sql-server-to-managed-instance\dms-source-database.png)

## <a name="specify-target-details"></a>Zadejte podrobnosti cíl

1.  Vyberte **Uložit**a pak na **cíle podrobnosti** obrazovky, zadat podrobnosti připojení pro cíl, který je předem zřízená Azure SQL spravované instanci databáze ke kterému  **AdventureWorks2012** databáze se budou migrovat.

    ![Vyberte cíl](media\tutorial-sql-server-to-managed-instance\dms-target-details.png)

1.  Vyberte **Uložit**.

1.  Na **souhrn projektu** obrazovce zkontrolujte a zkontrolujte podrobnosti o přidružený k projektu migrace.

## <a name="run-the-migration"></a>Spustit migrace

1.  Vyberte nedávno uložený projekt, vyberte **+ nová aktivita**a potom vyberte **spuštění migrace**.

    ![Vytvořit novou aktivitu](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity.png)

1.  Po zobrazení výzvy zadejte přihlašovací údaje zdrojové a cílové servery a potom vyberte **Uložit**.

1.  Na **mapu, která bude cílovým databázím** vyberte zdrojové databáze, kterou chcete migrovat.

    ![Vyberte zdrojové databáze](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases.png)

1.  Vyberte **Uložit**na **nakonfigurovat nastavení migrace** obrazovky, zadejte následující podrobnosti:

    | | |
    |--------|---------|
    |**Sever umístění zálohy** | Místní síti sdílet, že služba Azure databáze migrace může trvat zdroj zálohy databází na. Účtu služby, zdroj instance systému SQL Server musí mít oprávnění k zápisu v této sdílené síťové složce. |
    |**Uživatelské jméno** | Uživatelské jméno systému windows, můžete službu Azure databáze migrace zosobnění a nahrát záložní soubory do kontejneru úložiště Azure pro operaci obnovení. |
    |**Heslo** | Heslo pro výše uvedené uživatele. |
    |**Identifikátor URI SAS úložiště** | Identifikátor URI SAS, která poskytuje přístup k vaší kontejneru účtu úložiště, který služba bude nahrávat soubory zálohování a který se použije pro migraci službu Azure databáze migrace databáze do Azure SQL Database spravované Instance.  [Zjistěte, jak získat identifikátor URI SAS pro kontejner objektů blob](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![Nakonfigurovat nastavení migrace](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings.png)

1.  Vyberte **Uložit**na obrazovce souhrnné migrace, **název aktivity** text pole, zadejte název aktivity migrace.

    ![Souhrn migrace](media\tutorial-sql-server-to-managed-instance\dms-migration-summary.png)


## <a name="monitor-the-migration"></a>Monitorování migrace

1.  Vyberte aktivitu migrace, můžete zkontrolovat stav aktivity.

1.  Po dokončení migrace, ověřte cílovým databázím na cílové Instance spravované databáze Azure SQL.

    ![Monitorování migrace](media\tutorial-sql-server-to-managed-instance\dms-monitor-migration.png)

