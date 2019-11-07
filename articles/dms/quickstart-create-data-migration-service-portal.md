---
title: 'Rychlý Start: vytvoření instance Azure Database Migration Service pomocí Azure Portal | Microsoft Docs'
description: Vytvoření instance Azure Database Migration Service pomocí Azure Portal
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/05/2019
ms.openlocfilehash: 6427de61ebf30a5e4a62070becd1cc1f750f6c1b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571610"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Rychlý start: Vytvoření instance služby Azure Database Migration Service pomocí webu Azure Portal

V tomto rychlém startu použijete Azure Portal k vytvoření instance Azure Database Migration Service.  Po vytvoření služby ji můžete použít k migraci dat z SQL Server místně do Azure SQL Database.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Otevřete webový prohlížeč, přejděte na web [Microsoft Azure Portal](https://portal.azure.com/) a pak zadejte svoje přihlašovací údaje pro přihlášení k portálu.

Výchozím zobrazením je váš řídicí panel služby.

## <a name="register-the-resource-provider"></a>Registrace poskytovatele prostředků

Než vytvoříte první instanci služby Database Migration Service, zaregistrujte poskytovatele prostředků Microsoft.DataMigration.

1. Na webu Azure Portal vyberte **Všechny služby** a pak vyberte **Předplatná**.

2. Vyberte předplatné, ve kterém chcete vytvořit instanci Azure Database Migration Service a pak vyberte **poskytovatelé prostředků**.

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Vytvoření instance služby

1. Vyberte +**vytvořit prostředek** pro vytvoření instance Azure Database Migration Service.

2. Vyhledejte na marketplace „migration“, vyberte **Azure Database Migration Service** a pak na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

3. Na obrazovce **Vytvořit službu Migration Service**:

    - Vyberte **název služby** , který je zapamatovatelné a jedinečný pro identifikaci vaší instance Azure Database Migration Service.
    - Vyberte **Předplatné** Azure, ve kterém chcete instanci vytvořit.
    - Vyberte existující **skupinu prostředků** nebo vytvořte novou.
    - Zvolte **Umístění**, které je nejblíže vašemu zdrojovému nebo cílovému serveru.
    - Vyberte existující **virtuální síť** (VNET) nebo ji vytvořte.

        Virtuální síť poskytuje Azure Database Migration Service s přístupem ke zdrojové databázi a cílovému prostředí.

        Další informace o tom, jak vytvořit virtuální síť v Azure Portal, najdete v článku [vytvoření virtuální sítě pomocí Azure Portal](https://aka.ms/vnet).

    - Jako **cenovou úroveň** vyberte Basic: 1 virtuální jádro.

        ![Vytvoření služby Migration Service](media/quickstart-create-data-migration-service-portal/dms-create-service1.png)

4. Vyberte **Vytvořit**.

    Po chvíli je vaše instance služby Azure Database Migration Service vytvořená a připravená k použití. Azure Database Migration Service se zobrazí, jak je znázorněno na následujícím obrázku:

    ![Vytvořená služba Migration Service](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Všechny prostředky vytvořené v rámci tohoto rychlého startu můžete vyčistit odstraněním [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md). Pokud chcete odstranit skupinu prostředků, přejděte do instance služby Azure Database Migration Service, kterou jste vytvořili. Vyberte název **skupiny prostředků** a pak vyberte **Odstranit skupinu prostředků**. Tato akce odstraní všechny prostředky ve skupině prostředků i samotnou skupinu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Migrace místního SQL Serveru do služby Azure SQL Database](tutorial-sql-server-to-azure-sql.md)