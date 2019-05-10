---
title: Vytvořit image virtuálního počítače a pomocí spravované identity přiřazené uživateli přístup k souborům ve službě Azure Storage (preview)
description: Vytvoření image virtuálního počítače pomocí Image Builder pro Azure, s přístupem k souborům uloženým ve službě Azure Storage pomocí uživatelsky přiřazené identity spravované.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: b0a6c016b2be12ac6686b3748b4b16281899323e
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511063"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Vytvoření image a pomocí spravované identity přiřazené uživateli přístup k souborům ve službě Azure Storage 

Azure Image Builder podporuje pomocí skriptů nebo kopírování souborů z více míst, jako jsou GitHub a Azure storage atd. Pokud chcete použít, musí nebyla zvenku přístupný pro Image Builder pro Azure, ale můžete ochránit objektů BLOB Azure Storage pomocí tokeny SAS.

Tento článek popisuje, jak vytvořit vlastní image pomocí Image Builder Azure virtuální počítač, ve kterém bude služba používat [uživatelsky přiřazené Identity spravované](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pro přístup k souborům ve službě Azure storage pro přizpůsobení image bez nutnosti provádět soubory veřejně přístupný nebo při nastavování tokeny SAS.

V následujícím příkladu se vytvoří dvě skupiny prostředků, jeden se použije pro vlastní image a druhá bude hostovat účet úložiště Azure, která obsahuje soubor skriptu, který. To simuluje reálné scénáře, kdy můžete mít artefakty sestavení nebo soubory obrázků do různých účtů úložiště, mimo Image Builder. Musíte vytvořit uživatelsky přiřazené identity a udělení, která oprávnění ke čtení v souboru skriptu, ale nebude nastavíte veřejný přístup do tohoto souboru. Pak použijete modifikátor prostředí si stáhněte a spusťte tento skript z účtu úložiště.


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


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Použijeme některé údaje opakovaně, takže si vytvoříme několik proměnných k ukládání těchto informací.


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

Vytvoření proměnné pro ID vašeho předplatného. Můžete získat pomocí `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Vytvoření skupiny prostředků pro skript úložiště i image.

```azurecli-interactive
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


Vytvořit úložiště a do něj zkopírovat ukázkový skript z Githubu.

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



Zadejte Image Builder oprávnění k vytváření prostředků ve skupině prostředků bitové kopie. `--assignee` Hodnota je ID registrace aplikace pro Image Builder pro službu. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>Vytvoření uživatelsky přiřazené identity spravované

Vytvořte identitu a přiřaďte oprávnění pro účet úložiště skriptu. Další informace najdete v tématu [Identity spravované User-Assigned](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

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


## <a name="modify-the-example"></a>Upravte příklad

Stáhněte si ukázkový soubor .json a nakonfigurovat proměnné, které jste vytvořili.

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

Odeslání konfigurace image do služby Azure Image Builder.

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

Počkejte na dokončení sestavení. Může to trvat přibližně 15 minut.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače z image. 

```bash
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Po vytvoření virtuálního počítače, spusťte s virtuálním Počítačem relaci SSH.

```azurecli-interactive
ssh aibuser@<publicIp>
```

Měli byste vidět, že byl upraven na obrázku a zobrazí se zpráva dne, jakmile se naváže připojení SSH.

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Vyčištění

Až budete hotovi, můžete odstranit prostředky, pokud už nepotřebujete.

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Další postup

Pokud máte jakékoli potíže při práci s Azure Image Builder, přečtěte si [Poradce při potížích s](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json).