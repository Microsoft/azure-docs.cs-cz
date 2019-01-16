---
title: Získání podrobností o databázi služby Azure Blockchain Workbench
description: Naučte se získat informace o databázi a databázovém serveru služby Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 0d806491c58c4b1881adc2fd830de7c7b9f0859d
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331924"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Získání informací o databázi služby Azure Blockchain Workbench

Tento článek popisuje, jak získáte podrobné informace o databázi služby Azure Blockchain Workbench.

## <a name="overview"></a>Přehled

Informace o spouštění aplikací, pracovních postupů a chytrých kontraktů se poskytují pomocí zobrazení databáze SQL služby Blockchain Workbench. Vývojáři mohou pomocí těchto informací při používání nástrojů, jako je například aplikace Microsoft Excel, Power BI, Visual Studio a SQL Server Management Studio.

Aby se vývojář mohl k této databázi připojit, potřebuje:

* Povolený přístup externího klienta v bráně firewall databáze. Tento článek o konfiguraci brány firewall databáze vysvětluje, jak povolit přístup.
* Název databázového serveru a název databáze

## <a name="connect-to-the-blockchain-workbench-database"></a>Připojení k databázi služby Blockchain Workbench

K databázi se připojíte takto:

1. Přihlaste se k webu Azure portal pomocí účtu, který má **vlastníka** oprávnění pro prostředky Azure Blockchain Workbench.
2. V levém navigačním podokně zvolte **Skupiny prostředků**.
3. Zvolte název skupiny prostředků svého nasazení služby Blockchain Workbench.
4. Výběrem možnosti **Typ** tento seznam prostředků seřaďte a pak zvolte **SQL Server**. V seřazeném seznamu na dalším snímku obrazovky se zobrazují dvě databáze SQL – jedna „master“ a jedna, která jako **předponu prostředku** používá „lhgn“.

   ![Seřazený seznam prostředků služby Blockchain Workbench](./media/getdb-details/sorted-workbench-resource-list.png)

5. Pokud si chcete prohlédnout podrobné informace o databázi služby Blockchain Workbench, vyberte odkaz na databázi s **předponou prostředku**, kterou jste zadali pro nasazení služby Blockchain Workbench.

   ![Podrobnosti databáze](./media/getdb-details/workbench-db-details.png)

Název databázového serveru a název databáze vám umožní se připojit k databázi služby Blockchain Workbench pomocí vývojového nebo vykazovacího nástroje.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zobrazení databáze ve službě Azure Blockchain Workbench](database-views.md)