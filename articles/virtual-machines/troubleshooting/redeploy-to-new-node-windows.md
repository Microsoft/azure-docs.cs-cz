---
title: Opětovné nasazení virtuálních počítačů s Windows v Azure | Dokumenty společnosti Microsoft
description: Jak znovu nasadit virtuální počítače Windows v Azure ke zmírnění problémů s připojením RDP.
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
ms.openlocfilehash: 36af0eeb43fb209ed65f950576f2dc9e97ec3633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058625"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Opětovné nasazení virtuálního počítače s Windows na nový uzel Azure
Pokud jste se potýkali s potížemi při řešení připojení ke vzdálené ploše (RDP) nebo přístupu k aplikacím k virtuálnímu počítači Azure založenému na Windows, může vám pomoct opětovné nasazení virtuálního počítače. Když znovu nasadíte virtuální počítač, Azure virtuální počítač vypne, přesune virtuální počítač do nového uzlu v rámci infrastruktury Azure a pak ho znovu zapne a zachová všechny možnosti konfigurace a přidružené prostředky. Tento článek ukazuje, jak znovu nasadit virtuální počítač pomocí Azure PowerShellu nebo portálu Azure.

> [!NOTE]
> Po opětovném nasazení virtuálního počítače se ztratí dočasný disk a budou aktualizovány dynamické ADRESY IP přidružené k rozhraní virtuální sítě. 


## <a name="using-azure-powershell"></a>Použití Azure Powershell
Ujistěte se, že máte v počítači nainstalovanou nejnovější Azure PowerShell 1.x. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

Následující příklad nasazuje `myVM` virtuální hod `myResourceGroup`pojmenovaný ve skupině prostředků s názvem :

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Další kroky
Pokud máte problémy s připojením k virtuálnímu počítači, můžete najít konkrétní nápovědu k [řešení potíží s připojením RDP](troubleshoot-rdp-connection.md) nebo [podrobným krokům řešení potíží s RDP](detailed-troubleshoot-rdp.md). Pokud nemáte přístup k aplikaci spuštěné na vašem virtuálním počítači, můžete si také přečíst [problémy s řešením potíží s aplikací](../windows/troubleshoot-app-connection.md).

