---
title: Kurz – vytváření vlastních imagí virtuálních počítačů pomocí Azure CLI
description: V tomto kurzu zjistíte, jak pomocí Azure CLI vytvořit vlastní image virtuálního počítače v Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: dc7b395d46fd28cde9ccbbda8a8a55447efa61c9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81460048"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Kurz: Vytvoření vlastní image virtuálního počítače Azure pomocí Azure CLI

Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spouštění konfigurací, jako jsou předběžné načítání aplikací, konfigurace aplikací a další konfigurace operačního systému. V tomto kurzu vytvoříte vlastní image virtuálního počítače Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Zrušení zřízení a generalizace virtuálních počítačů
> * Vytvoření vlastní image
> * Vytvoření virtuálního počítače z vlastní image
> * Výpis všech imagí v předplatném
> * Odstranění image

V tomto kurzu se používá CLI v rámci [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), který se průběžně aktualizuje na nejnovější verzi. Chcete-li otevřít Cloud Shell, vyberte možnost **vyzkoušet** v horní části libovolného bloku kódu.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Před zahájením

Následující postup podrobně popisuje přeměnu existujícího virtuálního počítače na opětovně použitelnou vlastní image, pomocí které můžete vytvářet nové instance virtuálních počítačů.

K dokončení příkladu v tomto kurzu potřebujete existující virtuální počítač. V případě potřeby může tento [ukázkový skript](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) vytvořit jednu za vás. V průběhu kurzu nahraďte na příslušných místech názvy skupiny prostředků a virtuálních počítačů.

## <a name="create-a-custom-image"></a>Vytvoření vlastní image

Pokud chcete vytvořit image virtuálního počítače, musíte virtuální počítač připravit tak, že zrušíte jeho zřízení, uvolníte ho a pak označíte zdrojový virtuální počítač za generalizovaný. Jakmile bude virtuální počítač připravený, můžete vytvořit image.

### <a name="deprovision-the-vm"></a>Zrušení zřízení virtuálního počítače 

Zrušením zřízení dojde ke generalizaci virtuálního počítače tím, že se odeberou informace specifické pro počítač. Díky této generalizaci je možné z jedné image nasazovat více virtuálních počítačů. Během rušení zřízení se název hostitele resetuje na *localhost.localdomain*. Odstraní se také klíče hostitele SSH, konfigurace názvového serveru, kořenové heslo a zapůjčení DHCP uložená v mezipaměti.

> [!WARNING]
> Zrušení zřízení a označení virtuálního počítače jako zobecněného způsobí, že zdrojový virtuální počítač nebude použitelný a nemůže být restartován. 

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

Abyste mohli vytvořit image, virtuální počítač musí být uvolněný. Uvolněte virtuální počítač pomocí příkazu [az vm deallocate](/cli//azure/vm). 
   
```azurecli-interactive 
az vm deallocate --resource-group myResourceGroup --name myVM
```

Nakonec pomocí příkazu [az vm generalize](/cli//azure/vm) nastavte stav virtuálního počítače na generalizovaný, aby platforma Azure věděla, že byl virtuální počítač generalizovaný. Image můžete vytvořit pouze z generalizovaného počítače.
   
```azurecli-interactive 
az vm generalize --resource-group myResourceGroup --name myVM
```

### <a name="create-the-image"></a>Vytvoření image

Teď můžete vytvořit image virtuálního počítače pomocí příkazu [az image create](/cli//azure/image). Následující příklad vytvoří image *myImage* z virtuálního počítače *myVM*.
   
```azurecli-interactive 
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>Vytvoření virtuálních počítačů z image

Když teď máte image, můžete vytvořit jeden nebo více nových virtuálních počítačů pomocí příkazu [az vm create](/cli/azure/vm). Následující příklad vytvoří virtuální počítač *myVMfromImage* z image *myImage*.

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Doporučujeme omezit počet souběžných nasazení na 20 virtuálních počítačů z jedné image. Pokud plánujete rozsáhlé souběžné nasazení více než 20 virtuálních počítačů ze stejné vlastní image, měli byste použít [sdílenou galerii imagí](shared-image-galleries.md) s více replikami imagí. 

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
> [Vytvořte virtuální počítače s vysokou dostupností](tutorial-availability-sets.md).

