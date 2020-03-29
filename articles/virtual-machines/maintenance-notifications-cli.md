---
title: Získejte oznámení o údržbě pomocí cli
description: Zobrazení oznámení o údržbě pro virtuální počítače spuštěné v Azure a spuštění samoobslužné údržby pomocí nařízení o nastavení příkazu k řipodlu Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 4ad57c1c71a51f948bd405a5487a1e27e36bfff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920888"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Zpracování oznámení o plánované údržbě pomocí příkazového příkazového příkazu Azure

**Tento článek se vztahuje na virtuální počítače se systémem Linux i Windows.**

Pomocí vykreslování účtu můžete zjistit, kdy jsou virtuální mích naplánovány [na údržbu](maintenance-notifications.md). Informace o plánované údržbě jsou k dispozici z [az vm get-instance-view](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view).
 
Informace o údržbě jsou vráceny pouze v případě, že je plánována údržba. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>Zahájit údržbu

Následující volání spustí údržbu na virtuálním počítači, pokud `IsCustomerInitiatedMaintenanceAllowed` je nastavena na hodnotu true.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Nasazení Classic

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Pokud máte stále starší virtuální počítače, které byly nasazeny pomocí klasického modelu nasazení, můžete použít klasické rozhraní příkazového příkazového příkazu Azure k dotazování na virtuální počítače a zahájení údržby.

Ujistěte se, že jste ve správném režimu pro práci s klasickým virtuálním virtuálním virtuálním montovnam zadáním:

```
azure config mode asm
```

Chcete-li získat stav údržby virtuálního virtuálního soudu s názvem *myVM*, zadejte:

```
azure vm show myVM 
``` 

Chcete-li zahájit údržbu klasického virtuálního počítače s názvem *myVM* ve službě *myService* a *nasazení myDeployment,* zadejte:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Další kroky

Plánovanou údržbu můžete taky zpracovat pomocí [Prostředí Azure PowerShell](maintenance-notifications-powershell.md) nebo [portálu](maintenance-notifications-portal.md).
