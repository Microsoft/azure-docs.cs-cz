---
title: Povolení a zakázání služby Azure Serial Console | Microsoft Docs
description: Jak povolit a zakázat službu Azure Serial Console
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: 1c1fe208c77142351a786fa636896e64a8a467d7
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129650"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Povolení a zakázání konzole sériového úložiště Azure

Stejně jako u jakéhokoli jiného prostředku je možné povolit a zakázat službu sériového portu Azure. Sériová konzola je ve výchozím nastavení povolená pro všechna předplatná v globálním Azure. V současné době zakázání sériové konzoly zakáže službu pro celé předplatné. Zakázání nebo opětovné povolení sériové konzoly pro předplatné vyžaduje přístup na úrovni přispěvatele nebo vyšší úrovně v rámci předplatného.

Službu sériového portu pro jednotlivé virtuální počítače nebo instance sady škálování virtuálních počítačů můžete také zakázat zakázáním diagnostiky spouštění. Budete vyžadovat přístup na úrovni přispěvatele nebo vyšší úrovně jak na virtuálním počítači, tak i v účtu úložiště pro diagnostiku spouštění.

## <a name="vm-level-disable"></a>Zakázat úrovni virtuálního počítače
Sériová konzola se dá zakázat pro konkrétní virtuální počítač nebo sadu škálování virtuálního počítače zakázáním nastavení diagnostiky spouštění. Vypnutím diagnostiky spouštění z Azure Portal zakážete sériovou konzoli pro virtuální počítač nebo sadu škálování virtuálního počítače. Pokud v sadě škálování virtuálního počítače používáte sériovou konzolu, ujistěte se, že upgradujete instance sady škálování virtuálních počítačů na nejnovější model.


## <a name="subscription-level-disable"></a>Zakázat úroveň předplatného

### <a name="azure-cli"></a>Azure CLI

Sériová konzola můžete zakázat a znovu povolit pro celé předplatné pomocí následujících příkazů v Azure CLI:

Pokud chcete zakázat sériovou konzoli pro předplatné, použijte následující příkazy:
```azurecli-interactive
subscriptionId=$(az account show -o=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default"
```

Pokud chcete pro odběr povolit sériovou konzolu, použijte následující příkazy:
```azurecli-interactive
subscriptionId=$(az account show -o=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default"
```

Chcete-li získat aktuální stav povoleného a zakázaného sériového portu pro odběr, použijte následující příkazy:
```azurecli-interactive
subscriptionId=$(az account show -o=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" -o=json | jq .properties
```

### <a name="powershell"></a>PowerShell

Sériová konzola taky můžete povolit a zakázat pomocí PowerShellu.

Pokud chcete zakázat sériovou konzoli pro předplatné, použijte následující příkazy:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

Pokud chcete pro odběr povolit sériovou konzolu, použijte následující příkazy:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>Další postup
* Další informace o [konzole sériového rozhraní Azure pro virtuální počítače se systémem Linux](./serial-console-linux.md)
* Další informace o [konzole sériového rozhraní Azure pro virtuální počítače s Windows](./serial-console-windows.md)
* Další informace o [možnostech řízení spotřeby v rámci konzoly Azure Serial Console](./serial-console-power-options.md)