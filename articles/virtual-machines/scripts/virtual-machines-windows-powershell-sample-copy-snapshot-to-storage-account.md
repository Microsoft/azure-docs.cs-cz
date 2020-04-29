---
title: Export/Kopírování snímku jako virtuálního pevného disku do účtu úložiště v jiné oblasti – ukázka PowerShellu
description: Ukázka skriptu Azure PowerShell – export nebo kopírování snímku jako virtuálního pevného disku do účtu úložiště ve stejné jiné oblasti
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
ms.openlocfilehash: 760b6ae2d791267053acdb1e216ab5daeab7c061
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81459351"
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>Export/kopírování spravovaných snímků jako VHD do účtu úložiště v jiné oblasti pomocí PowerShellu

Tento skript exportuje spravovaný snímek do účtu úložiště v jiné oblasti. Nejprve vygeneruje identifikátor URI SAS snímku a pak pomocí něj zkopíruje snímek do účtu úložiště v jiné oblasti. Tento skript můžete použít k udržování zálohy spravovaných disků v jiné oblasti pro účely zotavení po havárii.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vygenerování identifikátoru URI SAS pro spravovaný snímek a zkopírování snímku do účtu úložiště s použitím tohoto identifikátoru URI SAS používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Grant – AzSnapshotAccess](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Generuje identifikátor URI SAS pro snímek, který se používá ke zkopírování do účtu úložiště. |
| [New-AzStorageContext](https://docs.microsoft.com/powershell/module/az.storage/new-azstoragecontext) | Vytvoří kontext účtu úložiště s použitím názvu a klíče účtu. Tento kontext je možné použít k provádění operací čtení a zápisu v účtu úložiště. |
| [Start-AzStorageBlobCopy](https://docs.microsoft.com/powershell/module/az.storage/start-azstorageblobcopy) | Zkopíruje základní virtuální pevný disk snímku do účtu úložiště. |

## <a name="next-steps"></a>Další kroky

[Vytvoření spravovaného disku z virtuálního pevného disku](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[Vytvoření virtuálního počítače ze spravovaného disku](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
