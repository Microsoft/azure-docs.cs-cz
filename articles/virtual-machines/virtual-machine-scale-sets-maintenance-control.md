---
title: Přehled řízení údržby pro upgrady imagí operačního systému na Azure Virtual Machine Scale Sets
description: Naučte se řídit, kdy se mají automatické upgrady imagí operačního systému zavádět do služby Azure Virtual Machine Scale Sets pomocí řízení údržby.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: 55cbc5db534dd1b05f91a24e0104b1f2dc110547
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90532604"
---
# <a name="preview-maintenance-control-for-azure-virtual-machine-scale-sets"></a>Preview: řízení údržby pro Azure Virtual Machine Scale Sets 

Spravujte [automatické upgrady bitových kopií operačního systému](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) pro vaše služby Virtual Machine Scale Sets pomocí řízení údržby.

Řízení údržby vám umožní určit, kdy se mají na disky s operačním systémem v sadě škálování virtuálních počítačů instalovat aktualizace pomocí jednoduššího a více předvídatelných zkušeností. 

Konfigurace údržby pracují v rámci předplatných a skupin prostředků.

Celý pracovní postup se skládá z těchto kroků: 
- Vytvořte konfiguraci údržby.
- Přidružte sadu škálování virtuálního počítače ke konfiguraci údržby.
- Povolte automatické upgrady operačního systému.

> [!IMPORTANT]
> Řízení údržby pro upgrady imagí operačního systému v Azure Virtual Machine Scale Sets je momentálně v Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="limitations"></a>Omezení

- Virtuální počítače musí být v sadě škálování.
- Uživatel musí mít přístup k **přispěvateli prostředků** .
- Doba trvání údržby musí být v konfiguraci údržby 5 hodin nebo i déle.
- Opakování údržby musí být v konfiguraci údržby nastaveno na ' Day '


## <a name="management-options"></a>Možnosti správy

Konfigurace údržby můžete vytvořit a spravovat pomocí kterékoli z následujících možností:

- [Azure PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)


## <a name="next-steps"></a>Další kroky

Naučte se řídit, kdy se v Azure Virtual Machine Scale Sets používá údržba pomocí řízení údržby a PowerShellu.

> [!div class="nextstepaction"]
> [Řízení údržby sady škálování virtuálního počítače pomocí PowerShellu](virtual-machine-scale-sets-maintenance-control-powershell.md)
