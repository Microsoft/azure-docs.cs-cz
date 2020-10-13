---
title: Použití Azure image Builder s galerií imagí pro virtuální počítače se systémem Linux (Preview)
description: Vytvářejte image virtuálních počítačů se systémem Linux pomocí nástroje Azure image Builder a galerie sdílených imagí.
author: cynthn
ms.author: cynthn
ms.date: 05/05/2019
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: danis
ms.openlocfilehash: d4715bd8b7a13a5ab53d254ac853ac324440b403
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502609"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery-by-using-azure-cli"></a>Preview: vytvoření image pro Linux a její distribuce do galerie sdílených imagí pomocí Azure CLI

V tomto článku se dozvíte, jak můžete pomocí Tvůrce imagí Azure a rozhraní příkazového řádku Azure vytvořit verzi image v [galerii sdílených imagí](../windows/shared-image-galleries.md)a pak tuto image distribuovat globálně. Můžete to provést také pomocí [Azure PowerShell](../windows/image-builder-gallery.md).


K nakonfigurování image budeme používat šablonu Sample. JSON. Soubor. JSON, který používáme, je tady: [helloImageTemplateforSIG.js](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Pro distribuci image do galerie sdílených imagí šablona používá [sharedImage](image-builder-json.md#distribute-sharedimage) jako hodnotu pro `distribute` oddíl šablony.

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
```

Pokud nevyžadují registraci, spusťte tento příkaz:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
```

## <a name="set-variables-and-permissions"></a>Nastavení proměnných a oprávnění 

Některé informace budeme používat opakovaně, takže vytvoříme některé proměnné, které tyto informace uloží.

Pro verzi Preview podporuje tvůrce imagí jenom vytváření vlastních imagí ve stejné skupině prostředků jako spravovaná zdrojová image. Aktualizujte název skupiny prostředků v tomto příkladu tak, aby byla stejnou skupinou prostředků jako spravovaná zdrojová image.

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

Vytvořte proměnnou pro ID předplatného. Můžete to získat pomocí `az account show | grep id` .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Vytvořte skupinu prostředků.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Vytvoření uživatelsky přiřazené identity a nastavení oprávnění pro skupinu prostředků
Nástroj image Builder použije poskytnutou [identitu uživatele](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) k vložení image do galerie sdílených imagí Azure (SIG). V tomto příkladu vytvoříte definici role Azure, která má podrobné akce k provedení distribuce image do SIG. Definice role se pak přiřadí identitě User-identity.

```bash
# create user assigned identity for image builder to access the storage account where the script is located
identityName=aibBuiUserId$(date +'%s')
az identity create -g $sigResourceGroup -n $identityName

# get identity id
imgBuilderCliId=$(az identity show -g $sigResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$sigResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName

# this command will download an Azure role definition template, and update the template with the parameters specified earlier.
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
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
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>Stažení a konfigurace formátu. JSON

Stáhněte šablonu. JSON a nakonfigurujte ji pomocí proměnných.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>Vytvoření verze image

V této další části se vytvoří verze image v galerii. 

Odešlete konfiguraci image do služby Azure image Builder.

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

Vytvoření image a její replikace do obou oblastí může chvíli trvat. Před přechodem k vytvoření virtuálního počítače počkejte na dokončení této části.


## <a name="create-the-vm"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač z verze image, kterou vytvořila služba Azure image Builder.

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

Měli byste vidět, že obrázek byl přizpůsoben se *zprávou dne* , jakmile se naváže připojení SSH.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se teď chcete pokusit znovu přizpůsobit verzi image, aby se vytvořila nová verze stejné image, přeskočte další kroky a [použijte Azure image Builder k vytvoření další verze image](image-builder-gallery-update-image-version.md).


Tato akce odstraní vytvořenou bitovou kopii spolu se všemi ostatními soubory prostředků. Před odstraněním prostředků se ujistěte, že jste hotovi s tímto nasazením.

Při odstraňování prostředků Galerie imagí musíte odstranit všechny verze imagí, abyste mohli odstranit definici image, která se používá k jejich vytvoření. Abyste mohli galerii odstranit, musíte nejdřív odstranit všechny definice imagí v galerii.

Odstraňte šablonu tvůrce imagí.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Odstranění přiřazení oprávnění, rolí a identity
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

Získat verzi image vytvořenou tvůrcem imagí, vždy se spustí `0.` a pak se odstraní verze image.

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

Přečtěte si další informace o [galeriích sdílených imagí Azure](shared-image-galleries.md).
