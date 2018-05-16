---
title: Ukázka skriptu Azure CLI – spuštění virtuálního počítače ve vlastním testovacím prostředí | Microsoft Docs
description: Tento skript Azure CLI spustí virtuální počítač ve vlastním testovacím prostředí.
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
ms.openlocfilehash: c14328904f29f8160a1ccc1130fe6d2cfa629822
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-cli-to-start-a-virtual-machine-in-a-custom-lab"></a>Použití Azure CLI ke spuštění virtuálního počítače ve vlastním testovacím prostředí

Tento skript Azure CLI spustí virtuální počítač ve vlastním testovacím prostředí. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/start-connect-virtual-machine-in-lab/start-connect-virtual-machine-in-lab.sh "Start a VM")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy:

| Příkaz | Poznámky |
|---|---|
| [az lab vm start ](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-start) | Spustí virtuální počítač ve vlastním testovacím prostředí. Tato operace může chvíli trvat. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázky skriptu PowerShellu pro Azure Lab Services najdete v [ukázkách Azure Lab Services CLI](../samples-cli.md).