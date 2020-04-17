---
title: Jak označit virtuální počítač Azure Linux
description: Přečtěte si o označování virtuálního počítače Azure Linux vytvořeného v Azure pomocí modelu nasazení Správce prostředků.
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
ms.openlocfilehash: fd4a93f4c0b2f052fe5c9890bee01e5da0dcead2
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460949"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Jak označit virtuální počítač SIP v Azure
Tento článek popisuje různé způsoby označení virtuálního počítače Linux u Azure prostřednictvím modelu nasazení Správce prostředků. Značky jsou uživatelem definované dvojice klíč/hodnota, které lze umístit přímo na prostředek nebo skupinu prostředků. Azure aktuálně podporuje až 50 značek na prostředek a skupinu prostředků. Značky mohou být umístěny na prostředek v době vytvoření nebo přidány do existujícího prostředku. Upozorňujeme, že značky jsou podporovány pro prostředky vytvořené pouze pomocí modelu nasazení Správce prostředků.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Označování pomocí azure cli

Chcete-li začít, budete potřebovat nejnovější [Azure CLI](/cli/azure/install-azure-cli) nainstalován a přihlášen k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index#az-login).

Pomocí tohoto příkazu můžete zobrazit všechny vlastnosti daného virtuálního počítače, včetně značek:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Chcete-li přidat novou značku virtuálního počítače prostřednictvím `azure vm update` rozhraní příkazu Azure CLI, můžete použít příkaz spolu s parametrem značky **--set**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Chcete-li odebrat značky, můžete `azure vm update` použít parametr **--remove** v příkazu.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Teď, když jsme použili značky pro naše prostředky Azure CLI a portálu, podívejme se na podrobnosti o využití zobrazíte značky na fakturačním portálu.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Další kroky
* Další informace o označování prostředků Azure najdete v [tématu Přehled Správce prostředků Azure][Azure Resource Manager Overview] a použití značek k uspořádání prostředků [Azure][Using Tags to organize your Azure Resources].
* Informace o tom, jak vám značky můžou pomoct se správou využití prostředků Azure, najdete [v tématu Principy účtu Azure][Understanding your Azure Bill] a získání [přehledu o spotřebě prostředků Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
