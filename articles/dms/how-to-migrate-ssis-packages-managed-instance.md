---
title: Migrace balíčků SSIS do spravované instance SQL
titleSuffix: Azure Database Migration Service
description: Zjistěte, jak migrovat balíčky a projekty služby SQL Server Integration Services (SSIS) do spravované instance Azure SQL Database pomocí služby migrace databáze Azure nebo Pomocníka pro migraci dat.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 97a466ab033a42016c0d82465d1f98e2dcae8080
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297178"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>Migrace balíčků služby SSIS (SQL Server Integration Services) do spravované instance Azure SQL Database
Pokud používáte službu SQL Server Integration Services (SSIS) a chcete migrovat projekty/balíčky SSIS ze zdrojového SSISDB hostovaného SQL Serverem do cílové databáze SSISDB hostované instancí spravované databází Azure SQL Database, můžete použít službu Migrace databáze Azure.

Pokud je verze SSIS, kterou používáte, starší než 2012 nebo používáte jiné typy obchodů s balíčky SSISDB, před migrací projektů/balíčků SSIS je nutné je převést pomocí Průvodce převodem projektu integračních služeb, který lze spustit také ze služby SSMS. Další informace naleznete v článku [Převod projektů na model nasazení projektu](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Služba Migrace databáze Azure (DMS) momentálně nepodporuje Azure SQL Database jako cílovou migraci. Pokud chcete znovu nasadit projekty/balíčky SSIS do Azure SQL Database, přečtěte si článek [Znovu nasadit balíčky služby SQL Server Integration Services do Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
>
> * Posoudit zdroj SSIS projekty/balíčky.
> * Migrace projektů/balíčků SSIS do Azure.

## <a name="prerequisites"></a>Požadavky

Chcete-li provést tyto kroky, potřebujete:

* Vytvoření virtuální sítě Microsoft Azure pro službu migrace databáze Azure pomocí modelu nasazení Azure Resource Manager, který poskytuje připojení k místním zdrojovým serverům site-to-site pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Další informace najdete v článku [Síťové topologií pro migrace spravovaných instancí Azure SQL Database pomocí služby Migrace databáze Azure]( https://aka.ms/dmsnetworkformi). Další informace o vytvoření virtuální sítě naleznete v [dokumentaci k virtuální síti](https://docs.microsoft.com/azure/virtual-network/)a zejména v článcích rychlého startu s podrobnými podrobnostmi.
* Chcete-li zajistit, aby pravidla skupiny zabezpečení sítě virtuální sítě neblokovala následující příchozí komunikační porty služby Azure Database Migration Service: 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování přenosů skupin nsg ve virtuální síti naleznete v článku [Filtrování síťového provozu se skupinami zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Konfigurace brány [Windows Firewall pro přístup ke zdrojovému databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017).
* Otevření brány Windows Firewall umožňuje službě Migrace databáze Azure přístup ke zdrojovému serveru SQL Server, který je ve výchozím nastavení port TCP 1433.
* Pokud provozujete několik pojmenovaných instancí SQL Serveru s využitím dynamických portů, možná budete chtít povolit službu SQL Browser a přístup k portu UDP 1434 přes vaše brány firewall, aby se služba Azure Database Migration Service mohla připojit k pojmenované instanci na vašem zdrojovém serveru.
* Pokud před zdrojovými databázemi používáte zařízení brány firewall, možná bude potřeba přidat pravidla brány firewall, která službě Azure Database Migration Service povolí přístup ke zdrojovým databázím za účelem migrace a také k souborům přes port SMB 445.
* Instance spravované službou Azure SQL Database pro hostování SSISDB. Pokud potřebujete vytvořit, postupujte podle podrobností v článku [Vytvořit Azure SQL Database Spravované instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Chcete-li zajistit, že přihlášení slouží k připojení zdrojsql server a cíl spravované instance jsou členy role serveru sysadmin.
* Ověření, že se SSIS zřápá v Azure Data Factory (ADF) obsahující modul Runtime integrace Azure-SSIS (IR) s cílovým SSISDB hostovaným spravovanou instancí Azure SQL Database (jak je popsáno v článku [Vytvoření runtime integrace Azure-SSIS v Azure Data Factory).](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)

## <a name="assess-source-ssis-projectspackages"></a>Posoudit zdroj projektů/balíčků SSIS

Zatímco hodnocení zdrojového SSISDB ještě není integrováno do Pomocníka pro migraci databáze (DMA), vaše projekty/balíčky SSIS budou posouzeny nebo ověřeny, jakmile se znovu nasadí do cílové Hodsu SIsDB hostovaného v spravované instanci Azure SQL Database.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

    ![Zobrazení předplatných na portálu](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Migrace databáze Azure, a pak vyberte **zprostředkovatele prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Vytvoření instance služby Azure Database Migration Service

1. Na webu Azure Portal vyberte + **Vytvořit prostředek**, vyhledejte **službu Migrace databáze Azure**a v rozevíracím seznamu vyberte **službu Migrace databáze Azure.**

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete instanci DMS vytvořit.

5. Vyberte existující virtuální síť nebo ji vytvořte.

    Virtuální síť poskytuje službě Migrace databáze Azure přístup ke zdrojovému serveru SQL Server a cílové instanci spravované službou Azure SQL Database.

    Další informace o tom, jak vytvořit virtuální síť na webu Azure Portal, najdete v článku [Vytvoření virtuální sítě pomocí portálu Azure](https://aka.ms/DMSVnet).

    Další podrobnosti najdete v článku [Síťové topologií pro migrace spravovaných instancí Azure SQL DB pomocí služby Migrace databáze Azure](https://aka.ms/dmsnetworkformi).

6. Vyberte cenovou úroveň.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Vytvoření služby DMS](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření instance služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

    ![Vyhledání všech instancí služby Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. Na obrazovce **Azure Database Migration Service** vyhledejte název instance, kterou jste vytvořili, a vyberte instanci.

3. Vyberte **+ Nový projekt migrace**.

4. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli Typ **zdrojového serveru** vyberte sql **server**, v textovém poli **Typ cílového serveru,** vyberte Azure **SQL Database Managed Instance**a potom pro zvolit **typ aktivity**, vyberte **migraci balíčku SSIS**.

   ![Vytvoření projektu DMS](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Vyberte **Vytvořit** a vytvořte projekt.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Podrobnosti zdroje migrace** zadejte podrobnosti o připojení ke zdrojovému SQL Serveru.

2. Pokud jste na svém serveru nenainstalovali důvěryhodný certifikát, zaškrtněte políčko **Důvěřovat certifikátu serveru**.

    Pokud není nainstalovaný důvěryhodný certifikát, SQL Server při spuštění instance vygeneruje certifikát podepsaný svým držitelem. Tento certifikát slouží k šifrování přihlašovacích údajů pro připojení klientů.

    > [!CAUTION]
    > Připojení TLS, která jsou šifrována pomocí certifikátu podepsaného svým držitelem, neposkytuje silné zabezpečení. Jsou náchylná na útoky, kdy se útočníci vydávají za prostředníky. Na protokol TLS byste neměli spoléhat pomocí certifikátů podepsaných svým držitelem v produkčním prostředí nebo na serverech, které jsou připojeny k Internetu.

   ![Podrobnosti zdroje](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Vyberte **Uložit**.

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Na obrazovce **podrobnosti cíle migrace** zadejte podrobnosti o připojení pro cíl.

     ![Podrobnosti o cíli](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Vyberte **Uložit**.

## <a name="review-the-migration-summary"></a>Kontrola shrnutí migrace

1. Na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace.

2. Pro **projekty SSIS a prostředí přepsání možnost**, určete, zda chcete přepsat nebo ignorovat existující projekty SSIS a prostředí.

    ![Shrnutí projektu migrace](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Zkontrolujte a ověřte podrobnosti související s projektem migrace.

## <a name="run-the-migration"></a>Spuštění migrace

* Vyberte **Spustit migraci**.

## <a name="next-steps"></a>Další kroky

* Přečtěte si pokyny k migraci v [Příručce pro migraci databáze](https://datamigration.microsoft.com/)společnosti Microsoft .
