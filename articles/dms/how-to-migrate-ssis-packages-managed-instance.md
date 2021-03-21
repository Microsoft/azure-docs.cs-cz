---
title: Migrace balíčků SSIS do spravované instance SQL
titleSuffix: Azure Database Migration Service
description: Naučte se migrovat balíčky a projekty služba SSIS (SQL Server Integration Services) (SSIS) do spravované instance Azure SQL pomocí Azure Database Migration Service nebo Data Migration Assistant.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: e3e2aa055baf3dfb4bee0629040fc7c140844637
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101094013"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-managed-instance"></a>Migrace balíčků služba SSIS (SQL Server Integration Services) do spravované instance Azure SQL
Pokud používáte služba SSIS (SQL Server Integration Services) (SSIS) a chcete migrovat projekty SSIS/balíčky ze zdrojového SSISDB hostovaného SQL Server do cílového SSISDBu hostovaného službou Azure SQL Managed instance, můžete použít Azure Database Migration Service.

Pokud je verze SSIS, kterou používáte, starší než 2012 nebo používáte typy úložiště balíčků bez SSISDB, je nutné je před migrací projektů a balíčků SSIS převést pomocí Průvodce převodem projektu integrační služby, který lze také spustit z SSMS. Další informace naleznete v článku [Převod projektů do modelu nasazení projektu](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) v současné době nepodporuje Azure SQL Database jako cílovou cílovou migraci. Chcete-li znovu nasadit projekty nebo balíčky SSIS do Azure SQL Database, přečtěte si článek [služba SSIS (SQL Server Integration Services) balíčky znovu nasaďte do Azure SQL Database](./how-to-migrate-ssis-packages.md).

V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
>
> * Vyhodnoťte zdrojové SSIS projekty/balíčky.
> * Migrujte projekty a balíčky SSIS do Azure.

## <a name="prerequisites"></a>Předpoklady

K provedení těchto kroků potřebujete:

