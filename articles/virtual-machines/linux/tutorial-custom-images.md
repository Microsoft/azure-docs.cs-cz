---
title: Kurz – vytváření vlastních imagí virtuálních počítačů pomocí Azure CLI
description: V tomto kurzu zjistíte, jak pomocí Azure CLI vytvořit vlastní image virtuálního počítače v Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: 40ec86030e136a06fe240e473a469681dcfa7c4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557757"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Kurz: Vytvoření vlastní image virtuálního počítače Azure pomocí Azure CLI

Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spouštění konfigurací, jako jsou předběžné načítání aplikací, konfigurace aplikací a další konfigurace operačního systému. V tomto kurzu vytvoříte vlastní image virtuálního počítače Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření služby Shared Image Gallery
> * Vytvoření definice obrázku
> * Vytvoření verze image
> * Vytvoření virtuálního počítače z Image 
> * Sdílení Galerie imagí


V tomto kurzu se používá CLI v rámci [Azure Cloud Shell](../../cloud-shell/overview.md), který se průběžně aktualizuje na nejnovější verzi. Chcete-li otevřít Cloud Shell, vyberte možnost **vyzkoušet** v horní části libovolného bloku kódu.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.4.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="overview"></a>Přehled

[Galerie sdílených imagí](../shared-image-galleries.md) zjednodušuje sdílení vlastních imagí v rámci vaší organizace. Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spouštění konfigurací, jako jsou předběžné načítání aplikací, konfigurace aplikací a další konfigurace operačního systému. 

Galerie sdílených imagí umožňuje sdílet vlastní image virtuálních počítačů s ostatními. Vyberte, které Image chcete sdílet, které oblasti mají být v nástroji dostupné a které chcete sdílet s. 

Funkce Galerie sdílených imagí má více typů prostředků:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="before-you-begin"></a>Než začnete

Následující postup podrobně popisuje přeměnu existujícího virtuálního počítače na opětovně použitelnou vlastní image, pomocí které můžete vytvářet nové instance virtuálních počítačů.

K dokončení příkladu v tomto kurzu potřebujete existující virtuální počítač. V případě potřeby se můžete podívat na úvodní seznam rozhraní příkazového [řádku](quick-create-cli.md) a vytvořit virtuální počítač pro použití v tomto kurzu. Při práci v kurzu nahraďte názvy prostředků tam, kde je to potřeba.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="create-an-image-gallery"></a>Vytvoření galerie imagí 

Galerie imagí je primární prostředek, který slouží k povolení sdílení obrázků. 

Povolenými znaky pro název galerie jsou velká a malá písmena, číslice, tečky a tečky. Název galerie nesmí obsahovat pomlčky.   Názvy galerií musí být v rámci vašeho předplatného jedinečné. 

