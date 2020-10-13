---
title: Použití Azure image Builder pro virtuální počítače se systémem Linux umožňující přístup k existující virtuální síti Azure (Preview)
description: Vytvoření imagí virtuálních počítačů se systémem Linux pomocí Azure image Builder, který umožňuje přístup k existující virtuální síti Azure
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: danis
ms.openlocfilehash: d75d73fcd64917257b850861142e7f4a67da834c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972301"
---
# <a name="use-azure-image-builder-for-linux-vms-allowing-access-to-an-existing-azure-vnet"></a>Použití Azure image Builder pro virtuální počítače se systémem Linux umožňující přístup k existující virtuální síti Azure

V tomto článku se dozvíte, jak můžete pomocí nástroje Azure image Builder vytvořit základní image pro Linux, která má přístup k existujícím prostředkům ve virtuální síti. Virtuální počítač sestavení, který vytvoříte, je nasazený na novou nebo existující virtuální síť, kterou zadáte v rámci vašeho předplatného. Když použijete existující virtuální síť Azure, služba Azure image Builder nevyžaduje připojení k veřejné síti.

> [!IMPORTANT]
> Azure image Builder je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="register-the-features"></a>Registrace funkcí

Nejdřív se musíte zaregistrovat pro službu Azure image Builder. Registrace uděluje službě oprávnění vytvořit, spravovat a odstranit pracovní skupinu prostředků. Tato služba má také práva k přidání prostředků do skupiny, která je potřebná pro sestavení image.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="set-variables-and-permissions"></a>Nastavení proměnných a oprávnění 

Opakovaně budete používat některé informace. Vytvořte některé proměnné pro uložení těchto informací.

```azurecli-interactive
# set your environment variables here!!!!

# destination image resource group
imageResourceGroup=aibImageRG01

# location (see possible locations in main docs)
location=WestUS2

# your subscription
# get the current subID : 'az account show | grep id'
subscriptionID=$(az account show | grep id | tr -d '",' | cut -c7-)

# name of the image to be created
imageName=aibCustomLinuxImg01

# image distribution metadata reference name
runOutputName=aibCustLinManImg01ro


# VNET properties (update to match your existing VNET, or leave as-is for demo)
# VNET name
vnetName=myexistingvnet01
# subnet name
subnetName=subnet01
# VNET resource group name
# NOTE! The VNET must always be in the same region as the AIB service region.
vnetRgName=existingVnetRG
# Existing Subnet NSG Name or the demo will create it
nsgName=aibdemoNsg
```

Vytvořte skupinu prostředků.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="configure-networking"></a>Konfigurace sítí

Pokud nemáte existující VNET\Subnet\NSG, vytvořte ho pomocí následujícího skriptu.

```bash

# Create a resource group

az group create -n $vnetRgName -l $location

# Create VNET

az network vnet create \
    --resource-group $vnetRgName \
    --name $vnetName --address-prefix 10.0.0.0/16 \
    --subnet-name $subnetName --subnet-prefix 10.0.0.0/24

# Create base NSG to simulate an existing NSG

az network nsg create -g $vnetRgName -n $nsgName

az network vnet subnet update \
    --resource-group $vnetRgName \
    --vnet-name $vnetName \
    --name $subnetName \
    --network-security-group $nsgName
    
#  NOTE! The VNET must always be in the same region as the Azure Image Builder service region.
```

### <a name="add-network-security-group-rule"></a>Přidat pravidlo skupiny zabezpečení sítě

Toto pravidlo umožňuje připojení z nástroje pro vyrovnávání zatížení Azure image Builder k virtuálnímu počítači proxy. Port 60001 je pro Linux OSs a port 60000 pro Windows OSs. Proxy virtuální počítač se připojuje k virtuálnímu počítači sestavení pomocí portu 22 pro Linux OSs nebo portu 5986 pro Windows OSs.

```azurecli-interactive
az network nsg rule create \
    --resource-group $vnetRgName \
    --nsg-name $nsgName \
    -n AzureImageBuilderNsgRule \
    --priority 400 \
    --source-address-prefixes AzureLoadBalancer \
    --destination-address-prefixes VirtualNetwork \
    --destination-port-ranges 60000-60001 --direction inbound \
    --access Allow --protocol Tcp \
    --description "Allow Image Builder Private Link Access to Proxy VM"
```

