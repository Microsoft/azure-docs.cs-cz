---
title: Postup označení se virtuální počítač Azure Linux | Microsoft Docs
description: Informace o označování se virtuální počítač Azure Linux vytvořené v Azure pomocí modelu nasazení Resource Manager.
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
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937886"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Postup označení virtuální počítač s Linuxem v Azure
Tento článek popisuje různé způsoby, jak označit virtuální počítač s Linuxem v Azure pomocí modelu nasazení Resource Manager. Značky jsou páry klíč – hodnota definovaný uživatelem, které mohou být umístěny přímo na prostředek nebo skupina zdrojů. Azure aktuálně podporuje až 15 značky pro každý prostředků a skupina prostředků. Značky mohou umístit na prostředku v době vytvoření nebo přidat do existující prostředek. Poznámka: značky jsou podporovány pro prostředky vytvořené pomocí modelu nasazení Resource Manager jenom.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Označování pomocí rozhraní příkazového řádku Azure
Chcete-li začít, je třeba nejnovější [Azure CLI 2.0](/cli/azure/install-azure-cli) nainstalován a přihlášení k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index#az-login).

Můžete zobrazit všechny vlastnosti pro daný virtuální počítač, včetně značky, použití tohoto příkazu:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Chcete-li přidat novou značku virtuálních počítačů prostřednictvím rozhraní příkazového řádku Azure, můžete použít `azure vm update` příkazu společně s parametrem značka **– nastavte**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Chcete-li odebrat značky, můžete použít **– odebrat** parametr ve `azure vm update` příkaz.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Teď, když jsme bylo použito značky naše prostředky Azure CLI a portálu, Podívejme se na podrobnosti využití zobrazíte značky v portálu fakturace.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Další postup
* Další informace o označování prostředků Azure, najdete v části [přehled Azure Resource Manageru] [ Azure Resource Manager Overview] a [pomocí značek k uspořádání prostředků Azure] [ Using Tags to organize your Azure Resources].
* Jak značky vám může pomoci spravovat používání prostředků Azure, najdete v tématu [pochopení vaše faktura Azure] [ Understanding your Azure Bill] a [proniknout do vaší spotřeby prostředků Microsoft Azure] [Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
