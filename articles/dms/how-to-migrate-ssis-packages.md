---
title: Migrace balíčků SQL Server Integration Services do Azure | Dokumentace Microsoftu
description: Zjistěte, jak přenést balíčky SQL Server Integration Services do Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 12/15/2018
ms.openlocfilehash: 2fa37d31e984399c1b676cf6c6112617fac43fd9
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721933"
---
# <a name="migrate-sql-server-integration-services-packages-to-azure"></a>Migrace balíčků SQL Server Integration Services do Azure
Pokud používáte SQL Server Integration Services (SSIS) a chcete provést migraci vašich projektů/balíčků služby SSIS ze zdroje databází SSISDB hostovanou službou SQL Server do cílového umístění databází SSISDB hostovanou ve serveru Azure SQL Database nebo Azure SQL Database Managed Instance, můžete znovu nasadit pomocí Průvodce nasazením integračního služby. Můžete spustit Průvodce spustíte z v rámci SQL Server Management Studio (SSMS).

Pokud je verze služby SSIS, můžete použít starší než 2012, před opětovného nasazení vaší služby SSIS projektů/balíčků do modelu nasazení projektu, musíte nejprve převést pomocí integrace služby projektu převod průvodce, který můžete také spustit z aplikace SSMS. Další informace najdete v článku [převod projektů do modelu nasazení projektu](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) aktuálně nepodporuje migraci ze zdrojové databáze SSISDB, ale nenasadíte vašich projektů/balíčků služby SSIS, pomocí následujícího postupu. 

V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
> * Vyhodnoťte zdroj projektů/balíčků služby SSIS.
> * Migrace projektů/balíčků služby SSIS do Azure.

## <a name="prerequisites"></a>Požadavky
K dokončení těchto kroků budete potřebovat:

- SSMS verze 17.2 nebo novější.
- Instance cílové databázový server pro hostování služby SSISDB.
 
  Pokud již nemáte:
    - Pro službu Azure SQL Database, vytvoření serveru Azure SQL Database (bez databáze) pomocí webu Azure portal tak, že přejdete na SQL Server (pouze logický server) [formuláře](https://ms.portal.azure.com/#create/Microsoft.SQLServer).
    - Azure SQL Database Managed Instance, postupujte podle podrobností v článku [vytvořit Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).

- Musí být poskytnuty služby SSIS v Azure Data Factory (ADF) obsahující prostředí Azure-SSIS Integration Runtime (IR) s cílovým databází SSISDB hostovanou v instanci serveru Azure SQL Database (jak je popsáno v článku [zřízení prostředí Azure-SSIS Integration Modul runtime ve službě Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)) nebo Azure SQL Database Managed Instance (jak je popsáno v článku [vytvořit prostředí Azure-SSIS integration runtime ve službě Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)). 

## <a name="assess-source-ssis-projectspackages"></a>Vyhodnotit zdroj projektů/balíčků služby SSIS
Během posouzení zdroje SSISDB není zatím integrované do databáze Migration Assistant (DMA) nebo Azure Database Migration Service (DMS), projektů/balíčků služby SSIS použit k vyhodnocení/ověří jako se znovu nasadit do databáze SSISDB hostované na cílový Azure server služby SQL Database nebo Azure SQL Database Managed Instance.

## <a name="migrate-ssis-projectspackages"></a>Migrace projektů/balíčků služby SSIS
Pro migraci projektů/balíčků služby SSIS do serveru Azure SQL Database nebo Azure SQL Database Managed Instance, proveďte následující kroky.

1.  Otevřete SSMS a pak vyberte **možnosti** zobrazíte **připojit k serveru** dialogové okno.

2.  Na **přihlášení** kartu, zadejte informace potřebné pro připojení k serveru Azure SQL Database nebo Azure SQL Database Managed Instance hostování cílové databáze SSISDB.

    ![Karta přihlášení služby SSIS](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)
 
3.  Na **vlastnosti připojení** kartě **připojit k databázi** textového pole, vyberte nebo zadejte **SSISDB**a pak vyberte **připojit**.

    ![Karta Vlastnosti připojení služby SSIS](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4.  V Průzkumníku objektů aplikace SSMS, rozbalte **integrace služby katalogy** uzlu, rozbalte **SSISDB**a pokud nejsou žádné stávající složky, klikněte pravým tlačítkem na **SSISDB** a Vytvořte novou složku.

5.  V části **SSISDB**rozbalte všechny složky, klikněte pravým tlačítkem na **projekty**a pak vyberte **nasazení projektu**.

    ![Rozbalený uzel SSIS SSISDB](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6.  V Průvodci nasazení integrační služby na **ÚVOD** stránky, zkontrolujte informace a pak vyberte **Další**.

    ![Nasazení průvodce úvodní stránka](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7.  Na **vybrat zdroj** zadejte existující projekt služby SSIS, kterou chcete nasadit.

    Pokud aplikace SSMS je také připojený k serveru SQL, který hostuje zdrojové databáze SSISDB, vyberte **katalog integračních služeb**a pak zadejte název a projekt cestu na serveru ve vašem katalogu nasadit přímo do projektu.

    Můžete také vybrat **soubor projektu nasazení**a poté zadejte cestu k existujícímu projektu nasazení souboru (.ispac) k nasazení vašeho projektu.

    ![Stránka průvodce vyberte zdroj nasazení](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8.  Vyberte **Další**.
9.  Na **vyberte cíl** stránky, zadejte cílovou složku pro váš projekt.

       a. Do textového pole název serveru, zadejte plně kvalifikovaný název serveru Azure SQL Database (< název_serveru >. database.windows.net) nebo Azure SQL Database Managed Instance (< server_name.dns_prefix >. database.windows.net).
 
       b. Zadejte informace o ověřování a pak vyberte **připojit**.
    
       ![Stránka průvodce vyberte cíl nasazení](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Kliknout na Procházet a zadejte cílovou složku v SSISDB a potom klepněte na tlačítko Další.

    > [!NOTE]
    > **Další** tlačítko je dostupné pouze po dokončení výběru **připojit**. 

10. Na **ověřit** stránce, zobrazit všechny chyby a upozornění a potom v případě potřeby upravte balíčky odpovídajícím způsobem.

    ![Stránka Průvodce ověření nasazení](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Vyberte **Další**.

12. Na **zkontrolujte** stránky, zkontrolujte nastavení nasazení.

    > [!NOTE]
    > Nastavení můžete změnit tak, že vyberete předchozí nebo výběrem některé z odkazů na krok v levém podokně.

13. Vyberte **nasadit** k zahájení procesu nasazení.

14. Po dokončení procesu nasazení můžete zobrazit na stránce výsledky zobrazí úspěch nebo neúspěch akce každého nasazení.
    a. Pokud chyba jakékoli akce, **výsledek** sloupci vyberte **neúspěšné** zobrazíte vysvětlení chyby.
    b. Volitelně vyberte **uložit sestavu** uložte výsledky do souboru XML.

15. Vyberte **Zavřít** ukončíte Průvodce nasazením integračního služby.

Pokud nasazení projektu bude úspěšné bez chyby, můžete vybrat všechny balíčky, které obsahuje ke spuštění v Azure-SSIS IR.

## <a name="next-steps"></a>Další postup
- Přečtěte si pokyny k migraci v Microsoftu [Průvodce migrací databází](https://datamigration.microsoft.com/).