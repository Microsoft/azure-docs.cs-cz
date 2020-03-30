---
title: Integrace správy zdrojového kódu
description: Prostředí Database DevOps podnikové třídy pro fond SQL s nativní integrací správy zdrojového kódu pomocí Azure Repos (Git a GitHub).
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 9898addfa840752e27de8716d2185e767aac809b
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350087"
---
# <a name="source-control-integration-for-sql-pool"></a>Integrace správy zdrojového kódu pro fond SQL

Tento kurz popisuje, jak integrovat databázový projekt databázových nástrojů SQL Server (SSDT) se správou zdrojového kódu.  Integrace správy zdrojového kódu je prvním krokem při vytváření kanálu průběžné integrace a nasazení s prostředkem fondu SQL v Azure Synapse Analytics. 

## <a name="before-you-begin"></a>Než začnete

- Registrace k [organizaci Azure DevOps](https://azure.microsoft.com/services/devops/)
- Procházení kurzu [Vytvořit a připojit](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)
-  [Instalace Visual Studia 2019](https://visualstudio.microsoft.com/vs/older-downloads/) 

## <a name="set-up-and-connect-to-azure-devops"></a>Nastavení a připojení k Azure DevOps

1. V organizaci Azure DevOps vytvořte projekt, který bude hostovat databázový projekt SSDT prostřednictvím úložiště Azure Repo.

   ![Vytvořit projekt](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Vytvořit projekt")

2. Otevřete Visual Studio a připojte se k organizaci a projektu Azure DevOps od kroku 1 výběrem možnosti Spravovat připojení.

   ![Správa připojení](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Správa připojení")

   ![Připojit](./media/sql-data-warehouse-source-control-integration/3-connect.png "Připojení")

3. Klonování úložiště Azure Repo z projektu do místního počítače

   ![Klonovat repo](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Klonovat repo")

## <a name="create-and-connect-your-project"></a>Vytvoření a propojení projektu

1. V sadě Visual Studio vytvořte nový databázový projekt serveru SQL Server s adresářovým i místním úložištěm Git v **místním klonovaném úložišti.**

   ![Vytvořit nový projekt](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Vytvoření nového projektu")  

2. Klikněte pravým tlačítkem myši na prázdný sqlproject a importujte datový sklad do databázového projektu.

   ![Importovat projekt](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Importovat projekt")  

3. V Průzkumníkovi týmu ve Visual Studiu potvrdíte všechny změny v místním úložišti Git. 

   ![Potvrzení](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Potvrzení")  

4. Teď, když máte změny potvrzené místně v klonovanéúložiště, synchronizovat a tlačit změny do úložiště Azure Repo v projektu Azure DevOps.

   ![Synchronizace a push - pracovní](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Synchronizace a nabízení - pracovní")

   ![Synchronizace a nabízení](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Synchronizace a nabízení")  

## <a name="validation"></a>Ověřování

1. Ověřte, zda byly změny do vašeho azure repo posunuty aktualizací sloupce tabulky v databázovém projektu z datových nástrojů SSDT sady Visual Studio SQL Server.

   ![Ověřit sloupec aktualizace](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Ověřit sloupec aktualizace")

2. Potvrzení a posununí změny z místního úložiště do úložiště Azure

   ![Nasdílení změn](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Nasdílení změn")

3. Ověřte, zda byla změna posunuta v úložišti Azure Repo.

   ![Ověřit](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Ověření změn")

4. (**Nepovinné**) Použití schématu Porovnání a aktualizace změn cílového datového skladu pomocí ssdt, abyste zajistili, že definice objektů v úložišti Azure Repo a místním úložišti budou odrážet váš datový sklad.

## <a name="next-steps"></a>Další kroky

- [Vývoj pro fond SQL](sql-data-warehouse-overview-develop.md)
