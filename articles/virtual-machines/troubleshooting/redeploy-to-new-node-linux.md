---
title: Opětovné nasazení Linux Virtual Machines v Azure | Microsoft Docs
description: Postup opětovného nasazení virtuálních počítačů se systémem Linux v Azure za účelem zmírnění problémů s připojením SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: d553fb6b2061f987e3e098ae47ebca9cd3f60984
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203401"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Opětovné nasazení virtuálního počítače s Linuxem na nový uzel Azure
Pokud se při řešení potíží s přístupem SSH nebo aplikace k virtuálnímu počítači s Linuxem (VM) v Azure setkáte s problémy, může vám tento virtuální počítač znovu nasadit. Když znovu nasadíte virtuální počítač, přesune virtuální počítač do nového uzlu v rámci infrastruktury Azure a pak ho znovu zavolá. Všechny možnosti konfigurace a přidružené prostředky jsou zachovány. V tomto článku se dozvíte, jak znovu nasadit virtuální počítač pomocí Azure CLI nebo Azure Portal.

> [!NOTE]
> Po opětovném nasazení virtuálního počítače dojde ke ztrátě dočasného disku a dynamické IP adresy přidružené k rozhraní virtuální sítě se aktualizují. 


## <a name="use-the-azure-cli"></a>Použití Azure CLI
Nainstalujte si nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a přihlaste se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index).

Znovu nasaďte virtuální počítač pomocí [AZ VM redeploy](/cli/azure/vm). Následující příklad znovu nasadí virtuální počítač s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Použití rozhraní příkazového řádku Azure Classic

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Nainstalujte si nejnovější rozhraní příkazového [řádku Azure Classic](/cli/azure/install-classic-cli) a přihlaste se ke svému účtu Azure. Ujistěte se, že jste v režimu Správce prostředků ( `azure config mode arm` ).

Následující příklad znovu nasadí virtuální počítač s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

```console
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Další kroky
Pokud máte problémy s připojením k VIRTUÁLNÍmu počítači, můžete najít konkrétní pomoc týkající se [řešení potíží s připojením SSH](troubleshoot-ssh-connection.md) nebo [podrobného postupu pro řešení potíží s SSH](detailed-troubleshoot-ssh-connection.md). Pokud nemůžete získat přístup k aplikaci běžící na vašem VIRTUÁLNÍm počítači, můžete si také přečíst [problémy s odstraňováním potíží s aplikacemi](troubleshoot-app-connection.md).
