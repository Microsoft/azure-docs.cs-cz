---
title: Azure ukázkový skript prostředí PowerShell - Export nebo zkopírování snímku jako virtuální pevný disk na účet úložiště v jiné oblasti. | Microsoft Docs
description: Azure ukázkový skript prostředí PowerShell - Export nebo zkopírování snímku jako virtuální pevný disk na účet úložiště ve stejné oblasti jiné
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: be21a891121df1d645b430d87b572cde6c945d61
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23879642"
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>Export nebo zkopírování spravované snímků jako virtuální pevný disk na účet úložiště v jiné oblasti pomocí prostředí PowerShell

Tento skript exporty spravované snímku na účet úložiště v jiné oblasti. Nejprve generuje identifikátor URI SAS snímku a používá je zkopírovat do účtu úložiště v jiné oblasti. Tento skript lze použijte k udržování zálohování spravované disky v jiné oblasti pro obnovení po havárii.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá tyto příkazy ke generování identifikátor URI pro SAS pro spravované snímek a zkopíruje snímku na účet úložiště pomocí SAS URI. Každý příkaz v tabulce odkazy na dokumentaci konkrétní příkaz.

| Příkaz | Poznámky |
|---|---|
| [Udělení AzureRmSnapshotAccess](/powershell/module/azurerm.compute/New-AzureRmDisk) | Identifikátor URI pro SAS se generuje pro snímek, který se používá ke zkopírování na účet úložiště. |
| [Nové AzureStorageContext](/powershell/module/azure.storage/New-AzureStorageContext) | Vytvoří kontext účtu úložiště pomocí názvu účtu a klíč. Tento kontext lze použít k provedení operace čtení a zápisu v účtu úložiště. |
| [Počáteční AzureStorageBlobCopy](/powershell/module/azure.storage/Start-AzureStorageBlobCopy) | Zkopíruje základní virtuální pevný disk snímku na účet úložiště |

## <a name="next-steps"></a>Další kroky

[Vytvoření spravovaného disku z virtuálního pevného disku](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[Vytvoření virtuálního počítače z spravovaného disku](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Další informace o modulu Azure PowerShell najdete v tématu [dokumentace Azure PowerShell](/powershell/azure/overview).

Ukázky skriptu PowerShell další virtuální počítač nachází v [virtuálního počítače Windows Azure dokumentaci](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).