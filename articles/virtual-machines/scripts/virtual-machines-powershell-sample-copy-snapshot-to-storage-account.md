---
title: Ukázka prostředí PowerShell – export nebo kopírování snímku jako virtuálního pevného disku do účtu úložiště v jiné oblasti
description: Ukázka skriptu Azure PowerShell – export nebo kopírování snímku jako virtuálního pevného disku do účtu úložiště ve stejné jiné oblasti
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.topic: sample
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 389c256e951c8a97f0233e5082ea13cc1573302d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89320354"
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
| [Grant – AzSnapshotAccess](/powershell/module/az.compute/new-azdisk) | Generuje identifikátor URI SAS pro snímek, který se používá ke zkopírování do účtu úložiště. |
| [New-AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext) | Vytvoří kontext účtu úložiště s použitím názvu a klíče účtu. Tento kontext je možné použít k provádění operací čtení a zápisu v účtu úložiště. |
| [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) | Zkopíruje základní virtuální pevný disk snímku do účtu úložiště. |

## <a name="next-steps"></a>Další kroky

[Vytvoření spravovaného disku z virtuálního pevného disku](virtual-machines-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Vytvoření virtuálního počítače ze spravovaného disku](./virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
