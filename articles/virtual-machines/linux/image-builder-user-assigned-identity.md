---
title: Vytvoření image virtuálního počítače a použití spravované identity přiřazené uživateli pro přístup k souborům ve Službě Azure Storage (preview)
description: Vytvořte image virtuálního počítače pomocí Azure Image Builder, který může přistupovat k souborům uloženým v Azure Storage pomocí uživatelem přiřazené spravované identity.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: 27f4073efc8647d331faa14afbda0e15f92b8d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060749"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Vytvoření bitové kopie a použití spravované identity přiřazené uživateli pro přístup k souborům ve službě Azure Storage 

Azure Image Builder podporuje používání skriptů nebo kopírování souborů z více umístění, jako je GitHub a úložiště Azure atd. Chcete-li je použít, musí být externě přístupné pro Azure Image Builder, ale můžete chránit objekty BLOB služby Azure Storage pomocí tokenů SAS.

Tento článek ukazuje, jak vytvořit přizpůsobenou image pomocí Azure VM Image Builder, kde služba bude používat [uživatelem přiřazenou spravovanou identitu](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pro přístup k souborům v úložišti Azure pro přizpůsobení image, aniž byste museli soubory veřejně přístupné nebo nastavení tokenů SAS.

V níže uvedeném příkladu vytvoříte dvě skupiny prostředků, jedna se použije pro vlastní bitovou kopii a druhá bude hostovat účet úložiště Azure, který obsahuje soubor skriptu. To simuluje scénář reálného života, kde můžete mít artefakty sestavení nebo obrazové soubory v různých účtech úložiště mimo Image Builder. Vytvoříte identitu přiřazenou uživateli a poté udělíte oprávnění ke čtení souboru skriptu, ale nenastavíte žádný veřejný přístup k tomuto souboru. Potom použijete úpravce prostředí ke stažení a spuštění tohoto skriptu z účtu úložiště.


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


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Budeme používat některé informace opakovaně, takže vytvoříme některé proměnné pro ukládání těchto informací.


```console
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

Vytvořte proměnnou pro ID předplatného. Můžete si to `az account show | grep id`pomocí .

```console
subscriptionID=<Your subscription ID>
```

Vytvořte skupiny prostředků pro obraz i úložiště skriptů.

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


Vytvořte úložiště a zkopírujte do něj ukázkový skript z GitHubu.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```



Udělit Tvůrce obrázků oprávnění k vytváření prostředků ve skupině prostředků image. Hodnota `--assignee` je ID registrace aplikace pro službu Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>Vytvořit spravovanou identitu přiřazenou uživateli

Vytvořte identitu a přiřaďte oprávnění pro účet úložiště skriptů. Další informace naleznete [v tématu Spravovaná identita přiřazená uživatelem](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

```azurecli-interactive
# Create the user assigned identity 
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName
# assign the identity permissions to the storage account, so it can read the script blob
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
# create the user identity URI
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName
```


## <a name="modify-the-example"></a>Změna příkladu

Stáhněte si ukázkový soubor JSON a nakonfigurujte jej pomocí proměnných, které jste vytvořili.

```console
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>Vytvoření image

Odešlete konfiguraci image do služby Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Spusťte sestavení bitové kopie.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Počkejte na dokončení sestavení. To může trvat asi 15 minut.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Vytvořte virtuální ho svitek z obrázku. 

```azurecli
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Po vytvoření virtuálního virtuálního montu spusťte relaci SSH s virtuálním virtuálním ms.

```console
ssh aibuser@<publicIp>
```

Měli byste vidět, že obraz byl přizpůsoben zprávou dne, jakmile je vaše připojení SSH navázáno!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Vyčištění

Po dokončení můžete odstranit prostředky, pokud již nejsou potřeba.

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Další kroky

Pokud máte nějaké problémy s prací s Azure Image Builder, najdete [v tématu Řešení potíží](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json).