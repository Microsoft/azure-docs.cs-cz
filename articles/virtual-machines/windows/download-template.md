---
title: Stažení šablony pro virtuální počítač Azure
description: Stáhněte si šablonu pro virtuální počítače pomocí portálu nebo PowerShellu.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: af6905f0ba62a9053e44134348721312ade6b9d7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085378"
---
# <a name="download-the-template-for-a-vm"></a>Stažení šablony pro virtuální počítač
Když vytvoříte virtuální počítač v Azure pomocí portálu nebo PowerShellu, automaticky se za vás vytvoří šablona Správce prostředků. Pomocí této šablony můžete rychle duplikovat nasazení. Šablona obsahuje informace o všech prostředcích ve skupině prostředků. Pro virtuální počítač to znamená, že šablona obsahuje vše, co se vytvoří na podporu virtuálního počítače v této skupině prostředků, včetně síťových prostředků.

## <a name="download-the-template-using-the-portal"></a>Stažení šablony pomocí portálu
1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Jedna z levé nabídky, vyberte **Virtuální počítače**.
3. Ze seznamu vyberte virtuální počítač.
4. Vyberte **Exportovat šablonu**.
5. V horní části nabídky vyberte **Stáhnout** a soubor ZIP uložte do místního počítače.
6. Otevřete soubor ZIP a extrahujte soubory do složky. Soubor ZIP obsahuje:
   
   * parameters.json
   * šablona.json

Soubor template.json je šablona.

## <a name="download-the-template-using-powershell"></a>Stažení šablony pomocí PowerShellu
Soubor šablony JSON můžete také stáhnout pomocí rutiny [Export-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) `-path` Parametr můžete použít k zadání názvu souboru a cesty pro soubor JSON. Tento příklad ukazuje, jak stáhnout šablonu pro skupinu prostředků s názvem **myResourceGroup** do složky **C:\users\public\downloads** v místním počítači.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Další kroky
Další informace o nasazení prostředků pomocí šablon najdete v [tématu Návod k šabloně Správce prostředků](../../azure-resource-manager/resource-manager-template-walkthrough.md).

