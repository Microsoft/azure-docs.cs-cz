---
title: Integrace správy zdrojového kódu
description: DevOps prostředí databáze podnikové třídy pro SQL Data Warehouse s integrací nativní správy zdrojového kódu pomocí Azure Repos (Git a GitHub).
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 82d366b3f807e21e3531da00da78520fed90bc66
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645261"
---
# <a name="source-control-integration-for-azure-sql-data-warehouse"></a>Integrace správy zdrojového kódu pro Azure SQL Data Warehouse

V tomto kurzu se naučíte, jak integrovat projekt databáze SSDT (SQL Server Data Tools) se správou zdrojových kódů.  Integrace správy zdrojového kódu je prvním krokem při sestavování kanálu průběžné integrace a nasazování pomocí SQL Data Warehouse. 

## <a name="before-you-begin"></a>Než začnete

- Registrace k [organizaci Azure DevOps](https://azure.microsoft.com/services/devops/)
- Projděte si kurz [Vytvoření a připojení](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal) .
-  [Instalace sady Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/) 

## <a name="set-up-and-connect-to-azure-devops"></a>Nastavení a připojení k Azure DevOps

1. V organizaci Azure DevOps vytvořte projekt, který bude hostovat váš projekt databáze SSDT prostřednictvím úložiště Azure úložiště.

   ![Vytvořit projekt](media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Vytvořit projekt")

2. Otevřete Visual Studio a připojte se ke svojí organizaci a projektu Azure DevOps z kroku 1 výběrem možnosti spravovat připojení.

   ![Spravovat připojení](media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Spravovat připojení")

   ![Připojení](media/sql-data-warehouse-source-control-integration/3-connect.png "Připojení")

3. Naklonujte úložiště Azure úložiště z vašeho projektu na místní počítač.

   ![Klonovat úložiště](media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Klonovat úložiště")

## <a name="create-and-connect-your-project"></a>Vytvoření a připojení projektu

1. V aplikaci Visual Studio vytvořte nový databázový projekt SQL Server s adresářovým a místním úložištěm Git v **místním klonovaném úložišti** .

   ![Vytvořit nový projekt](media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Vytvoření nového projektu")  

2. Klikněte pravým tlačítkem na prázdnou sqlproject a importujte datový sklad do databázového projektu.

   ![Importovat projekt](media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Importovat projekt")  

3. V Průzkumníku týmových souborů v aplikaci Visual Studio potvrďte všechny změny v místním úložišti Git. 

   ![Potvrzuj](media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Potvrzení")  

4. Teď, když jste provedli změny místně v naklonovaném úložišti, synchronizujte a nahrajte změny do úložiště Azure úložiště v projektu Azure DevOps.

   ![Synchronizace a nabízené přípravy](media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Synchronizace a nabízené přípravy")

   ![Synchronizovat a vložit](media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Synchronizovat a vložit")  

## <a name="validation"></a>Ověření

1. Pomocí sady Visual Studio SQL Server Data Tools (SSDT) ověřte, že jste do svého úložiště Azure posunuli změny pomocí aktualizace sloupce tabulky v projektu databáze.

   ![Sloupec pro ověření aktualizace](media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Sloupec pro ověření aktualizace")

2. Potvrzení změn z místního úložiště a jejich vložení do úložiště Azure

   ![Doručovat změny](media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Nasdílení změn")

3. Ověřte, že se změna provedla v úložišti úložiště Azure.

   ![Ověříte](media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Ověřit změny")

4. (**Volitelné**) Použijte porovnání schématu a aktualizujte změny svého cílového datového skladu pomocí SSDT, abyste zajistili, že definice objektů v úložišti úložiště Azure a místním úložišti odráží váš datový sklad.

## <a name="next-steps"></a>Další kroky

- [Vývoj pro Azure SQL Data Warehouse](sql-data-warehouse-overview-develop.md)

<!--Image references-->

<!--Article references-->


<!--MSDN references-->

<!--Other Web references-->

