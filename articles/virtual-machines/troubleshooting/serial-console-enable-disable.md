---
title: Povolení a zakázání konzoly Azure Serial Console | Dokumenty společnosti Microsoft
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
ms.openlocfilehash: e09e08f8ba36cf576bc27551254225adee3bb0fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451304"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Povolení a zakázání konzoly Azure Serial Console

Stejně jako jakýkoli jiný prostředek, azure serial console může být povolena a zakázána. Serial Console je ve výchozím nastavení povolená pro všechna předplatná v globálním Azure. V současné době zakázání min. konzole Serial Console zakáže službu pro celé předplatné. Zakázání nebo opětovné povolení konzoly Serial Console pro předplatné vyžaduje přístup na úrovni přispěvatele nebo vyšší v předplatném.

Sériovou konzolu můžete zakázat také pro jednotlivé instance škálovací sady virtuálních počítačů nebo virtuálních strojů zakázáním diagnostiky spouštění. Budete vyžadovat přístup na úrovni přispěvatele nebo vyšší na škálovací sadě virtuálních zařízení nebo virtuálních počítačů i v účtu úložiště pro diagnostiku spouštění.

## <a name="vm-level-disable"></a>Zakázání na úrovni virtuálního virtuálního montova
Sériovou konzolu lze zakázat pro konkrétní virtuální počítač nebo škálovací sadu virtuálních počítačů zakázáním nastavení diagnostiky spouštění. Vypnutím diagnostiky spouštění z portálu Azure zakažte sériovou konzolu pro virtuální počítač nebo škálovací sadu virtuálních strojů. Pokud používáte konzolu sériového počítače ve škálovací sadě virtuálních strojů, ujistěte se, že upgradujete instance škálovací sady virtuálních strojů na nejnovější model.


## <a name="subscription-level-enabledisable"></a>Povolení/zakázání na úrovni předplatného

> [!NOTE]
> Ujistěte se, že jste ve správném cloudu (Azure Public Cloud, Azure US Government Cloud) před spuštěním tohoto příkazu. Můžete zkontrolovat `az cloud list` a nastavit svůj `az cloud set -n <Name of cloud>`cloud s .

### <a name="azure-cli"></a>Azure CLI

Sériovou konzolu lze zakázat a znovu povolit pro celé předplatné pomocí následujících příkazů v rozhraní příkazového příkazu Azure (pomocí tlačítka "Try it" můžete spustit instanci prostředí Azure Cloud Shell, ve které můžete příkazy spustit):

Chcete-li zakázat konzolu sériového zařízení pro odběr, použijte následující příkazy:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Chcete-li povolit sériovou konzolu pro odběr, použijte následující příkazy:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Chcete-li získat aktuální povolený nebo zakázaný stav sériové konzoly pro odběr, použijte následující příkazy:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

### <a name="powershell"></a>PowerShell

Sériovou konzolu lze také povolit a zakázat pomocí prostředí PowerShell.

Chcete-li zakázat konzolu sériového zařízení pro odběr, použijte následující příkazy:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

Chcete-li povolit sériovou konzolu pro odběr, použijte následující příkazy:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>Další kroky
* Další informace o virtuálních [počítačích Azure Serial Console pro Linux](./serial-console-linux.md)
* Další informace o [službě Azure Serial Console pro virtuální počítače s Windows](./serial-console-windows.md)
* Informace o [možnostech řízení spotřeby v rámci konzoly Azure Serial Console](./serial-console-power-options.md)