---
title: Stažení šablony pro virtuální počítač Azure | Microsoft Docs
description: Stáhněte si templatefor virtuální počítač, který vám umožní automatizovat nasazení v modelu nasazení Správce prostředků.
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
ms.openlocfilehash: 37e833e5ba2fcbc9fcfe0a27d5b607a5c6cdd55b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079532"
---
# <a name="download-the-template-for-a-vm"></a>Stažení šablony pro virtuální počítač
Když vytvoříte virtuální počítač v Azure pomocí portálu nebo PowerShellu, automaticky se vytvoří šablona Správce prostředků. Tuto šablonu můžete použít k rychlému duplikování nasazení. Šablona obsahuje informace o všech prostředcích ve skupině prostředků. V případě virtuálního počítače to znamená, že šablona obsahuje vše, co je vytvořeno v rámci podpory virtuálního počítače v dané skupině prostředků, včetně síťových prostředků.

## <a name="download-the-template-using-the-portal"></a>Stažení šablony pomocí portálu
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo vyberte **Virtual Machines**.
3. Ze seznamu vyberte virtuální počítač.
4. Vyberte **Exportovat šablonu**.
5. V nabídce v horní části vyberte **Stáhnout** a uložte soubor. zip do svého místního počítače.
6. Otevřete soubor. zip a extrahujte soubory do složky. Soubor. zip obsahuje:
   
   * deploy.ps1
   * deploy.sh 
   * Deployer. RB
   * DeploymentHelper.cs
   * parameters.json
   * template.json

Soubor Template. JSON je šablona.

## <a name="download-the-template-using-powershell"></a>Stažení šablony pomocí PowerShellu
Soubor šablony. JSON můžete také stáhnout pomocí rutiny [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) . Pomocí `-path` parametru můžete zadat název souboru a cestu k souboru. JSON. Tento příklad ukazuje, jak stáhnout šablonu pro skupinu prostředků s názvem **myResourceGroup** do složky **C:\users\public\downloads** na místním počítači.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Další postup
Další informace o nasazení prostředků pomocí šablon naleznete v tématu [Správce prostředků návodu](../../azure-resource-manager/resource-manager-template-walkthrough.md)k šablonám.

