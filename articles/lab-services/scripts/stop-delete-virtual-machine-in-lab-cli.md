---
title: Ukázka skriptu Azure CLI – zastavení a odstranění virtuálního počítače ve vlastním testovacím prostředí | Microsoft Docs
description: Tento skript Azure CLI zastaví a odstraní virtuální počítač ve vlastním testovacím prostředí.
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
ms.openlocfilehash: 0da99bb5e029f4d1c6be74d09ad11c6017121a7c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-custom-lab"></a>Použití Azure CLI k zastavení a odstranění virtuálního počítače ve vlastním testovacím prostředí

Tento skript Azure CLI zastaví a odstraní virtuální počítač ve vlastním testovacím prostředí. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a custom lab")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy:

| Příkaz | Poznámky |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-stop) | Zastaví virtuální počítač ve vlastním testovacím prostředí. Tato operace může chvíli trvat. |
| [az lab vm delete](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-delete) | Odstraní virtuální počítač ve vlastním testovacím prostředí. Tato operace může chvíli trvat. |


## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázky skriptu PowerShellu pro Azure Lab Services najdete v [ukázkách Azure Lab Services CLI](../samples-cli.md).