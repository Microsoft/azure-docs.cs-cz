---
title: Zachycení spravované image virtuálního počítače se systémem Linux pomocí Azure CLI
description: Zachyťte spravovanou bitovou kopii virtuálního počítače Azure, který se použije pro Hromadná nasazení pomocí Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 10/08/2018
ms.author: cynthn
ms.custom: legacy, devx-track-azurecli
ms.collection: linux
ms.openlocfilehash: 8e81c204c1f05b7fc6bdf1efc7060e2094c648e5
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630618"
---
# <a name="how-to-create-a-managed-image-of-a-virtual-machine-or-vhd"></a>Postup vytvoření spravované image virtuálního počítače nebo virtuálního pevného disku

Pokud chcete vytvořit více kopií virtuálního počítače pro použití v Azure pro vývoj a testování, Zachyťte spravovanou bitovou kopii virtuálního počítače nebo virtuálního pevného disku s operačním systémem. Pokud chcete vytvářet, ukládat a sdílet image ve velkém měřítku, přečtěte si téma [Galerie sdílených imagí](../shared-images-cli.md).

Jedna spravovaná bitová kopie podporuje až 20 současných nasazení. Při pokusu o vytvoření více než 20 virtuálních počítačů současně ze stejné spravované image může docházet k vypršení časového limitu zřizování z důvodu omezení výkonu úložiště u jednoho virtuálního pevného disku. Pokud chcete vytvořit více než 20 virtuálních počítačů současně, použijte image [Galerie sdílených imagí](../shared-image-galleries.md) nakonfigurovanou s 1 replikou pro každé 20 souběžných nasazení virtuálních počítačů.

Chcete-li vytvořit spravovanou bitovou kopii, bude nutné odebrat informace o osobním účtu. V následujících krocích zrušíte zřízení existujícího virtuálního počítače, zrušíte jeho přidělení a vytvoříte image. Tuto image můžete použít k vytvoření virtuálních počítačů napříč všemi skupinami prostředků v rámci vašeho předplatného.

Pokud chcete vytvořit kopii stávajícího virtuálního počítače se systémem Linux pro zálohování nebo ladění nebo nahrát specializovaný virtuální pevný disk se systémem Linux z místního virtuálního počítače, přečtěte si téma [nahrání a vytvoření virtuálního počítače se systémem Linux z vlastní image disku](upload-vhd.md).  

Pomocí služby **Azure VM Image Builder (Public Preview)** můžete vytvořit vlastní image, nemusíte se učit žádné nástroje ani nastavovat kanály sestavení, jednoduše poskytnout konfiguraci image a tvůrce imagí vytvoří image. Další informace najdete v tématu [Začínáme s nástrojem Azure VM Image Builder](../image-builder-overview.md).

Před vytvořením image budete potřebovat tyto položky:

* Virtuální počítač Azure vytvořený v modelu nasazení Správce prostředků, který používá spravované disky. Pokud jste ještě nevytvořili virtuální počítač Linux, můžete použít šablony [portálu](quick-create-portal.md), [Azure CLI](quick-create-cli.md)nebo [Správce prostředků](create-ssh-secured-vm-from-template.md). Nakonfigurujte virtuální počítač podle potřeby. Můžete například [přidat datové disky](add-disk.md), použít aktualizace a nainstalovat aplikace. 

* Je nainstalované nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a přihlásili jste se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index#az-login).

## <a name="prefer-a-tutorial-instead"></a>Chcete místo toho použít kurz?

Pro zjednodušenou verzi tohoto článku a pro testování, hodnocení nebo učení o virtuálních počítačích v Azure se podívejte na téma [Vytvoření vlastní image virtuálního počítače Azure pomocí rozhraní](tutorial-custom-images.md)příkazového řádku (CLI).  V opačném případě pokračujte v čtení, abyste získali úplný přehled.


## <a name="step-1-deprovision-the-vm"></a>Krok 1: zrušení zřízení virtuálního počítače
Nejprve zrušíte zřízení virtuálního počítače pomocí agenta virtuálního počítače Azure k odstranění souborů a dat specifických pro konkrétní počítač. Použijte `waagent` příkaz s `-deprovision+user` parametrem na ZDROJovém virtuálním počítači Linux. Další informace najdete v [uživatelské příručce agenta Azure Linux](../extensions/agent-linux.md). Tento proces nelze vrátit zpět.

