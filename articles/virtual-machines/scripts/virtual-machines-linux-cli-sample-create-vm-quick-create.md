---
title: Ukázkový skript Azure CLI – Rychlé vytvoření virtuálního počítače s Linuxem
description: Ukázkový skript Azure CLI – Rychlé vytvoření virtuálního počítače s Linuxem
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 4611095b936afed0870d12e1ce7b2cbd60f03b88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87479623"
---
# <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Tento skript vytvoří virtuální počítač Azure s operačním systémem Ubuntu a související síťové prostředky. Po spuštění skriptu můžete k virtuálnímu počítači přistupovat přes SSH.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-quick/create-vm-quick.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuálního počítače a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az vm create](/cli/azure/vm) | Vytvoří virtuální počítač a připojí ho k síťové kartě, virtuální síti, podsíti a skupině zabezpečení sítě. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu.  |
| [az group delete](/cli/azure/vm/extension) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
