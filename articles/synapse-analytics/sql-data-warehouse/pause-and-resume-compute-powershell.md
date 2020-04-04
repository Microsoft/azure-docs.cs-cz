---
title: Pozastavení a obnovení výpočetních prostředků ve fondu Synapse SQL pomocí Azure PowerShellu
description: Azure PowerShell můžete pozastavit a obnovit fond Synapse SQL (datový sklad). výpočetních prostředků.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 03/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f257f3751e7a411015ca188d704b676950845a74
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633833"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-with-azure-powershell"></a>Úvodní příručka: Pozastavení a obnovení výpočetních prostředků ve fondu Synapse SQL pomocí Azure PowerShellu

Azure PowerShell můžete pozastavit a obnovit výpočetní prostředky fondu Synapse SQL (datového skladu).
Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tento rychlý start předpokládá, že již máte fond SQL, který můžete pozastavit a obnovit. Pokud potřebujete vytvořit, můžete použít [vytvořit a připojit - portál](create-data-warehouse-portal.md) k vytvoření fondu SQL s názvem **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

Chcete-li zjistit, které předplatné používáte, spusťte [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Pokud potřebujete použít jiné předplatné než výchozí, spusťte [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-sql-pool-information"></a>Vyhledat informace o fondu SQL

Vyhledejte název databáze, název serveru a skupinu prostředků fondu SQL, který chcete pozastavit a obnovit.

Chcete-li najít informace o poloze fondu SQL, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
1. Klikněte na **Azure Synapse Analytics (dříve SQL DW)** na levé stránce portálu Azure.
1. Na stránce **Azure Synapse Analytics (dříve SQL DW)** vyberte **mySampleDataWarehouse.** Otevře se fond SQL.

    ![Název serveru a skupina prostředků](./media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Poznamenejte si název fondu SQL, což je název databáze. Také poznamenejte si název serveru a skupinu prostředků.
1. V rutinách prostředí PowerShell použijte pouze první část názvu serveru. V předchozí bitové kopii je sqlpoolservername.database.windows.net úplný název serveru. Jako název serveru v rutině prostředí PowerShell používáme **název sqlpoolservername.**

## <a name="pause-compute"></a>Pozastavení výpočtu

Chcete-li ušetřit náklady, můžete pozastavit a obnovit výpočetní prostředky na vyžádání. Například pokud nepoužíváte databázi v noci a o víkendech, můžete pozastavit během těchto časů a pokračovat v něm během dne.

>[!NOTE]
>Neexistuje žádný poplatek za výpočetní prostředky, zatímco databáze je pozastavena. Za úložiště vám se však bude nadále účtovat.

Chcete-li pozastavit databázi, použijte rutinu [Suspend-AzSqlDatabase.](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Následující příklad pozastaví fond SQL s názvem **mySampleDataWarehouse** hostovaný na serveru s názvem **sqlpoolservername**. Server je ve skupině prostředků Azure s názvem **myResourceGroup**.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "nsqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

Následující příklad načte databázi do objektu $database. Potom kanály [objektu Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Výsledky jsou uloženy v objektu resultDatabase. Konečný příkaz zobrazí výsledky.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>Pokračovat v výpočtu

Chcete-li spustit databázi, použijte rutinu [Resume-AzSqlDatabase.](/powershell/module/az.sql/resume-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Následující příklad spustí databázi s názvem **mySampleDataWarehouse** hostovanou na serveru s názvem **sqlpoolservername**. Server je ve skupině prostředků Azure s názvem **myResourceGroup**.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

Další příklad načte databázi do $database objektu. Potom kanály [objektu Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) a ukládá výsledky v $resultDatabase. Konečný příkaz zobrazí výsledky.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-sql-pool-operation"></a>Kontrola stavu operace fondu SQL

Chcete-li zkontrolovat stav fondu SQL, použijte rutinu [Get-AzSqlDatabaseActivity.](/powershell/module/az.sql/Get-AzSqlDatabaseActivity#description?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jsou účtovány za jednotky datového skladu a data uložená ve fondu SQL. Výpočetní prostředky a prostředky úložiště se účtují odděleně.

- Pokud chcete data uchovávat v úložišti, pozastavte výpočetní výkon.
- Pokud chcete odebrat budoucí poplatky, můžete odstranit fond SQL.

Pomocí tohoto postupu podle potřeby vyčistěte prostředky.

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a klikněte na svůj fond SQL.

    ![Vyčištění prostředků](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Pokud chcete pozastavit výpočetní prostředky, klikněte na tlačítko **Pozastavit**. Když je fond SQL pozastaven, zobrazí se tlačítko **Start.**  Pokud chcete obnovit výpočetní prostředky, klikněte na **Spustit**.

3. Pokud chcete fond SQL odebrat, abyste se neúčtovali za výpočetní prostředky nebo úložiště, klikněte na **odstranit**.

4. Chcete-li odebrat vytvořený server SQL, klepněte na **tlačítko sqlpoolservername.database.windows.net**a potom klepněte na tlačítko **Odstranit**.  S tímto odstraněním buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

5. Pokud chcete odebrat skupinu prostředků, klikněte na **myResourceGroup** a pak klikněte na **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

Další informace o fondu SQL, pokračujte [načíst data do](load-data-from-azure-blob-storage-using-polybase.md) článku fondu SQL. Další informace o správě výpočetních funkcí najdete v článku [Správa přehledu výpočetních prostředků.](sql-data-warehouse-manage-compute-overview.md)
