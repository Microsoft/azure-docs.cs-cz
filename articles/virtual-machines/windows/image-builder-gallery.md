---
title: Image Builder pro Azure pomocí Galerie obrázků pro virtuální počítače s Windows (preview)
description: Vytváření imagí Windows pomocí Image Builder pro Azure a Galerie obrázků Shared.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-widows
manager: jeconnoc
ms.openlocfilehash: 2453d37720bcf48b95b428cf78c6186de40b31aa
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160107"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Verze Preview: Vytvoření Windows image a distribuovat ji do Galerie Imagí Shared 

V tomto článku se dozvíte, jak můžete použít Azure Image Builder k vytvoření image verze v [Galerie obrázků Shared](shared-image-galleries.md), globálně distribuovat bitovou kopii.

Použijeme šablonu .json konfigurace image. Soubor .json, který se používá, je zde: [helloImageTemplateforWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json). 

Distribuovat bitovou kopii do Galerie Imagí sdílené, šablona používá [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) hodnotu `distribute` část šablony.

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
az provider show -n Microsoft.Compute | grep registrationState
```

Pokud třeba není registrovaný, spusťte následující příkaz:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```

## <a name="set-variables-and-permissions"></a>Oprávnění a nastavení proměnných 

Použijeme některé údaje opakovaně, takže si vytvoříme několik proměnných k ukládání těchto informací. Nahraďte hodnoty pro proměnné, jako je třeba `username` a `vmpassword`, nahraďte svými vlastními informacemi.

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

Vytvoření proměnné pro ID vašeho předplatného. Můžete získat pomocí `az account show | grep id`.

```azurecli-interactive
subscriptionID="Subscription ID"
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
   --publisher corpIT \
   --offer myOffer \
   --sku 2019 \
   --os-type Windows
```


## <a name="download-and-configure-the-json"></a>Stáhnout a nakonfigurovat .json

Stáhněte si šablonu .json a nakonfigurovat proměnných.

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

## <a name="create-the-image-version"></a>Vytvoření image verze

Tato další části se vytvoří verze image v galerii. 

Odeslání konfigurace image do služby Azure Image Builder.

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

Vytváření bitové kopie a replikaci pro obě oblasti může chvíli trvat. Počkejte na dokončení této části se před přechodem k vytvoření virtuálního počítače.


## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Vytvoření virtuálního počítače z image verze, který byl vytvořen tvůrcem Image Azure.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm001 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```


## <a name="verify-the-customization"></a>Ověřte, přizpůsobení
Vytvoření připojení vzdálené plochy k virtuálnímu počítači pomocí uživatelského jména a hesla, které jste nastavili při vytváření virtuálního počítače. V tomto virtuálním počítači otevřete příkazový řádek a zadejte:

```console
dir c:\
```

Zobrazí se adresář s názvem `buildActions` , který jste vytvořili během přizpůsobení bitové kopie.


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete teď to zkuste znovu přizpůsobení verze image k vytvoření nové verze stejnou bitovou kopii, **tento krok přeskočit** a přejděte k [Image Builder pro použití Azure k vytvoření jinou verzi image](image-builder-gallery-update-image-version.md).


Tato akce odstraní obrázku, který byl vytvořen, spolu s všechny další soubory prostředků. Ujistěte se, že jste hotovi s tímto nasazením před odstraněním zdroje.

Při odstraňování prostředků Galerie obrázků, třeba všechny verze image odstranit před odstraněním k definici image použitých pro jejich vytvoření. Pokud chcete odstranit galerii, musíte nejprve jste odstranili všechny definice image v galerii.

Odstraňte šablonu image builder.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
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

## <a name="next-steps"></a>Další kroky

Zjistěte, jak aktualizovat verzi image, který jste vytvořili, najdete v článku [Image Builder pro použití Azure k vytvoření jinou verzi image](image-builder-gallery-update-image-version.md).