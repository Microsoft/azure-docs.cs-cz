---
title: Opětovné nasazení virtuálních počítačů Linuxu v Azure | Dokumenty společnosti Microsoft
description: Jak znovu nasadit virtuální počítače Linuxu v Azure, aby se zmírnily problémy s připojením SSH.
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
ms.openlocfilehash: 18e96f9463176b0fce04252492eea6dbede416c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531103"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Opětovné nasazení virtuálního počítače s Linuxem na nový uzel Azure
Pokud se potýkáte s potížemi při řešení problémů s SSH nebo přístupem k aplikacím linuxového virtuálního počítače (VM) v Azure, může vám pomoct opětovné nasazení virtuálního počítače. Když znovu nasadíte virtuální počítač, přesune virtuální počítač do nového uzlu v rámci infrastruktury Azure a pak ho znovu zapne. Všechny možnosti konfigurace a přidružené prostředky jsou zachovány. Tento článek ukazuje, jak znovu nasadit virtuální počítač pomocí Azure CLI nebo portálu Azure.

> [!NOTE]
> Po opětovném nasazení virtuálního počítače se ztratí dočasný disk a budou aktualizovány dynamické ADRESY IP přidružené k rozhraní virtuální sítě. 


## <a name="use-the-azure-cli"></a>Použití Azure CLI
Nainstalujte si nejnovější [azure cli](/cli/azure/install-az-cli2) a přihlaste se ke svému účtu Azure pomocí [az přihlášení](/cli/azure/reference-index).

Znovu nasadit virtuální počítač s [az vm znovu nasadit](/cli/azure/vm). Následující příklad znovu nasadí virtuální hod s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Použití klasického příkazového příkazového příkazu Azure

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Nainstalujte [si nejnovější klasické velné příkazové příkazové příkazové příkazové příkazy Azure](../../cli-install-nodejs.md) a přihlaste se ke svému účtu Azure. Ujistěte se, že jste`azure config mode arm`v režimu Správce prostředků ( ).

Následující příklad znovu nasadí virtuální hod s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

```console
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Další kroky
Pokud máte problémy s připojením k virtuálnímu počítači, najdete konkrétní nápovědu k [řešení potíží s připojením SSH](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [podrobným krokům řešení potíží SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Pokud nemáte přístup k aplikaci spuštěné na vašem virtuálním počítači, můžete si také přečíst [problémy s řešením potíží s aplikací](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


