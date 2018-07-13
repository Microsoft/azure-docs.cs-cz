---
title: Znovu nasadit virtuální počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Jak znovu nasadit virtuální počítače s Linuxem v Azure a zmírnění problémů s připojením SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: 1bd13c35ed49aeaab1a4f4aa94c984dc28f6c111
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38308001"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Znovu nasadit virtuální počítač s Linuxem do nového uzlu Azure
Pokud čelí potíží při řešení potíží s SSH nebo přístup k aplikaci pro virtuální počítač s Linuxem (VM) v Azure, znovu se nasazuje virtuální počítač může pomoci. Při opětovném nasazování virtuálního počítače, přesune virtuální počítač do nového uzlu v rámci infrastruktury Azure a pak ji zpět zapne. Možnosti konfigurace a přidružené prostředky zůstanou zachovány. Tento článek ukazuje, jak znovu nasadit virtuální počítač pomocí webu Azure portal nebo rozhraní příkazového řádku Azure.

> [!NOTE]
> Po opětovném nasazování virtuálního počítače, dočasný disk je ztraceny a dynamické IP adresy přidružené k rozhraní virtuální sítě se aktualizují. 

Můžete znovu nasadit virtuální počítač pomocí jedné z následujících možností. Stačí vybrat jednu možnost k opětovnému nasazení vašeho virtuálního počítače:

- [Azure CLI 2.0](#azure-cli-20)
- [Azure CLI 1.0](#azure-cli-10)
- [Azure Portal](#using-azure-portal)

## <a name="use-the-azure-cli-20"></a>Použití Azure CLI 2.0
Nainstalujte nejnovější [příkazového řádku Azure CLI 2.0](/cli/azure/install-az-cli2) a přihlaste se k Azure pomocí účtu [az login](/cli/azure/reference-index#az_login).

Opětovné nasazení virtuálního počítače s [opětovné nasazení virtuálního počítače v rámci az](/cli/azure/vm#az_vm_redeploy). Následující příklad znovu nasadí virtuální počítač s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-cli-10"></a>Použití Azure CLI 1.0
Nainstalujte [nejnovější Azure CLI 1.0](../../cli-install-nodejs.md) a přihlaste se ke svému účtu Azure. Ujistěte se, že jste v režimu Resource Manageru (`azure config mode arm`).

Následující příklad znovu nasadí virtuální počítač s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Další postup
Pokud máte problémy s připojením k virtuálnímu počítači, můžete najít nápovědu na [řešení potíží s připojením SSH](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [podrobné kroky pro řešení potíží SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Pokud nelze získat přístup k aplikaci spuštěné na virtuálním počítači, můžete si také přečíst [řešení potíží s aplikací](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

