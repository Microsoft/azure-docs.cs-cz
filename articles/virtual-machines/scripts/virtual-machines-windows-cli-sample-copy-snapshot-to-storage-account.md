---
title: Zkopírování snímku do účtu úložiště v jiné oblasti – ukázka vyjednané zúčtovacích úsecích
description: Ukázka skriptu Azure CLI – export/kopírování snímku jako virtuálního pevného disku do účtu úložiště ve stejné nebo jiné oblasti.
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: d470c20718b007b05360ad539973cbc2a9d7ad98
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459572"
---
# <a name="exportcopy-a-snapshot-to-a-storage-account-in-different-region-with-cli"></a>Export/kopírování snímku do účtu úložiště v jiné oblasti pomocí příkazového příkazového příkazu

Tento skript exportuje spravovaný snímek do účtu úložiště v jiné oblasti. Nejprve vygeneruje identifikátor URI SAS snímku a pak pomocí něj zkopíruje snímek do účtu úložiště v jiné oblasti. Tento skript můžete použít k udržování zálohy spravovaných disků v jiné oblasti pro účely zotavení po havárii.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vygenerování identifikátoru URI SAS pro spravovaný snímek a zkopírování snímku do účtu úložiště s použitím tohoto identifikátoru URI SAS používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az snapshot grant-access](https://docs.microsoft.com/cli/azure/snapshot) | Vygeneruje sdílený přístupový podpis jen pro čtení, který se použije ke zkopírování základního souboru VHD do účtu úložiště nebo jeho stažení do místního prostředí.  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy) | Asynchronně zkopíruje objekt blob z jednoho účtu úložiště do jiného. |

## <a name="next-steps"></a>Další kroky

[Vytvoření spravovaného disku z virtuálního pevného disku](virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázky skriptu příkazového příkazu virtuálního počítače a spravovaných disků najdete v [dokumentaci k virtuálnímu počítači Azure Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
