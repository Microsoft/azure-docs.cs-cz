---
title: Azure CLI ukázkový – kopírování spravovaných disků do účtu úložiště
description: Azure CLI ukázkový – Export nebo kopírování spravovaných disků do účtu úložiště s.
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/17/2018
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: 6252049c2258c391aa4483dd51ec0d9ee766bed0
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55727130"
---
# <a name="exportcopy-a-managed-disk-to-a-storage-account-using-the-azure-cli"></a>Export nebo kopírování spravovaného disku do účtu úložiště pomocí Azure CLI

Tento skript exportuje základní virtuální pevný disk spravovaného disku do účtu úložiště ve stejné nebo jiné oblasti. Nejprve vygeneruje identifikátor URI SAS spravovaného disku a pak pomocí něj zkopíruje virtuální pevný disk do účtu úložiště. Tento skript můžete použít ke kopírování spravovaných disků za účelem regionálního rozšíření. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.sh "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vygenerování identifikátoru URI SAS spravovaného disku a zkopírování základního virtuálního pevného disku do účtu úložiště s použitím tohoto identifikátoru URI SAS používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az disk grant-access](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az-disk-grant-access) | Vygeneruje sdílený přístupový podpis jen pro čtení, který se použije ke zkopírování základního souboru VHD do účtu úložiště nebo jeho stažení do místního prostředí.  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy) | Asynchronně zkopíruje objekt blob z jednoho účtu úložiště do jiného. |

## <a name="next-steps"></a>Další postup

[Vytvoření spravovaného disku z virtuálního pevného disku](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[Vytvoření virtuálního počítače ze spravovaného disku](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače a spravované disky najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
