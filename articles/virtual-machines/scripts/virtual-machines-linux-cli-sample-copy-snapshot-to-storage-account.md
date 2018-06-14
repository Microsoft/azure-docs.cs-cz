---
title: Ukázkový skript Azure CLI – Export nebo kopírování snímku jako virtuálního pevného disku do účtu úložiště v jiné oblasti | Microsoft Docs
description: Ukázkový skript Azure CLI – Export nebo kopírování snímku jako virtuálního pevného disku do účtu úložiště ve stejném nebo jiném předplatném
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
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: cd555cfea6a84a0bdf0d0c4c9b9b68ae079fbc7b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
ms.locfileid: "29852599"
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-cli"></a>Export nebo kopírování spravovaných snímků jako virtuálních pevných disků do účtu úložiště v jiné oblasti pomocí rozhraní příkazového řádku

Tento skript exportuje spravovaný snímek do účtu úložiště v jiné oblasti. Nejprve vygeneruje identifikátor URI SAS snímku a pak pomocí něj zkopíruje snímek do účtu úložiště v jiné oblasti. Tento skript můžete použít k udržování zálohy spravovaných disků v jiné oblasti pro účely zotavení po havárii. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vygenerování identifikátoru URI SAS pro spravovaný snímek a zkopírování snímku do účtu úložiště s použitím tohoto identifikátoru URI SAS používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az snapshot grant-access](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_grant_access) | Vygeneruje sdílený přístupový podpis jen pro čtení, který se použije ke zkopírování základního souboru VHD do účtu úložiště nebo jeho stažení do místního prostředí.  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy#az_storage_blob_copy_start) | Asynchronně zkopíruje objekt blob z jednoho účtu úložiště do jiného. |

## <a name="next-steps"></a>Další kroky

[Vytvoření spravovaného disku z virtuálního pevného disku](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[Vytvoření virtuálního počítače ze spravovaného disku](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače a spravované disky najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
