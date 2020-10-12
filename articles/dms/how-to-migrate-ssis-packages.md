---
title: Opětovné nasazení balíčků SSIS do jediné databáze SQL
titleSuffix: Azure Database Migration Service
description: Naučte se migrovat nebo znovu nasadit balíčky a projekty služba SSIS (SQL Server Integration Services), abyste Azure SQL Database izolovanou databázi pomocí Azure Database Migration Service a Data Migration Assistant.
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
ms.openlocfilehash: e5f9ba7ea4afd81d62cba7b970693f603b53ef9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316082"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>Opětovné nasazení balíčků SSIS do Azure SQL Database s využitím Azure Database Migration Service

Pokud používáte služba SSIS (SQL Server Integration Services) (SSIS) a chcete migrovat projekty a balíčky SSIS ze zdrojového SSISDBu hostovaného SQL Server do cílového SSISDB hostovaného v Azure SQL Database, můžete je znovu nasadit pomocí Průvodce nasazením integračních služeb. Průvodce můžete spustit v rámci SQL Server Management Studio (SSMS).

Pokud verze SSIS, kterou používáte, je starší než 2012, před opětovným nasazením SSIS projektů/balíčků do modelu nasazení projektu je nejprve nutné je převést pomocí Průvodce převodem projektu integrační služby, který lze také spustit z SSMS. Další informace naleznete v článku [Převod projektů do modelu nasazení projektu](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) v současné době nepodporuje migraci zdrojového SSISDB do Azure SQL Database, ale projekty a balíčky SSIS můžete znovu nasadit pomocí následujícího postupu.

V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
>
> * Vyhodnoťte zdrojové SSIS projekty/balíčky.
> * Migrujte projekty a balíčky SSIS do Azure.

## <a name="prerequisites"></a>Požadavky

K provedení těchto kroků potřebujete:

* SSMS verze 17,2 nebo novější.
* Instance cílového databázového serveru pro hostování SSISDB. Pokud ho ještě nemáte, vytvořte pomocí Azure Portal [logický SQL Server](../azure-sql/database/logical-servers.md) (bez databáze), a to tak, že přejdete na [formulář](https://ms.portal.azure.com/#create/Microsoft.SQLServer)SQL Server (jenom logický Server).
* SSIS se musí zřídit v Azure Data Factory (ADF) obsahující Azure-SSIS Integration Runtime (IR) s cílovým SSISDB hostovaným v SQL Database (jak je popsáno v článku [zřizování Azure-SSIS Integration runtime v Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)).

## <a name="assess-source-ssis-projectspackages"></a>Vyhodnocení zdrojového SSIS projektů/balíčků

I když se hodnocení source SSISDB ještě Neintegruje do databáze Pomocník s migrací (DMA) nebo Azure Database Migration Service (DMS), vaše projekty nebo balíčky SSIS se vyhodnotí nebo ověří při jejich opětovném nasazení do cílového SSISDB hostovaného v Azure SQL Database.

## <a name="migrate-ssis-projectspackages"></a>Migrace projektů a balíčků SSIS

Pokud chcete migrovat SSIS projekty/balíčky na Azure SQL Database, proveďte následující kroky.

1. Otevřete SSMS a pak vyberte **Možnosti** , které se zobrazí v dialogovém okně **připojit k serveru** .

2. Na kartě **přihlášení** zadejte informace potřebné pro připojení k serveru, který bude HOSTOVAT cílové SSISDB.

    ![Karta přihlášení k SSIS](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. Na kartě **Vlastnosti připojení** v textovém poli **připojit k databázi** vyberte nebo zadejte **SSISDB**a pak vyberte **připojit**.

    ![Karta Vlastnosti připojení SSIS](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. V Průzkumník objektů SSMS rozbalte uzel **katalogy integračních služeb** , rozbalte **SSISDB**a pokud nejsou žádné existující složky, klikněte pravým tlačítkem myši na **SSISDB** a vytvořte novou složku.

5. V části **SSISDB**rozbalte všechny složky, klikněte pravým tlačítkem na **projekty**a pak vyberte **nasadit projekt**.

    ![Rozbalený uzel SSIS SSISDB](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. V Průvodci nasazením integrační služby zkontrolujte informace na stránce **Úvod** a potom vyberte **Další**.

    ![Úvodní stránka Průvodce nasazením](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. Na stránce **Vybrat zdroj** Zadejte existující projekt SSIS, který chcete nasadit.

    Pokud je SSMS také připojeno k SQL Server hostujícím zdrojové SSISDB, vyberte **katalog integračních služeb**a potom zadejte název serveru a cestu k projektu ve vašem katalogu, abyste nasadili projekt přímo.

    Případně vyberte **soubor nasazení projektu**a pak zadejte cestu k existujícímu souboru nasazení projektu (. ispac) pro nasazení projektu.

    ![Stránka pro výběr zdrojové stránky Průvodce nasazením](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Vyberte **Další**.
9. Na stránce **vybrat cíl** zadejte cíl pro váš projekt.

    a. Do textového pole název serveru zadejte plně kvalifikovaný název serveru (<server_name>. database.windows.net).

    b. Zadejte ověřovací informace a pak vyberte **připojit**.

    ![Stránka pro výběr cílové stránky Průvodce nasazením](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Vyberte **Procházet** a určete cílovou SLOŽKU v SSISDB a pak vyberte **Další**.

    > [!NOTE]
    > Tlačítko **Další** bude povoleno až po výběru **připojit**.

10. Na stránce **ověřit** zobrazte všechny chyby a upozornění a v případě potřeby upravte balíčky odpovídajícím způsobem.

    ![Stránka pro ověření Průvodce nasazením](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Vyberte **Další**.

12. Na stránce **Kontrola** zkontrolujte nastavení nasazení.

    > [!NOTE]
    > Nastavení můžete změnit výběrem možnosti **Předchozí** nebo výběrem kteréhokoli z odkazů na krok v levém podokně.

13. Vyberte **nasadit** a spusťte proces nasazení.

14. Po dokončení procesu nasazení si můžete zobrazit stránku výsledků, která zobrazuje úspěšnost nebo selhání každé akce nasazení.
    a. Pokud se některá akce nezdařila, ve sloupci **výsledek** vyberte **nezdařilo se** Zobrazit vysvětlení chyby.
    b. Volitelně můžete vybrat **Uložit sestavu** a výsledky Uložit do souboru XML.

15. Kliknutím na **Zavřít** ukončíte průvodce nasazením integračních služeb.

Pokud se nasazení projektu zdaří bez selhání, můžete vybrat všechny balíčky, které obsahuje, aby běžely na Azure-SSIS IR.

## <a name="next-steps"></a>Další kroky

* Přečtěte si pokyny k migraci v [Průvodci migrací databáze](https://datamigration.microsoft.com/)společnosti Microsoft.
