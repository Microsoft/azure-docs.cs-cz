---
title: Provádění operací Azure Table storage pomocí Powershellu | Dokumentace Microsoftu
description: Provádění operací Azure Table storage pomocí Powershellu.
services: cosmos-db
author: roygara
ms.service: cosmos-db
ms.topic: article
ms.date: 03/14/2018
ms.author: rogarana
ms.subservice: cosmosdb-table
ms.openlocfilehash: 8993aea208e4ccdcf92f676cc07f2912979da606
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55476992"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Provádění operací Azure Table storage pomocí Azure Powershellu 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Azure Table storage je úložištěm dat typu NoSQL, které můžete použít k ukládání a dotazování obrovských sad strukturovaných, nerelačních dat. Hlavní součástí služby jsou tabulky, entit a vlastnosti. Tabulka je kolekce entit. Entita je sada vlastností. Každá entita může mít až 252 vlastností, které jsou všechny dvojice název hodnota. Tento článek předpokládá, že jste už obeznámení s koncepty služby Azure Table Storage. Podrobné informace najdete v tématu [Principy datového modelu služby Table Service](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) a [Začínáme s Azure Table storage pomocí .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md).

Tento článek popisuje běžné operace Azure Table storage. Získáte informace o těchto tématech: 

> [!div class="checklist"]
> * Vytvoření tabulky
> * Načíst tabulku
> * Přidání tabulky entit
> * Dotaz na tabulku
> * Odstraňovat tabulkové entity
> * Odstranění tabulky

Tento článek ukazuje, jak vytvořit nový účet úložiště Azure do nové skupiny prostředků, takže je můžete snadno odebrat po dokončení. Pokud místo toho můžete využít existující účet úložiště, můžete to udělat místo.

V příkladech vyžaduje modul Azure PowerShell `AzureRM` verze 4.4.0 nebo novější. V okně Powershellu, spusťte `Get-Module -ListAvailable AzureRM` k vyhledání verze. Pokud se nezobrazí nebo je potřeba upgradovat, najdete v článku [instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Po instalaci prostředí Azure PowerShell nebo aktualizaci, musíte nainstalovat modul **AzureRmStorageTable**, který obsahuje příkazy pro správu entity. Chcete-li nainstalovat tento modul, spusťte PowerShell jako správce a použijte **Install-Module** příkazu.

```powershell
Install-Module AzureRmStorageTable
```

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k předplatnému Azure pomocí příkazu `Connect-AzureRmAccount` a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Načíst seznam umístění

Pokud nevíte, jaké umístění máte použít, můžete vypsat všechna dostupná umístění. Po zobrazení seznamu vyhledejte umístění, které chcete použít. Tyto příklady používají **eastus**. Store tuto hodnotu do proměnné **umístění** pro budoucí použití.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup). 

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Název skupiny prostředků Store do proměnné pro budoucí použití. V tomto příkladu skupina prostředků s názvem *pshtablesrg* se vytvoří v *eastus* oblasti.

```powershell
$resourceGroup = "pshtablesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Vytvoření účtu úložiště

Vytvořit účet úložiště úrovně standard pro obecné účely s využitím místně redundantního úložiště (LRS) [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Získáte kontext účtu úložiště, který definuje účet úložiště, který se má použít. Když používáte účet úložiště, namísto opakovaného zadávání přihlašovacích údajů odkazujete na jeho kontext.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Vytvořit novou tabulku

Chcete-li vytvořit tabulku, použijte [New-AzureStorageTable](/powershell/module/azure.storage/New-AzureStorageTable) rutiny. V tomto příkladu je volána v tabulce `pshtesttable`.

```powershell
$tableName = "pshtesttable"
New-AzureStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Načíst seznam tabulek v účtu úložiště

Načíst seznam tabulek v účtu úložiště pomocí [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
Get-AzureStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Načtěte odkaz na konkrétní tabulky

K provádění operací v tabulce, potřebujete odkaz na konkrétní tabulku. Získat odkaz pomocí [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable). 

```powershell
$storageTable = Get-AzureStorageTable –Name $tableName –Context $ctx
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Odstranění tabulky

Pokud chcete odstranit tabulku, použijte [odebrat AzureStorageTable](/powershell/module/azure.storage/Remove-AzureStorageTable). Tato rutina odebere tabulky, včetně všechna jeho data.

```powershell
Remove-AzureStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzureStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste vytvořili nový účet skupiny a úložiště prostředků na začátku tento návod, můžete odebrat všechny prostředky, které jste vytvořili v tomto cvičení odstraněním skupiny prostředků. Tento příkaz odstraní všechny prostředky v rámci skupiny, jakož i samotnou skupinu prostředků.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto článku s postupy jste se dozvěděli o běžných operací Azure Table storage s využitím Powershellu, také se naučíte: 

> [!div class="checklist"]
> * Vytvoření tabulky
> * Načíst tabulku
> * Přidání tabulky entit
> * Dotaz na tabulku
> * Odstraňovat tabulkové entity
> * Odstranění tabulky

Další informace naleznete v následujících článcích

* [Rutiny PowerShellu pro úložiště](/powershell/module/azurerm.storage#storage)

* [Práce s tabulkami Azure Storage z prostředí PowerShell](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/)

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.
