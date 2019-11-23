---
title: Získání podrobností o databázi služby Azure Blockchain Workbench
description: Learn how to get Azure Blockchain Workbench Preview database and database server information.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 2b3190a9d042be8ead1ff3d5ef48d4a2a19e8963
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324692"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Získání informací o databázi služby Azure Blockchain Workbench

This article shows how to get detailed information about your Azure Blockchain Workbench Preview database.

## <a name="overview"></a>Přehled

Informace o spouštění aplikací, pracovních postupů a chytrých kontraktů se poskytují pomocí zobrazení databáze SQL služby Blockchain Workbench. Developers can use this information when using tools such as Microsoft Excel, Power BI, Visual Studio, and SQL Server Management Studio.

Aby se vývojář mohl k této databázi připojit, potřebuje:

* Povolený přístup externího klienta v bráně firewall databáze. Tento článek o konfiguraci brány firewall databáze vysvětluje, jak povolit přístup.
* Název databázového serveru a název databáze

## <a name="connect-to-the-blockchain-workbench-database"></a>Připojení k databázi Blockchain Workbench

K databázi se připojíte takto:

1. Sign in to the Azure portal with an account that has **Owner** permissions for the Azure Blockchain Workbench resources.
2. V levém navigačním podokně zvolte **Skupiny prostředků**.
3. Zvolte název skupiny prostředků svého nasazení služby Blockchain Workbench.
4. Výběrem možnosti **Typ** tento seznam prostředků seřaďte a pak zvolte **SQL Server**. V seřazeném seznamu na dalším snímku obrazovky se zobrazují dvě databáze SQL – jedna „master“ a jedna, která jako **předponu prostředku** používá „lhgn“.

   ![Seřazený seznam prostředků služby Blockchain Workbench](./media/getdb-details/sorted-workbench-resource-list.png)

5. Pokud si chcete prohlédnout podrobné informace o databázi služby Blockchain Workbench, vyberte odkaz na databázi s **předponou prostředku**, kterou jste zadali pro nasazení služby Blockchain Workbench.

   ![Podrobnosti databáze](./media/getdb-details/workbench-db-details.png)

Název databázového serveru a název databáze vám umožní se připojit k databázi služby Blockchain Workbench pomocí vývojového nebo vykazovacího nástroje.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zobrazení databáze ve službě Azure Blockchain Workbench](database-views.md)