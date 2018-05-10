---
title: Použití Azure Blockchain Workbench dat v Microsoft Power BI
description: Zjistěte, jak načíst a zobrazení dat Azure Blockchain Workbench SQL databáze v Microsoft Power BI.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 2b909c0a8441010b87c913e5937d25c8127058f1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Použití Azure Blockchain Workbench data s Microsoft Power BI

Microsoft Power BI umožňuje snadno generovat výkonné sestavy z databáze SQL DB pomocí Power BI Desktop a pak publikovat je do [ https://www.powerbi.com ](http://www.powerbi.com).

Tento článek obsahuje podrobný návod, jak se připojit k databázi SQL Azure Blockchain Workbench z v rámci PowerBI desktop, vytvoření sestavy a nasadit sestavu na powerbi.com.

## <a name="prerequisites"></a>Požadavky

* Stáhněte si [PowerBI Desktop](https://aka.ms/pbidesktopstore).

## <a name="connecting-powerbi-to-data-in-azure-blockchain-workbench"></a>Připojování k datům v Azure Blockchain Workbench PowerBI

1.  Otevřít Power BI Desktop.
2.  Vyberte **získat Data**.

    ![Získání dat](media/blockchain-workbench-data-powerbi/get-data.png)
3.  Vyberte **systému SQL Server** ze seznamu typy zdrojů dat.

4.  Zadejte název serveru a databáze v dialogovém okně. Zadejte, pokud chcete importovat data nebo provádět **DirectQuery**. Vyberte **OK**.

    ![Výběr SQL Serveru](media/blockchain-workbench-data-powerbi/select-sql.png)

5.  Zadejte přihlašovací údaje databáze pro přístup k Azure Blockchain Workbench. Vyberte **databáze** a zadejte svá pověření.

    Pokud používáte přihlašovací údaje vytvořit proces nasazení Azure Blockchain Workbench, uživatelské jméno je **dbadmin** a heslo je ten, který jste zadali při nasazení.

    ![Nastavení SQL DB](media/blockchain-workbench-data-powerbi/db-settings.png)

6.  Po připojení k databázi, **Navigátor** zobrazuje tabulky a zobrazení, které jsou k dispozici v rámci databáze. Zobrazení jsou navrženy pro vytváření sestav a jsou předponu **zobrazit**.

    ![Navigátor](media/blockchain-workbench-data-powerbi/navigator.png)

7.  Vyberte zobrazení, které chcete zahrnout. Pro demonstrační účely, zařadíme **vwContractAction**, který poskytuje podrobné informace o všechny akce, které se vyskytly na kontraktu.

    ![Vyberte zobrazení](media/blockchain-workbench-data-powerbi/select-views.png)

Teď můžete vytvářet a publikovat sestavy běžným způsobem s Power BI.

## <a name="next-steps"></a>Další postup

* [Zobrazení databáze v Azure Blockchain Workbench](blockchain-workbench-database-views.md)