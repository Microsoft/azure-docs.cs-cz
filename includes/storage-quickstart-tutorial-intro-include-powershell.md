---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 027b370d2497822dcbd6f3958556357957f9e8f5
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51817875"
---
## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se ke svému předplatnému Azure pomocí příkazu `Connect-AzureRmAccount` a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzureRmAccount
```

Pokud nevíte, jaké umístění máte použít, můžete vypsat všechna dostupná umístění. Pomocí následujícího příkladu kódu zobrazte seznam umístění a vyhledejte umístění, které chcete použít. Tento příklad používá **eastus**. Uložte umístění do proměnné a používejte tuto proměnnou, abyste umístění mohli změnit na jednom místě.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

```powershell
$resourceGroup = "myResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvořte standardní účet úložiště pro obecné účely s replikací LRS pomocí rutiny [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Dále získejte kontext účtu úložiště definující účet úložiště, který chcete použít. Když používáte účet úložiště, namísto opakovaného předávání přihlašovacích údajů odkazujte na jeho kontext. Pomocí následujícího příkladu vytvořte účet úložiště *mystorageaccount* s místně redundantním úložištěm (LRS) a šifrováním objektů blob (které bude ve výchozím nastavení povolené).

```powershell
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `
  -Kind Storage

$ctx = $storageAccount.Context
```
