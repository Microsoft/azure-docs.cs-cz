---
title: Vytvoření image virtuálního počítače a použití spravované identity přiřazené uživatelem pro přístup k souborům v Azure Storage (Preview)
description: Pomocí nástroje Azure image Builder můžete vytvořit image virtuálního počítače, která bude mít přístup k souborům uloženým v Azure Storage pomocí uživatelsky přiřazené spravované identity.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: f3990037d75f9f77eaedc7ec4049f14814216d9c
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944966"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Vytvoření image a použití spravované identity přiřazené uživatelem pro přístup k souborům v Azure Storage 

Azure image Builder podporuje používání skriptů nebo kopírování souborů z několika umístění, jako je GitHub a Azure Storage atd. Aby je bylo možné použít, musí být externě přístupné pro Azure image Builder, ale můžete chránit Azure Storage objekty BLOB pomocí tokenů SAS.

V tomto článku se dozvíte, jak vytvořit přizpůsobenou Image pomocí Tvůrce imagí virtuálních počítačů Azure, kde služba pro přizpůsobení image používá [spravovanou identitu přiřazenou uživatelem](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pro přístup k souborům ve službě Azure Storage, a to bez toho, aby bylo možné soubory zpřístupnit, nebo nastavit tokeny SAS.

V následujícím příkladu vytvoříte dvě skupiny prostředků, jednu se použije pro vlastní image a druhá bude hostovat účet Azure Storage, který obsahuje soubor skriptu. To simuluje scénář reálného života, kde můžete mít artefakty sestavení nebo soubory obrázků v různých účtech úložiště mimo tvůrce imagí. Vytvoříte identitu přiřazenou uživatelem a potom udělíte oprávnění ke čtení souboru skriptu, ale nenastavíte žádný veřejný přístup k tomuto souboru. Pak použijete modul úprav prostředí ke stažení a spuštění tohoto skriptu z účtu úložiště.


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

az provider show -n Microsoft.Storage | grep registrationState
```

Pokud nevyžadují registraci, spusťte tento příkaz:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Některé informace budeme používat opakovaně, takže vytvoříme některé proměnné, které tyto informace uloží.


```azurecli-interactive
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

Vytvořte proměnnou pro ID předplatného. Můžete to získat pomocí `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Vytvořte skupiny prostředků pro image i pro úložiště skriptů.

```azurecli-interactive
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


Vytvořte úložiště a zkopírujte do něho ukázkový skript z GitHubu.

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



Udělte tvůrci imagí oprávnění k vytváření prostředků ve skupině prostředků image. Hodnota `--assignee` je ID registrace aplikace pro službu Tvůrce imagí. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

Vytvořte identitu a přiřaďte oprávnění k účtu úložiště skriptů. Další informace najdete v tématu [spravovaná identita přiřazená uživatelem](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

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


## <a name="modify-the-example"></a>Úprava příkladu

Stáhněte soubor example. JSON a nakonfigurujte ho pomocí proměnných, které jste vytvořili.

```azurecli-interactive
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

Odešlete konfiguraci image do služby Azure image Builder.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Spusťte sestavení image.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Počkejte na dokončení sestavení. Tato možnost může trvat přibližně 15 minut.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Z image vytvořte virtuální počítač. 

```bash
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Po vytvoření virtuálního počítače spusťte relaci SSH s virtuálním počítačem.

```azurecli-interactive
ssh aibuser@<publicIp>
```

Měli byste vidět, že obrázek byl přizpůsoben se zprávou dne, jakmile se naváže připojení SSH.

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Vyčištění

Až budete hotovi, můžete prostředky odstranit, pokud už je nepotřebujete.

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

Pokud máte při práci s nástrojem Azure image Builder problémy, přečtěte si téma [řešení potíží](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json).