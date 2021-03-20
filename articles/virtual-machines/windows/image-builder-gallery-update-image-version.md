---
title: Vytvoří novou verzi image z existující verze Image pomocí Azure image Builder (Preview).
description: Vytvoří novou verzi image virtuálního počítače z existující verze Image pomocí Azure image Builder ve Windows.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: 0a53e8de8dd832e793ae12034c96ce9fe634ed7a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694100"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder-in-windows"></a>Preview: vytvoření nové verze image virtuálního počítače z existující verze Image pomocí Azure image Builder ve Windows

V tomto článku se dozvíte, jak v [galerii sdílených imagí](../shared-image-galleries.md)získat existující verzi image, aktualizovat ji a publikovat jako novou verzi image do galerie.

K nakonfigurování image budeme používat šablonu Sample. JSON. Soubor. JSON, který používáme, je tady: [helloImageTemplateforSIGfromWinSIG.js](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 

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

Pokud jste k vytvoření galerie sdílených imagí použili [vytvořit image a distribuovat ji do galerie sdílených imagí](image-builder-gallery.md) , už máte vytvořené proměnné, které potřebujeme. V takovém případě nastavte některé proměnné, které se mají použít v tomto příkladu.

Pro verzi Preview podporuje tvůrce imagí jenom vytváření vlastních imagí ve stejné skupině prostředků jako spravovaná zdrojová image. Aktualizujte název skupiny prostředků v tomto příkladu tak, aby byla stejnou skupinou prostředků jako spravovaná zdrojová image.

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

Vytvořte proměnnou pro ID předplatného. Můžete to získat pomocí `az account show | grep id` .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Získejte verzi image, kterou chcete aktualizovat.

```azurecli-interactive
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
Můžete si prohlédnout příklad, který se chystáme použít otevřením souboru. JSON tady: [helloImageTemplateforSIGfromSIG.jsna](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) společně s [odkazem na šablonu pro tvůrce imagí](../linux/image-builder-json.md). 


Stáhněte si příklad. JSON a nakonfigurujte ho pomocí proměnných. 

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>Vytvoření image

Odešlete konfiguraci image do služby tvůrce imagí virtuálních počítačů.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Spusťte sestavení image.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Před přechodem k dalšímu kroku počkejte, než se obrázek sestaví a provede replikace.


## <a name="create-the-vm"></a>Vytvoření virtuálního počítače

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
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

Nyní byste měli vidět dva adresáře:
- `buildActions` který byl vytvořen v první verzi bitové kopie.
- `buildActions2` který byl vytvořen jako součást aktualizace první verze image, aby se vytvořila druhá verze image.


## <a name="next-steps"></a>Další kroky

Další informace o součástech souboru. JSON používaných v tomto článku najdete v tématu Referenční dokumentace k [šablonám tvůrce imagí](../linux/image-builder-json.md).
