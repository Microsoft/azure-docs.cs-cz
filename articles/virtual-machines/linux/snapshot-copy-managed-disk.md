---
title: Vytvořte snímek virtuálního pevného disku v Azure | Microsoft Docs
description: Postup vytvoření kopie virtuálního pevného disku v Azure jako zpět nahoru nebo řešení potíží.
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
ms.date: 03/20/2018
ms.author: cynthn
ms.openlocfilehash: e5882b2ddc708544a7715da13c1f0d18384ce4e3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-snapshot"></a>Vytvoření snímku 

Pořízení snímku diskem operačního systému nebo data pro zálohování nebo řešení problémů s virtuálních počítačů. Snímek je úplná, jen pro čtení kopie virtuálního pevného disku. 

## <a name="use-azure-cli"></a>Použití Azure CLI 

Následující příklad vyžaduje Azure CLI 2.0 nainstalovaná a přihlášení k účtu Azure. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Následující kroky ukazují, jak provést snímek pomocí `az snapshot create` s `--source-disk` parametr. Následující příklad předpokládá, že je virtuální počítač s názvem `myVM` v `myResourceGroup` skupinu prostředků.

Získání ID disku.
```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Pořízení snímku s názvem *osDisk zálohování*.

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Pokud chcete uložit snímku do zóny odolná úložiště, budete muset vytvořit v oblasti, která podporuje [dostupnost zóny](../../availability-zones/az-overview.md) a zahrnout `--sku Standard_ZRS` parametr.

## <a name="use-azure-portal"></a>Použití webu Azure Portal 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Spuštění v levé horní, klikněte na tlačítko **vytvořit prostředek** a vyhledejte **snímku**.
3. V okně snímku, klikněte na tlačítko **vytvořit**.
4. Zadejte **název** pro snímku.
5. Vyberte existující [skupinu prostředků](../../azure-resource-manager/resource-group-overview.md#resource-groups) nebo zadejte název nové skupiny prostředků. 
6. Vyberte umístění datového centra Azure.  
7. Pro **zdrojový disk**, vyberte spravované Disk snímek.
8. Vyberte **typ účtu** používat k uložení snímku. Doporučujeme, abyste **Standard_LRS** Pokud to uložená na vysokou provádění disku potřebujete.
9. Klikněte na možnost **Vytvořit**.


## <a name="next-steps"></a>Další postup

 Vytvoření virtuálního počítače ze snímku vytvořením se spravovaným diskem ze snímku a potom nový spravované disk jako disk operačního systému se připojuje. Další informace najdete v tématu [vytvoření virtuálního počítače ze snímku](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) skriptu.

