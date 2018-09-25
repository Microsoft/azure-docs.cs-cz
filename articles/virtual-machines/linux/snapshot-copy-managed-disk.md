---
title: Vytvoření snímku virtuálního pevného disku v Azure | Dokumentace Microsoftu
description: Informace o vytvoření kopie virtuálního pevného disku v Azure jako tlačítko zpět nahoru nebo pro řešení potíží.
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: 9fbbbb4f73b5295b648008878c8145fe926fbaad
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974387"
---
# <a name="create-a-snapshot"></a>Vytvoření snímku 

Pořízení snímku disk s operačním systémem nebo datovými zálohování nebo informace o řešení potíží virtuální počítač. Snímek je kopie virtuálního pevného disku úplné, jen pro čtení. 

## <a name="use-azure-cli"></a>Použití Azure CLI 

V následujícím příkladu vyžaduje použití [Cloud Shell](https://shell.azure.com/bash) nebo máte nainstalované Azure CLI.

Následující kroky ukazují, jak využít snímek pomocí **vytvoření snímku az** příkazů **– zdrojový disk** parametru. V následujícím příkladu se předpokládá, že je virtuální počítač volá *myVM* v *myResourceGroup* skupinu prostředků.

Získat disk pomocí ID [az vm show](/cli/azure/vm#az-vm-show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Pořízení snímku s názvem *osDisk zálohování* pomocí [vytvoření snímku az](/cli/azure/snapshot#az-snapshot-create).

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Pokud chcete uložit snímek do zóny odolná úložiště, budete muset vytvořit v oblasti, která podporuje [zóny dostupnosti](../../availability-zones/az-overview.md) a zahrnout **--sku Standard_ZRS** parametru.

Zobrazí se seznam snímků pomocí [az snapshot list](/cli/azure/snapshot#az-snapshot-list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Použití webu Azure Portal 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Začněte v levém horním rohu a klikněte na **vytvořit prostředek** a vyhledejte **snímku**. Vyberte **snímku** ve výsledcích hledání.
3. V **snímku** okna, klikněte na tlačítko **vytvořit**.
4. Zadejte **název** snímku.
5. Vyberte existující skupinu prostředků nebo zadejte název pro nový. 
7. Pro **zdrojový disk**, vyberte spravovaného disku do snímku.
8. Vyberte **typ účtu** používat k uložení snímku. Použití **standardní HDD** Pokud nepotřebujete uloží na vysoký výkon SSD.
9. Klikněte na možnost **Vytvořit**.


## <a name="next-steps"></a>Další postup

 Vytvoření virtuálního počítače ze snímku pomocí vytvoření spravovaného disku ze snímku a pak připojení nového spravovaného disku jako disku s operačním systémem. Další informace najdete v tématu [vytvoření virtuálního počítače ze snímku](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) skriptu.