Pomocí [AZ SIG Create](/cli/azure/sig#az-sig-create)vytvořte galerii imagí. Následující příklad vytvoří skupinu prostředků s názvem Gallery s názvem *myGalleryRG* v *východní USA* a galerii s názvem *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="get-information-about-the-vm"></a>Získat informace o virtuálním počítači

Seznam virtuálních počítačů, které jsou k dispozici, můžete zobrazit pomocí [seznamu AZ VM list](/cli/azure/vm#az-vm-list). 

```azurecli-interactive
az vm list --output table
```

Jakmile budete znát název virtuálního počítače a k čemu se v něm skupina prostředků, Získejte ID virtuálního počítače pomocí [AZ VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```

Zkopírujte ID virtuálního počítače, který chcete použít později.

## <a name="create-an-image-definition"></a>Vytvoření definice obrázku

Definice obrázků vytvoří logické seskupení obrázků. Slouží ke správě informací o verzích imagí, které jsou v nich vytvořeny. 

Názvy definic obrázků mohou být tvořeny velkými a malými písmeny, číslicemi, tečkami, pomlčkami a tečkami. 

Další informace o hodnotách, které můžete zadat pro definici obrázku, najdete v tématu [definice imagí](../shared-image-galleries.md#image-definitions).

Vytvořte definici obrázku v galerii pomocí [AZ SIG image-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create). 

V tomto příkladu se definice image jmenuje *myImageDefinition* a je určena pro [specializovanou](../shared-image-galleries.md#generalized-and-specialized-images) image operačního systému Linux. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

Zkopírujte ID definice obrázku z výstupu pro pozdější použití.

## <a name="create-the-image-version"></a>Vytvoření verze image

Z virtuálního počítače vytvořte verzi Image pomocí [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create).  

Povolené znaky pro verzi obrázku jsou čísla a tečky. Čísla musí být v rozsahu 32 celé číslo. Formát: *MajorVersion*. *Podverze.* *Oprava*.

V tomto příkladu je verze naší image *1.0.0* a v oblasti *středozápadní USA* se vytvoří 2 repliky, 1 replika v *střed USA – jih* oblasti a 1 replika v oblasti *východní USA 2* s použitím redundantního úložiště v zóně. Oblasti replikace musí zahrnovat oblast, ve které je umístěný zdrojový virtuální počítač.

`--managed-image`V tomto příkladu nahraďte hodnotu ID virtuálního počítače z předchozího kroku.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Aby bylo možné použít stejnou spravovanou bitovou kopii k vytvoření jiné verze bitové kopie, je třeba počkat na dokončení sestavení a repliky verze image.
>
> Image můžete ukládat do úložiště Premiun přidáním `--storage-account-type  premium_lrs` nebo [redundantním úložištěm zóny](../../storage/common/storage-redundancy.md) přidáním `--storage-account-type  standard_zrs` při vytváření verze image.
>

 
## <a name="create-the-vm"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí příkazového typu [AZ VM Create](/cli/azure/vm#az-vm-create) pomocí parametru--specializované, který označuje, že se jedná o specializovanou image. 

Použijte ID definice image pro `--image` k vytvoření virtuálního počítače z nejnovější verze image, která je k dispozici. Virtuální počítač můžete vytvořit také z konkrétní verze zadáním ID verze image pro `--image` . 

V tomto příkladu vytváříme virtuální počítač z nejnovější verze *myImageDefinition* image.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

## <a name="share-the-gallery"></a>Sdílení galerie

Image můžete sdílet mezi předplatnými pomocí řízení přístupu na základě role Azure (RBAC). Image můžete sdílet v galerii, definici Image nebo imagi verze Leve. Každý uživatel, který má oprávnění ke čtení verze image, dokonce i přes odběry, bude moci nasadit virtuální počítač pomocí verze image.

Doporučujeme sdílet s ostatními uživateli na úrovni galerie. Chcete-li získat ID objektu galerie, použijte příkaz [AZ SIG show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Pomocí ID objektu jako oboru společně s e-mailovou adresou a [AZ role Assignment vytvořit](/cli/azure/role/assignment#az-role-assignment-create) poskytněte uživateli přístup k galerii sdílených imagí. Nahraďte `<email-address>` a `<gallery iD>` vlastními informacemi.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Další informace o tom, jak sdílet prostředky pomocí Azure RBAC, najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="azure-image-builder"></a>Azure Image Builder

Azure také nabízí službu, která je založená na balíčku, [tvůrci imagí virtuálních počítačů Azure](../image-builder-overview.md). Jednoduše popište vlastní nastavení v šabloně a zpracuje se tím vytváření obrázků. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili vlastní image virtuálního počítače. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření služby Shared Image Gallery
> * Vytvoření definice obrázku
> * Vytvoření verze image
> * Vytvoření virtuálního počítače z Image 
> * Sdílení Galerie imagí

Přejděte k dalšímu kurzu, kde se seznámíte s vysoce dostupnými virtuálními počítači.

> [!div class="nextstepaction"]
> [Vytvoření vysoce dostupných virtuálních počítačů](tutorial-availability-sets.md)