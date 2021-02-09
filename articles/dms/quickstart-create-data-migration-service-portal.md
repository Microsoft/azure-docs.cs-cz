---
title: 'Rychlý Start: vytvoření instance pomocí Azure Portal'
titleSuffix: Azure Database Migration Service
description: K vytvoření instance Azure Database Migration Service použijte Azure Portal.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 01/29/2021
ms.openlocfilehash: 6232c842514c10a5440e574621ca74e2f4867d86
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981620"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Rychlý start: Vytvoření instance služby Azure Database Migration Service pomocí webu Azure Portal

V tomto rychlém startu použijete Azure Portal k vytvoření instance Azure Database Migration Service. Po vytvoření instance ji můžete použít k migraci dat z více databázových zdrojů do datových platforem Azure, například z SQL Server do Azure SQL Database nebo z SQL Server do spravované instance Azure SQL.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Otevřete webový prohlížeč, přejděte na web [Microsoft Azure Portal](https://portal.azure.com/) a pak zadejte svoje přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.

> [!NOTE]
> Můžete vytvořit až 10 instancí DMS na předplatné a oblast. Pokud požadujete větší počet instancí, vytvořte lístek podpory.

## <a name="register-the-resource-provider"></a>Registrace poskytovatele prostředků

Než vytvoříte první instanci služby Database Migration Service, zaregistrujte poskytovatele prostředků Microsoft.DataMigration.

1. V Azure Portal vyhledejte a vyberte **předplatná**.

   ![Zobrazení předplatných na portálu](media/quickstart-create-data-migration-service-portal/portal-select-subscription.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci Azure Database Migration Service a pak vyberte **poskytovatelé prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/quickstart-create-data-migration-service-portal/portal-select-resource-provider.png)

3. Vyhledejte migraci a pak vyberte **zaregistrovat** pro **migraci Microsoft.** data.

    ![Registrace poskytovatele prostředků](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Vytvoření instance služby

1. V nabídce Azure Portal nebo na **domovské** stránce vyberte **vytvořit prostředek**. Vyhledejte a vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/quickstart-create-data-migration-service-portal/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/quickstart-create-data-migration-service-portal/dms-create.png)

3. Na obrazovce základy **Vytvoření služby Migration Service** :

     - Vyberte předplatné.
     - Vytvořte novou skupinu prostředků nebo vyberte některou z existujících.
     - Zadejte název instance Azure Database Migration Service.
     - Vyberte umístění, ve kterém chcete vytvořit instanci Azure Database Migration Service.
     - Jako režim služby vyberte **Azure** .
     - Vyberte cenovou úroveň. Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).
     
    ![Konfigurace nastavení základy instance Azure Database Migration Service](media/quickstart-create-data-migration-service-portal/dms-create-basics.png)

     - Až skončíte, vyberte Další: Sítě.

4. Na obrazovce **vytvoření sítě služby Migration Service** :

    - Vyberte existující virtuální síť nebo vytvořte novou. Virtuální síť poskytuje Azure Database Migration Service s přístupem ke zdrojové databázi a cílovému prostředí. Další informace o tom, jak vytvořit virtuální síť v Azure Portal, najdete v článku [vytvoření virtuální sítě pomocí Azure Portal](../virtual-network/quick-create-portal.md).

    ![Konfigurace nastavení sítě Azure Database Migration Service instance](media/quickstart-create-data-migration-service-portal/dms-network-settings.png)

    - Vyberte možnost **zkontrolovat + vytvořit** a vytvořte službu. 
    
    - Po chvíli je vaše instance služby Azure Database Migration Service vytvořená a připravená k použití:

    ![Vytvořená služba Migration Service](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Prostředky vytvořené v rámci tohoto rychlého startu můžete vyčistit odstraněním [skupiny prostředků Azure](../azure-resource-manager/management/overview.md). Pokud chcete odstranit skupinu prostředků, přejděte do instance služby Azure Database Migration Service, kterou jste vytvořili. Vyberte název **skupiny prostředků** a pak vyberte **Odstranit skupinu prostředků**. Tato akce odstraní všechny prostředky ve skupině prostředků i samotnou skupinu.

## <a name="next-steps"></a>Další kroky

* [Offline migrace SQL Serveru do služby Azure SQL Database](tutorial-sql-server-to-azure-sql.md)
* [Online migrace SQL Serveru do služby Azure SQL Database](tutorial-sql-server-azure-sql-online.md)
* [Migrace SQL Server do offline spravované instance služby Azure SQL](tutorial-sql-server-to-managed-instance.md)
* [Migrace SQL Server do online spravované instance služby Azure SQL](tutorial-sql-server-managed-instance-online.md)