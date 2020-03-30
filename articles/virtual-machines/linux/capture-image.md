---
title: Zachycení bitové kopie virtuálního počítače s Linuxem pomocí azure CLI
description: Zachyťte bitovou kopii virtuálního počítače Azure, která se použije pro hromadná nasazení pomocí příkazového příkazového příkazu k Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 77f6244651551763f5460432655d66267775a256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250397"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Vytvoření image virtuálního počítače nebo virtuálního pevného disku

Chcete-li vytvořit více kopií virtuálního počítače (VM) pro použití v Azure, zachyťte bitovou kopii virtuálního počítače nebo virtuálního pevného disku operačního systému. Chcete-li vytvořit bitovou kopii pro nasazení, budete muset odebrat informace o osobním účtu. V následujících krocích zrušíte zřízení existujícího virtuálního mandu, zrušíte jeho přidělení a vytvoříte bitovou kopii. Pomocí této bitové kopie můžete vytvořit virtuální počítače v rámci libovolné skupiny prostředků v rámci předplatného.

Pokud chcete vytvořit kopii stávajícího virtuálního počítače s Linuxem pro zálohování nebo ladění nebo nahrát speciální Linux VHD z místního virtuálního počítače, přečtěte si část [Nahrát a vytvořit virtuální počítač SIF z vlastní bitové kopie disku](upload-vhd.md).  

Službu **Azure VM Image Builder (Public Preview)** můžete použít k vytvoření vlastní image, není třeba se učit žádné nástroje nebo kanály sestavení nastavení, jednoduše poskytuje konfiguraci image a Image Builder vytvoří image. Další informace najdete [v tématu Začínáme s Azure VM Image Builder](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview).

Před vytvořením obrázku budete potřebovat následující položky:

* Virtuální počítač Azure vytvořený v modelu nasazení Správce prostředků, který používá spravované disky. Pokud jste ještě nevytvořili virtuální počítač s Linuxem, můžete použít [portál](quick-create-portal.md), [Azure CLI](quick-create-cli.md)nebo [šablony Správce prostředků](create-ssh-secured-vm-from-template.md). Podle potřeby nakonfigurujte virtuální ho. Můžete například [přidat datové disky](add-disk.md), použít aktualizace a nainstalovat aplikace. 

