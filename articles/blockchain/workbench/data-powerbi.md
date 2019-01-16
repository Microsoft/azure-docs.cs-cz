---
title: Použití dat služby Azure Blockchain Workbench v Power BI
description: Zjistěte, jak načíst a zobrazit data z databáze SQL DB služby Azure Blockchain Workbench v Power BI.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: e50a82f0f141fec266a8348c0a1a8b5fbdb38b45
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329841"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Použití dat služby Azure Blockchain Workbench v Power BI

Microsoft Power BI umožňuje snadno generovat výkonné sestavy z databází SQL DB pomocí Power BI Desktopu a potom je publikovat na stránku [https://www.powerbi.com](http://www.powerbi.com).

Tento článek obsahuje podrobný návod, jak se připojit k databázi SQL Database služby Azure Blockchain Workbench z Power BI Desktopu, vytvořit sestavu a nasadit ji na stránku powerbi.com.

## <a name="prerequisites"></a>Požadavky

* Stáhněte si [Power BI Desktop](https://aka.ms/pbidesktopstore).

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Připojení Power BI k datům v Azure Blockchain Workbench

1.  Otevřete Power BI Desktop.
2.  Vyberte **Načíst data**.

    ![Získání dat](./media/data-powerbi/get-data.png)
3.  Ze seznamu typů zdrojů dat vyberte **SQL Server**.

4.  Do dialogového okna zadejte server a název databáze. Určete, jestli chcete data importovat nebo použít **DirectQuery**. Vyberte **OK**.

    ![Výběr SQL Serveru](./media/data-powerbi/select-sql.png)

5.  Zadejte přihlašovací údaje databáze, abyste získali přístup ke službě Azure Blockchain Workbench. Vyberte **Databáze** a zadejte přihlašovací údaje.

    Pokud používáte přihlašovací údaje vytvořené při nasazení služby Azure Blockchain Workbench, pak uživatelským jménem je **dbadmin** a heslo je stejné jako to, které jste při zadali nasazení.

    ![Nastavení databáze SQL DB](./media/data-powerbi/db-settings.png)

6.  Jakmile se připojíte k databázi, dialogové okno **Navigátor** zobrazí tabulky a zobrazení, která jsou v databázi k dispozici. Zobrazení jsou navržená pro generování sestav a mají předponu **vw**.

    ![Navigátor](./media/data-powerbi/navigator.png)

7.  Vyberte zobrazení, které chcete zahrnout. Pro demonstrační účely jsme zahrnuli **vwContractAction**, který obsahuje podrobné informace o akce provedené na kontrakt.

    ![Výběr zobrazení](./media/data-powerbi/select-views.png)

Nyní můžete v Power BI vytvářet a publikovat sestavy běžným způsobem.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zobrazení databáze ve službě Azure Blockchain Workbench](database-views.md)