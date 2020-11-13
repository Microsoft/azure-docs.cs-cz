---
title: Jak označit virtuální počítač Azure pomocí rozhraní příkazového řádku
description: Přečtěte si informace o označení virtuálního počítače pomocí rozhraní příkazového řádku Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 48f906bf0025bda03df226f32db1a0d6afdb9cee
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594966"
---
# <a name="how-to-tag-a-vm-using-the-cli"></a>Označení virtuálního počítače pomocí rozhraní příkazového řádku

Tento článek popisuje, jak označit virtuální počítač pomocí Azure CLI. Značky jsou páry klíč/hodnota definované uživatelem, které lze umístit přímo do prostředku nebo skupiny prostředků. Azure v současné době podporuje až 50 značek na jeden prostředek a skupinu prostředků. Značky lze umístit na prostředek v době vytvoření nebo přidání do existujícího prostředku. Můžete také označit virtuální počítač pomocí [prostředí Azure PowerShell](tag-powershell.md).


Můžete zobrazit všechny vlastnosti daného virtuálního počítače, včetně značek, pomocí `az vm show` .

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Pokud chcete přidat novou značku virtuálního počítače přes rozhraní příkazového řádku Azure, můžete použít `azure vm update` příkaz spolu s parametrem značky `--set` :

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Chcete-li odebrat značky, můžete použít `--remove` parametr v `azure vm update` příkazu.

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

Teď, když jsme použili značky pro naše prostředky rozhraní příkazového řádku Azure CLI a portál, se podíváme na podrobnosti o využití a zobrazíme značky na fakturačním portálu.


**Další kroky**

- Další informace o označování vašich prostředků Azure najdete v tématu [přehled Azure Resource Manager přehledu](../azure-resource-manager/management/overview.md) a [použití značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md).
- Pokud chcete zjistit, jak vám značky pomůžou spravovat vaše používání prostředků Azure, přečtěte si článek [Princip fakturace Azure](../cost-management-billing/understand/review-individual-bill.md) a [Získejte přehled o využití prostředků Microsoft Azure](../cost-management-billing/manage/usage-rate-card-overview.md).
