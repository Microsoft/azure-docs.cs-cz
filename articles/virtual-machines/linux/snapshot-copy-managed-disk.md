---
title: Vytvoření snímku virtuálního pevného disku v Azure
description: Přečtěte si, jak vytvořit kopii virtuálního pevného disku v Azure jako záložní nebo jak řešit problémy.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: f2d905d041198bd35c4657052bf18d981904dc44
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758388"
---
# <a name="create-a-snapshot"></a>Vytvoření snímku 

Pořiďte snímek operačního systému nebo datového disku pro zálohování nebo řešení problémů s virtuálním počítačem. Snímek je úplná kopie virtuálního pevného disku jen pro čtení. 

## <a name="use-azure-cli"></a>Použití Azure CLI 

Následující příklad vyžaduje, abyste používali [Cloud Shell](https://shell.azure.com/bash) nebo měli nainstalované azure CLI.

Následující kroky ukazují, jak pořizovat snímek pomocí příkazu **az snapshot create** s parametrem **--source-disk.** Následující příklad předpokládá, že je virtuální hod s názvem *myVM* ve skupině prostředků *myResourceGroup.*

Získejte ID disku pomocí [az vm show](/cli/azure/vm#az-vm-show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Pořiďte snímek s názvem *osDisk-backup* pomocí [az snímek vytvořit](/cli/azure/snapshot#az-snapshot-create).

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Pokud chcete uložit snímek do úložiště odolného proti zónám, musíte jej vytvořit v oblasti, která podporuje [zóny dostupnosti](../../availability-zones/az-overview.md) a zahrnout parametr **--sku Standard_ZRS.**

Můžete zobrazit seznam snímků pomocí [seznamu snímků az](/cli/azure/snapshot#az-snapshot-list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Použití webu Azure Portal 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Začněte v levém horním rohu na tlačítko **Vytvořit prostředek** a vyhledejte **snímek**. Ve výsledcích hledání vyberte **Snímek.**
3. V okně **Snímek** klepněte na tlačítko **Vytvořit**.
4. Zadejte **název** snímku.
5. Vyberte existující skupinu prostředků nebo zadejte název nové skupiny. 
7. V **části Zdrojový disk**vyberte spravovaný disk, který chcete snímek.
8. Vyberte **typ účtu,** který chcete použít k uložení snímku. Používejte **standardní pevný disk,** pokud jej nepotřebujete uložit na vysoce výkonný Disk SSD.
9. Klikněte na **Vytvořit**.


## <a name="next-steps"></a>Další kroky

 Vytvořte virtuální počítač ze snímku vytvořením spravovaného disku ze snímku a připojením nového spravovaného disku jako disku operačního systému. Další informace najdete v tématu [Vytvoření virtuálního počítače ze skriptu snímek.](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json)

