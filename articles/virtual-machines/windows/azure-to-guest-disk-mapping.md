---
title: Postup mapování disků Azure na disky hosta virtuálních počítačů s Windows
description: Jak určit disky Azure, které Underlay disky hosta pro virtuální počítače s Windows
author: timbasham
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.openlocfilehash: 7a4fad066af37217eb42060d5fc5a7ef716770c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560986"
---
# <a name="how-to-map-azure-disks-to-windows-vm-guest-disks"></a>Postup mapování disků Azure na disky hosta virtuálních počítačů s Windows

Možná budete muset určit disky Azure, které zálohují disky hosta virtuálního počítače. V některých scénářích můžete porovnat velikost disku nebo svazku s velikostí připojených disků Azure. V případech, kdy je k virtuálnímu počítači připojeno několik disků Azure se stejnou velikostí, musíte použít logickou jednotku (LUN) datových disků. 

## <a name="what-is-a-lun"></a>Co je logická jednotka (LUN)?

Logická jednotka (LUN) je číslo, které se používá k identifikaci konkrétního úložného zařízení. Každému úložnému zařízení je přiřazen jedinečný číselný identifikátor, který začíná nulou. Úplná cesta k zařízení je reprezentovaná číslem sběrnice, číslem ID cíle a logickou jednotkou (LUN). 

Příklad: ***Sběrnice číslo 0, cílové ID 0, LUN 3***

Pro naše cvičení stačí použít logickou jednotku (LUN).

## <a name="finding-the-lun"></a>Hledání logické jednotky (LUN)

Existují dvě metody, jak najít logickou jednotku (LUN), kterou si vyberete, bude záviset na tom, zda používáte [prostory úložiště](/windows-server/storage/storage-spaces/overview) nebo ne.

### <a name="disk-management"></a>Správa disků

Pokud nepoužíváte fondy úložiště, můžete logickou jednotku najít pomocí [nástroje Správa disků](/windows-server/storage/disk-management/overview-of-disk-management) .

1. Připojte se k virtuálnímu počítači a otevřete správu disků a. Klikněte na tlačítko Start pravým tlačítkem myši a vyberte "Správa disků" a. Můžete také zadat `diskmgmt.msc` do pole Zahájit hledání.
1. V dolním podokně klikněte pravým tlačítkem na některý z disků a vyberte vlastnosti.
1. Logická jednotka (LUN) bude uvedena ve vlastnosti Location (umístění) na kartě Obecné.

### <a name="storage-pools"></a>Fondy úložiště

1. Připojte se k virtuálnímu počítači a otevřete Správce serveru
1. Vyberte souborové služby a služby úložiště, svazky, fondy úložišť.
1. V pravém dolním rohu Správce serveru bude oddíl "fyzické disky". Disky, které tvoří fond úložiště, jsou uvedeny zde a také jednotkou LUN pro každý disk.

## <a name="finding-the-lun-for-the-azure-disks"></a>Vyhledání logické jednotky (LUN) pro disky Azure

Logickou jednotku (LUN) pro disk Azure můžete najít pomocí Azure Portal, Azure CLI nebo Azure PowerShell

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Nalezení logické jednotky disku Azure v Azure Portal

1. V Azure Portal vyberte "Virtual Machines", chcete-li zobrazit seznam Virtual Machines
1. Vybrat virtuální počítač
1. Vybrat disky
1. Vyberte datový disk ze seznamu připojených disků.
1. Logická jednotka disku se zobrazí v podokně Podrobnosti o disku. Logická jednotka zobrazená tady koreluje s logickými jednotkami, které byly vyhledány v hostu pomocí Správce zařízení nebo Správce serveru.

### <a name="finding-an-azure-disks-lun-using-azure-cli-or-azure-powershell"></a>Vyhledání logické jednotky disku Azure pomocí rozhraní příkazového řádku Azure nebo Azure PowerShell

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
$vm.StorageProfile.DataDisks | ft
```
---