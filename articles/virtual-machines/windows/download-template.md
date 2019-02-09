---
title: Stažení šablony pro virtuální počítač Azure | Dokumentace Microsoftu
description: Stáhněte si templatefor virtuálního počítače, které vám pomůžou s automatizací nasazení v modelu nasazení Resource Manager
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
ms.openlocfilehash: 574227e010a37340ce7248d2e4657f6a3f231d0a
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984524"
---
# <a name="download-the-template-for-a-vm"></a>Stažení šablony pro virtuální počítač
Při vytváření virtuálního počítače v Azure pomocí portálu nebo prostředí PowerShell, šablony Resource Manageru se automaticky vytvoří za vás. Tuto šablonu můžete použít k rychlému duplicitní nasazení. Šablona obsahuje informace o všech prostředků ve skupině prostředků. Pro virtuální počítač to znamená, že šablona obsahuje všechno, co se vytvoření efektivnějších virtuálního počítače v příslušné skupině prostředků, včetně síťových prostředků.

## <a name="download-the-template-using-the-portal"></a>Stáhněte si šablonu pomocí portálu
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Jeden v levé nabídce vyberte **virtuálních počítačů**.
3. Ze seznamu vyberte virtuální počítač.
4. Vyberte **automatizační skript**.
5. Vyberte **Stáhnout** v nabídce v horní části a uložte soubor .zip na místním počítači.
6. Otevřete soubor ZIP a rozbalit soubory do složky. Soubor ZIP obsahuje:
   
   * deploy.ps1
   * deploy.sh 
   * deployer.rb
   * DeploymentHelper.cs
   * parameters.json
   * template.json

Soubor template.json je šablona.

## <a name="download-the-template-using-powershell"></a>Stáhněte si šablonu pomocí Powershellu
Můžete také stáhnout pomocí souboru .json šablony [Export AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) rutiny. Můžete použít `-path` parametr zadejte cestu a název souboru pro soubor .json. Tento příklad ukazuje, jak stáhnout šablonu pro skupinu prostředků s názvem **myResourceGroup** k **C:\users\public\downloads** složky na místním počítači.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Další postup
Další informace o nasazení prostředků pomocí šablon najdete v tématu [názorný Průvodce šablonou Resource Manageru](../../azure-resource-manager/resource-manager-template-walkthrough.md).

