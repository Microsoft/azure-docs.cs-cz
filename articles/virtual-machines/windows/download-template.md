---
title: Stáhnout šablonu pro virtuální počítač Azure | Microsoft Docs
description: Stáhněte si templatefor virtuálního počítače můžete k automatizaci nasazení v modelu nasazení Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 93ed84cb146119c877c3a143c5f7af9ca8ba0656
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2017
ms.locfileid: "26055785"
---
# <a name="download-the-template-for-a-vm"></a>Stažení šablony pro virtuální počítač
Při vytváření virtuálního počítače v Azure pomocí portálu nebo prostředí PowerShell, je pro vás automaticky vytvoří šablonu Resource Manager. Tuto šablonu můžete použít k rychlé duplicitní nasazení. Šablona obsahuje informace o všechny prostředky ve skupině prostředků. Pro virtuální počítač to znamená, že šablona obsahuje všechno, která je vytvořena na podporu virtuálních počítačů v příslušné skupině prostředků, včetně síťových prostředků.

## <a name="download-the-template-using-the-portal"></a>Stažení šablony pomocí portálu
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).
2. Jeden v levé nabídce vyberte **virtuální počítače**.
3. Ze seznamu vyberte virtuální počítač.
4. Vyberte **skriptu pro automatizaci**.
5. Vyberte **Stáhnout** v nabídce v horní části a uložte soubor .zip do místního počítače.
6. Otevřete soubor .zip a rozbalte soubory do složky. Soubor ZIP obsahuje:
   
   * Deploy.ps1
   * Deploy.SH 
   * deployer.RB
   * DeploymentHelper.cs
   * Parameters.JSON tímto kódem
   * Template.JSON

Soubor template.json je šablona.

## <a name="download-the-template-using-powershell"></a>Stažení šablony pomocí prostředí PowerShell
Můžete také stáhnout pomocí soubor .json [Export-AzureRMResourceGroup](https://msdn.microsoft.com/library/mt715427.aspx) rutiny. Můžete použít `-path` parametru zadat název a cesta k souboru .json. Tento příklad ukazuje, jak stáhnout šablonu pro skupinu prostředků s názvem **myResourceGroup** k **C:\users\public\downloads** složky v místním počítači.

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Další kroky
Další informace o nasazení prostředků pomocí šablony najdete v tématu [názorný Průvodce šablonou Resource Manageru](../../azure-resource-manager/resource-manager-template-walkthrough.md).

