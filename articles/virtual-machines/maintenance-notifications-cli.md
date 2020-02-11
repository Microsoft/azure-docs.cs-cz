---
title: Získání oznámení o údržbě pomocí rozhraní příkazového řádku
description: Zobrazení oznámení o údržbě pro virtuální počítače běžící v Azure a spuštění samoobslužné údržby pomocí Azure CLI.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 5750d7cc6219714849aaf3e47b23708b54eefab1
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77115725"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Zpracování oznámení o plánované údržbě pomocí Azure CLI

**Tento článek se týká virtuálních počítačů, na kterých běží Linux i Windows.**

Pomocí rozhraní příkazového řádku můžete zjistit, kdy se virtuální počítače naplánovaly na [údržbu](maintenance-notifications.md). Informace o plánované údržbě jsou k dispozici z [AZ VM Get-instance-View](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view).
 
Informace o údržbě jsou vráceny pouze v případě, že je naplánována údržba. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>Spustit údržbu

Pokud je `IsCustomerInitiatedMaintenanceAllowed` nastaveno na hodnotu true, spustí se v rámci tohoto volání údržba virtuálního počítače.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Nasazení Classic


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
