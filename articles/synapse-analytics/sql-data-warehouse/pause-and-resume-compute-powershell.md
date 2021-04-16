---
title: 'Rychlý Start: pozastavení a obnovení výpočetní kapacity ve vyhrazeném fondu SQL (dřív SQL DW) s Azure PowerShell'
description: Můžete použít Azure PowerShell k pozastavení a obnovení vyhrazeného fondu SQL (dříve SQL DW). výpočetní prostředky.
services: synapse-analytics
author: gaursa
ms.author: gaursa
manager: craigg
ms.reviewer: igorstan
ms.date: 03/20/2019
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: b204132a49a8790b35cc99af8eebf465fd90f041
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536370"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Rychlý Start: pozastavení a obnovení výpočetní kapacity ve vyhrazeném fondu SQL (dřív SQL DW) s Azure PowerShell

Pomocí Azure PowerShell můžete pozastavit a obnovit vyhrazené fondy SQL (dříve SQL DW) výpočetních prostředků.
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V tomto rychlém startu se předpokládá, že už máte vyhrazený fond SQL (dřív SQL DW), který můžete pozastavit a obnovit. Pokud ho potřebujete vytvořit, můžete k vytvoření vyhrazeného fondu SQL (dřív SQL DW) s názvem **mySampleDataWarehouse** použít možnost [vytvořit a připojit-portál](create-data-warehouse-portal.md) .

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

## <a name="look-up-dedicated-sql-pool-formerly-sql-dw-information"></a>Vyhledat vyhrazené informace o fondu SQL (dříve SQL DW)

Vyhledejte název databáze, název serveru a skupinu prostředků pro vyhrazený fond SQL (dříve SQL DW), který plánujete pozastavit a obnovit.

Pomocí těchto kroků můžete najít informace o umístění vyhrazeného fondu SQL (dříve SQL DW):

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Na levé straně Azure Portal klikněte na **Azure synapse Analytics (dřív SQL DW)** .
1. Vyberte **mySampleDataWarehouse** ze stránky **Azure synapse Analytics (dříve SQL DW)** . Otevře se fond SQL.

    ![Název serveru a skupina prostředků](./media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Zapište vyhrazený fond SQL (dříve SQL DW), což je název databáze. Také poznamenejte si název serveru a skupinu prostředků.
1. V rutinách PowerShellu použijte jenom první část názvu serveru. Na předchozím obrázku je úplný název serveru sqlpoolservername.database.windows.net. V rutině PowerShellu používáme jako název serveru **sqlpoolservername** .

## <a name="pause-compute"></a>Pozastavit výpočetní prostředky

Pokud chcete ušetřit náklady, můžete pozastavit a obnovit výpočetní prostředky na vyžádání. Pokud například nepoužíváte databázi v noci a na víkendech, můžete ji během těchto časů pozastavit a obnovit během dne.

>[!NOTE]
>Při pozastavení databáze se za výpočetní prostředky neúčtují žádné poplatky. Nicméně se vám bude účtovat i nadále za úložiště.

Chcete-li pozastavit databázi, použijte rutinu [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . Následující příklad pozastaví fond SQL s názvem **mySampleDataWarehouse** hostovaný na serveru s názvem **sqlpoolservername**. Server je ve skupině prostředků Azure s názvem **myResourceGroup**.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
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

Pokud chcete zjistit stav vyhrazeného fondu SQL (dřív SQL DW), použijte rutinu [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/Get-AzSqlDatabaseActivity?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Účtují se vám poplatky za jednotky datového skladu a data uložená ve vašem vyhrazeném fondu SQL (dřív SQL DW). Výpočetní prostředky a prostředky úložiště se účtují odděleně.

- Pokud chcete uchovávat data v úložišti, pozastavte výpočetní prostředí.
- Pokud chcete odebrat budoucí poplatky, můžete odstranit fond SQL.

Pomocí tohoto postupu podle potřeby vyčistěte prostředky.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a klikněte na svůj fond SQL.

    ![Vyčištění prostředků](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Pokud chcete pozastavit výpočetní prostředky, klikněte na tlačítko **Pozastavit**. Když je fond SQL pozastaven, zobrazí se tlačítko **Start** .  Pokud chcete obnovit výpočetní prostředky, klikněte na **Spustit**.

3. Pokud chcete odebrat fond SQL, abyste vám neúčtovali výpočetní výkon nebo úložiště, klikněte na **Odstranit**.

4. Pokud chcete odebrat vytvořený SQL Server, klikněte na **sqlpoolservername.Database.Windows.NET** a pak klikněte na **Odstranit**.  S tímto odstraněním buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

5. Pokud chcete odebrat skupinu prostředků, klikněte na **myResourceGroup** a pak klikněte na **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc o fondu SQL, přejděte do článku o [načtení dat do vyhrazeného fondu SQL (dřív SQL DW)](./load-data-from-azure-blob-storage-using-copy.md) . Další informace o správě výpočetních funkcí najdete v článku [Správa výpočetních přehledů](sql-data-warehouse-manage-compute-overview.md) .
