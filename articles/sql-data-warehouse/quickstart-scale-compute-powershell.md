---
title: 'Rychlý start: Horizontální navýšení kapacity výpočetních prostředků ve službě Azure SQL Data Warehouse – PowerShell | Dokumentace Microsoftu'
description: Škálujte kapacitu výpočetních prostředků ve službě Azure SQL Data Warehouse pomocí PowerShellu. Kapacitu výpočetních prostředků můžete horizontálně navýšit, abyste získali lepší výkon, nebo snížit, abyste dosáhli nižších nákladů.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6df0ff292c21ceb99bc30c7cd8cab007a27a0fcb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469443"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-powershell"></a>Rychlý start: Škálujte kapacitu výpočetních prostředků ve službě Azure SQL Data Warehouse v Powershellu

Škálujte kapacitu výpočetních prostředků ve službě Azure SQL Data Warehouse pomocí PowerShellu. Kapacitu výpočetních prostředků můžete [horizontálně navýšit](sql-data-warehouse-manage-compute-overview.md), abyste získali lepší výkon, nebo snížit, abyste dosáhli nižších nákladů.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Tento kurz vyžaduje modul Azure PowerShell verze 5.1.1 nebo novější. To, jakou verzi aktuálně používáte, zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

## <a name="before-you-begin"></a>Před zahájením

Tento rychlý start předpokládá, že už máte datový sklad SQL, který lze škálovat. Pokud ho potřebujete vytvořit, postupujte podle pokynů v článku [Vytvoření a připojení – portál](create-data-warehouse-portal.md) a vytvořte datový sklad s názvem **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzureRmAccount
```

Pokud chcete zobrazit, které předplatné používáte, spusťte příkaz [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

```powershell
Get-AzureRmSubscription
```

Pokud budete muset použít jiné předplatné než výchozí, spusťte [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext).

```powershell
Set-AzureRmContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Vyhledání informací o datovém skladu

Vyhledejte název databáze, název serveru a skupinu prostředků pro datový sklad, jehož provoz chcete pozastavit a obnovit.

Informace o umístění vašeho datového skladu vyhledáte pomocí následujících kroků.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Na webu Azure Portal klikněte vlevo na **Datové sklady SQL**.
3. Na stránce **Datové sklady SQLL** vyberte **mySampleDataWarehouse**. Tím se otevře datový sklad.

    ![Název serveru a skupina prostředků](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Poznamenejte si název datového skladu, který se použije jako název databáze. Pamatujte, že datový sklad je jedním z typů databáze. Také poznamenejte si název serveru a skupinu prostředků. Použijete je v příkazech pro pozastavení a obnovení.
5. Pokud využíváte server foo.database.windows.net, v rutinách PowerShellu používejte jako název serveru jen jeho první část. Na předchozím obrázku je úplný název serveru newserver-20171113.database.windows.net. Jako název serveru v rutině PowerShellu používáme **newserver-20180430**.

## <a name="scale-compute"></a>Škálování výpočetního výkonu

Ve službě SQL Data Warehouse můžete upravit počet jednotek datového skladu a zvýšit nebo snížit tak množství výpočetních prostředků. Podle postupu v článku [Vytvoření a připojení – portál](create-data-warehouse-portal.md) jste vytvořili **mySampleDataWarehouse** a inicializovali ho se 400 jednotkami datového skladu. V následujícím postupu upravíte jednotky datového skladu pro **mySampleDataWarehouse**.

Pokud chcete změnit jednotky datového skladu, použijte rutinu PowerShellu [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase). Následující příklad nastaví jako jednotky datového skladu DW300 pro databázi **mySampleDataWarehouse**, která je hostovaná ve skupině prostředků **myResourceGroup** na serveru **mynewserver-20180430**.

```Powershell
Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
```

## <a name="check-data-warehouse-state"></a>Kontrola stavu datového skladu

Pokud se chcete podívat na stav datového skladu, můžete použít powershellovou rutinu [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase). S její pomocí se načte stav databáze **mySampleDataWarehouse** ve skupině prostředků **myResourceGroup** na serveru **mynewserver-20180430.database.windows.net**.

```powershell
$database = Get-AzureRmSqlDatabase -ResourceGroupName myResourceGroup -ServerName mynewserver-20171113 -DatabaseName mySampleDataWarehouse
$database
```

Výsledek bude přibližně takový:

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

Ve výstupu vidíte **Stav** databáze. V tomto případě vidíte, že tato databáze je online.  Když spustíte tento příkaz, hodnota v poli Stav by měla být Online, Pozastavování, Obnovování, Škálování, nebo Pozastaveno.

Pokud chcete zobrazit jen samotný stav, použijte následující příkaz:

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>Další postup
Nyní už víte, jak škálovat výpočetní prostředky pro datový sklad. Další informace o službě Azure SQL Data Warehouse najdete v kurzu načítání dat.

> [!div class="nextstepaction"]
>[Načtení dat do datového skladu SQL](load-data-from-azure-blob-storage-using-polybase.md)
