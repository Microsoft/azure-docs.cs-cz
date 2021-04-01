---
title: Získání podrobností o databázi služby Azure Blockchain Workbench
description: Přečtěte si, jak získat informace o databázi a databázovém serveru Azure blockchain Workbench Preview.
ms.date: 09/05/2019
ms.topic: how-to
ms.reviewer: mmercuri
ms.openlocfilehash: 898360d8e22803e17ff4da7d3b63f5595cad47ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015481"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Získání informací o databázi služby Azure Blockchain Workbench

Tento článek ukazuje, jak získat podrobné informace o vaší databázi Azure blockchain Workbench Preview.

## <a name="overview"></a>Přehled

Informace o spouštění aplikací, pracovních postupů a chytrých kontraktů se poskytují pomocí zobrazení databáze SQL služby Blockchain Workbench. Vývojáři můžou tyto informace použít při používání nástrojů, jako je Microsoft Excel, Power BI, Visual Studio a SQL Server Management Studio.

Aby se vývojář mohl k této databázi připojit, potřebuje:

* Povolený přístup externího klienta v bráně firewall databáze. Tento článek o konfiguraci brány firewall databáze vysvětluje, jak povolit přístup.
* Název databázového serveru a název databáze

## <a name="connect-to-the-blockchain-workbench-database"></a>Připojení k databázi Blockchain Workbench

K databázi se připojíte takto:

1. Přihlaste se k Azure Portal pomocí účtu, který má oprávnění **vlastníka** pro prostředky Azure blockchain Workbench.
2. V levém navigačním podokně zvolte **Skupiny prostředků**.
3. Zvolte název skupiny prostředků svého nasazení služby Blockchain Workbench.
4. Výběrem možnosti **Typ** tento seznam prostředků seřaďte a pak zvolte **SQL Server**. Seřazený seznam v následujícím snímku obrazovky zobrazuje dvě databáze "Master" a jeden, který jako **předponu prostředku** používá "lhgn".

   ![Seřazený seznam prostředků služby Blockchain Workbench](./media/getdb-details/sorted-workbench-resource-list.png)

5. Pokud si chcete prohlédnout podrobné informace o databázi služby Blockchain Workbench, vyberte odkaz na databázi s **předponou prostředku**, kterou jste zadali pro nasazení služby Blockchain Workbench.

   ![Podrobnosti databáze](./media/getdb-details/workbench-db-details.png)

Název databázového serveru a název databáze vám umožní se připojit k databázi služby Blockchain Workbench pomocí vývojového nebo vykazovacího nástroje.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zobrazení databáze ve službě Azure Blockchain Workbench](database-views.md)