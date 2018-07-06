---
title: Zachycení image virtuálního počítače s Linuxem v Azure pomocí CLI 2.0 | Dokumentace Microsoftu
description: Zachycení image virtuálního počítače Azure pro hromadné nasazení pomocí rozhraní příkazového řádku Azure CLI 2.0.
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
ms.date: 03/22/2018
ms.author: cynthn
ms.openlocfilehash: ea202cad06130cfaaa134cad94ac08bede2f41a9
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867697"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Jak vytvořit image virtuálního počítače nebo virtuálního pevného disku

<!-- generalize, image - extended version of the tutorial-->

Pokud chcete vytvořit několik kopií virtuálního počítače (VM) pro použití v Azure, zachycení image virtuálního počítače nebo virtuální pevný disk operačního systému. Chcete-li vytvořit bitovou kopii, potřebujete odebrat informace o osobní účet, což zajišťuje bezpečnější nasadit více než jednou. V následujícím postupu zrušení zřízení existujícího virtuálního počítače uvolnit a vytvořit bitovou kopii. Tento obrázek můžete použít k vytvoření virtuálních počítačů v libovolné skupině prostředků v rámci vašeho předplatného.

Pokud chcete vytvořit kopii existujícího virtuálního počítače s Linuxem pro zálohování nebo pro ladění, nebo nahrání specializovaného VHD z virtuálního počítače v místním systému Linux, přečtěte si [nahrání a vytvoření virtuálního počítače s Linuxem z vlastní image disku](upload-vhd.md).  

Můžete také použít **Packeru** vytvořte vlastní konfiguraci. Další informace o použití Packeru, naleznete v tématu [jak vytvořit Linuxové Image virtuálních počítačů v Azure pomocí Packeru](build-image-with-packer.md).


## <a name="before-you-begin"></a>Než začnete
Ujistěte se, že splňujete následující požadavky:

* Je třeba virtuálního počítače Azure vytvořené v modelu nasazení Resource Manageru pomocí spravovaných disků. Pokud jste nevytvořili virtuální počítač s Linuxem, můžete použít [portál](quick-create-portal.md), [rozhraní příkazového řádku Azure](quick-create-cli.md), nebo [šablon Resource Manageru](create-ssh-secured-vm-from-template.md). Konfigurace virtuálního počítače podle potřeby. Například [datových disků](add-disk.md), aktualizace a instalovat aplikace. 

* Je taky potřeba mít na nejnovější verzi [příkazového řádku Azure CLI 2.0](/cli/azure/install-az-cli2) nainstalovaná a je zaznamenána účtu Azure pomocí [az login](/cli/azure/reference-index#az_login).

## <a name="quick-commands"></a>Rychlé příkazy

Zjednodušenou verzi tohoto tématu, testování, vaše rozhodnutí vyzkoušet nebo získat informace o virtuálních počítačích v Azure, najdete v článku [vytvořit vlastní image virtuálního počítače Azure pomocí rozhraní příkazového řádku](tutorial-custom-images.md).


## <a name="step-1-deprovision-the-vm"></a>Krok 1: Zrušení zřízení virtuálního počítače
Je-li zrušit zřízení virtuálního počítače, pomocí agenta virtuálního počítače Azure k odstranění počítače konkrétní soubory a data. Použití `waagent` příkazů *– zrušení zřízení + uživatel* parametru u vašeho zdrojového virtuálního počítače s Linuxem. Další informace najdete v [uživatelské příručce agenta Azure Linux](../extensions/agent-linux.md).

1. Připojte se k virtuálním počítačům s Linuxem pomocí klienta SSH.
2. V okně SSH zadejte následující příkaz:
   
    ```bash
    sudo waagent -deprovision+user
    ```
<br>
   > [!NOTE]
   > Tento příkaz lze spusťte pouze na virtuální počítač, který máte v úmyslu zachytit v podobě obrázku. Není zaručeno, že image se vymaže všechny citlivých informací nebo je vhodný pro distribuci. *+ Uživatel* parametr také odebere posledního zřízeného uživatelského účtu. Pokud chcete zachovat přihlašovací údaje k účtu ve virtuálním počítači, použijte *– zrušení zřízení* ponechte uživatelský účet na místě.
 
3. Typ **y** pokračujte. Můžete přidat **– platnost** parametr, aby tento krok potvrzení.
4. Po dokončení příkazu zadejte **ukončit**. Tento krok se zavře klienta SSH.

## <a name="step-2-create-vm-image"></a>Krok 2: Vytvoření image virtuálního počítače
Rozhraní příkazového řádku Azure CLI 2.0 můžete označit virtuální počítač za generalizovaný a zachycení bitové kopie. V následujících příkladech nahraďte ukázkové názvy parametrů s vlastními hodnotami. Zahrnout názvy parametrů příklad *myResourceGroup*, *myVnet*, a *myVM*.

1. Zrušit přidělení virtuálního počítače, zřízení s [az vm deallocate](/cli//azure/vm#deallocate). V následujícím příkladu se uvolní virtuální počítač s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. Označit virtuální počítač za generalizovaný s [az vm generalize](/cli//azure/vm#generalize). Následující příklad označuje virtuální počítač s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup* za generalizovaný:
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. Teď vytvořte image prostředku virtuálního počítače s [az image vytvořit](/cli/azure/image#az_image_create). Následující příklad vytvoří image *myImage* ve skupině prostředků s názvem *myResourceGroup* pomocí prostředku virtuálního počítače s názvem *myVM*:
   
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
Vytvoření virtuálního počítače pomocí bitové kopie, které jste vytvořili pomocí [az vm vytvořit](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *myVMDeployed* z image s názvem *myImage*:

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Vytváří se virtuální počítač v jiné skupině prostředků 

Můžete vytvořit virtuální počítače z image v libovolné skupině prostředků v rámci vašeho předplatného. Vytvoření virtuálního počítače v jiné skupině prostředků než image, zadejte úplné ID prostředku do bitové kopie. Použití [az image list](/cli/azure/image#az_image_list) Chcete-li zobrazit seznam imagí. Výstup se podobá následujícímu příkladu:

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

Následující příklad používá [az vm vytvořit](/cli/azure/vm#az_vm_create) vytvořit virtuální počítač v jiné skupině prostředků než zdrojového obrázku tak, že zadáte ID prostředku bitové kopie:

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Krok 4: Ověření nasazení

Nyní SSH k virtuálnímu počítači, který jste vytvořili pro ověření nasazení a začít používat nový virtuální počítač. Připojení přes protokol SSH, najít IP adresu nebo plně kvalifikovaný název domény virtuálního počítače pomocí [az vm show](/cli/azure/vm#az_vm_show):

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Další postup
Vytvoření několika virtuálních počítačů z vaší zdrojové imagi virtuálního počítače. Pokud potřebujete provést změny do bitové kopie: 

- Vytvoření virtuálního počítače z image.
- Ujistěte se, všech aktualizací nebo změny konfigurace.
- Postupujte podle kroků znovu pro zrušení zřízení, uvolnit, generalizace a vytvořit bitovou kopii.
- Pomocí této nové image pro budoucí nasazení. V případě potřeby odstraňte původní bitové kopie.

Další informace týkající se správy virtuálních počítačů pomocí rozhraní příkazového řádku najdete v tématu [příkazového řádku Azure CLI 2.0](/cli/azure).
