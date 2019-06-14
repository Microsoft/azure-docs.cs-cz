---
title: Migrace balíčků SQL Server Integration Services do spravované instance Azure SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak přenést balíčky SQL Server Integration Services do spravované instance Azure SQL Database.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/08/2019
ms.openlocfilehash: 82a047616c199e37bfa22f53e02f3f7b224b47c1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083181"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>Migrace balíčků SQL Server Integration Services do spravované instance Azure SQL Database
Pokud používáte SQL Server Integration Services (SSIS) a chcete provést migraci vašich projektů/balíčků služby SSIS ze zdroje databází SSISDB hostovanou službou SQL Server do cílového umístění databází SSISDB hostovanou ve spravované instanci Azure SQL Database, můžete použít Azure Database Migration Service.

Pokud je verze služby SSIS, můžete použít starší než 2012 nebo použijte typy úložiště balíčků – databáze SSISDB, před migrací vaší služby SSIS projektů/balíčků, budete muset převést pomocí integrace služby projektu převod průvodce, který můžete také spustit z aplikace SSMS. Další informace najdete v článku [převod projektů do modelu nasazení projektu](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) aktuálně nepodporuje Azure SQL Database jako migrace cílového místa. K opětovnému nasazení projektů/balíčků služby SSIS do Azure SQL Database, najdete v článku [balíčků znovu nasadit SQL Server Integration Services do služby Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
>
> * Vyhodnoťte zdroj projektů/balíčků služby SSIS.
> * Migrace projektů/balíčků služby SSIS do Azure.

## <a name="prerequisites"></a>Požadavky

K dokončení těchto kroků budete potřebovat:

* Vytvoření služby Azure Virtual Network (VNet) pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manageru, který poskytuje připojení site-to-site k vašich zdrojových serverů s místními pomocí [ExpressRoute ](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Další informace najdete v článku [síťové topologie pro Azure SQL Database managed instance migrace pomocí Azure Database Migration Service]( https://aka.ms/dmsnetworkformi). Další informace o vytvoření virtuální sítě, najdete v článku [dokumentace k Virtual Network](https://docs.microsoft.com/azure/virtual-network/)a hlavně článků rychlý start s podrobný.
* Chcete-li zajistit, že pravidla skupiny zabezpečení sítě VNet nedošlo k blokování následující porty příchozí komunikace k Azure Database Migration Service: 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování provozu skupiny zabezpečení sítě Azure VNet najdete v článku [filtrování provozu sítě s použitím skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Ke konfiguraci vaší [brány Windows Firewall pro přístup k databázi zdrojové](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017).
* Chcete-li otevřít bránu Windows Firewall a povolit službu Azure Database Migration Service přístup k zdrojového systému SQL Server, který ve výchozím nastavení je TCP port 1433.
* Pokud provozujete několik pojmenovaných instancí SQL Serveru s využitím dynamických portů, možná budete chtít povolit službu SQL Browser a přístup k portu UDP 1434 přes vaše brány firewall, aby se služba Azure Database Migration Service mohla připojit k pojmenované instanci na vašem zdrojovém serveru.
* Pokud před zdrojovými databázemi používáte zařízení brány firewall, možná bude potřeba přidat pravidla brány firewall, která službě Azure Database Migration Service povolí přístup ke zdrojovým databázím za účelem migrace a také k souborům přes port SMB 445.
* Azure SQL Database managed instance pro hostování služby SSISDB. Pokud je potřeba ho vytvořit, postupujte podle podrobností v článku [vytvořit Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Ujistěte se, že přihlašovací údaje použité pro připojení zdroje spravovanou instanci SQL serveru a cílová jsou členové role serveru sysadmin.
* Chcete-li ověřit, že je zřízený služby SSIS v Azure Data Factory (ADF) obsahující prostředí Azure-SSIS Integration Runtime (IR) s cílovým databází SSISDB hostovanou ve službě Azure SQL Database managed instance (jak je popsáno v článku [vytvořit prostředí Azure-SSIS prostředí Integration runtime ve službě Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)).

## <a name="assess-source-ssis-projectspackages"></a>Vyhodnotit zdroj projektů/balíčků služby SSIS

Během posouzení zdroje SSISDB není zatím integrované do Database Migration Assistant (DMA), projektů/balíčků služby SSIS použit k vyhodnocení/ověří jako už znovu nasadil do cíle, které SSISDB hostované na spravované instanci Azure SQL Database.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

    ![Zobrazení předplatných na portálu](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Azure Database Migration Service a pak vyberte **poskytovatelů prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Vytvoření instance služby Azure Database Migration Service

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte **Azure Database Migration Service** a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete instanci DMS vytvořit.

5. Vyberte existující virtuální síť nebo vytvořit novou.

    Virtuální síť poskytuje Azure Database Migration Service přístup k systému SQL Server zdrojové a cílové spravované instance Azure SQL Database.

    Další informace o tom, jak vytvořit virtuální síť na webu Azure portal najdete v článku [vytvořit virtuální síť pomocí webu Azure portal](https://aka.ms/DMSVnet).

    Další podrobnosti najdete v článku [síťové topologie pro Azure SQL DB managed instance migrace pomocí Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Vyberte cenovou úroveň.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Vytvoření služby DMS](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření instance služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

    ![Vyhledání všech instancí služby Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. Na obrazovce **Azure Database Migration Service** vyhledejte název instance, kterou jste vytvořili, a vyberte ji.

3. Vyberte **+ Nový projekt migrace**.

4. Na **nový projekt migrace** obrazovky, zadejte název projektu, v **serveru typ zdroje** textového pole, vyberte **systému SQL Server**v **cílového serveru typ** textového pole, vyberte **Azure SQL Database Managed Instance**a potom **zvolte typ aktivity**vyberte **balíčků služby SSIS migrace**.

   ![Vytvoření projektu DMS](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Vyberte **Vytvořit** a vytvořte projekt.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Podrobnosti zdroje migrace** zadejte podrobnosti o připojení ke zdrojovému SQL Serveru.

2. Pokud jste na svém serveru nenainstalovali důvěryhodný certifikát, zaškrtněte políčko **Důvěřovat certifikátu serveru**.

    Pokud není nainstalovaný důvěryhodný certifikát, SQL Server při spuštění instance vygeneruje certifikát podepsaný svým držitelem. Tento certifikát slouží k šifrování přihlašovacích údajů pro připojení klientů.

    > [!CAUTION]
    > Připojení SSL šifrovaná pomocí certifikátu podepsaného svým držitelem neposkytují silné zabezpečení. Jsou náchylná na útoky, kdy se útočníci vydávají za prostředníky. Na připojení SSL s použitím certifikátů podepsaných svým držitelem byste neměli spoléhat v produkčním prostředí ani na serverech připojených k internetu.

   ![Podrobnosti zdroje](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Vyberte **Uložit**.

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Na **podrobnosti o cíli migrace** obrazovky, zadat podrobnosti připojení pro cíl.

     ![Podrobnosti o cíli](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Vyberte **Uložit**.

## <a name="review-the-migration-summary"></a>Kontrola shrnutí migrace

1. Na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace.

2. Pro **SSIS projekty a prostředí možnost přepsání**, určete, zda chcete přepsat nebo ignorovat existující projekty služby SSIS a prostředí.

    ![Shrnutí projektu migrace](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Zkontrolujte a ověřte podrobnosti související s projektem migrace.

## <a name="run-the-migration"></a>Spuštění migrace

* Vyberte **Spustit migraci**.

## <a name="next-steps"></a>Další postup

* Přečtěte si pokyny k migraci v Microsoftu [Průvodce migrací databází](https://datamigration.microsoft.com/).
