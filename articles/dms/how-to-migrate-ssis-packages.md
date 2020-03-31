---
title: Opětovné nasazení balíčků SSIS do jediné databáze SQL
titleSuffix: Azure Database Migration Service
description: Zjistěte, jak migrovat nebo znovu nasadit balíčky a projekty služby SQL Server Integration Services do jedné databáze Azure SQL Database pomocí služby migrace databáze Azure a Pomocníka pro migraci dat.
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
ms.openlocfilehash: 90a39b8fe3604a05f1d35a875ae4e34491b47d72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648525"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>Opětovné nasazení balíčků SSIS do databáze Azure SQL pomocí služby Migrace databáze Azure

Pokud používáte službu SQL Server Integration Services (SSIS) a chcete migrovat projekty/balíčky SSIS ze zdrojového SSISDB hostovaného SQL Serverem do cílové databáze SSISDB hostované službou Azure SQL Database, můžete je znovu nasadit pomocí Průvodce nasazením integračních služeb. Průvodce můžete spustit z aplikace SQL Server Management Studio (SSMS).

Pokud je verze SSIS, kterou používáte, starší než 2012, před opětovným nasazením projektů/balíčků SSIS do modelu nasazení projektu je třeba nejprve převést pomocí Průvodce převodem projektu integračních služeb, který lze také spustit ze služby SSMS. Další informace naleznete v článku [Převod projektů na model nasazení projektu](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Služba Migrace databáze Azure (DMS) aktuálně nepodporuje migraci zdrojového SSISDB na server Azure SQL Database, ale můžete znovu nasadit projekty/balíčky SSIS pomocí následujícího procesu.

V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
>
> * Posoudit zdroj SSIS projekty/balíčky.
> * Migrace projektů/balíčků SSIS do Azure.

## <a name="prerequisites"></a>Požadavky

Chcete-li provést tyto kroky, potřebujete:

* SSMS verze 17.2 nebo novější.
* Instance cílového databázového serveru pro hostování SSISDB. Pokud ho ještě nemáte, vytvořte databázový server Azure SQL (bez databáze) pomocí portálu Azure tak, že přejdete do [formuláře](https://ms.portal.azure.com/#create/Microsoft.SQLServer)SQL Server (pouze logický server).
* SSIS musí být zřízena v Azure Data Factory (ADF) obsahující Azure-SSIS Integrace Runtime (IR) s cílOvou SSISDB hostované instance serveru Azure SQL Database (jak je popsáno v článku [Zřízení Azure-SSIS Integrace Runtime v Azure Data Factory).](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

## <a name="assess-source-ssis-projectspackages"></a>Posoudit zdroj projektů/balíčků SSIS

Zatímco hodnocení zdrojového SSISDB ještě není integrováno do Pomocníka pro migraci databáze (DMA) nebo služby Migrace databáze Azure (DMS), vaše projekty/balíčky SSIS se vyhodnotí nebo ověří, jakmile se znovu nasadí do cílové databáze SSISDB hostované na serveru Azure SQL Database.

## <a name="migrate-ssis-projectspackages"></a>Migrace projektů/balíčků SSIS

Chcete-li migrovat projekty/balíčky SSIS na server Azure SQL Database, proveďte následující kroky.

1. Otevřete SSMS a pak vyberte **Možnosti,** které chcete zobrazit v dialogovém okně **Připojit k serveru.**

2. Na kartě **Přihlášení** zadejte informace potřebné pro připojení k serveru Azure SQL Database, který bude hostitelem cílové databáze SSISDB.

    ![Karta Přihlášení SSIS](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. Na kartě **Vlastnosti připojení** vyberte nebo zadejte v textovém poli **Připojit k databázi** **sisisdb**a pak vyberte **Připojit**.

    ![Karta Vlastnosti připojení SSIS](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. V Průzkumníku objektů SSMS rozbalte uzel **Katalogy integračních služeb,** rozbalte **SSISDB**a pokud neexistují žádné existující složky, klepněte pravým tlačítkem myši na **položku SSISDB** a vytvořte novou složku.

5. V **části SSISDB**rozbalte libovolnou složku, klikněte pravým tlačítkem myši na **projekty**a vyberte **nasadit project**.

    ![SSIS SSISDB uzel rozšířen](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. V Průvodci nasazením integračních služeb na stránce **Úvod** zkontrolujte informace a vyberte **další**.

    ![Úvodní stránka Průvodce nasazením](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. Na stránce **Vybrat zdroj** zadejte existující projekt SSIS, který chcete nasadit.

    Pokud je Služba SSMS také připojena k serveru SQL Server hostujícízdrojový SSISDB, vyberte **katalog integration services**a zadejte název serveru a cestu k projektu do katalogu a nasadit projekt přímo.

    Alternativně vyberte **soubor nasazení projektu**a zadejte cestu k existujícímu souboru nasazení projektu (.ispac) k nasazení projektu.

    ![Stránka Vybrat zdroj průvodce nasazením](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Vyberte **další**.
9. Na stránce **Vybrat cíl** zadejte cíl projektu.

    a. Do textového pole Název serveru zadejte plně kvalifikovaný název serveru Azure SQL Database (<server_name>.database.windows.net).

    b. Zadejte informace o ověření a pak vyberte **připojit**.

    ![Stránka Průvodce nasazením Vybrat cíl](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Výběrem **možnosti Procházet** určete cílovou složku v SSISDB a pak vyberte **Další**.

    > [!NOTE]
    > Tlačítko **Další** je povoleno až po výběru možnosti **Připojit**.

10. Na stránce **Ověření** zobrazte všechny chyby/upozornění a v případě potřeby odpovídajícím způsobem upravte balíčky.

    ![Stránka Ověření Průvodce nasazením](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Vyberte **další**.

12. Na stránce **Revize** zkontrolujte nastavení nasazení.

    > [!NOTE]
    > Nastavení můžete změnit výběrem **možnosti Předchozí** nebo výběrem libovolného fázového propojení v levém podokně.

13. Chcete-li zahájit proces nasazení, vyberte **možnost Nasadit.**

14. Po dokončení procesu nasazení můžete zobrazit stránku Výsledky, která zobrazuje úspěch nebo neúspěch každé akce nasazení.
    a. Pokud se některá akce nezdařila, vyberte ve sloupci **Výsledek** **možnost Nepodařilo** se zobrazit vysvětlení chyby.
    b. Volitelně můžete vybrat **Uložit sestavu,** chcete-li výsledky uložit do souboru XML.

15. Chcete-li ukončit Průvodce nasazením integračních služeb, vyberte **zavřít.**

Pokud nasazení projektu proběhne bez selhání, můžete vybrat všechny balíčky, které obsahuje ke spuštění na Azure-SSIS IR.

## <a name="next-steps"></a>Další kroky

* Přečtěte si pokyny k migraci v [Příručce pro migraci databáze](https://datamigration.microsoft.com/)společnosti Microsoft .
