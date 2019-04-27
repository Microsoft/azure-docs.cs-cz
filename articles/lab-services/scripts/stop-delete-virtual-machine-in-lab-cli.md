---
title: Ukázka skriptu Azure CLI – zastavení a odstranění virtuálního počítače v laboratorním prostředí | Microsoft Docs
description: Tento skript Azure CLI zastaví a odstraní virtuální počítač v laboratorním prostředí.
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
ms.openlocfilehash: c89a5c069186a1612161f660d985ff9144c68e64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61084289"
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


## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázky skriptu Azure Lab Services CLI najdete v tématu s [ukázkami Azure Lab Services CLI](../samples-cli.md).
