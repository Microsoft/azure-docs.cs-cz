---
title: Vytvoření nové verze image z existující verze image pomocí Azure Image Builder (preview)
description: Vytvořte novou verzi image virtuálního počítače z existující verze image pomocí Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: how-to
ms.service: virtual-machines-windows
ms.openlocfilehash: 766e7d5c4151000a582bcf07d80b89af3b7d8a65
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869544"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Náhled: Vytvoření nové verze image virtuálního počítače z existující verze image pomocí Azure Image Builder

Tento článek ukazuje, jak vzít existující verzi obrázku v [galerii sdílených obrázků](shared-image-galleries.md), aktualizovat ji a publikovat ji jako novou verzi obrázku do galerie.

Ke konfiguraci bitové kopie použijeme ukázkovou šablonu JSON. Soubor JSON, který používáme, je zde: [helloImageTemplateforSIGfromWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 

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
az provider show -n Microsoft.Compute | grep registrationState
```

Pokud neříkají registrované, spusťte následující:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```


## <a name="set-variables-and-permissions"></a>Nastavení proměnných a oprávnění

Pokud jste k vytvoření Galerie sdílených obrázků použili [možnost Vytvořit obrázek a distribuovat](image-builder-gallery.md) je do galerie sdílených obrázků, už jste vytvořili proměnné, které potřebujeme. Pokud ne, nastavte některé proměnné, které mají být použity pro tento příklad.

Pro náhled bude tvůrce obrázků podporovat pouze vytváření vlastních irek ve stejné skupině prostředků jako zdrojová spravovaná bitová kopie. Aktualizujte název skupiny prostředků v tomto příkladu tak, aby byl ve stejné skupině prostředků jako zdrojová spravovaná bitová kopie.

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
username="user name for the VM"
vmpassword="password for the VM"
```

Vytvořte proměnnou pro ID předplatného. Můžete si to `az account show | grep id`pomocí .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Získejte verzi obrázku, kterou chcete aktualizovat.

```azurecli-interactive
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
Můžete si prohlédnout příklad, který se chystáme použít otevřením souboru .json zde: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) spolu s [odkazem na šablonu image builder .](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 


Stáhněte si příklad json a nakonfigurujte jej pomocí proměnných. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>Vytvoření image

Odešlete konfiguraci image do služby Tvůrce obrázků virtuálních počítačů.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Spusťte sestavení bitové kopie.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Před přechodem k dalšímu kroku počkejte, dokud nebude bitová kopie vytvořena, a replikaci.


## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>Ověření vlastního nastavení
Vytvořte připojení ke vzdálené ploše k virtuálnímu počítači pomocí uživatelského jména a hesla, které jste nastavili při vytváření virtuálního počítače. Uvnitř virtuálního provozu otevřete výzvu cmd a zadejte:

```console
dir c:\
```

Nyní byste měli vidět dva adresáře:
- `buildActions`který byl vytvořen v první verzi obrázku.
- `buildActions2`který byl vytvořen jako součást aktualizace první verze bitové kopie k vytvoření druhé verze bitové kopie.


## <a name="next-steps"></a>Další kroky

Další informace o součástech souboru JSON použitého v tomto článku naleznete v [tématu Odkaz na šablonu tvůrce obrázků](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).