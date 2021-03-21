---
title: Azure CLI – zastavení a odstranění virtuálního počítače v testovacím prostředí
description: Tento článek poskytuje skript Azure CLI, který v Azure DevTest Labs zastaví a odstraní virtuální počítač v testovacím prostředí.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 3f3802837685281339f0ca355c677e1a0ceac067
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198196"
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Skript Azure CLI slouží k zastavení a odstranění virtuálního počítače v laboratorním prostředí v Azure DevTest Labs.

Tento skript Azure CLI zastaví a odstraní virtuální počítač v laboratorním prostředí. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a lab")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy:

| Příkaz | Poznámky |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm#az-lab-vm-stop) | Zastaví virtuální počítač v laboratorním prostředí. Tato operace může chvíli trvat. |
| [az lab vm delete](/cli/azure/lab/vm#az-lab-vm-delete) | Odstraní virtuální počítač v laboratorním prostředí. Tato operace může chvíli trvat. |


## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázky skriptu Azure Lab Services CLI najdete v tématu s [ukázkami Azure Lab Services CLI](../samples-cli.md).
