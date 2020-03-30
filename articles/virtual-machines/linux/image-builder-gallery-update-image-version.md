---
title: Vytvoření nové verze image virtuálního počítače z existující verze image pomocí Azure Image Builder (preview)
description: Vytvořte novou verzi image virtuálního počítače z existující verze image pomocí Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: 5766e91dc6a17d50c46d396dd8a68d17081e0926
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246802"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Náhled: Vytvoření nové verze image virtuálního počítače z existující verze image pomocí Azure Image Builder

Tento článek ukazuje, jak vzít existující verzi obrázku v [galerii sdílených obrázků](shared-image-galleries.md), aktualizovat ji a publikovat ji jako novou verzi obrázku do galerie.

Ke konfiguraci bitové kopie použijeme ukázkovou šablonu JSON. Soubor JSON, který používáme, je zde: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


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

Pokud jste k vytvoření Galerie sdílených obrázků použili [možnost Vytvořit obrázek a distribuovat](image-builder-gallery.md) je do galerie sdílených obrázků, už jste vytvořili některé proměnné, které potřebujeme. Pokud ne, nastavte některé proměnné, které mají být použity pro tento příklad.

Pro náhled bude tvůrce obrázků podporovat pouze vytváření vlastních irek ve stejné skupině prostředků jako zdrojová spravovaná bitová kopie. Aktualizujte název skupiny prostředků v tomto příkladu tak, aby byl ve stejné skupině prostředků jako zdrojová spravovaná bitová kopie.


```console
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

Vytvořte proměnnou pro ID předplatného. Můžete si to `az account show | grep id`pomocí .

```console
subscriptionID=<Subscription ID>
```

Získejte verzi obrázku, kterou chcete aktualizovat.

```azurecli
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


Pokud již máte vlastní galerii sdílených obrázků a neřídili jste se předchozím příkladem, budete muset přiřazovat oprávnění pro tvůrce obrázků pro přístup ke skupině prostředků, aby měl přístup k galerii.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>Změnit helloImage příklad
Můžete si prohlédnout příklad, který se chystáme použít otevřením souboru .json zde: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) spolu s [odkazem na šablonu image builder .](image-builder-json.md) 


Stáhněte si příklad json a nakonfigurujte jej pomocí proměnných. 

```console
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

Odešlete konfiguraci image do služby Tvůrce obrázků virtuálních počítačů.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Spusťte sestavení bitové kopie.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Před přechodem k dalšímu kroku počkejte, dokud nebude bitová kopie vytvořena, a replikaci.


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

Vytvořte připojení SSH k virtuálnímu virtuálnímu počítačům pomocí veřejné IP adresy virtuálního soudu.

```console
ssh azureuser@<pubIp>
```

Měli byste vidět, že obrázek byl přizpůsoben "Poselství dne", jakmile je vaše připojení SSH navázáno.

```output
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Chcete-li připojení SSH zavřít, zadejte. `exit`

Můžete také uvést verze obrázků, které jsou nyní k dispozici ve vaší galerii.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Další kroky

Další informace o součástech souboru JSON použitého v tomto článku naleznete v [tématu Odkaz na šablonu tvůrce obrázků](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).