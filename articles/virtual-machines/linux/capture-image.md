---
title: Zachycení image virtuálního počítače s Linuxem v Azure pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Zachycení image virtuálního počítače Azure k používání pro hromadné nasazení pomocí rozhraní příkazového řádku Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 5022d765b5dfa4f1f973b7fb4370d5314bb887b8
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731933"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Jak vytvořit image virtuálního počítače nebo virtuálního pevného disku

<!-- generalize, image - extended version of the tutorial-->

Chcete-li vytvořit více kopií virtuálního počítače (VM) pro použití v Azure, zachycení image virtuálního počítače nebo virtuálního pevného disku operačního systému. Vytvoření image pro nasazení, budete muset odebrat informace o osobní účet. V následujících krocích zrušení zřízení existujícího virtuálního počítače, jeho uvolnění a vytvořit bitovou kopii. Tento obrázek můžete použít k vytvoření virtuálních počítačů v libovolné skupině prostředků v rámci vašeho předplatného.

Vytvořte kopii existujícího virtuálního počítače s Linuxem pro zálohování nebo pro ladění k nahrání specializovaného VHD z virtuálního počítače v místním systému Linux, najdete v článku [nahrání a vytvoření virtuálního počítače s Linuxem z vlastní image disku](upload-vhd.md).  

Můžete také použít **Packeru** vytvořte vlastní konfiguraci. Další informace najdete v tématu [jak vytvořit Linuxové Image virtuálních počítačů v Azure pomocí Packeru](build-image-with-packer.md).

Před vytvořením image budete potřebovat tyto položky:

* Virtuálního počítače Azure vytvořené v modelu nasazení Resource Manager, že používá spravované disky. Pokud jste ještě nevytvořili virtuálního počítače s Linuxem, můžete použít [portál](quick-create-portal.md), [rozhraní příkazového řádku Azure](quick-create-cli.md), nebo [šablon Resource Manageru](create-ssh-secured-vm-from-template.md). Konfigurace virtuálního počítače podle potřeby. Například [datových disků](add-disk.md), aktualizace a instalovat aplikace. 

* Nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) nainstalovaný a být přihlášení k účtu Azure pomocí [az login](/cli/azure/reference-index#az-login).

## <a name="quick-commands"></a>Rychlé příkazy

Zjednodušená verze tohoto článku a testování, vaše rozhodnutí vyzkoušet nebo získat informace o virtuálních počítačích v Azure, najdete v části [vytvořit vlastní image virtuálního počítače Azure pomocí rozhraní příkazového řádku](tutorial-custom-images.md).


## <a name="step-1-deprovision-the-vm"></a>Krok 1: Zrušení zřízení virtuálního počítače
Nejprve je budete zrušení zřízení virtuálního počítače pomocí agenta virtuálního počítače Azure se odstranit soubory specifické pro počítač a data. Použití `waagent` příkazů `-deprovision+user` parametru u vašeho zdrojového virtuálního počítače s Linuxem. Další informace najdete v [uživatelské příručce agenta Azure Linux](../extensions/agent-linux.md).

1. Připojte se k virtuálním počítačům s Linuxem pomocí klienta SSH.
2. V okně SSH zadejte následující příkaz:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Tento příkaz spusťte pouze na virtuálním počítači, kterou budete zachycovat jako obrázek. Tento příkaz nezaručuje, že na obrázku se vymaže všechny citlivých informací nebo je vhodný pro distribuci. `+user` Parametr také odebere posledního zřízeného uživatelského účtu. Chcete-li zachovat přihlašovací údaje uživatelského účtu ve virtuálním počítači, použijte pouze `-deprovision`.
 
3. Zadejte **y** pokračujte. Můžete přidat `-force` parametr, aby tento krok potvrzení.
4. Po dokončení příkazu zadejte **ukončit** zavřete klienta SSH.

## <a name="step-2-create-vm-image"></a>Krok 2: Vytvoření image virtuálního počítače
Pomocí Azure CLI označit virtuální počítač za generalizovaný a zachycení bitové kopie. V následujících příkladech nahraďte ukázkové názvy parametrů s vlastními hodnotami. Zahrnout názvy parametrů příklad *myResourceGroup*, *myVnet*, a *myVM*.

1. Zrušit přidělení virtuálního počítače, zřízení s [az vm deallocate](/cli/azure/vm). V následujícím příkladu se uvolní virtuální počítač s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*.
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. Označit virtuální počítač za generalizovaný s [az vm generalize](/cli/azure/vm). Následující příklad označuje virtuální počítač s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup* za generalizovaný.
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. Vytvoření bitové kopie prostředků virtuálního počítače s [az image vytvořit](/cli/azure/image#az-image-create). Následující příklad vytvoří image *myImage* ve skupině prostředků s názvem *myResourceGroup* pomocí prostředku virtuálního počítače s názvem *myVM*.
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > Image se vytvoří ve stejné skupině prostředků jako zdrojový virtuální počítač. Virtuální počítače můžete vytvořit v libovolné skupině prostředků v rámci svého předplatného z této image. Z hlediska správy můžete chtít vytvořit konkrétní skupině prostředků pro prostředky virtuálních počítačů a obrázky.
   >
   > Pokud chcete ukládat image v zóně odolná úložiště, budete muset vytvořit v oblasti, která podporuje [zóny dostupnosti](../../availability-zones/az-overview.md) a zahrnout `--zone-resilient true` parametru.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Krok 3: Vytvoření virtuálního počítače ze zaznamenané bitové kopie
Vytvoření virtuálního počítače pomocí bitové kopie, které jste vytvořili pomocí [az vm vytvořit](/cli/azure/vm). Následující příklad vytvoří virtuální počítač s názvem *myVMDeployed* z image s názvem *myImage*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Vytváří se virtuální počítač v jiné skupině prostředků 

Můžete vytvořit virtuální počítače z image v libovolné skupině prostředků v rámci vašeho předplatného. Vytvoření virtuálního počítače v jiné skupině prostředků než image, zadejte úplné ID prostředku do bitové kopie. Použití [az image list](/cli/azure/image#az-image-list) Chcete-li zobrazit seznam imagí. Výstup se podobá následujícímu příkladu.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

Následující příklad používá [az vm vytvořit](/cli/azure/vm#az-vm-create) vytvořit virtuální počítač v jiné skupině prostředků než zdrojového obrázku tak, že zadáte ID bitové kopie prostředku.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Krok 4: Ověření nasazení

Připojte přes SSH k virtuálnímu počítači, který jste vytvořili pro ověření nasazení a začít používat nový virtuální počítač. Připojení přes protokol SSH, najít IP adresu nebo plně kvalifikovaný název domény virtuálního počítače pomocí [az vm show](/cli/azure/vm#az-vm-show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Další postup
Vytvoření několika virtuálních počítačů z vaší zdrojové imagi virtuálního počítače. Změny bitové kopie: 

- Vytvoření virtuálního počítače z image.
- Ujistěte se, všech aktualizací nebo změny konfigurace.
- Postupujte podle kroků znovu pro zrušení zřízení, uvolnit, generalizace a vytvořit bitovou kopii.
- Pomocí této nové image pro budoucí nasazení. Můžete odstranit původní bitové kopie.

Další informace týkající se správy virtuálních počítačů pomocí rozhraní příkazového řádku najdete v tématu [rozhraní příkazového řádku Azure](/cli/azure).
