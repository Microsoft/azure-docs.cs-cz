---
title: Ukázkový skript Azure CLI – Vytvoření virtuálního počítače ze snímku | Microsoft Docs
description: Ukázkový skript Azure CLI – Vytvoření virtuálního počítače ze snímku
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: db0aa1781c3e35b68a59082cf7a1760f7e9a34b4
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239566"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-cli"></a>Vytvoření virtuálního počítače ze snímku pomocí rozhraní příkazového řádku

Tento skript vytvoří virtuální počítač ze snímku disku s operačním systémem.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.sh "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření spravovaného disku, virtuálního počítače a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_show) | Získá snímek s použitím názvu snímku a názvu skupiny prostředků. Vlastnost ID vráceného objektu se použije k vytvoření spravovaného disku.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Vytvoří ze snímku spravované disky s použitím ID snímku, názvu disku, velikosti a typu úložiště.  |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Vytvoří virtuální počítač s použitím spravovaného disku s operačním systémem. |

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
