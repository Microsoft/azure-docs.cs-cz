---
title: Získat podrobnosti databáze Azure blockchain Workbench Preview
description: Přečtěte si, jak získat informace o databázi a databázovém serveru Azure blockchain Workbench Preview.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: f4a4eaab9a03aeed27e29eb645b6e22a028b243b
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845070"
---
# <a name="get-information-about-your-azure-blockchain-workbench-preview-database"></a>Získat informace o databázi Azure blockchain Workbench Preview

Tento článek ukazuje, jak získat podrobné informace o vaší databázi Azure blockchain Workbench Preview.

## <a name="overview"></a>Přehled

Informace o spouštění aplikací, pracovních postupů a chytrých kontraktů se poskytují pomocí zobrazení databáze SQL služby Blockchain Workbench. Vývojáři můžou tyto informace použít při používání nástrojů, jako je Microsoft Excel, Power BI, Visual Studio a SQL Server Management Studio.

Aby se vývojář mohl k této databázi připojit, potřebuje:

* Povolený přístup externího klienta v bráně firewall databáze. Tento článek o konfiguraci brány firewall databáze vysvětluje, jak povolit přístup.
* Název databázového serveru a název databáze

## <a name="connect-to-the-blockchain-workbench-database"></a>Připojení k databázi služby Blockchain Workbench

K databázi se připojíte takto:

1. Přihlaste se k Azure Portal pomocí účtu, který má oprávnění **vlastníka** pro prostředky Azure blockchain Workbench.
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