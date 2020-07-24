---
title: Kopírování spravovaných disků do předplatného (Windows) – ukázka PowerShellu
description: Ukázka skriptu Azure PowerShell – kopírování nebo přesun spravovaných disků do stejného nebo jiného předplatného
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
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: 6f191eaf5c0a02f77d267999c307d34e083b6cdf
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069185"
---
# <a name="copy-managed-disks-in-the-same-subscription-or-different-subscription-with-powershell-windows"></a>Kopírování spravovaných disků v rámci stejného předplatného nebo jiného předplatného pomocí PowerShellu (Windows)

Tento skript vytvoří kopii existujícího spravovaného disku ve stejném předplatném nebo v jiném předplatném. Nový disk se vytvoří ve stejné oblasti jako nadřazený spravovaný disk.   

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.ps1 "Copy managed disk")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření nového spravovaného disku v cílovém předplatném pomocí ID zdrojového spravovaného disku používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Vytvoří konfiguraci disku, která se použije při vytvoření disku. Zahrnuje ID prostředku nadřazeného disku a umístění, které je stejné jako umístění nadřazeného disku.  |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Vytvoří disk pomocí konfigurace disku, názvu disku a názvu skupiny prostředků, které použije jako parametry. |


## <a name="next-steps"></a>Další kroky

[Vytvoření virtuálního počítače ze spravovaného disku](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
