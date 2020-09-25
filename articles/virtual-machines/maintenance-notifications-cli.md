---
title: Získání oznámení o údržbě pomocí rozhraní příkazového řádku
description: Zobrazení oznámení o údržbě pro virtuální počítače běžící v Azure a spuštění samoobslužné údržby pomocí Azure CLI.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: c449bce76cc9cb7e5f8b9659c11b443e186c65ad
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91306969"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Zpracování oznámení o plánované údržbě pomocí Azure CLI

**Tento článek se týká virtuálních počítačů, na kterých běží Linux i Windows.**

Pomocí rozhraní příkazového řádku můžete zjistit, kdy se virtuální počítače naplánovaly na [údržbu](maintenance-notifications.md). Informace o plánované údržbě jsou k dispozici z [AZ VM Get-instance-View](/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view).
 
Informace o údržbě jsou vráceny pouze v případě, že je naplánována údržba. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

Výstup
```
      "maintenanceRedeployStatus": {
      "additionalProperties": {},
      "isCustomerInitiatedMaintenanceAllowed": true,
      "lastOperationMessage": null,
      "lastOperationResultCode": "None",
      "maintenanceWindowEndTime": "2018-06-04T16:30:00+00:00",
      "maintenanceWindowStartTime": "2018-05-21T16:30:00+00:00",
      "preMaintenanceWindowEndTime": "2018-05-19T12:30:00+00:00",
      "preMaintenanceWindowStartTime": "2018-05-14T12:30:00+00:00"
```

## <a name="start-maintenance"></a>Spustit údržbu

Pokud je nastavená hodnota true, spustí se v rámci tohoto volání údržba virtuálního počítače `IsCustomerInitiatedMaintenanceAllowed` .

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Nasazení Classic

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Pokud stále máte starší virtuální počítače nasazené pomocí modelu nasazení Classic, můžete k dotazování na virtuální počítače a zahájení údržby použít Azure Classic CLI.

Ujistěte se, že jste ve správném režimu pro práci s klasickým virtuálním počítačem, a to zadáním:

```
azure config mode asm
```

Pokud chcete získat stav údržby pro virtuální počítač s názvem *myVM*, zadejte:

```
azure vm show myVM 
``` 

Pokud chcete spustit údržbu klasického virtuálního počítače s názvem *myVM* ve službě *Mojesluzba* a nasazení *myDeployment* , zadejte:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Další kroky

Plánovanou údržbu můžete také zvládnout pomocí [Azure PowerShell](maintenance-notifications-powershell.md) nebo [portálu](maintenance-notifications-portal.md).
