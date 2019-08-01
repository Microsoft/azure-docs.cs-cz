---
title: Použití Azure image Builder s galerií imagí pro virtuální počítače s Windows (Preview)
description: Vytvářejte image Windows pomocí Azure image Builder a galerie sdílených imagí.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 33f13c09a06885523298bd7c23744e79f68e5301
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698669"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Verze Preview: Vytvoření bitové kopie systému Windows a její distribuce do galerie sdílených imagí 

V tomto článku se dozvíte, jak můžete pomocí Tvůrce imagí Azure vytvořit verzi image v [galerii sdílených imagí](shared-image-galleries.md)a pak ji distribuovat globálně.

K nakonfigurování image budeme používat šablonu. JSON. Soubor. JSON, který používáme, je tady: [helloImageTemplateforWinSIG. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json). 

Pro distribuci image do galerie sdílených imagí šablona používá [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) jako hodnotu pro `distribute` oddíl šablony.

> [!IMPORTANT]
> Azure image Builder je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Registrace funkcí
Chcete-li používat Azure image Builder v rámci verze Preview, je nutné zaregistrovat novou funkci.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Ověřte stav registrace funkce.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Ověřte vaši registraci.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
```

Pokud nevyžadují registraci, spusťte tento příkaz:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```

## <a name="set-variables-and-permissions"></a>Nastavení proměnných a oprávnění 

Některé informace budeme používat opakovaně, takže vytvoříme některé proměnné, které tyto informace uloží. Nahraďte hodnoty proměnných, například `username` a `vmpassword`, vlastními informacemi.

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="azureuser"
vmpassword="passwordfortheVM"
```

Vytvořte proměnnou pro ID předplatného. Můžete to získat pomocí `az account show | grep id`.

```azurecli-interactive
subscriptionID="Subscription ID"
```

Vytvořte skupinu prostředků.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Udělte službě Azure image Builder oprávnění k vytváření prostředků v této skupině prostředků. `--assignee` Hodnota je ID registrace aplikace pro službu Tvůrce imagí. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="create-an-image-definition-and-gallery"></a>Vytvoření definice a galerie imagí

Chcete-li použít Tvůrce imagí s galerií sdílených imagí, je nutné mít existující definici obrázku a obrázku. Tvůrce imagí nevytvoří pro vás definici image a image.

Pokud ještě nemáte definici galerie a image k použití, začněte jejich vytvořením. Nejdřív vytvořte galerii imagí.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Pak vytvořte definici obrázku.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher corpIT \
   --offer myOffer \
   --sku 2019 \
   --os-type Windows
```


## <a name="download-and-configure-the-json"></a>Stažení a konfigurace formátu. JSON

Stáhněte šablonu. JSON a nakonfigurujte ji pomocí proměnných.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json -o helloImageTemplateforWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforWinSIG.json
```

## <a name="create-the-image-version"></a>Vytvoření verze image

V této další části se vytvoří verze image v galerii. 

Odešlete konfiguraci image do služby Azure image Builder.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Spusťte sestavení image.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforWinSIG01 \
     --action Run 
```

Vytvoření image a její replikace do obou oblastí může chvíli trvat. Před přechodem k vytvoření virtuálního počítače počkejte na dokončení této části.


## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Vytvořte virtuální počítač z verze image, kterou vytvořila služba Azure image Builder.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm001 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```


## <a name="verify-the-customization"></a>Ověření přizpůsobení
Vytvořte připojení ke vzdálené ploše virtuálního počítače pomocí uživatelského jména a hesla, které jste nastavili při vytváření virtuálního počítače. Uvnitř virtuálního počítače otevřete příkazový řádek a zadejte příkaz:

```console
dir c:\
```

Měl by se zobrazit adresář s `buildActions` názvem, který byl vytvořen během přizpůsobení bitové kopie.


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud se teď chcete pokusit znovu přizpůsobit verzi image, aby se vytvořila nová verze stejné image, přeskočte **Tento krok** a přejděte k části [použití Azure image Builder k vytvoření další verze image](image-builder-gallery-update-image-version.md).


Tato akce odstraní vytvořenou bitovou kopii spolu se všemi ostatními soubory prostředků. Před odstraněním prostředků se ujistěte, že jste hotovi s tímto nasazením.

Při odstraňování prostředků Galerie imagí musíte odstranit všechny verze imagí, abyste mohli odstranit definici image, která se používá k jejich vytvoření. Abyste mohli galerii odstranit, musíte nejdřív odstranit všechny definice imagí v galerii.

Odstraňte šablonu tvůrce imagí.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Získat verzi image vytvořenou tvůrcem imagí, vždy se `0.`spustí a pak se odstraní verze image.

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


Odstraňte definici image.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Odstraňte galerii.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Odstraňte skupinu prostředků.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Další kroky

Informace o tom, jak aktualizovat vytvořenou verzi image, najdete v tématu [použití nástroje Azure image Builder k vytvoření další verze image](image-builder-gallery-update-image-version.md).