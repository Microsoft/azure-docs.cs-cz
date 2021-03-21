---
title: Azure CLI – vytvoření a ověření virtuálního počítače v testovacím prostředí
description: Tento skript Azure CLI vytvoří virtuální počítač v laboratorním prostředí a ověří, že je k dispozici.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: c7625f62d7897d61903f864b216ccf9aa13648ea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198417"
---
# <a name="use-azure-cli-to-create-and-verify-availability-of-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Použití Azure CLI k vytvoření a ověření dostupnosti virtuálního počítače v laboratorním prostředí v Azure DevTest Labs

Tento skript Azure CLI vytvoří virtuální počítač v laboratorním prostředí. Virtuální počítač se vytvoří na základě image z Marketplace pomocí ověřování SSH. Skript potom ověří, že daný virtuální počítač je k dispozici pro použití. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/create-verify-virtual-machine-in-lab/create-verify-virtual-machine-in-lab.sh "Create and verify availability of a VM")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy:

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [AZ Lab VM Create](/cli/azure/lab/vm#az-lab-vm-create) | Vytvoří virtuální počítač v laboratorním prostředí. |
| [az lab vm show](/cli/azure/lab/vm#az-lab-vm-show) | Zobrazí stav virtuálního počítače v laboratorním prostředí. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázky skriptu Azure Lab Services CLI najdete v tématu s [ukázkami Azure Lab Services CLI](../samples-cli.md).
