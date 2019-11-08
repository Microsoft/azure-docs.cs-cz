---
title: 'Ukázkový skript Azure PowerShellu: Vytvoření spravovaného disku ze snímku | Microsoft Docs'
description: 'Ukázkový skript Azure PowerShellu: Vytvoření spravovaného disku ze snímku'
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
ms.openlocfilehash: be763f2df9352cafaedcd5c80a60c48e919d3f1c
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749937"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell"></a>Vytvoření spravovaného disku ze snímku pomocí PowerShellu

Tento skript vytvoří spravovaný disk ze snímku. Použijte ho k obnovení virtuálního počítače ze snímků disku s operačním systémem a datového disku. Z odpovídajících snímků vytvořte disk s operačním systémem a datový disk a pak připojením spravovaných disků vytvořte nový virtuální počítač. Připojením datových disků vytvořených ze snímků můžete také obnovit datové disky existujícího virtuálního počítače.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření spravovaného disku ze snímku používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/Get-AzSnapshot) | Načte vlastnosti snímku.  |
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Vytvoří konfiguraci disku, která se použije při vytvoření disku. Bude obsahovat ID prostředku nadřazeného snímku, stejné umístění, jako má nadřazený snímek, a typ úložiště.  |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Vytvoří disk pomocí konfigurace disku, názvu disku a názvu skupiny prostředků, které použije jako parametry. |

## <a name="next-steps"></a>Další kroky


[Vytvoření virtuálního počítače ze spravovaného disku](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
