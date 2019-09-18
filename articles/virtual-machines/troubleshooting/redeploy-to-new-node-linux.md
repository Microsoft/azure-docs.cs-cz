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
ms.openlocfilehash: d8096a14bf2abc0b06b7ab7c3d340a313b1cd24c
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057304"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Znovu nasadit virtuální počítač se systémem Linux do nového uzlu Azure
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
Nainstalujte si nejnovější rozhraní příkazového [řádku Azure Classic](../../cli-install-nodejs.md) a přihlaste se ke svému účtu Azure. Ujistěte se, že jste v režimu Správce prostředků (`azure config mode arm`).

Následující příklad znovu nasadí virtuální počítač s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Další postup
Pokud máte problémy s připojením k VIRTUÁLNÍmu počítači, můžete najít konkrétní pomoc týkající se [řešení potíží s připojením SSH](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [podrobného postupu pro řešení potíží s SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Pokud nemůžete získat přístup k aplikaci běžící na vašem VIRTUÁLNÍm počítači, můžete si také přečíst [problémy s odstraňováním potíží s aplikacemi](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


