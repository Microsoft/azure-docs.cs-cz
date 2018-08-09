---
title: Ukázka skriptu Azure CLI – vytvoření a ověření virtuálního počítače v laboratorním prostředí | Microsoft Docs
description: Tento skript Azure CLI vytvoří virtuální počítač v laboratorním prostředí a ověří, že je k dispozici.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2018
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: b8d48f221dc54a3cd96bf2dbec08e40a047b7940
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439994"
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
| [az lab vm create ](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-create) | Vytvoří virtuální počítač v laboratorním prostředí. |
| [az lab vm show](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-show) | Zobrazí stav virtuálního počítače v laboratorním prostředí. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázky skriptu Azure Lab Services CLI najdete v tématu s [ukázkami Azure Lab Services CLI](../samples-cli.md).
