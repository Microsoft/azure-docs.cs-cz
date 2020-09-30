---
title: 'Rychlý Start: škálování COMPUTE pro synapse fond SQL (Azure PowerShell)'
description: Výpočetní prostředky pro synapse fond SQL (datový sklad) můžete škálovat pomocí Azure PowerShell.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: 8077b1a52e44ce3a5160309c92288f756bed1014
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91566138"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-azure-powershell"></a>Rychlý Start: škálování COMPUTE pro synapse fond SQL s Azure PowerShell

Výpočetní prostředky pro synapse fond SQL (datový sklad) můžete škálovat pomocí Azure PowerShell. Kapacitu výpočetních prostředků můžete [horizontálně navýšit](sql-data-warehouse-manage-compute-overview.md), abyste získali lepší výkon, nebo snížit, abyste dosáhli nižších nákladů.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V tomto rychlém startu se předpokládá, že už máte fond SQL, který můžete škálovat. Pokud potřebujete vytvořit fond SQL s názvem **mySampleDataWarehouse**, vytvořte ho pomocí [Vytvoření a připojení – portál](create-data-warehouse-portal.md) .

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

## <a name="look-up-data-warehouse-information"></a>Vyhledání informací o datovém skladu

Vyhledejte název databáze, název serveru a skupinu prostředků pro datový sklad, jehož provoz chcete pozastavit a obnovit.

Informace o umístění vašeho datového skladu vyhledáte pomocí následujících kroků.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).
2. V levé navigační stránce Azure Portal klikněte na **Azure synapse Analytics (dřív SQL DW)** .
3. Vyberte **mySampleDataWarehouse** ze stránky **Azure synapse Analytics (dříve SQL DW)** a otevřete datový sklad.

    ![Název serveru a skupina prostředků](./media/quickstart-scale-compute-powershell/locate-data-warehouse-information.png)

4. Poznamenejte si název datového skladu, který se použije jako název databáze. Pamatujte, že datový sklad je jedním z typů databáze. Také poznamenejte si název serveru a skupinu prostředků. V příkazech pozastavit a pokračovat budete používat název serveru a název skupiny prostředků.
5. V rutinách PowerShellu použijte jenom první část názvu serveru. Na předchozím obrázku je úplný název serveru sqlpoolservername.database.windows.net. V rutině PowerShellu používáme jako název serveru **sqlpoolservername** .

## <a name="scale-compute"></a>Škálování výpočetního výkonu

Ve fondu SQL můžete zvýšit nebo snížit výpočetní prostředky úpravou jednotek datového skladu. Podle postupu v článku [Vytvoření a připojení – portál](create-data-warehouse-portal.md) jste vytvořili **mySampleDataWarehouse** a inicializovali ho se 400 jednotkami datového skladu. V následujícím postupu upravíte jednotky datového skladu pro **mySampleDataWarehouse**.

Pokud chcete změnit jednotky datového skladu, použijte rutinu [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) prostředí PowerShell. Následující příklad nastaví jednotky datového skladu tak, aby se DW300c pro databázi **mySampleDataWarehouse**, která je hostovaná ve skupině prostředků **ResourceGroupName** na serveru **sqlpoolservername**.

```Powershell
Set-AzSqlDatabase -ResourceGroupName "resourcegroupname" -DatabaseName "mySampleDataWarehouse" -ServerName "sqlpoolservername" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>Kontrola stavu datového skladu

Pokud chcete zobrazit aktuální stav datového skladu, použijte rutinu PowerShellu [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . Tato rutina zobrazuje stav databáze **mySampleDataWarehouse** ve zdroji **ResourceGroupName** a na serveru **sqlpoolservername.Database.Windows.NET**.

```powershell
$database = Get-AzSqlDatabase -ResourceGroupName resourcegroupname -ServerName sqlpoolservername -DatabaseName mySampleDataWarehouse
```

Výsledek bude přibližně takový:

```powershell
ResourceGroupName             : resourcegroupname
ServerName                    : sqlpoolservername
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300c
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/resourcegroupname/providers/Microsoft.Sql/servers/sqlpoolservername/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

Ve výstupu vidíte **Stav** databáze. V tomto případě vidíte, že tato databáze je online.  Když spustíte tento příkaz, hodnota v poli Stav by měla být Online, Pozastavování, Obnovování, Škálování, nebo Pozastaveno.

Pokud chcete zobrazit jen samotný stav, použijte následující příkaz:

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>Další kroky

Nyní jste zjistili, jak škálovat výpočetní prostředky pro fond SQL. Pokud chcete získat další informace o fondu SQL, pokračujte v kurzu načítání dat.

> [!div class="nextstepaction"]
>[Načtení dat do fondu SQL](load-data-from-azure-blob-storage-using-polybase.md)