* Nejnovější [Azure CLI](/cli/azure/install-az-cli2) nainstalován a být přihlášen i k účtu Azure s [az přihlášení](/cli/azure/reference-index#az-login).

## <a name="prefer-a-tutorial-instead"></a>Preferovat výukový program místo?

Zjednodušená verze tohoto článku a pro testování, vyhodnocování nebo učení o virtuálních počítačích v Azure najdete v článku [Vytvoření vlastní image virtuálního počítače Azure pomocí příkazového příkazu k příkazu .](tutorial-custom-images.md)  V opačném případě pokračujte ve čtení zde získat úplný obraz.


## <a name="step-1-deprovision-the-vm"></a>Krok 1: Zrušení zřízení virtuálního montovana
Nejprve budete zrušit zřízení virtuálního počítače pomocí agenta virtuálního počítače Azure k odstranění souborů a dat specifických pro počítač. Použijte `waagent` příkaz s `-deprovision+user` parametrem na zdrojovém virtuálním počítači s Linuxem. Další informace najdete v [uživatelské příručce agenta Azure Linux](../extensions/agent-linux.md).

1. Připojte se k virtuálnímu počítači s Linuxem s klientem SSH.
2. V okně SSH zadejte následující příkaz:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Spusťte tento příkaz jenom na virtuálním počítači, který zachytíte jako image. Tento příkaz nezaručuje, že obraz je vymazánvšechny citlivé informace nebo je vhodný pro další distribuci. Parametr `+user` také odebere poslední zřízený uživatelský účet. Chcete-li zachovat pověření uživatelského účtu `-deprovision`ve virtuálním provozu, použijte jenom .
 
3. Zadejte **y,** chcete-li pokračovat. Můžete přidat `-force` parametr, abyste se vyhnuli tomuto kroku potvrzení.
4. Po dokončení příkazu zadejte **ukončení** zavřete klienta SSH.  Virtuální virtuální měsíč bude v tomto okamžiku stále spuštěný.

## <a name="step-2-create-vm-image"></a>Krok 2: Vytvoření image virtuálního virtuálního montova
Pomocí azure cli označit virtuální počítač jako zobecněné a zachytit image. V následujících příkladech nahraďte názvy příkladů parametrů vlastními hodnotami. Příklady názvů parametrů zahrnují *myResourceGroup*, *myVnet*a *myVM*.

1. Navrátit virtuální ho virtuálního ms, který jste zrušili s [az vm navrátit](/cli/azure/vm). Následující příklad navrací virtuální hosti s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*.  
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```
    
    Počkejte na virtuální počítač zcela navrátit před přechodem na. Dokončení může trvat několik minut.  Virtuální měsíč se vypne během deallocation.

2. Označte virtuální ho jako zobecněný s [az vm generalizovat](/cli/azure/vm). Následující příklad označuje virtuální hosti s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup* jako generalizované.
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

    Virtuální ho virtuálního ms, který byl zobecněn již nelze restartovat.

3. Vytvořte bitovou kopii prostředku virtuálního soudu s [vytvořením image az](/cli/azure/image#az-image-create). Následující příklad vytvoří obrázek s názvem *myImage* ve skupině prostředků s názvem *myResourceGroup* pomocí prostředku virtuálního mě s názvem *myVM*.
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > Image se vytvoří ve stejné skupině prostředků jako zdrojový virtuální počítač. Virtuální počítače můžete vytvořit v libovolné skupině prostředků v rámci vašeho předplatného z této bitové kopie. Z hlediska správy můžete chtít vytvořit konkrétní skupinu prostředků pro prostředky virtuálních počítačových virtuálních zařízení a image.
   >
   > Pokud chcete uložit bitovou kopii do úložiště odolného vůči zóně, musíte ji vytvořit `--zone-resilient true` v oblasti, která podporuje [zóny dostupnosti](../../availability-zones/az-overview.md) a zahrnuje parametr.
   
Tento příkaz vrátí JSON, který popisuje image virtuálního počítače. Uložte tento výstup pro pozdější použití.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Krok 3: Vytvoření virtuálního virtuálního virtuálního masivu ze zachycené image
Vytvořte virtuální virtuální virtuální hospo, když použijete image, kterou jste vytvořili pomocí [vytvoření az vm](/cli/azure/vm). Následující příklad vytvoří virtuální hod s názvem *myVMDeployed* z obrázku s názvem *myImage*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Vytvoření virtuálního virtuálního uživatele v jiné skupině prostředků 

Virtuální počítače můžete vytvořit z image v libovolné skupině prostředků v rámci předplatného. Chcete-li vytvořit virtuální počítač v jiné skupině prostředků než obrázek, zadejte úplné ID prostředku k obrázku. Seznam obrázků slouží k zobrazení seznamu obrázků pomocí seznamu obrázků [az.](/cli/azure/image#az-image-list) Výstup se podobá následujícímu příkladu.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

Následující příklad používá [vytvoření virtuálního vm az](/cli/azure/vm#az-vm-create) k vytvoření virtuálního virtuálního pracovního virtuálního pracovního prostředku v jiné skupině prostředků než ve zdrojové bitové kopii zadáním ID prostředku bitové kopie.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Krok 4: Ověření nasazení

SSH do virtuálního počítače, který jste vytvořili k ověření nasazení a začít používat nový virtuální počítač. Chcete-li se připojit přes SSH, najděte IP adresu nebo hlavní obrazové propojení virtuálního počítače pomocí [az vm show](/cli/azure/vm#az-vm-show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Další kroky
Ze zdrojové image virtuálního počítače můžete vytvořit víc virtuálních počítačů. Jak provést změny v obrázku: 

- Vytvořte virtuální počítač z obrázku.
- Proveďte všechny aktualizace nebo změny konfigurace.
- Podle pokynů znovu zrušíte zřízení, navrátíte, zobecnita a vytvoříte bitovou kopii.
- Tuto novou bitu použijte pro budoucí nasazení. Původní obrázek můžete odstranit.

Další informace o správě virtuálních počítačů pomocí příkazového příkazového příkazu najdete v [tématu Azure CLI](/cli/azure).
