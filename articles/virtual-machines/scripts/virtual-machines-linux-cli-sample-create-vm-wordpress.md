---
title: Ukázkový skript Azure CLI – Vytvoření virtuálního počítače s Linuxem a WordPressem | Microsoft Docs
description: Ukázkový skript Azure CLI – Vytvoření virtuálního počítače s Linuxem a WordPressem
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 76b113167690d04af73cce9ffc208090b9c2328f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34653361"
---
# <a name="create-a-vm-with-wordpress"></a>Vytvoření virtuálního počítače s WordPressem

Tento skript vytvoří virtuální počítač a pomocí rozšíření vlastních skriptů virtuálního počítače Azure nainstaluje WordPress. Po spuštění skriptu můžete ke konfiguračnímu webu WordPressu přistupovat na adrese `http://<public IP of VM>/wordpress`. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-wordpress-mysql/create-wordpress-mysql.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuálního počítače a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Vytvoří virtuální počítač a připojí ho k síťové kartě, virtuální síti, podsíti a skupině NSG. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu.  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/vm#az_vm_open_port) | Vytvoří pravidlo skupiny zabezpečení sítě, které povolí příchozí provoz. V této ukázce se otevře port 80 pro přenosy HTTP. |
| [az vm extension set](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Přidá do virtuálního počítače rozšíření vlastních skriptů, které vyvolá skript pro instalaci WordPressu. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
