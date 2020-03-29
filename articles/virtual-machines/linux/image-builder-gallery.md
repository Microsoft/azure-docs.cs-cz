---
title: Použití Azure Image Builder s galerií obrázků pro virtuální počítače s Linuxem (preview)
description: Vytvářejte image virtuálních počítačů s Linuxem pomocí Azure Image Builder a Sdílené galerie obrázků.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: bf1dca61ec6b39e52d4f76c1c77cd3def6973ab8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945023"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Náhled: Vytvoření bitové kopie Linuxu a jeho distribuce do galerie sdílených obrázků 

Tento článek ukazuje, jak můžete použít Azure Image Builder a Azure CLI k vytvoření verze image v [galerii sdílených bitových obrázků](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)a pak distribuovat image globálně. Můžete to udělat také pomocí [Azure PowerShellu](../windows/image-builder-gallery.md).


Ke konfiguraci bitové kopie použijeme ukázkovou šablonu JSON. Soubor .json, který používáme, je zde: [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Chcete-li distribuovat obraz do galerie sdílených obrázků, šablona `distribute` používá [sharedImage](image-builder-json.md#distribute-sharedimage) jako hodnotu pro část šablony.

> [!IMPORTANT]
> Azure Image Builder je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Registrace funkcí
Chcete-li během náhledu používat Azure Image Builder, musíte zaregistrovat novou funkci.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Zkontrolujte stav registrace funkce.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Zkontrolujte svou registraci.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Pokud neříkají registrované, spusťte následující:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="set-variables-and-permissions"></a>Nastavení proměnných a oprávnění 

Budeme používat některé informace opakovaně, takže vytvoříme některé proměnné pro ukládání těchto informací.

Pro náhled bude tvůrce obrázků podporovat pouze vytváření vlastních irek ve stejné skupině prostředků jako zdrojová spravovaná bitová kopie. Aktualizujte název skupiny prostředků v tomto příkladu tak, aby byl ve stejné skupině prostředků jako zdrojová spravovaná bitová kopie.

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

Vytvořte proměnnou pro ID předplatného. Můžete si to `az account show | grep id`pomocí .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Vytvořte skupinu prostředků.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Udělit Azure Image Builder oprávnění k vytváření prostředků v této skupině prostředků. Hodnota `--assignee` je ID registrace aplikace pro službu Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Vytvoření definice obrazu a galerie

Chcete-li používat Tvůrce obrázků se sdílenou galerií obrázků, musíte mít existující galerii obrázků a definici obrázků. Image Builder nebude vytvářet galerii obrázků a definici obrazu pro vás.

Pokud ještě nemáte galerii a definici obrázku, začněte jejich vytvořením. Nejprve vytvořte galerii obrázků.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Potom vytvořte definici obrázku.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>Stažení a konfigurace souboru JSON

Stáhněte si šablonu json a nakonfigurujte ji pomocí proměnných.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>Vytvoření verze obrázku

Tato další část vytvoří obrazovou verzi v galerii. 

Odešlete konfiguraci image do služby Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Spusťte sestavení bitové kopie.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Vytvoření bitové kopie a její replikace do obou oblastí může chvíli trvat. Počkejte, až se tato část dokončí, než přejdeme k vytvoření virtuálního počítače.


## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Vytvořte virtuální počítač z verze image, kterou vytvořil Azure Image Builder.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

SSH do virtuálního počítače.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

Měli byste vidět, že obraz byl přizpůsoben *zprávou dne,* jakmile je vaše připojení SSH navázáno!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud teď chcete zkusit znovu přizpůsobit verzi image a vytvořit novou verzi stejné bitové kopie, přeskočte další kroky a [pokračujte v použití Azure Image Builder k vytvoření jiné verze bitové kopie](image-builder-gallery-update-image-version.md).


Tím odstraníte obrázek, který byl vytvořen, spolu se všemi ostatními soubory prostředků. Před odstraněním prostředků se ujistěte, že jste s tímto nasazením hotová.

Při odstraňování prostředků galerie obrázků je třeba před odstraněním definice obrazu použité k jejich vytvoření odstranit všechny verze bitové kopie. Chcete-li odstranit galerii, musíte nejprve odstranit všechny definice obrázků v galerii.

Odstraňte šablonu tvůrce obrázků.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Získejte verzi obrazu vytvořenou tvůrcem `0.`obrázků, která vždy začíná na , a poté odstraňte verzi bitové kopie

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


Odstraňte definici obrázku.

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

Další informace o [galeriích sdílených bitových obrázků Azure](shared-image-galleries.md).