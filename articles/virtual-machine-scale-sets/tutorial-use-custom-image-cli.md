---
title: Kurz – použití vlastní image virtuálního počítače ve škálované sadě pomocí Azure CLI
description: Zjistěte, jak pomocí Azure CLI vytvořit vlastní image virtuálního počítače, kterou můžete použít k nasazení škálovací sady virtuálních počítačů.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: b12715e299f523d7ace56a72b0098b5d7ffac0ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98683045"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Kurz: Vytvoření a použití vlastní image pro škálovací sady virtuálních počítačů pomocí Azure CLI
Při vytváření škálovací sady zadáte image, která se použije při nasazení instancí virtuálních počítačů. Pokud chcete snížit počet úloh po nasazení instancí virtuálních počítačů, můžete použít vlastní image virtuálního počítače. Tato vlastní image virtuálního počítače obsahuje instalace a konfigurace všech požadovaných aplikací. Všechny instance virtuálních počítačů vytvořené ve škálovací sadě používají vlastní image virtuálního počítače a jsou připravené k obsluze provozu aplikace. Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvoření služby Shared Image Gallery
> * Vytvoření specializované definice obrázku
> * Vytvoření verze image
> * Vytvoření sady škálování z specializované image
> * Sdílení Galerie imagí


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.4.0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="overview"></a>Přehled

[Galerie sdílených imagí](../virtual-machines/shared-image-galleries.md) zjednodušuje sdílení vlastních imagí v rámci vaší organizace. Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spouštění konfigurací, jako jsou předběžné načítání aplikací, konfigurace aplikací a další konfigurace operačního systému. 

Galerie sdílených imagí umožňuje sdílet vlastní image virtuálních počítačů s ostatními. Vyberte, které Image chcete sdílet, které oblasti mají být v nástroji dostupné a které chcete sdílet s. 

## <a name="create-and-configure-a-source-vm"></a>Vytvoření a konfigurace zdrojového virtuálního počítače

Nejprve vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group) a pak vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). Tento virtuální počítač se pak použije jako zdroj image. Následující příklad vytvoří virtuální počítač *myVM* ve skupině prostředků *myResourceGroup*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

> [!IMPORTANT]
> **ID** vašeho virtuálního počítače se zobrazí ve výstupu příkazu [AZ VM Create](/cli/azure/vm) . Zkopírujte tento někde došlo bezpečně, abyste ho mohli použít později v tomto kurzu.

Veřejná IP adresa vašeho virtuálního počítače se taky zobrazí ve výstupu příkazu [AZ VM Create](/cli/azure/vm) . Následujícím způsobem se připojte přes SSH k veřejné IP adrese virtuálního počítače:

```console
ssh azureuser@<publicIpAddress>
```

Teď vlastní virtuální počítač přizpůsobte nainstalováním základního webového serveru. Instance virtuálního počítače po nasazení ve škálovací sadě bude obsahovat všechny požadované balíčky ke spuštění webové aplikace. Následujícím způsobem pomocí příkazu `apt-get` nainstalujte *NGINX*:

```bash
sudo apt-get install -y nginx
```

Až skončíte, zadejte `exit` odpojení připojení SSH.

## <a name="create-an-image-gallery"></a>Vytvoření galerie imagí 

Galerie imagí je primární prostředek, který slouží k povolení sdílení obrázků. 

Povolenými znaky pro název galerie jsou velká a malá písmena, číslice, tečky a tečky. Název galerie nesmí obsahovat pomlčky.   Názvy galerií musí být v rámci vašeho předplatného jedinečné. 

