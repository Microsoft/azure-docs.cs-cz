---
title: Spravovaný disk ze souboru VHD ve stejném účtu (Linux) – ukázka CLI
description: Ukázkový skript Azure CLI – Vytvoření spravovaného disku ze souboru VHD v účtu úložiště ve stejném předplatném
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: bc02fb6801aa72d5c46d5fb0dc6498e5c1ded4ee
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86501513"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli-linux"></a>Vytvoření spravovaného disku ze souboru VHD v účtu úložiště ve stejném předplatném s rozhraním příkazového řádku (Linux)

Tento skript vytvoří spravovaný disk ze souboru VHD v účtu úložiště ve stejném předplatném. Pomocí tohoto skriptu můžete importovat specializovaný (negeneralizovaný a nepřipravený nástrojem Sysprep) virtuální pevný disk do spravovaného disku s operačním systémem a vytvořit tak virtuální počítač. Případně ho můžete použít k importu datového virtuálního pevného disku do spravovaného datového disku. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření spravovaného disku z virtuálního pevného disku používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az disk create](/cli/azure/disk) | Vytvoří spravovaný disk s použitím identifikátoru URI virtuálního pevného disku v účtu úložiště ve stejném předplatném. |

## <a name="next-steps"></a>Další kroky

[Vytvoření virtuálního počítače připojením spravovaného disku jako disku s operačním systémem](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače a spravované disky najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