### <a name="disable-private-service-policy-on-subnet"></a>Zakázat zásady pro privátní služby v podsíti

```azurecli-interactive
az network vnet subnet update \
  --name $subnetName \
  --resource-group $vnetRgName \
  --vnet-name $vnetName \
  --disable-private-link-service-network-policies true 
```

Další informace o sítích tvůrce imagí najdete v tématu [Možnosti sítě služby Azure image Builder](image-builder-networking.md).

## <a name="modify-the-example-template-and-create-role"></a>Úprava ukázkové šablony a vytvoření role

```bash
# download the example and configure it with your vars

curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1a_Creating_a_Custom_Linux_Image_on_Existing_VNET/existingVNETLinux.json -o existingVNETLinux.json
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" existingVNETLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" existingVNETLinux.json
sed -i -e "s/<region>/$location/g" existingVNETLinux.json
sed -i -e "s/<imageName>/$imageName/g" existingVNETLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" existingVNETLinux.json

sed -i -e "s/<vnetName>/$vnetName/g" existingVNETLinux.json
sed -i -e "s/<subnetName>/$subnetName/g" existingVNETLinux.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" existingVNETLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json

```

## <a name="set-permissions-on-the-resource-group"></a>Nastavení oprávnění pro skupinu prostředků

Nástroj image Builder použije poskytnutou [identitu uživatele](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) k vložení image do galerie sdílených imagí Azure (SIG). V tomto příkladu vytvoříte definici role Azure, která má podrobné akce k provedení distribuce image do SIG. Definice role se pak přiřadí identitě User-identity.

```bash
# create user assigned identity for image builder
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# update the template
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" existingVNETLinux.json

# make role name unique, to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# update the definitions
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json
```

Místo udělení nižší členitosti a zvýšeného oprávnění tvůrce imagí můžete vytvořit dvě role. Jedna má oprávnění Tvůrce k vytvoření image, druhá umožňuje připojit virtuální počítač buildu a nástroj pro vyrovnávání zatížení do vaší virtuální sítě.

```bash
# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json
az role definition create --role-definition ./aibRoleNetworking.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName
```

Další informace o oprávněních najdete v tématu [Konfigurace oprávnění služby Azure image Builder pomocí Azure CLI](image-builder-permissions-cli.md) nebo [Konfigurace oprávnění služby Azure image Builder pomocí PowerShellu](image-builder-permissions-powershell.md).

## <a name="create-the-image"></a>Vytvoření image

Odešlete konfiguraci image do služby Azure image Builder.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @existingVNETLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01

# Wait approximately 1-3 mins (validation, permissions etc.)
```

Spusťte sestavení image.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n existingVNETLinuxTemplate01 \
     --action Run 

# Wait approximately 15 mins
```

Vytvoření image a její replikace do obou oblastí může chvíli trvat. Před přechodem k vytvoření virtuálního počítače počkejte na dokončení této části.


## <a name="create-the-vm"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač z verze image, kterou vytvořila služba Azure image Builder.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm0001 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

SSH do virtuálního počítače.

```bash
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

Pokud se teď chcete pokusit znovu přizpůsobit verzi image, aby se vytvořila nová verze stejné image, přeskočte další kroky a [pomocí Azure image Builder vytvořte další verzi image](image-builder-gallery-update-image-version.md).


Následující odstraní vytvořenou bitovou kopii spolu se všemi ostatními soubory prostředků. Před odstraněním prostředků se ujistěte, že jste hotovi s tímto nasazením.

Při odstraňování prostředků Galerie imagí musíte odstranit všechny verze imagí, abyste mohli odstranit definici image, která se používá k jejich vytvoření. Abyste mohli galerii odstranit, musíte nejdřív odstranit všechny definice imagí v galerii.

Odstraňte šablonu tvůrce imagí.

```azurecli
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01
```

Odstranění přiřazení oprávnění, rolí a identity
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName


az role definition delete --name "$imageRoleDefName"
az role definition delete --name "$netRoleDefName"

az identity delete --ids $imgBuilderId
```

Odstraňte skupinu prostředků.

```azurecli-interactive
az group delete -n $imageResourceGroup
```

Pokud jste pro tento rychlý Start vytvořili virtuální síť, můžete virtuální síť odstranit, pokud už se nepoužívá.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [galeriích sdílených imagí Azure](shared-image-galleries.md).