---
title: 'Rychlý Start: pozastavení a obnovení výpočetní kapacity ve fondu synapse SQL pomocí Azure PowerShell'
description: Pomocí Azure PowerShell můžete pozastavit a obnovit synapse fond SQL (datový sklad). výpočetní prostředky.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 03/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse, devx-track-azurepowershell
ms.openlocfilehash: 08ddb6077887043d798af9790a7b66f1b8ebb95c
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570696"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-with-azure-powershell"></a>Rychlý Start: pozastavení a obnovení výpočetní kapacity ve fondu synapse SQL pomocí Azure PowerShell

Azure PowerShell můžete použít k pozastavení a obnovení výpočetních prostředků fondu SQL synapse (datový sklad).
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V tomto rychlém startu se předpokládá, že už máte fond SQL, který můžete pozastavit a obnovit. Pokud ho potřebujete vytvořit, můžete vytvořit fond SQL s názvem **mySampleDataWarehouse**pomocí [Vytvoření a připojení – portál](create-data-warehouse-portal.md) .

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

Pokud chcete zjistit, které předplatné používáte, spusťte rutinu [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Pokud potřebujete použít jiné předplatné než výchozí, spusťte rutinu [set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-sql-pool-information"></a>Vyhledat informace o fondu SQL

Vyhledejte název databáze, název serveru a skupinu prostředků pro fond SQL, který plánujete pozastavit a obnovit.

Pomocí těchto kroků můžete najít informace o poloze pro váš fond SQL:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).
1. Na levé straně Azure Portal klikněte na **Azure synapse Analytics (dřív SQL DW)** .
1. Vyberte **mySampleDataWarehouse** ze stránky **Azure synapse Analytics (dříve SQL DW)** . Otevře se fond SQL.

    ![Název serveru a skupina prostředků](./media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Zapište název fondu SQL, což je název databáze. Také poznamenejte si název serveru a skupinu prostředků.
1. V rutinách PowerShellu použijte jenom první část názvu serveru. Na předchozím obrázku je úplný název serveru sqlpoolservername.database.windows.net. V rutině PowerShellu používáme jako název serveru **sqlpoolservername** .

## <a name="pause-compute"></a>Pozastavit výpočetní prostředky

Pokud chcete ušetřit náklady, můžete pozastavit a obnovit výpočetní prostředky na vyžádání. Pokud například nepoužíváte databázi v noci a na víkendech, můžete ji během těchto časů pozastavit a obnovit během dne.

>[!NOTE]
>Při pozastavení databáze se za výpočetní prostředky neúčtují žádné poplatky. Nicméně se vám bude účtovat i nadále za úložiště.

Chcete-li pozastavit databázi, použijte rutinu [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . Následující příklad pozastaví fond SQL s názvem **mySampleDataWarehouse** hostovaný na serveru s názvem **sqlpoolservername**. Server je ve skupině prostředků Azure s názvem **myResourceGroup**.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "nsqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

Následující příklad načte databázi do objektu $database. Pak objekt [přeruší na AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Výsledky jsou uloženy v objektu resultDatabase. Poslední příkaz zobrazí výsledky.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>Pokračovat v COMPUTE

Chcete-li spustit databázi, použijte rutinu [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . V následujícím příkladu se spustí databáze s názvem **mySampleDataWarehouse** hostovaná na serveru s názvem **sqlpoolservername**. Server je ve skupině prostředků Azure s názvem **myResourceGroup**.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

Následující příklad načte databázi do objektu $database. Pak objekt převede na [pokračování – AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a výsledky uloží do $resultDatabase. Poslední příkaz zobrazí výsledky.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-sql-pool-operation"></a>Zkontroluje stav operace vašeho fondu SQL.

Pokud chcete zjistit stav svého fondu SQL, použijte rutinu [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/Get-AzSqlDatabaseActivity?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Účtují se vám poplatky za jednotky datového skladu a data uložená ve vašem fondu SQL. Výpočetní prostředky a prostředky úložiště se účtují odděleně.

- Pokud chcete uchovávat data v úložišti, pozastavte výpočetní prostředí.
- Pokud chcete odebrat budoucí poplatky, můžete odstranit fond SQL.

Pomocí tohoto postupu podle potřeby vyčistěte prostředky.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a klikněte na svůj fond SQL.

    ![Vyčištění prostředků](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Pokud chcete pozastavit výpočetní prostředky, klikněte na tlačítko **Pozastavit**. Když je fond SQL pozastaven, zobrazí se tlačítko **Start** .  Pokud chcete obnovit výpočetní prostředky, klikněte na **Spustit**.

3. Pokud chcete odebrat fond SQL, abyste vám neúčtovali výpočetní výkon nebo úložiště, klikněte na **Odstranit**.

4. Pokud chcete odebrat vytvořený SQL Server, klikněte na **sqlpoolservername.Database.Windows.NET**a pak klikněte na **Odstranit**.  S tímto odstraněním buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

5. Pokud chcete odebrat skupinu prostředků, klikněte na **myResourceGroup** a pak klikněte na **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc o fondu SQL, přejděte k článku o [načtení dat do fondu SQL](load-data-from-azure-blob-storage-using-polybase.md) . Další informace o správě výpočetních funkcí najdete v článku [Správa výpočetních přehledů](sql-data-warehouse-manage-compute-overview.md) .
