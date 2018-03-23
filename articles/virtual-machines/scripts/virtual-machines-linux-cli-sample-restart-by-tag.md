---
title: Ukázkový skript Azure CLI – Restartování virtuálních počítačů | Microsoft Docs
description: Ukázkový skript Azure CLI – Restartování virtuálních počítačů podle značky a podle ID
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: a9f7cf8ba492004cb6d9e359bfb392448dfbe813
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="restart-vms"></a>Restartování virtuálních počítačů

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

V této ukázce najdete několik způsobů, jak získat některé virtuální počítače a restartovat je.

První příklad restartuje všechny virtuální počítače ve skupině prostředků.

```bash
az vm restart --ids $(az vm list --resource-group myResourceGroup --query "[].id" -o tsv)
```

Druhý příklad pomocí příkazu `az resouce list` získá označené virtuální počítače, vyfiltruje prostředky, které jsou virtuálními počítači, a tyto virtuální počítače restartuje.

```bash
az vm restart --ids $(az resource list --tag "restart-tag" --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv)
```

Tato ukázka funguje v prostředí Bash. Možnosti spuštění skriptů Azure CLI na klientovi s Windows najdete v tématu věnovaném [spuštění Azure CLI ve Windows](../windows/cli-options.md).


## <a name="sample-script"></a>Ukázkový skript

Ukázka obsahuje tři skripty.
První skript zřídí virtuální počítače.
Používá možnost no-wait, takže se příkaz vrátí, aniž by čekal na zřízení všech virtuálních počítačů.
Druhý skript čeká na úplné zřízení virtuálních počítačů.
Třetí skript restartuje všechny zřízené virtuální počítače a pak pouze označené virtuální počítače.

### <a name="provision-the-vms"></a>Zřízení virtuálních počítačů

Tento skript vytvoří skupinu prostředků a pak vytvoří tři virtuální počítače, které se budou restartovat.
Dva z nich jsou označené.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/provision.sh "Provision the VMs")]

### <a name="wait"></a>Wait

Tento skript každých 20 sekund kontroluje stav zřizování, dokud se nezřídí všechny tři virtuální počítače nebo zřízení některého z nich neselže.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/wait.sh "Wait for the VMs to be provisioned")]

### <a name="restart-the-vms"></a>Restartování virtuálních počítačů

Tento skript restartuje všechny virtuální počítače ve skupině prostředků a pak restartuje pouze označené virtuální počítače.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart.sh "Restart VMs by tag")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupiny prostředků, virtuální počítače a všechny související prostředky.

```azurecli-interactive 
az group delete -n myResourceGroup --no-wait --yes
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuálního počítače, skupiny dostupnosti, nástroje pro vyrovnávání zatížení a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Vytvoří virtuální počítače.  |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Používá se s parametrem `--query` k zajištění, aby virtuální počítače byly před restartováním zřízené, a pak k získání ID virtuálních počítačů, pomocí kterých je restartuje. |
| [az resource list](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Používá se s parametrem `--query` k získání ID virtuálních počítačů s použitím značky. |
| [az vm restart](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Restartuje virtuální počítače. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
