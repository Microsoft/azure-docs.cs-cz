---
title: Jak označit virtuálního počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Přečtěte si o označování virtuálního počítače s Linuxem Azure vytvořené v Azure s využitím modelu nasazení Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: c26019bad63a904f8ebd1241d2b58923d786e181
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38696748"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Jak označit virtuální počítač s Linuxem v Azure
Tento článek popisuje různé způsoby, jak označit virtuální počítač s Linuxem v Azure prostřednictvím modelu nasazení Resource Manager. Značky jsou páry klíč/hodnota definovaná uživatelem, které je možné použít přímo na prostředek nebo skupinu prostředků. Azure v současné době podporuje až 15 značek na prostředek a skupinu prostředků. Značky může být umístěné na zdroje v době vytvoření nebo přidat do existujícího prostředku. Mějte prosím na paměti, značky jsou podporované pro prostředky vytvořené prostřednictvím modelu nasazení Resource Manager pouze.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Označování pomocí Azure CLI
Pokud chcete začít, je třeba nejnovější [příkazového řádku Azure CLI 2.0](/cli/azure/install-azure-cli) nainstalovaný a přihlášení k účtu Azure pomocí [az login](/cli/azure/reference-index#az-login).

Zobrazit všechny vlastnosti pro daný virtuální počítač, včetně značky, pomocí tohoto příkazu:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Chcete-li přidat novou značku virtuálních počítačů přes rozhraní příkazového řádku Azure, můžete použít `azure vm update` příkaz spolu s parametr tag **– nastavte**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Chcete-li odebrat značky, můžete použít **– odebrat** parametr v `azure vm update` příkazu.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Teď, když jsme použili značek k prostředkům, naše rozhraní příkazového řádku Azure a na portálu, Pojďme se podívat na podrobnosti o použití chcete zobrazit značky na fakturačním portálu.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Další postup
* Další informace o označování prostředků Azure najdete v tématu [přehled Azure Resource Manageru] [ Azure Resource Manager Overview] a [pomocí značek k uspořádání prostředků Azure] [ Using Tags to organize your Azure Resources].
* Jak značky vám pomůže se správou vašeho využití prostředků Azure najdete v tématu [vysvětlení vašeho vyúčtování Azure] [ Understanding your Azure Bill] a [získání přehledů o spotřebě prostředků Microsoft Azure] [Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
