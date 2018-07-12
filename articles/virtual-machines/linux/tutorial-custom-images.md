---
title: Kurz – Vytváření vlastních imagí virtuálních počítačů pomocí Azure CLI | Microsoft Docs
description: V tomto kurzu zjistíte, jak pomocí Azure CLI 2.0 vytvořit a spravovat vlastní image virtuálního počítače v Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 766e247775e61d7427b658b66948aa6699a7241a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38705924"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli-20"></a>Kurz: Vytvoření vlastní image virtuálního počítače Azure pomocí Azure CLI 2.0

Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spouštění konfigurací, jako jsou předběžné načítání aplikací, konfigurace aplikací a další konfigurace operačního systému. V tomto kurzu vytvoříte vlastní image virtuálního počítače Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Zrušení zřízení a generalizace virtuálních počítačů
> * Vytvoření vlastní image
> * Vytvoření virtuálního počítače z vlastní image
> * Výpis všech imagí v předplatném
> * Odstranění image

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte pro tento kurz mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Než začnete

Následující postup podrobně popisuje přeměnu existujícího virtuálního počítače na opětovně použitelnou vlastní image, pomocí které můžete vytvářet nové instance virtuálních počítačů.

K dokončení příkladu v tomto kurzu potřebujete existující virtuální počítač. V případě potřeby si ho můžete nechat vytvořit pomocí tohoto [ukázkového skriptu](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md). V průběhu kurzu nahraďte na příslušných místech názvy skupiny prostředků a virtuálních počítačů.

## <a name="create-a-custom-image"></a>Vytvoření vlastní image

Pokud chcete vytvořit image virtuálního počítače, musíte virtuální počítač připravit tak, že zrušíte jeho zřízení, uvolníte ho a pak označíte zdrojový virtuální počítač za generalizovaný. Jakmile bude virtuální počítač připravený, můžete vytvořit image.

### <a name="deprovision-the-vm"></a>Zrušení zřízení virtuálního počítače 

Zrušením zřízení dojde ke generalizaci virtuálního počítače tím, že se odeberou informace specifické pro počítač. Díky této generalizaci je možné z jedné image nasazovat více virtuálních počítačů. Během rušení zřízení se název hostitele resetuje na *localhost.localdomain*. Odstraní se také klíče hostitele SSH, konfigurace názvového serveru, kořenové heslo a zapůjčení DHCP uložená v mezipaměti.

Ke zrušení zřízení virtuálního počítače použijte agenta virtuálního počítače Azure (waagent). Agent virtuálního počítače Azure je nainstalovaný na virtuálním počítači a spravuje zřizování a interakci s kontrolerem prostředků infrastruktury Azure. Další informace najdete v [uživatelské příručce agenta Azure Linux](../extensions/agent-linux.md).

Připojte se ke svému virtuálnímu počítači pomocí SSH a spusťte příkaz pro zrušení zřízení virtuálního počítače. Argument `+user` zajistí také odstranění posledního zřízeného uživatelského účtu a všech přidružených dat. Příklad IP adresy nahraďte veřejnou IP adresou vašeho virtuálního počítače.

Připojte se k virtuálnímu počítači přes SSH.
```bash
ssh azureuser@52.174.34.95
```
Zrušte zřízení virtuálního počítače.

```bash
sudo waagent -deprovision+user -force
```
Ukončete relaci SSH.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Uvolnění virtuálního počítače a jeho označení za generalizovaný

Abyste mohli vytvořit image, virtuální počítač musí být uvolněný. Uvolněte virtuální počítač pomocí příkazu [az vm deallocate](/cli//azure/vm#deallocate). 
   
```azurecli-interactive 
az vm deallocate --resource-group myResourceGroup --name myVM
```

Nakonec pomocí příkazu [az vm generalize](/cli//azure/vm#generalize) nastavte stav virtuálního počítače na generalizovaný, aby platforma Azure věděla, že byl virtuální počítač generalizovaný. Image můžete vytvořit pouze z generalizovaného počítače.
   
```azurecli-interactive 
az vm generalize --resource-group myResourceGroup --name myVM
```

### <a name="create-the-image"></a>Vytvoření image

Teď můžete vytvořit image virtuálního počítače pomocí příkazu [az image create](/cli//azure/image#create). Následující příklad vytvoří image *myImage* z virtuálního počítače *myVM*.
   
```azurecli-interactive 
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>Vytvoření virtuálních počítačů z image

Když teď máte image, můžete vytvořit jeden nebo více nových virtuálních počítačů pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač *myVMfromImage* z image *myImage*.

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

## <a name="image-management"></a>Správa imagí 

Tady je několik příkladů běžných úloh správy imagí a postup pro jejich provedení pomocí Azure CLI.

Výpis všech imagí podle názvu ve formátu tabulky.

```azurecli-interactive 
az image list \
    --resource-group myResourceGroup
```

Odstranění image. Tento příklad odstraní image *myOldImage* ze skupiny prostředků *myResourceGroup*.

```azurecli-interactive 
az image delete \
    --name myOldImage \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili vlastní image virtuálního počítače. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Zrušení zřízení a generalizace virtuálních počítačů
> * Vytvoření vlastní image
> * Vytvoření virtuálního počítače z vlastní image
> * Výpis všech imagí v předplatném
> * Odstranění image

Přejděte k dalšímu kurzu, kde se seznámíte s vysoce dostupnými virtuálními počítači.

> [!div class="nextstepaction"]
> [Vytváření vysoce dostupných virtuálních počítačů](tutorial-availability-sets.md)