* Pokud chcete vytvořit Microsoft Azure Virtual Network pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manager, který zajišťuje připojení typu Site-to-site k místním zdrojovým serverům pomocí [ExpressRoute](../expressroute/expressroute-introduction.md) nebo [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Další informace najdete v článku [síťové topologie pro migrace spravované instance SQL pomocí Azure Database Migration Service]( https://aka.ms/dmsnetworkformi). Další informace o vytváření virtuálních sítí najdete v [dokumentaci k Virtual Network](../virtual-network/index.yml)a zejména v článcích rychlý Start s podrobnými údaji.
* Aby se zajistilo, že pravidla skupiny zabezpečení sítě virtuální sítě neblokují odchozí port 443 ServiceTag pro ServiceBus, Storage a AzureMonitor. Další podrobnosti o filtrování provozu NSG virtuální sítě najdete v článku [filtrování provozu sítě pomocí skupin zabezpečení sítě](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Ke konfiguraci [brány Windows Firewall pro přístup ke zdrojovému databázovému stroji](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017).
* Otevřete bránu Windows Firewall, aby Azure Database Migration Service mohl získat přístup ke zdrojovému SQL Server, což je standardně port TCP 1433.
* Pokud provozujete několik pojmenovaných instancí SQL Serveru s využitím dynamických portů, možná budete chtít povolit službu SQL Browser a přístup k portu UDP 1434 přes vaše brány firewall, aby se služba Azure Database Migration Service mohla připojit k pojmenované instanci na vašem zdrojovém serveru.
* Pokud před zdrojovými databázemi používáte zařízení brány firewall, možná bude potřeba přidat pravidla brány firewall, která službě Azure Database Migration Service povolí přístup ke zdrojovým databázím za účelem migrace a také k souborům přes port SMB 445.
* Spravovaná instance SQL pro hostování SSISDB. Pokud ho potřebujete vytvořit, postupujte podle podrobných pokynů v článku [Vytvoření spravované instance Azure SQL](../azure-sql/managed-instance/instance-create-quickstart.md).
* Aby se zajistilo, že přihlášení používaná k připojení zdrojového SQL Server a cílové spravované instance jsou členy role serveru sysadmin.
* Pokud chcete ověřit, že se SSIS zřídí v Azure Data Factory (ADF) obsahující Azure-SSIS Integration Runtime (IR) s cílovým SSISDB hostovaným spravovanou instancí SQL (jak je popsáno v článku [vytvoření prostředí Azure-SSIS Integration runtime v Azure Data Factory](../data-factory/create-azure-ssis-integration-runtime.md)).

## <a name="assess-source-ssis-projectspackages"></a>Vyhodnocení zdrojového SSIS projektů/balíčků

I když hodnocení source SSISDB ještě není integrované do databáze Pomocník s migrací (DMA), vaše SSIS projekty nebo balíčky se vyhodnotí nebo ověří při jejich opětovném nasazení do cílového SSISDB hostovaného ve spravované instanci SQL Azure.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

    ![Zobrazení předplatných na portálu](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci Azure Database Migration Service a pak vyberte **poskytovatelé prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Vytvoření instance služby Azure Database Migration Service

1. V Azure Portal vyberte + **vytvořit prostředek**, vyhledejte **Azure Database Migration Service** a v rozevíracím seznamu vyberte **Azure Database Migration Service** .

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete instanci DMS vytvořit.

5. Vyberte existující virtuální síť nebo ji vytvořte.

    Virtuální síť poskytuje Azure Database Migration Service s přístupem ke zdrojové SQL Server a cílovou službou Azure SQL Managed instance.

    Další informace o tom, jak vytvořit virtuální síť v Azure Portal, najdete v článku [vytvoření virtuální sítě pomocí Azure Portal](../virtual-network/quick-create-portal.md).

    Další podrobnosti najdete v článku [síťové topologie pro migrace spravované instance SQL Azure pomocí Azure Database Migration Service](./resource-network-topologies.md).

6. Vyberte cenovou úroveň.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Vytvoření služby DMS](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření instance služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

    ![Vyhledání všech instancí služby Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. Na obrazovce **Azure Database Migration Service** vyhledejte název instance, kterou jste vytvořili, a pak vyberte instanci.

3. Vyberte **+ Nový projekt migrace**.

4. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli **typ zdrojového serveru** vyberte možnost **SQL Server**, v textovém poli **typ CÍLOVÉHO serveru** vyberte možnost **spravovaná instance Azure SQL** a pak pro **zvolit typ aktivity** vyberte možnost **migrace balíčku SSIS**.

   ![Vytvoření projektu DMS](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Vyberte **Vytvořit** a vytvořte projekt.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Podrobnosti zdroje migrace** zadejte podrobnosti o připojení ke zdrojovému SQL Serveru.

2. Pokud jste na svém serveru nenainstalovali důvěryhodný certifikát, zaškrtněte políčko **Důvěřovat certifikátu serveru**.

    Pokud není nainstalovaný důvěryhodný certifikát, SQL Server při spuštění instance vygeneruje certifikát podepsaný svým držitelem. Tento certifikát slouží k šifrování přihlašovacích údajů pro připojení klientů.

    > [!CAUTION]
    > Připojení TLS zašifrovaná pomocí certifikátu podepsaného svým držitelem neposkytují silné zabezpečení. Jsou náchylná na útoky, kdy se útočníci vydávají za prostředníky. Neměli byste spoléhat na protokol TLS pomocí certifikátů podepsaných držitelem v produkčním prostředí nebo na serverech, které jsou připojené k Internetu.

   ![Podrobnosti zdroje](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Vyberte **Uložit**.

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Na obrazovce **Podrobnosti cíle migrace** zadejte podrobnosti o připojení pro cíl.

     ![Údaje o cíli](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Vyberte **Uložit**.

## <a name="review-the-migration-summary"></a>Kontrola shrnutí migrace

1. Na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace.

2. Pro **SSIS projekty a prostředí (y) možnost přepsat** určete, jestli se mají přepsat nebo ignorovat existující projekty a prostředí SSIS.

    ![Shrnutí projektu migrace](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Zkontrolujte a ověřte podrobnosti související s projektem migrace.

## <a name="run-the-migration"></a>Spuštění migrace

* Vyberte **Spustit migraci**.

## <a name="next-steps"></a>Další kroky

* Přečtěte si pokyny k migraci v [Průvodci migrací databáze](https://datamigration.microsoft.com/)společnosti Microsoft.