Pomocí [AZ SIG Create](/cli/azure/sig#az-sig-create)vytvořte galerii imagí. Následující příklad vytvoří skupinu prostředků s názvem Gallery s názvem *myGalleryRG* v *východní USA* a galerii s názvem *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Vytvoření definice obrázku

Definice obrázků vytvoří logické seskupení obrázků. Slouží ke správě informací o verzích imagí, které jsou v nich vytvořeny. 

Názvy definic obrázků mohou být tvořeny velkými a malými písmeny, číslicemi, tečkami, pomlčkami a tečkami. 

Ujistěte se, že je vaše definice image správným typem. Pokud jste virtuální počítač zobecněni (pomocí nástroje Sysprep pro Windows nebo waagent-devisioning pro Linux), měli byste vytvořit zobecněnou definici Image pomocí `--os-state generalized` . Pokud chcete virtuální počítač použít bez odebrání stávajících uživatelských účtů, vytvořte pomocí nástroje specializovanou definici image `--os-state specialized` .

Další informace o hodnotách, které můžete zadat pro definici obrázku, najdete v tématu [definice imagí](../virtual-machines/shared-image-galleries.md#image-definitions).

Vytvořte definici obrázku v galerii pomocí [AZ SIG image-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

V tomto příkladu se definice image jmenuje *myImageDefinition* a je určena pro [specializovanou](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) image operačního systému Linux. Pokud chcete vytvořit definici imagí pomocí operačního systému Windows, použijte `--os-type Windows` . 

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

> [!IMPORTANT]
> Ve výstupu příkazu se zobrazí **ID** definice obrázku. Zkopírujte tento někde došlo bezpečně, abyste ho mohli použít později v tomto kurzu.


## <a name="create-the-image-version"></a>Vytvoření verze image

Z virtuálního počítače vytvořte verzi Image pomocí [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create).  

Povolené znaky pro verzi obrázku jsou čísla a tečky. Čísla musí být v rozsahu 32 celé číslo. Formát: *MajorVersion*. *Podverze.* *Oprava*.

V tomto příkladu je verze naší image *1.0.0* a my vytvoříme jednu repliku v oblasti *střed USA – jih* a 1 replice v *východní USA 2* oblasti. Oblasti replikace musí zahrnovat oblast, ve které je umístěný zdrojový virtuální počítač.

`--managed-image`V tomto příkladu nahraďte hodnotu ID virtuálního počítače z předchozího kroku.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1" \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Aby bylo možné použít stejnou spravovanou bitovou kopii k vytvoření jiné verze bitové kopie, je třeba počkat na dokončení sestavení a repliky verze image.
>
> Image můžete ukládat do Premium Storage tím, že přidáte nebo zadáte `--storage-account-type  premium_lrs` [redundantní úložiště zóny](../storage/common/storage-redundancy.md) , a to přidáním, `--storage-account-type  standard_zrs` Když vytvoříte verzi image.
>




## <a name="create-a-scale-set-from-the-image"></a>Vytvoření sady škálování z Image
Vytvořte sadu škálování z specializované Image pomocí [`az vmss create`](/cli/azure/vmss#az-vmss-create) . 

Vytvořením sady škálování pomocí [`az vmss create`](/cli/azure/vmss#az-vmss-create) parametru--specializované použijte k označení, že se jedná o specializovanou image. 

Pomocí ID definice image pro `--image` můžete vytvořit instance sady škálování z nejnovější verze image, která je k dispozici. Můžete také vytvořit instance sady škálování z konkrétní verze, a to zadáním ID verze image pro `--image` . 

Vytvořte sadu škálování s názvem *myScaleSet* nejnovější verzi image *myImageDefinition* , kterou jsme vytvořili dříve.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```

Vytvoření a konfigurace všech prostředků škálovací sady a virtuálních počítačů trvá několik minut.


## <a name="test-your-scale-set"></a>Test škálovací sady
Pokud chcete škálovací sadě povolit příjem provozu a ověřit správné fungování webového serveru, vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí příkazu [az network lb rule create](/cli/azure/network/lb/rule). Následující příklad vytvoří pravidlo *myLoadBalancerRuleWeb* povolující provoz na portu *TCP**80*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Pokud chcete vidět svou škálovací sadu v akci, získejte veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení pomocí příkazu [az network public-ip show](/cli/azure/network/public-ip). Následující příklad získá IP adresu *myScaleSetLBPublicIP* vytvořenou jako součást škálovací sady:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Zadejte veřejnou IP adresu do svého webového prohlížeče. Zobrazí se výchozí webová stránka serveru NGINX, jak je znázorněno v následujícím příkladu:

![Server NGINX spuštěný z vlastní image virtuálního počítače](media/tutorial-use-custom-image-cli/default-nginx-website.png)



## <a name="share-the-gallery"></a>Sdílení galerie

Image můžete sdílet mezi předplatnými pomocí řízení přístupu na základě role Azure (RBAC). Obrázky můžete sdílet v galerii, definici obrázku nebo verzi image. Každý uživatel, který má oprávnění ke čtení verze image, dokonce i přes odběry, bude moci nasadit virtuální počítač pomocí verze image.

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

Další informace o tom, jak sdílet prostředky pomocí Azure RBAC, najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure CLI](../role-based-access-control/role-assignments-cli.md).


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete odebrat sadu škálování a další prostředky, odstraňte skupinu prostředků a všechny její prostředky pomocí [AZ Group Delete](/cli/azure/group). Parametr `--no-wait` vrátí řízení na příkazový řádek bez čekání na dokončení operace. Parametr `--yes` potvrdí, že chcete prostředky odstranit, aniž by se na to zobrazoval další dotaz.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak vytvořit a použít vlastní image virtuálního počítače pro škálovací sady pomocí Azure CLI:

> [!div class="checklist"]
> * Vytvoření služby Shared Image Gallery
> * Vytvoření specializované definice obrázku
> * Vytvoření verze image
> * Vytvoření sady škálování z specializované image
> * Sdílení Galerie imagí

V dalším kurzu se dozvíte, jak do škálovací sady nasadit aplikace.

> [!div class="nextstepaction"]
> [Nasazování aplikací do škálovacích sad](tutorial-install-apps-cli.md)