1. Připojte se k VIRTUÁLNÍmu počítači se systémem Linux pomocí klienta SSH.
2. V okně SSH zadejte následující příkaz:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Spusťte tento příkaz jenom na virtuálním počítači, který budete zachytit jako image. Tento příkaz nezaručuje, že image je smazána u všech citlivých informací, nebo je vhodná pro redistribuci. `+user`Parametr také odebere naposledy zřízený uživatelský účet. Pokud chcete zachovat přihlašovací údaje uživatelského účtu ve virtuálním počítači, používejte jenom `-deprovision` .
 
3. Pokračujte zadáním **y** . `-force`Chcete-li se tomuto kroku potvrzení vyhnout, můžete přidat parametr.
4. Po dokončení příkazu zadejte **Exit** a zavřete tak klienta ssh.  Virtuální počítač bude v tuto chvíli i nadále spuštěn.

## <a name="step-2-create-vm-image"></a>Krok 2: vytvoření image virtuálního počítače
Pomocí rozhraní příkazového řádku Azure můžete označit virtuální počítač jako zobecněný a zachytit image. V následujících příkladech nahraďte příklady názvů parametrů vlastními hodnotami. Příklady názvů parametrů jsou *myResourceGroup*, *myVnet* a *myVM*.

1. Zrušte přidělení virtuálního počítače, který jste zrušili pomocí [AZ VM disallocate](/cli/azure/vm). Následující příklad zruší přidělení virtuálního počítače s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*.  
   
    ```azurecli
    az vm deallocate \
        --resource-group myResourceGroup \
        --name myVM
    ```
    
    Počkejte, až se virtuální počítač kompletně uvolní, než se přesunete na. Dokončení tohoto může trvat několik minut.  Virtuální počítač je vypnutý během navracení.

2. Označte virtuální počítač jako zobecněný pomocí příkazu [AZ VM generalize](/cli/azure/vm). Následující příklad označí virtuální počítač s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup* jako zobecněnou.
   
    ```azurecli
    az vm generalize \
        --resource-group myResourceGroup \
        --name myVM
    ```

    Virtuální počítač, který je zobecněn, již nelze restartovat.

3. Vytvořte bitovou kopii prostředku virtuálního počítače pomocí [AZ image Create](/cli/azure/image#az-image-create). Následující příklad vytvoří image s názvem *myImage* ve skupině prostředků s názvem *myResourceGroup* pomocí prostředku virtuálního počítače s názvem *myVM*.
   
    ```azurecli
    az image create \
        --resource-group myResourceGroup \
        --name myImage --source myVM
    ```
   
   > [!NOTE]
   > Image se vytvoří ve stejné skupině prostředků jako váš zdrojový virtuální počítač. Z této image můžete vytvořit virtuální počítače v libovolné skupině prostředků v rámci svého předplatného. Z perspektivy správy můžete chtít vytvořit konkrétní skupinu prostředků pro prostředky a image virtuálních počítačů.
   >
   > Pokud chcete uložit image do odolného úložiště v zóně, je nutné ji vytvořit v oblasti, která podporuje [zóny dostupnosti](../../availability-zones/az-overview.md) a zahrnout `--zone-resilient true` parametr.
   
Tento příkaz vrátí JSON, který popisuje image virtuálního počítače. Uložte si tento výstup pro pozdější referenci.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Krok 3: Vytvoření virtuálního počítače ze zaznamenané bitové kopie
Vytvořte virtuální počítač pomocí Image, kterou jste vytvořili pomocí [AZ VM Create](/cli/azure/vm). Následující příklad vytvoří virtuální počítač s názvem *myVMDeployed* z image s názvem *myImage*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Vytvoření virtuálního počítače v jiné skupině prostředků 

Virtuální počítače můžete vytvořit z image v libovolné skupině prostředků v rámci vašeho předplatného. Pokud chcete vytvořit virtuální počítač v jiné skupině prostředků než image, zadejte úplné ID prostředku k imagi. K zobrazení seznamu imagí použijte [AZ image list](/cli/azure/image#az-image-list) . Výstup se podobá následujícímu příkladu.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

Následující příklad používá příkaz [AZ VM Create](/cli/azure/vm#az-vm-create) k vytvoření virtuálního počítače ve skupině prostředků, která je jiná než zdrojová image, zadáním ID prostředku image.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Krok 4: Ověření nasazení

Připojte se přes SSH k virtuálnímu počítači, který jste vytvořili pro ověření nasazení a začněte používat nový virtuální počítač. Pokud se chcete připojit přes SSH, vyhledejte IP adresu nebo plně kvalifikovaný název domény virtuálního počítače pomocí [AZ VM show](/cli/azure/vm#az-vm-show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Další kroky
Pokud chcete vytvářet, ukládat a sdílet image ve velkém měřítku, přečtěte si téma [Galerie sdílených imagí](../shared-images-cli.md).
