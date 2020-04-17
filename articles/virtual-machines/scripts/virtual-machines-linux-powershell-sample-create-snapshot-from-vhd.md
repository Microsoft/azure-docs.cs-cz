---
title: Vytvoření snímku z virtuálního pevného disku pro vytvoření více identických spravovaných disků – ukázka prostředí PowerShell
description: 'Ukázkový skript Azure PowerShellu: Vytvoření snímku ze souboru VHD za účelem rychlého vytvoření několika identických spravovaných disků'
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: b163b5a1aad343382b9b2d22a91c1b07078c14df
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459708"
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
| [Nový-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Vytvoří konfiguraci disku, která se použije při vytvoření disku. Její součástí je typ úložiště, umístění, ID prostředku účtu úložiště, kde je nadřazený virtuální pevný disk uložený, a identifikátor URI nadřazeného virtuálního pevného disku. |
| [Nový-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Vytvoří disk pomocí konfigurace disku, názvu disku a názvu skupiny prostředků, které použije jako parametry. |

## <a name="next-steps"></a>Další kroky

[Vytvoření spravovaného disku ze snímku](virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
