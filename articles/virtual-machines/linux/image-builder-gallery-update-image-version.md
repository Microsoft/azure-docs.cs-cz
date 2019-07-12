---
title: Vytvořit novou verzi image z existující verze image pomocí Image Builder pro Azure (preview)
description: Vytvořte novou verzi image z existující verze image pomocí Image Builder pro Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 9155f6fc1243f0d2e4d63f2718ccfd6846ebbc50
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671503"
---
# <a name="preview-create-a-new-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Verze Preview: Vytvořit novou verzi image z existující verze image pomocí Image Builder pro Azure

Tento článek ukazuje, jak využít stávající verze image [Galerie obrázků Shared](shared-image-galleries.md), aktualizujte ji a publikovat jako novou verzi image v galerii.

Použijeme Ukázková šablona .json konfigurace image. Soubor .json, který se používá, je zde: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


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

Pokud jste použili [vytvořit bitovou kopii a distribuovat do Galerie Imagí Shared](image-builder-gallery.md) vytvoření Galerie obrázků sdílené, jste už vytvořili určité proměnné potřebujeme. Pokud ne, nastavte prosím některé proměnné používané v tomto příkladu.

Ve verzi Preview image builder pro bude podporovat jenom vytváření vlastních imagí ve stejné skupině prostředků jako spravované image zdroje. Aktualizujte název skupiny prostředků v tomto příkladu bude stejné skupině prostředků jako zdroj spravované image.


```azurecli-interactive
# Resource group name 
sigResourceGroup=ibLinuxGalleryRG
# Gallery location 
location=westus2
# Additional region to replicate the image version to 
additionalregion=eastus
# Name of the shared image gallery 
sigName=myIbGallery
# Name of the image definition to use
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibSIGLinuxUpdate
```

Vytvoření proměnné pro ID vašeho předplatného. Můžete získat pomocí `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Získáte verzi image, kterou chcete aktualizovat.

```
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


Pokud už máte vlastní galerie obrázků Shared a neřídil předchozí příklad, musíte přiřadit oprávnění pro Image Builder pro přístup k skupinu prostředků, takže může přistupovat k galerii.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>Upravte příklad helloImage
Můžete zkontrolovat v příkladu jsme se chystáte použít tak, že otevřete soubor .json tady: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) spolu s [referenčními informacemi k šablonám Image Builder](image-builder-json.md). 


Stažení příkladu JSON při použití a nakonfigurovat proměnných. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>Vytvoření image

Odeslání konfigurace image do služby Tvůrce Image virtuálního počítače.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Spusťte sestavení image.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Počkejte, než je sestavený na obrázku a replikace, než budete pokračovat k dalšímu kroku.


## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Vytvořte připojení SSH k virtuálnímu počítači pomocí veřejné IP adresy virtuálního počítače.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Měli byste vidět, že image byla přizpůsobený "zpráva dne" poté, co vaše připojení SSH.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Typ `exit` pro ukončení připojení SSH.

Můžete také zařadit verze image, které jsou teď dostupné v galerii.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Další postup

Další informace o součástech soubor .json, používané v tomto článku najdete v tématu [Image builder referenčními informacemi k šablonám](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).