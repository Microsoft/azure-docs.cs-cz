---
title: Získání informací o databázi Azure Blockchain Workbench
description: Další informace o získání Azure Blockchain Workbench databázi a informace o databázovém serveru.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/4/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: bf7cc85e823e6630dbd3278bc91fba85f404059f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Získat informace o databázi Azure Blockchain Workbench

Tento článek ukazuje, jak získat podrobné informace o databázi Azure Blockchain Workbench.

## <a name="overview"></a>Přehled

Informace o aplikacích, pracovní postupy a provádění inteligentní kontrakt je prováděno pomocí zobrazení databáze v databázi SQL Blockchain Workbench. Vývojáři mohou použít tyto informace při použití nástrojů, jako je Microsoft Excel, PowerBI, Visual Studio a SQL Server Management Studio.

Než vývojář se může připojit k databázi, které potřebují:

* Přístup k externí klienta je povolený v bráně firewall databáze. Tento článek o konfiguraci brány firewall článku na databázi vysvětluje, jak povolit přístup.
* Název databázového serveru a název databáze.

## <a name="connect-to-the-blockchain-workbench-database"></a>Připojení k databázi Blockchain Workbench

Připojení k databázi:

1. Přihlaste se k portálu Azure pomocí účtu, který má **vlastníka** oprávnění pro prostředky Azure Blockchain Workbench.
2. V levém navigačním podokně zvolte **skupiny prostředků**.
3. Vyberte název skupiny prostředků pro vaše nasazení Blockchain Workbench.
4. Vyberte **typ** seřaďte seznam prostředků, a pak vyberte vaše **systému SQL server**. Seřazený seznam v další snímek obrazovky ukazuje dvě databáze SQL, "hlavní" a ten, který používá "lhgn" jako **prostředků předponu**.

   ![Seřazený seznam zdrojů Blockchain Workbench](media/blockchain-workbench-getdb-details/sorted-workbench-resource-list.png)

5. Pokud chcete zobrazit podrobné informace o databázi Blockchain Workbench, vyberte odkaz pro databázi s **prostředků předponu** jste zadali pro nasazení Blockchain Workbench.

   ![Podrobnosti databáze](media/blockchain-workbench-getdb-details/workbench-db-details.png)

Název databázového serveru a název databáze umožňuje připojení k databázi Blockchain Workbench pomocí vaší vývoj nebo nástroj pro generování sestav.

## <a name="next-steps"></a>Další postup

* [Zobrazení databáze v Azure Blockchain Workbench](blockchain-workbench-database-views.md)