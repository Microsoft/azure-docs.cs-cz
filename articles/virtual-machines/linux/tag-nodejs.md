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
ms.openlocfilehash: 103784d97301313379b2fd336624e5cda591c5a6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Postup označení virtuální počítač s Linuxem v Azure
Tento článek popisuje různé způsoby, jak označit virtuální počítač s Linuxem v Azure pomocí modelu nasazení Resource Manager. Značky jsou páry klíč – hodnota definovaný uživatelem, které mohou být umístěny přímo na prostředek nebo skupina zdrojů. Azure aktuálně podporuje až 15 značky pro každý prostředků a skupina prostředků. Značky mohou umístit na prostředku v době vytvoření nebo přidat do existující prostředek. Poznámka: značky jsou podporovány pro prostředky vytvořené pomocí modelu nasazení Resource Manager jenom.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Označování pomocí rozhraní příkazového řádku Azure
Pokud chcete začít, [instalace a konfigurace rozhraní příkazového řádku Azure](../../xplat-cli-azure-resource-manager.md) a zkontrolujte, zda jsou v režimu Resource Manager (`azure config mode arm`).

Můžete zobrazit všechny vlastnosti pro daný virtuální počítač, včetně značky, použití tohoto příkazu:

        azure vm show -g MyResourceGroup -n MyTestVM

Chcete-li přidat novou značku virtuálních počítačů prostřednictvím rozhraní příkazového řádku Azure, můžete použít `azure vm set` příkazu společně s parametrem značka **-t**:

        azure vm set -g MyResourceGroup -n MyTestVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Chcete-li odebrat všechny značky, můžete použít **– T** parametr ve `azure vm set` příkaz.

        azure vm set – g MyResourceGroup –n MyTestVM -T


Teď, když jsme bylo použito značky naše prostředky Azure CLI a portálu, Podívejme se na podrobnosti využití zobrazíte značky v portálu fakturace.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Další postup
* Další informace o označování prostředků Azure, najdete v části [přehled Azure Resource Manageru] [ Azure Resource Manager Overview] a [pomocí značek k uspořádání prostředků Azure][Using Tags to organize your Azure Resources].
* Jak značky vám může pomoci spravovat používání prostředků Azure, najdete v tématu [pochopení vaše faktura Azure] [ Understanding your Azure Bill] a [proniknout do vaší spotřeby prostředků Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
