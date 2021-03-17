---
title: Vytvoření snímku VHD pomocí Azure CLI
description: Naučte se vytvořit kopii VHD v Azure jako zálohu nebo pro řešení problémů.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d041f864c6c8cd3ae9c522d79447d71c86f9ac04
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98875600"
---
# <a name="create-a-snapshot-using-the-portal-or-azure-cli"></a>Vytvoření snímku pomocí portálu nebo rozhraní příkazového řádku Azure

Pořídit snímek operačního systému nebo datového disku pro zálohování nebo řešení potíží s virtuálním počítačem. Snímek je plná kopie VHD, která je jen pro čtení. 

## <a name="use-azure-cli"></a>Použití Azure CLI 

Následující příklad vyžaduje, abyste používali [Cloud Shell](https://shell.azure.com/bash) nebo máte nainstalované rozhraní příkazového řádku Azure CLI.

Následující kroky ukazují, jak pořídit snímek pomocí příkazu **AZ Snapshot Create** s parametrem **--source-disk** . Následující příklad předpokládá, že ve skupině prostředků *myResourceGroup* existuje virtuální počítač s názvem *myVM* .

ID disku získáte pomocí [AZ VM show](/cli/azure/vm#az-vm-show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Pořídit snímek s názvem *osDisk-Backup* pomocí [AZ Snapshot Create](/cli/azure/snapshot#az-snapshot-create).

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Pokud chcete snímek Uložit do odolného úložiště v zóně, je nutné ho vytvořit v oblasti, která podporuje [zóny dostupnosti](../../availability-zones/az-overview.md) , a zahrnout parametr **--SKU Standard_ZRS** .

Seznam snímků můžete zobrazit pomocí [seznamu AZ Snapshot list](/cli/azure/snapshot#az-snapshot-list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Použití webu Azure Portal 

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Začněte v levém horním rohu, klikněte na **vytvořit prostředek** a vyhledejte **snímek**. Z výsledků hledání vyberte **snímek** .
3. V okně **snímek** klikněte na **vytvořit**.
4. Zadejte **název** snímku.
5. Vyberte existující skupinu prostředků nebo zadejte název nového. 
7. Pro **zdrojový disk** vyberte spravovaný disk, který se má snímek.
8. Vyberte **typ účtu** , který chcete použít k uložení snímku. Použijte **HDD úrovně Standard** , pokud ho nepotřebujete, aby byl uložený na disku SSD s vysokou úrovní.
9. Klikněte na **Vytvořit**.


## <a name="next-steps"></a>Další kroky

 Vytvořte virtuální počítač ze snímku vytvořením spravovaného disku ze snímku a připojením nového spravovaného disku jako disku s operačním systémem. Další informace najdete v tématu [Vytvoření virtuálního počítače ze skriptu snímku](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot?toc=%2fcli%2fmodule%2ftoc.json) .