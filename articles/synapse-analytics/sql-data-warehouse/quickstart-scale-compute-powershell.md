---
title: Škálování výpočetních prostředků pro fond Synapse SQL (Azure PowerShell)
description: Výpočetní prostředky pro fond Synapse SQL (datový sklad) můžete škálovat pomocí Azure PowerShellu.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c7ec8db212a24f1f23f393e4cb0e7f4150605a56
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350791"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-azure-powershell"></a>Úvodní příručka: Škálování výpočetních prostředků pro fond Synapse SQL pomocí Azure PowerShellu

Výpočetní prostředky pro fond Synapse SQL (datový sklad) můžete škálovat pomocí Azure PowerShellu. Kapacitu výpočetních prostředků můžete [horizontálně navýšit](sql-data-warehouse-manage-compute-overview.md), abyste získali lepší výkon, nebo snížit, abyste dosáhli nižších nákladů. 

Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tento rychlý start předpokládá, že již máte fond SQL, který můžete škálovat. Pokud potřebujete vytvořit jeden, použijte [Vytvořit a připojit - portál](create-data-warehouse-portal.md) k vytvoření fondu SQL s názvem **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

Chcete-li zjistit, které předplatné používáte, spusťte [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Pokud potřebujete použít jiné předplatné než výchozí, spusťte [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Vyhledání informací o datovém skladu

Vyhledejte název databáze, název serveru a skupinu prostředků pro datový sklad, jehož provoz chcete pozastavit a obnovit.

Informace o umístění vašeho datového skladu vyhledáte pomocí následujících kroků.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Klikněte na **Azure Synapse Analytics (dříve SQL DW)** na levé navigační stránce portálu Azure.
3. Vyberte **mySampleDataWarehouse** ze stránky **Azure Synapse Analytics (dříve SQL DW)** a otevřete datový sklad.

    ![Název serveru a skupina prostředků](./media/quickstart-scale-compute-powershell/locate-data-warehouse-information.png)

4. Poznamenejte si název datového skladu, který se použije jako název databáze. Pamatujte, že datový sklad je jedním z typů databáze. Také poznamenejte si název serveru a skupinu prostředků. V příkazech pause and resume použijete název serveru a název skupiny prostředků.
5. V rutinách prostředí PowerShell použijte pouze první část názvu serveru. V předchozí bitové kopii je sqlpoolservername.database.windows.net úplný název serveru. Jako název serveru v rutině prostředí PowerShell používáme **název sqlpoolservername.**

## <a name="scale-compute"></a>Škálování výpočetního výkonu

Ve fondu SQL můžete zvýšit nebo snížit výpočetní prostředky úpravou jednotek datového skladu. Podle postupu v článku [Vytvoření a připojení – portál](create-data-warehouse-portal.md) jste vytvořili **mySampleDataWarehouse** a inicializovali ho se 400 jednotkami datového skladu. V následujícím postupu upravíte jednotky datového skladu pro **mySampleDataWarehouse**.

Chcete-li změnit jednotky datového skladu, použijte rutinu [Prostředí PowerShell Set-AzSqlDatabase.](/powershell/module/az.sql/set-azsqldatabase) Následující příklad nastaví jednotky datového skladu na DW300c pro databázi **mySampleDataWarehouse**, která je hostována v **názvu skupiny prostředků resourcegroupname** na serveru **sqlpoolservername**.

```Powershell
Set-AzSqlDatabase -ResourceGroupName "resourcegroupname" -DatabaseName "mySampleDataWarehouse" -ServerName "sqlpoolservername" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>Kontrola stavu datového skladu

Chcete-li zobrazit aktuální stav datového skladu, použijte rutinu [Prostředí Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) PowerShell. Tato rutina zobrazuje stav databáze **mySampleDataWarehouse** v **názvu skupiny resourcegroup** group a **sqlpoolservername.database.windows.net**serveru .

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
Nyní jste se naučili škálovat výpočetní prostředky pro fond SQL. Další informace o fondu SQL, pokračujte v kurzu pro načítání dat.

> [!div class="nextstepaction"]
>[Načtení dat do fondu SQL](load-data-from-azure-blob-storage-using-polybase.md)
