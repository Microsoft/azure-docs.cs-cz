---
title: Vytvoří novou verzi image virtuálního počítače z existující verze Image pomocí Azure image Builder (Preview).
description: Vytvoří novou verzi image virtuálního počítače z existující verze Image pomocí Azure image Builder v systému Linux.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: danis
ms.openlocfilehash: 0887051ffa396f1eac8bc00dc2437b8e92bec45a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101695630"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder-in-linux"></a>Preview: vytvoření nové verze image virtuálního počítače z existující verze Image pomocí Azure image Builder v systému Linux

V tomto článku se dozvíte, jak v [galerii sdílených imagí](../shared-image-galleries.md)získat existující verzi image, aktualizovat ji a publikovat jako novou verzi image do galerie.

K nakonfigurování image budeme používat šablonu Sample. JSON. Soubor. JSON, který používáme, je tady: [helloImageTemplateforSIGfromSIG.js](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


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
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Network | grep registrationState
```

Pokud nevyžadují registraci, spusťte tento příkaz:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```


## <a name="set-variables-and-permissions"></a>Nastavení proměnných a oprávnění

Pokud jste k vytvoření galerie sdílených imagí použili [Vytvoření image a distribuci do galerie sdílených imagí](image-builder-gallery.md) , už jste vytvořili některé z proměnných, které potřebujeme. V takovém případě nastavte některé proměnné, které se mají použít v tomto příkladu.


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

Vytvořte proměnnou pro ID předplatného. Můžete to získat pomocí `az account show | grep id` .

```console
subscriptionID=<Subscription ID>
```

Získejte verzi image, kterou chcete aktualizovat.

```azurecli
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Vytvoření uživatelsky přiřazené identity a nastavení oprávnění pro skupinu prostředků
Vzhledem k tomu, že jste v předchozím příkladu nastavili identitu uživatele, stačí získat ID prostředku, takže se tato akce připojí k šabloně.

```azurecli-interactive
#get identity used previously
imgBuilderId=$(az identity list -g $sigResourceGroup --query "[?contains(name, 'aibBuiUserId')].id" -o tsv)
```

Pokud už máte vlastní galerii sdílených imagí a nepoužili jste předchozí příklad, budete muset pro tvůrce imagí přiřadit oprávnění pro přístup ke skupině prostředků, aby měl přístup k této galerii. Podívejte se prosím na příklad postupu v části [Vytvoření image a distribuce do galerie sdílených imagí](image-builder-gallery.md) .


## <a name="modify-helloimage-example"></a>Příklad úpravy helloImage
Můžete si prohlédnout příklad, který se chystáme použít otevřením souboru. JSON tady: [helloImageTemplateforSIGfromSIG.jsna](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) společně s [odkazem na šablonu pro tvůrce imagí](image-builder-json.md). 


Stáhněte si příklad. JSON a nakonfigurujte ho pomocí proměnných. 

```console
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>Vytvoření image

Odešlete konfiguraci image do služby tvůrce imagí virtuálních počítačů.

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

Před přechodem k dalšímu kroku počkejte, než se obrázek sestaví a provede replikace.


## <a name="create-the-vm"></a>Vytvoření virtuálního počítače

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

```console
ssh azureuser@<pubIp>
```

Měli byste vidět, že image byla upravena s "zprávou o dni", jakmile se naváže připojení SSH.

```output
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Zadejte `exit` pro zavření připojení SSH.

Můžete také zobrazit seznam verzí imagí, které jsou nyní k dispozici v galerii.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Další kroky

Další informace o součástech souboru. JSON používaných v tomto článku najdete v tématu Referenční dokumentace k [šablonám tvůrce imagí](../linux/image-builder-json.md).
