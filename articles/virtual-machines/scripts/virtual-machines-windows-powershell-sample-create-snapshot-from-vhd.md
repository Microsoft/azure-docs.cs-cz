---
title: Vytvoření snímku z VHD pro vytvoření několika identických spravovaných disků – ukázka PowerShellu
description: 'Ukázkový skript Azure PowerShellu: Vytvoření snímku ze souboru VHD za účelem rychlého vytvoření několika identických spravovaných disků'
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 9da8adb786baebcb2e798c7ffb5998aca0f68265
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81459249"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>Vytvoření snímku ze souboru VHD za účelem rychlého vytvoření několika identických spravovaných disků

Tento skript vytvoří snímek ze souboru VHD v účtu úložiště ve stejném nebo jiném předplatném. Pomocí tohoto skriptu můžete importovat specializovaný virtuální pevný disk (nezobecněný/nepřipravený pomocí nástroje Sysprep) do snímku a pak pomocí tohoto snímku rychle vytvořit několik identických spravovaných disků. Také ho můžete použít k importu datového virtuálního pevného disku do snímku a pak pomocí tohoto snímku rychle vytvořit několik spravovaných disků. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="next-steps"></a>Další kroky

[Vytvoření spravovaného disku ze snímku](virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[Vytvoření virtuálního počítače připojením spravovaného disku jako disku s operačním systémem](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
