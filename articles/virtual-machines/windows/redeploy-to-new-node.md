---
title: Opětovné nasazení Windows virtual machines v Azure | Dokumentace Microsoftu
description: Jak znovu nasadit virtuální počítače Windows v Azure a zmírnění problémů s připojením RDP.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: f0bda14634e6c8bea5800b9798086fc38279030a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38531498"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Opětovné nasazení Windows virtuálního počítače do nového uzlu Azure
Pokud mají byla připojena potíže je řešení potíží s Remote Desktop (RDP) připojení nebo aplikací přístup k systémem Windows virtuální počítač Azure (VM), znovu se nasazuje virtuální počítač může pomoct. Při opětovném nasazování virtuálního počítače, virtuální počítač přesune do nového uzlu v rámci infrastruktury Azure a potom využívá zpět na možnosti konfigurace a přidružených prostředků. Tento článek ukazuje, jak znovu nasadit virtuální počítač pomocí Azure Powershellu nebo na webu Azure portal.

> [!NOTE]
> Po opětovném nasazování virtuálního počítače, dočasný disk je ztraceny a dynamické IP adresy přidružené k rozhraní virtuální sítě se aktualizují. 


## <a name="using-azure-powershell"></a>Použití Azure Powershell
Ujistěte se, že máte nejnovější Azure PowerShell 1.x na vašem počítači nainstalovaný. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

Tento příklad nasadí virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`:

```powershell
Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```


[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Další postup
Pokud máte problémy s připojením k virtuálnímu počítači, můžete najít nápovědu na [řešení potíží s připojením RDP](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) nebo [podrobný postup řešení potíží s RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Pokud nelze získat přístup k aplikaci spuštěné na virtuálním počítači, můžete si také přečíst [řešení potíží s aplikací](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

