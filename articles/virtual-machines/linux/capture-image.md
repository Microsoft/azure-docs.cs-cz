---
title: Vytvořte bitovou kopii virtuálního počítače s Linuxem v Azure pomocí rozhraní příkazového řádku 2.0 | Microsoft Docs
description: Vytvořte bitovou kopii virtuálního počítače Azure pro velkokapacitní nasazení pomocí Azure CLI 2.0.
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
ms.openlocfilehash: bb70b3ff84392797ce0d93b8cf5d4018ff8ebdd8
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33941965"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Postup vytvoření bitové kopie virtuálního počítače nebo virtuální pevný disk

<!-- generalize, image - extended version of the tutorial-->

Pokud chcete vytvořit více kopií virtuálního počítače (VM) pro použití v Azure, vytvořte bitovou kopii virtuálního počítače nebo virtuální pevný disk operačního systému. Pokud chcete vytvořit bitovou kopii, je nutné odebrat informace osobního účtu, takže je bezpečnější nasadit více než jednou. V následujících krocích zrušení zřízení existující virtuální počítač, navrácení a vytvořit bitovou kopii. Tuto bitovou kopii můžete použít k vytvoření virtuálních počítačů v libovolné skupině prostředků v rámci vašeho předplatného.

Pokud chcete vytvořit kopii existující virtuální počítač Linux zálohování nebo ladění, nebo nahrát specializované Linux virtuální pevný disk z virtuálního počítače místní najdete v tématu [nahrát a vytvoření virtuálního počítače s Linuxem z bitové kopie disku vlastní](upload-vhd.md).  

Můžete také použít **balírna** k vytvoření vlastní konfigurace. Další informace o používání balírna najdete v tématu [postup k vytvoření bitové kopie virtuálních počítačů Linux v Azure použijte balírna](build-image-with-packer.md).


## <a name="before-you-begin"></a>Než začnete
Ujistěte se, že splňujete následující požadavky:

* Je nutné virtuální počítač Azure vytvořené v modelu nasazení Resource Manager pomocí spravovaných disků. Pokud jste nevytvořili virtuální počítač s Linuxem, můžete použít [portál](quick-create-portal.md), [rozhraní příkazového řádku Azure](quick-create-cli.md), nebo [šablony Resource Manageru](create-ssh-secured-vm-from-template.md). Podle potřeby nakonfigurujte virtuální počítač. Například [přidat datových disků](add-disk.md), aktualizace a instalovat aplikace. 

* Také je potřeba mít nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) nainstalován a být přihlášení k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login).

## <a name="quick-commands"></a>Rychlé příkazy

Zjednodušené verzi tohoto tématu pro testování, vyhodnocení nebo získávání informací o virtuálních počítačů v Azure, naleznete v části [vytvořit vlastní image virtuálního počítače Azure pomocí rozhraní příkazového řádku](tutorial-custom-images.md).


## <a name="step-1-deprovision-the-vm"></a>Krok 1: Zrušení zřízení virtuálního počítače
Můžete zrušit jejich zřízení virtuálního počítače pomocí agenta virtuálního počítače Azure se odstranit počítače konkrétní soubory a data. Použití `waagent` s *-deprovision + uživatele* parametr na svůj zdroj virtuálního počítače s Linuxem. Další informace najdete v [uživatelské příručce agenta Azure Linux](../extensions/agent-linux.md).

1. Připojte k virtuálním počítačům s Linuxem pomocí klienta SSH.
2. V okně SSH zadejte následující příkaz:
   
    ```bash
    sudo waagent -deprovision+user
    ```
<br>
   > [!NOTE]
   > Tento příkaz lze spusťte pouze na virtuální počítač, který máte v úmyslu bitovou kopii. Není zaručeno, že bitovou kopii vymaže všechny citlivých informací nebo je vhodný pro opětovnou distribuci. *+ Uživatele* parametr také odebere poslední účet zřízení uživatele. Pokud chcete zachovat přihlašovací údaje účtu ve virtuálním počítači, použijte *-deprovision* opustit uživatelský účet na místě.
 
