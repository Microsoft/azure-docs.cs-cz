---
title: Vytvoření spravovaného disku ze snímku (Linux) – ukázka PowerShellu
description: 'Ukázkový skript Azure PowerShellu: Vytvoření spravovaného disku ze snímku'
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
ms.openlocfilehash: 2faa98ec21d260e6a7cbdf9eceba24baf4b13c61
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86501124"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell-linux"></a>Vytvoření spravovaného disku ze snímku pomocí PowerShellu (Linux)

Tento skript vytvoří spravovaný disk ze snímku. Použijte ho k obnovení virtuálního počítače ze snímků disku s operačním systémem a datového disku. Z odpovídajících snímků vytvořte disk s operačním systémem a datový disk a pak připojením spravovaných disků vytvořte nový virtuální počítač. Připojením datových disků vytvořených ze snímků můžete také obnovit datové disky existujícího virtuálního počítače.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření spravovaného disku ze snímku používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | Načte vlastnosti snímku.  |
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Vytvoří konfiguraci disku, která se použije při vytvoření disku. Bude obsahovat ID prostředku nadřazeného snímku, stejné umístění, jako má nadřazený snímek, a typ úložiště.  |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Vytvoří disk pomocí konfigurace disku, názvu disku a názvu skupiny prostředků, které použije jako parametry. |

## <a name="next-steps"></a>Další kroky


[Vytvoření virtuálního počítače ze spravovaného disku](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
