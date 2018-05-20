---
title: Získání podrobností o databázi služby Azure Blockchain Workbench
description: Naučte se získat informace o databázi a databázovém serveru služby Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/4/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 63b718bcb8722c5fd501891d162eadfae9fb8ec2
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Získání informací o databázi služby Azure Blockchain Workbench

Tento článek popisuje, jak získáte podrobné informace o databázi služby Azure Blockchain Workbench.

## <a name="overview"></a>Přehled

Informace o spouštění aplikací, pracovních postupů a chytrých kontraktů se poskytují pomocí zobrazení databáze SQL služby Blockchain Workbench. Vývojáři mohou tyto informace využívat, když pracují s nástroji jako je Microsoft Excel, Power BI, Visual Studio a SQL Server Management Studio.

Aby se vývojář mohl k této databázi připojit, potřebuje:

* Povolený přístup externího klienta v bráně firewall databáze. Tento článek o konfiguraci brány firewall databáze vysvětluje, jak povolit přístup.
* Název databázového serveru a název databáze

## <a name="connect-to-the-blockchain-workbench-database"></a>Připojení k databázi služby Blockchain Workbench

K databázi se připojíte takto:

1. Přihlaste se k Azure Portalu pod účtem, který má oprávnění **Vlastník** k prostředkům služby Azure Blockchain Workbench.
2. V levém navigačním podokně zvolte **Skupiny prostředků**.
3. Zvolte název skupiny prostředků svého nasazení služby Blockchain Workbench.
4. Výběrem možnosti **Typ** tento seznam prostředků seřaďte a pak zvolte **SQL Server**. V seřazeném seznamu na dalším snímku obrazovky se zobrazují dvě databáze SQL – jedna „master“ a jedna, která jako **předponu prostředku** používá „lhgn“.

   ![Seřazený seznam prostředků služby Blockchain Workbench](media/blockchain-workbench-getdb-details/sorted-workbench-resource-list.png)

5. Pokud si chcete prohlédnout podrobné informace o databázi služby Blockchain Workbench, vyberte odkaz na databázi s **předponou prostředku**, kterou jste zadali pro nasazení služby Blockchain Workbench.

   ![Podrobnosti databáze](media/blockchain-workbench-getdb-details/workbench-db-details.png)

Název databázového serveru a název databáze vám umožní se připojit k databázi služby Blockchain Workbench pomocí vývojového nebo vykazovacího nástroje.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zobrazení databáze ve službě Azure Blockchain Workbench](blockchain-workbench-database-views.md)