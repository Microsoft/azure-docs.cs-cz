---
title: Stažení šablony pro virtuální počítač Azure
description: Stáhněte si šablonu pro virtuální počítače, která vám pomůže s automatizací nasazení v modelu nasazení Správce prostředků
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: c73026515f0d7fde4e2f82838696700b1bb17c77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033547"
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

