---
title: Opětovné nasazení virtuálních počítačů s Windows v Azure | Microsoft Docs
description: Postup opětovného nasazení virtuálních počítačů s Windows v Azure za účelem zmírnění problémů s připojením RDP
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: afbea39a080e1dd768a14d6e0eacda1bad23c5a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074417"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Opětovné nasazení virtuálního počítače s Windows na nový uzel Azure
Pokud jste se setkali s řešením potíží s připojením vzdálené plochy (RDP) nebo aplikací k virtuálnímu počítači Azure se systémem Windows, může vám tento virtuální počítač znovu nasadit. Když znovu nasadíte virtuální počítač, Azure vypne virtuální počítač, přesune virtuální počítač do nového uzlu v rámci infrastruktury Azure a pak ho znovu zachová a uchová všechny možnosti konfigurace a přidružené prostředky. V tomto článku se dozvíte, jak nasadit virtuální počítač pomocí Azure PowerShell nebo Azure Portal.

> [!NOTE]
> Po opětovném nasazení virtuálního počítače dojde ke ztrátě dočasného disku a dynamické IP adresy přidružené k rozhraní virtuální sítě se aktualizují. 


## <a name="using-azure-powershell"></a>Použití Azure Powershell
Ujistěte se, že je na vašem počítači nainstalovaný nejnovější Azure PowerShell 1. x. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/).

Následující příklad nasadí virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup` :

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Další kroky
Pokud máte problémy s připojením k VIRTUÁLNÍmu počítači, můžete najít konkrétní pomoc týkající se [řešení potíží s připojením RDP](troubleshoot-rdp-connection.md) nebo [podrobného postupu pro řešení potíží s](detailed-troubleshoot-rdp.md)protokolem RDP. Pokud nemůžete získat přístup k aplikaci běžící na vašem VIRTUÁLNÍm počítači, můžete si také přečíst [problémy s odstraňováním potíží s aplikacemi](./troubleshoot-app-connection.md).
