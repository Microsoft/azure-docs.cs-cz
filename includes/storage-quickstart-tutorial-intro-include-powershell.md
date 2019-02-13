---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 1f8f8d314a8bb37a08b3696f597b395a8a4beb8e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212991"
---
## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se ke svému předplatnému Azure pomocí příkazu `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

Pokud nevíte, jaké umístění máte použít, můžete vypsat všechna dostupná umístění. Pomocí následujícího příkladu kódu zobrazte seznam umístění a vyhledejte umístění, které chcete použít. Tento příklad používá **eastus**. Uložte umístění do proměnné a používejte tuto proměnnou, abyste umístění mohli změnit na jednom místě.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

```powershell
$resourceGroup = "myResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvořte účet standardního úložiště pro obecné účely s replikací LRS pomocí [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Dále získejte kontext účtu úložiště definující účet úložiště, který chcete použít. Když používáte účet úložiště, namísto opakovaného předávání přihlašovacích údajů odkazujte na jeho kontext. Pomocí následujícího příkladu vytvořte účet úložiště *mystorageaccount* s místně redundantním úložištěm (LRS) a šifrováním objektů blob (které bude ve výchozím nastavení povolené).

```powershell
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `

$ctx = $storageAccount.Context
```
