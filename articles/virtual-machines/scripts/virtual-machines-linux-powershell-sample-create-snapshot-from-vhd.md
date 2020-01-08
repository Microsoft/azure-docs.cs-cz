---
title: Vytvoření snímku z VHD pro vytvoření několika identických spravovaných disků – ukázka PowerShellu
description: 'Ukázkový skript Azure PowerShellu: Vytvoření snímku ze souboru VHD za účelem rychlého vytvoření několika identických spravovaných disků'
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 28c97e1cb5dd329269a822be6b0b1f6f120a6b13
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463722"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>Vytvoření snímku ze souboru VHD za účelem rychlého vytvoření několika identických spravovaných disků

Tento skript vytvoří snímek ze souboru VHD v účtu úložiště ve stejném nebo jiném předplatném. Pomocí tohoto skriptu můžete importovat specializovaný virtuální pevný disk (nezobecněný/nepřipravený pomocí nástroje Sysprep) do snímku a pak pomocí tohoto snímku rychle vytvořit několik identických spravovaných disků. Také ho můžete použít k importu datového virtuálního pevného disku do snímku a pak pomocí tohoto snímku rychle vytvořit několik spravovaných disků.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá pro vytvoření spravovaného disku z virtuálního pevného disku v jiném předplatném následující příkazy: Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Vytvoří konfiguraci disku, která se použije při vytvoření disku. Její součástí je typ úložiště, umístění, ID prostředku účtu úložiště, kde je nadřazený virtuální pevný disk uložený, a identifikátor URI nadřazeného virtuálního pevného disku. |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Vytvoří disk pomocí konfigurace disku, názvu disku a názvu skupiny prostředků, které použije jako parametry. |

## <a name="next-steps"></a>Další kroky

[Vytvoření spravovaného disku ze snímku](virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
