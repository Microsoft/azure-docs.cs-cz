---
title: Azure CLI – zastavení a odstranění virtuálního počítače v testovacím prostředí
description: Tento článek poskytuje skript Azure CLI, který v Azure DevTest Labs zastaví a odstraní virtuální počítač v testovacím prostředí.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: 3ba2b2c3924f7fea481907b825ba5a04ab50b0bf
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290328"
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
| [az lab vm stop](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-stop) | Zastaví virtuální počítač v laboratorním prostředí. Tato operace může chvíli trvat. |
| [az lab vm delete](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-delete) | Odstraní virtuální počítač v laboratorním prostředí. Tato operace může chvíli trvat. |


## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázky skriptu Azure Lab Services CLI najdete v tématu s [ukázkami Azure Lab Services CLI](../samples-cli.md).