3. Typ **y** pokračujte. Můžete přidat **-force** parametr předejdete tento krok potvrzení.
4. Po dokončení příkazu, zadejte **ukončete**. Tento krok zavře použije klient SSH.

## <a name="step-2-create-vm-image"></a>Krok 2: Vytvoření image virtuálního počítače
Pomocí Azure CLI 2.0 označit virtuální počítač jako zobecněn a zachycení bitové kopie. V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Zahrnout názvy parametrů příklad *myResourceGroup*, *myVnet*, a *Můjvp*.

1. Zrušit přidělení virtuálního počítače, který jste se zrušit [az OM deallocate](/cli//azure/vm#deallocate). Následující příklad zruší přidělení virtuálního počítače s názvem *Můjvp* ve skupině prostředků s názvem *myResourceGroup*:
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. Označit virtuální počítač jako zobecněn s [az virtuálních počítačů zobecní](/cli//azure/vm#generalize). Následující příklad značky virtuální počítač s názvem *Můjvp* ve skupině prostředků s názvem *myResourceGroup* jako zobecněn:
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. Nyní vytvoření bitové kopie prostředků virtuálního počítače s [vytvoření bitové kopie az](/cli/azure/image#az_image_create). Následující příklad vytvoří bitovou kopii s názvem *myImage* ve skupině prostředků s názvem *myResourceGroup* pomocí prostředků virtuálního počítače s názvem *Můjvp*:
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > Obrázek se vytvoří ve stejné skupině prostředků jako vašeho zdrojového virtuálního počítače. Virtuální počítače můžete vytvořit v libovolné skupině prostředků v rámci vašeho předplatného z této bitové kopie. Z hlediska správy můžete chtít vytvořit skupinu prostředků specifické pro vaše prostředky virtuálních počítačů a bitové kopie.
   >
   > Pokud chcete uložit bitové kopie do zóny odolná úložiště, budete muset vytvořit v oblasti, která podporuje [dostupnost zóny](../../availability-zones/az-overview.md) a zahrnout `--zone-resilient true` parametr.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Krok 3: Vytvoření virtuálního počítače ze zaznamenané bitové kopie
Vytvoření virtuálního počítače pomocí bitové kopie vytvořené pomocí [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *myVMDeployed* z bitové kopie s názvem *myImage*:

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Vytvoření virtuálního počítače v jiné skupině prostředků 

Virtuální počítače můžete vytvořit z image v libovolné skupině prostředků v rámci vašeho předplatného. Pokud chcete vytvořit virtuální počítač v jiné skupině prostředků než bitovou kopii, zadejte je úplné ID prostředku do bitové kopie. Použití [seznamu obrázků az](/cli/azure/image#az_image_list) zobrazení seznamu obrázků. Výstup se podobá následujícímu příkladu:

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

Následující příklad používá [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) vytvoření virtuálního počítače v jiné skupině prostředků než zdrojové bitové kopie zadáním ID prostředku bitové kopie:

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Krok 4: Ověření nasazení

Nyní SSH k virtuálnímu počítači, který jste vytvořili pro ověření nasazení a začít používat nový virtuální počítač. Pro připojení pomocí protokolu SSH, najít IP adresu nebo plně kvalifikovaný název domény vašeho virtuálního počítače s [az virtuálních počítačů zobrazit](/cli/azure/vm#az_vm_show):

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Další postup
Můžete vytvořit víc virtuálních počítačů z vaší zdrojové bitové kopie virtuálního počítače. Pokud potřebujete provést změny do bitové kopie: 

- Vytvořte virtuální počítač z bitové kopie.
- Nastavit všechny aktualizace nebo změny konfigurace.
- Postup opakujte zrušení zřízení, navrácení, generalize a vytvořte bitovou kopii.
- Použijte tuto novou bitovou kopii pro budoucí nasazení. V případě potřeby odstraňte původní bitové kopie.

Další informace týkající se správy virtuálních počítačů pomocí rozhraní příkazového řádku najdete v tématu [Azure CLI 2.0](/cli/azure).
