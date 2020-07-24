---
title: Jak označit virtuální počítač Azure Linux
description: Přečtěte si informace o označení virtuálního počítače Azure Linux vytvořeného v Azure pomocí modelu nasazení Správce prostředků.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: gwallace
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: 171b362cee2696375611d7967649889a74ca5f71
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87028844"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Označení virtuálního počítače se systémem Linux v Azure
Tento článek popisuje různé způsoby, jak označit virtuální počítač Linux v Azure pomocí modelu nasazení Správce prostředků. Značky jsou páry klíč/hodnota definované uživatelem, které lze umístit přímo do prostředku nebo skupiny prostředků. Azure v současné době podporuje až 50 značek na jeden prostředek a skupinu prostředků. Značky lze umístit na prostředek v době vytvoření nebo přidání do existujícího prostředku. Upozorňujeme, že značky se podporují jenom u prostředků vytvořených pomocí modelu nasazení Správce prostředků.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Označování pomocí Azure CLI

Chcete-li začít, potřebujete nainstalovat nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) a přihlásit se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index#az-login).

Všechny vlastnosti pro daný virtuální počítač, včetně značek, můžete zobrazit pomocí tohoto příkazu:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Pokud chcete přidat novou značku virtuálního počítače přes rozhraní příkazového řádku Azure, můžete použít `azure vm update` příkaz spolu s parametrem značky **--set**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Chcete-li odebrat značky, můžete použít parametr **--Remove** v `azure vm update` příkazu.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Teď, když jsme použili značky pro naše prostředky rozhraní příkazového řádku Azure CLI a portál, se podíváme na podrobnosti o využití a zobrazíme značky na fakturačním portálu.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Další kroky
* Další informace o označování vašich prostředků Azure najdete v tématu [přehled Azure Resource Manager přehledu][Azure Resource Manager Overview] a [použití značek k uspořádání prostředků Azure][Using Tags to organize your Azure Resources].
* Pokud chcete zjistit, jak vám značky pomůžou spravovat vaše používání prostředků Azure, přečtěte si článek [Princip fakturace Azure][Understanding your Azure Bill] a [Získejte přehled o využití prostředků Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/management/manage-resources-cli.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
