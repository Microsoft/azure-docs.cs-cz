---
title: Integrace správy zdrojového kódu
description: DevOps prostředí databáze podnikové třídy pro vyhrazený fond SQL s integrací nativní správy zdrojového kódu pomocí Azure Repos (Git a GitHub).
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 08/23/2019
ms.author: gaursa
ms.reviewer: igorstan
ms.openlocfilehash: 0864ab90f35e4af0443b418bc139ee5f8a7db665
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585532"
---
# <a name="source-control-integration-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Integrace správy zdrojového kódu pro vyhrazený fond SQL ve službě Azure synapse Analytics

V tomto kurzu se naučíte, jak integrovat projekt databáze SSDT (SQL Server Data Tools) se správou zdrojových kódů.  Integrace správy zdrojového kódu je prvním krokem při sestavování kanálu průběžné integrace a nasazování s vyhrazeným prostředkem fondu SQL ve službě Azure synapse Analytics.

## <a name="before-you-begin"></a>Než začnete

- Registrace k [organizaci Azure DevOps](https://azure.microsoft.com/services/devops/)
- Projděte si kurz [Vytvoření a připojení](create-data-warehouse-portal.md) .
- [Instalace sady Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/)

## <a name="set-up-and-connect-to-azure-devops"></a>Nastavení a připojení k Azure DevOps

1. V organizaci Azure DevOps vytvořte projekt, který bude hostovat váš projekt databáze SSDT prostřednictvím úložiště úložiště Azure.

   ![Vytvořit projekt](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Vytvořit projekt")

2. Otevřete Visual Studio a připojte se ke svojí organizaci Azure DevOps a projektu z kroku One výběrem **možnosti spravovat připojení**.

   ![Spravovat připojení](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Spravovat připojení")

3. Připojte se k projektu výběrem možnosti **Spravovat připojení** a pak **se připojte k projektu**.
 
    ![Connect1](./media/sql-data-warehouse-source-control-integration/3-connect-project.png "Připojit")


4. Vyhledejte projekt, který jste vytvořili v kroku One, a vyberte **připojit**.
 
    ![Connect2](./media/sql-data-warehouse-source-control-integration/3.5-connect.png "Připojit")


3. Naklonujte úložiště Azure DevOps z vašeho projektu na svůj místní počítač.

   ![Klonovat úložiště](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Klonovat úložiště")

Další informace o propojení projektů pomocí sady Visual Studio naleznete v tématu [připojení k projektům v Team Explorer](/visualstudio/ide/connect-team-project?view=vs-2019&preserve-view=true). Pokyny pro klonování úložiště pomocí sady Visual Studio najdete v článku o [klonování a ukončování úložiště Git](/azure/devops/repos/git/clone?tabs=visual-studio) . 

## <a name="create-and-connect-your-project"></a>Vytvoření a připojení projektu

1. V aplikaci Visual Studio vytvořte nový databázový projekt SQL Server s adresářovým a místním úložištěm Git v **místním klonovaném úložišti**.

   ![Vytvoření nového projektu](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Vytvoření nového projektu")  

2. Klikněte pravým tlačítkem na prázdné sqlproject a importujte datový sklad do databázového projektu.

   ![Importovat projekt](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Importovat projekt")  

3. V Team Explorer v aplikaci Visual Studio potvrďte změny v místním úložišti Git.

   ![Potvrzení](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Potvrzení")  

4. Teď, když jste provedli změny místně v naklonovaném úložišti, synchronizujte a nahrajte změny do úložiště Azure úložiště v projektu Azure DevOps.

   ![Synchronizace a nabízené přípravy](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Synchronizace a nabízené přípravy")

   ![Synchronizovat a vložit](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Synchronizovat a vložit")  

## <a name="validation"></a>Ověřování

1. Pomocí sady Visual Studio SQL Server Data Tools (SSDT) ověřte, že jste do úložiště Azure posunuli změny, které jste odeslali do úložiště Azure.

   ![Sloupec pro ověření aktualizace](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Sloupec pro ověření aktualizace")

2. Potvrďte a nahrajte změnu z místního úložiště do úložiště Azure.

   ![Nasdílení změn](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Nasdílení změn")

3. Ověřte, že se změna provedla v úložišti úložiště Azure.

   ![Ověřit](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Ověření změn")

4. (**Volitelné**) Pomocí SSDT můžete použít porovnání schématu a aktualizovat změny ve svém cílovém vyhrazeném fondu SQL a zajistit, aby definice objektů v úložišti úložiště Azure a místním úložišti odrážely vyhrazený fond SQL.

## <a name="next-steps"></a>Další kroky

- [Vývoj pro vyhrazený fond SQL](sql-data-warehouse-overview-develop.md)