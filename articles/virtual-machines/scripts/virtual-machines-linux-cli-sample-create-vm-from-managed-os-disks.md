---
title: Ukázkový skript Azure CLI – Vytvoření virtuálního počítače připojením spravovaného disku jako disku s operačním systémem | Microsoft Docs
description: Ukázkový skript Azure CLI – Vytvoření virtuálního počítače připojením spravovaného disku jako disku s operačním systémem
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
ms.openlocfilehash: 3ec3d152864ab8caf9f0c68966b6d4f8fd9e64dd
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55695232"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-cli"></a>Vytvoření virtuálního počítače s použitím existujícího spravovaného disku s operačním systémem pomocí rozhraní příkazového řádku

Tento skript vytvoří virtuální počítač připojením existujícího spravovaného disku jako disku s operačním systémem. Tento skript můžete použít v následujících scénářích:
* Vytvoření virtuálního počítače z existujícího spravovaného disku s operačním systémem zkopírovaného ze spravovaného disku v jiném předplatném
* Vytvoření virtuálního počítače z existujícího spravovaného disku vytvořeného ze specializovaného souboru VHD 
* Vytvoření virtuálního počítače z existujícího spravovaného disku s operačním systémem vytvořeného ze snímku 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-attach-existing-managed-os-disk/create-vm-attach-existing-managed-os-disk.sh "Create VM from a managed disk")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k získání vlastností spravovaného disku, připojení spravovaného disku k novému virtuálnímu počítači a vytvoření virtuálního počítače používá následující příkazy. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk) | Získá vlastnosti spravovaného disku s použitím názvu disku a názvu skupiny prostředků. Vlastnost ID se použije k připojení spravovaného disku k novému virtuálnímu počítači. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Vytvoří virtuální počítač s použitím spravovaného disku s operačním systémem. |
## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
