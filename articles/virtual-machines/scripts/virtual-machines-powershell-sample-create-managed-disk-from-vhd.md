---
title: Vytvoření spravovaného disku ze souboru VHD v účtu úložiště v předplatném – ukázka PowerShellu
description: 'Ukázkový skript Azure PowerShellu: Vytvoření spravovaného disku ze souboru VHD v účtu úložiště ve stejném nebo jiném předplatném'
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.topic: sample
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 1d743d840afc4505cdc39b258b21856b6c3e7aa9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89323132"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>Vytvoření spravovaného disku ze souboru VHD v účtu úložiště ve stejném nebo jiném předplatném pomocí PowerShellu

Tento skript vytvoří spravovaný disk ze souboru VHD v účtu úložiště ve stejném nebo jiném předplatném. Pomocí tohoto skriptu můžete importovat specializovaný (negeneralizovaný a nepřipravený nástrojem Sysprep) virtuální pevný disk do spravovaného disku s operačním systémem a vytvořit tak virtuální počítač. Také ho můžete použít k importu datového virtuálního pevného disku do spravovaného datového disku. 

Nevytvářejte několik identických spravovaných disků ze souboru VHD během krátké doby. Když vytváříte spravované disky ze souboru VHD, nejdřív se vytvoří snímek objektu blob daného souboru VHD, který se následně použije pro vytvoření spravovaných disků. Aby vytvoření disků neselhalo, kvůli omezení lze během jedné minuty vytvořit jen jeden snímek objektu blob. Tomuto omezení se vyhnete tak, že vytvoříte [spravovaný snímek ze souboru VHD](virtual-machines-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) a následně s jeho pomocí rychle vytvoříte několik spravovaných disků. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá pro vytvoření spravovaného disku z virtuálního pevného disku v jiném předplatném následující příkazy: Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Vytvoří konfiguraci disku, která se použije při vytvoření disku. Zahrnuje typ úložiště, umístění, ID prostředku účtu úložiště, kde je nadřazený virtuální pevný disk uložený, identifikátor URI nadřazeného virtuálního pevného disku (VHD). |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Vytvoří disk pomocí konfigurace disku, názvu disku a názvu skupiny prostředků, které použije jako parametry. |

## <a name="next-steps"></a>Další kroky

[Vytvoření virtuálního počítače připojením spravovaného disku jako disku s operačním systémem](virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md)

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
