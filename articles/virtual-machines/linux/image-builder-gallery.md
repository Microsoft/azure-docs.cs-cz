---
title: Image Builder pro Azure pomocí Galerie obrázků pro virtuální počítače s Linuxem (preview)
description: Vytvoření imagí Linuxu pomocí Image Builder pro Azure a Galerie obrázků Shared.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: e9a8a30d9f5f170073c0ad671a248703b1078864
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159492"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Verze Preview: Vytvořit image Linuxu a distribuovat ji do Galerie Imagí Shared 

Tento článek ukazuje, jak můžete použít Azure Image Builder k vytvoření image verze v [Galerie obrázků Shared](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/shared-image-galleries), globálně distribuovat bitovou kopii.


Použijeme Ukázková šablona .json konfigurace image. Soubor .json, který se používá, je zde: [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Distribuovat bitovou kopii do Galerie Imagí sdílené, šablona používá [sharedImage](image-builder-json.md#distribute-sharedimage) hodnotu `distribute` část šablony.

> [!IMPORTANT]
> Image Builder pro Azure je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Registrace funkce
Pokud chcete použít Image Builder pro Azure ve verzi preview, budete muset registrovat novou funkci.

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

Pokud třeba není registrovaný, spusťte následující příkaz:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="set-variables-and-permissions"></a>Oprávnění a nastavení proměnných 

Použijeme některé údaje opakovaně, takže si vytvoříme několik proměnných k ukládání těchto informací.

Ve verzi Preview image builder pro bude podporovat jenom vytváření vlastních imagí ve stejné skupině prostředků jako spravované image zdroje. Aktualizujte název skupiny prostředků v tomto příkladu bude stejné skupině prostředků jako zdroj spravované image.

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

Vytvoření proměnné pro ID vašeho předplatného. Můžete získat pomocí `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Vytvořte skupinu prostředků.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Oprávnění Azure Image Builder vytvářet prostředky v příslušné skupině prostředků. `--assignee` Hodnota je ID registrace aplikace pro Image Builder pro službu. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Vytvořit definici image a Galerie

Vytvoření galerie obrázků. 

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Vytvořte definici image.

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


## <a name="download-and-configure-the-json"></a>Stáhnout a nakonfigurovat .json

Stáhněte si šablonu .json a nakonfigurovat proměnných.

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

## <a name="create-the-image-version"></a>Vytvoření image verze

Tato další části se vytvoří verze image v galerii. 

Odeslání konfigurace image do služby Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Spusťte sestavení image.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Vytváření bitové kopie a replikaci pro obě oblasti může chvíli trvat. Počkejte na dokončení této části se před přechodem k vytvoření virtuálního počítače.


## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Vytvoření virtuálního počítače z image verze, který byl vytvořen tvůrcem Image Azure.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Připojte přes SSH k virtuálnímu počítači.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

Byste měli vidět na obrázku byl přizpůsoben *zpráva dne* poté, co vaše připojení SSH!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete teď to zkuste znovu přizpůsobení verze image k vytvoření nové verze stejnou bitovou kopii, přeskočte další kroky a přejít k [Image Builder pro použití Azure k vytvoření jinou verzi image](image-builder-gallery-update-image-version.md).


Tato akce odstraní obrázku, který byl vytvořen, spolu s všechny další soubory prostředků. Ujistěte se, že jste hotovi s tímto nasazením před odstraněním zdroje.

Při odstraňování prostředků Galerie obrázků, třeba všechny verze image odstranit před odstraněním k definici image použitých pro jejich vytvoření. Pokud chcete odstranit galerii, musíte nejprve jste odstranili všechny definice image v galerii.

Odstraňte šablonu image builder.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Získat verzi image vytvořené image builder, to vždy začíná `0.`a pak odstraňte verze image

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

Odstraníte skupinu prostředků.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Další postup

Další informace o [Galerie obrázků Shared Azure](shared-image-galleries.md).