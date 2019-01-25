---
title: Ukázkový skript Azure CLI – Instalace služby IIS | Microsoft Docs
description: Ukázkový skript Azure CLI – Instalace služby IIS
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-machines-Windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/28/2017
ms.author: cynthn
ms.openlocfilehash: 1e90fac2bdfc93e2865389d43f36e7db258fbc31
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54882884"
---
# <a name="quick-create-a-virtual-machine-with-the-azure-cli"></a>Rychlé vytvoření virtuálního počítače pomocí Azure CLI

Tento skript vytvoří virtuální počítač Azure s Windows Serverem 2016 a pomocí rozšíření vlastních skriptů virtuálního počítače Azure nainstaluje službu IIS. Po spuštění skriptu můžete k výchozímu webu služby IIS přistupovat na veřejné IP adrese virtuálního počítače.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-windows-iis/create-vm-windows-iis.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuálního počítače a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Vytvoří virtuální počítač a připojí ho k síťové kartě, virtuální síti, podsíti a skupině zabezpečení sítě. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu.  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Vytvoří pravidlo skupiny zabezpečení sítě, které povolí příchozí provoz. V této ukázce se otevře port 80 pro přenosy HTTP. |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Přidá do virtuálního počítače rozšíření virtuálního počítače a spustí ho. V této ukázce se rozšíření vlastních skriptů používá k instalaci služby IIS.|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
