---
title: 'Rychlý úvod: Škálovat výpočetní v Azure SQL Data Warehouse – prostředí PowerShell | Microsoft Docs'
description: Prostředí PowerShell úlohy chcete škálovat výpočetní prostředky úpravou jednotky datového skladu.
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: ''
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 03/16/2018
ms.author: elbutter;barbkess
ms.openlocfilehash: 3236c0ad9676712afd220a3c8a9326f3ea1f59d5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-powershell"></a>Rychlý úvod: Škálovat výpočetní v Azure SQL Data Warehouse v prostředí PowerShell

Škálování výpočetní kapacity v Azure SQL Data Warehouse v prostředí PowerShell. [Škálovat výpočetní](sql-data-warehouse-manage-compute-overview.md) pro lepší výkon, nebo určený počet číslic zpět výpočetní abyste ušetřili náklady. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Tento kurz vyžaduje prostředí Azure PowerShell verze modulu 5.1.1 nebo novější. Spustit `Get-Module -ListAvailable AzureRM` najít verzi můžete nyní k dispozici. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps.md). 

## <a name="before-you-begin"></a>Než začnete

Tento rychlý start předpokládá, že již máte datový sklad SQL, které je možné škálovat. Pokud potřebujete vytvořit, použijte [vytvořit a připojit - portál](create-data-warehouse-portal.md) k vytvoření datového skladu názvem **mySampleDataWarehouse**. 

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) a postupujte podle pokynů na obrazovce.

```powershell
Add-AzureRmAccount
```

Chcete-li zobrazit předplatné, které používáte, spusťte [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

```powershell
Get-AzureRmSubscription
```

Pokud budete muset použít jiné předplatné než výchozí, spusťte [Select-AzureRmSubscription](/powershell/module/azurerm.profile/select-azurermsubscription).

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Vyhledat informace o datovém skladu

Najděte název databáze, název serveru a skupinu prostředků pro datový sklad, který máte v úmyslu pozastavení a obnovení. 

Postupujte podle těchto kroků se najít informace o umístění pro datový sklad.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Klikněte na tlačítko **databází SQL** v levé straně na portálu Azure.
3. Vyberte **mySampleDataWarehouse** z **databází SQL** stránky. Otevře se datového skladu. 

    ![Název a prostředek skupiny serverů](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Poznamenejte si název datového skladu, který se použije jako název databáze. Pamatujte si, že se datový sklad je jeden typ databáze. Také poznamenejte si název serveru a skupině prostředků. Budete používat v pozastavit a obnovit příkazy.
5. Pokud je server foo.database.windows.net, použijte pouze první část jako název serveru v rutin prostředí PowerShell. Na předchozím obrázku je úplný název serveru NovyServer 20171113.database.windows.net. Používáme **NovyServer 20171113** jako název serveru ve výsledcích rutiny Powershellu.

## <a name="scale-compute"></a>Škálování výpočetního výkonu

V SQL Data Warehouse můžete zvýšit nebo snížit výpočetní prostředky úpravou jednotky datového skladu. [Vytvořit a připojit - portál](create-data-warehouse-portal.md) vytvořit **mySampleDataWarehouse** a inicializovat se 400 Dwu. Následující kroky upravit Dwu pro **mySampleDataWarehouse**.

Chcete-li změnit jednotky datového skladu, použijte [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) rutiny prostředí PowerShell. Následující příklad ilustruje jednotky datového skladu na DW300 pro databázi **mySampleDataWarehouse** která je hostovaná ve skupině prostředků **myResourceGroup** na serveru  **mynewserver 20171113**.

```Powershell
Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
```

## <a name="check-data-warehouse-state"></a>Zkontrolujte stav datového skladu

Pokud chcete zobrazit aktuální stav datového skladu, použijte [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) rutiny prostředí PowerShell. To získá stav **mySampleDataWarehouse** databáze v ResourceGroup **myResourceGroup** a server **mynewserver 20171113.database.windows.net**.

```powershell
$database = Get-AzureRmSqlDatabase -ResourceGroupName myResourceGroup -ServerName mynewserver-20171113 -DatabaseName mySampleDataWarehouse
$database
```

Důsledkem přibližně takto:

```powershell   
ResourceGroupName             : myResourceGroup
ServerName                    : mynewserver-20171113
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
CurrentServiceObjectiveName   : DW300
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mynewserver-20171113/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

Můžete zobrazit **stav** databáze ve výstupu. V takovém případě se zobrazí, že tato databáze je online.  Když spustíte tento příkaz, měli byste obdržet hodnotou stavu Online, pozastavení, obnovování, škálování nebo pozastaveno. 

Pokud chcete zobrazit stav samostatně, použijte následující příkaz:

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>Další postup
Jste se naučili nyní škálování výpočetní pro datový sklad. Další informace o službě Azure SQL Data Warehouse najdete v kurzu načítání dat.

> [!div class="nextstepaction"]
>[Načtení dat do datového skladu SQL](load-data-from-azure-blob-storage-using-polybase.md)
