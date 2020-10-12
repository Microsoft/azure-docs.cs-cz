---
title: Konfigurace oprávnění služby Azure image Builder pomocí PowerShellu
description: Konfigurace požadavků pro službu Azure VM Image Builder, včetně oprávnění a oprávnění, pomocí PowerShellu
author: danielsollondon
ms.author: danis
ms.date: 05/06/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: cfe3efc77e065ac3685b72d0eab501034609b59b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88068156"
---
# <a name="configure-azure-image-builder-service-permissions-using-powershell"></a>Konfigurace oprávnění služby Azure image Builder pomocí PowerShellu

Služba Azure image Builder vyžaduje před vytvořením image konfiguraci oprávnění a oprávnění. Následující části podrobně popisují, jak nakonfigurovat možné scénáře pomocí prostředí PowerShell.

> [!IMPORTANT]
> Azure image Builder je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="register-the-features"></a>Registrace funkcí

Nejdřív se musíte zaregistrovat pro službu Azure image Builder. Registrace uděluje službě oprávnění vytvořit, spravovat a odstranit pracovní skupinu prostředků. Tato služba má také práva k přidání prostředků do skupiny, která je potřebná pro sestavení image.

```powershell-interactive
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem Azure

Azure image Builder vyžaduje, abyste vytvořili [spravovanou identitu přiřazenou uživatelem Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Azure image Builder používá spravovanou identitu přiřazenou uživatelem ke čtení imagí, zápisu imagí a přístupu k účtům Azure Storage. Udělíte oprávnění identitě v rámci svého předplatného ke konkrétním akcím.

> [!NOTE]
> V minulosti používal Azure image Builder hlavní název služby (SPN) Azure image Builder k udělení oprávnění skupinám prostředků imagí. Používání hlavního názvu služby (SPN) bude zastaralé. Místo toho použijte spravovanou identitu přiřazenou uživatelem.

Následující příklad ukazuje, jak vytvořit spravovanou identitu přiřazenou uživateli Azure. Chcete-li nastavit proměnné, nahraďte nastavení zástupných symbolů.

| Nastavení | Popis |
|---------|-------------|
| \<Resource group\> | Skupina prostředků, ve které se má vytvořit spravovaná identita přiřazená uživatelem |

```powershell-interactive
## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

$parameters = @{
    Name = 'aibIdentity'
    ResourceGroupName = '<Resource group>'
}
# create identity
New-AzUserAssignedIdentity @parameters
```

Další informace o identitách přiřazených uživateli Azure najdete v dokumentaci [spravované identity přiřazené uživatelem Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) , jak vytvořit identitu.

## <a name="allow-image-builder-to-distribute-images"></a>Povolení distribuce imagí v Tvůrci imagí

Aby mohla služba Azure image Builder distribuovat Image (spravované image/sdílená Galerie imagí), musí do těchto skupin prostředků vkládat image. Pokud chcete přidělit požadovaná oprávnění, musíte vytvořit spravovanou identitu přiřazenou uživatelem a udělit jí oprávnění pro skupinu prostředků, ve které je image vytvořená. Azure **Image Builder nemá** oprávnění pro přístup k prostředkům v jiných skupinách prostředků v rámci předplatného. Je nutné provést explicitní akce, aby byl přístup povolen, aby nedocházelo k selhání sestavení.

K distribuci imagí nemusíte oprávnění přispěvatele spravované identity přidělená uživateli udělit skupině prostředků. Spravovaná identita přiřazená uživatelem ale potřebuje následující `Actions` oprávnění Azure ve skupině prostředků distribuce:

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

Pokud distribuujete do galerie sdílených imagí, budete také potřebovat:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>Oprávnění k přizpůsobení existujících imagí

Aby mohl Azure image Builder vytvářet image ze zdrojových vlastních imagí (spravované Image/Galerie sdílených imagí), musí mít služba Azure image Builder povolený přístup ke čtení imagí do těchto skupin prostředků. Pokud chcete přidělit požadovaná oprávnění, musíte vytvořit spravovanou identitu přiřazenou uživatelem a udělit jí práva pro skupinu prostředků, ve které se image nachází.

Sestavte z existující vlastní image:

```Actions
Microsoft.Compute/galleries/read
```

Sestavovat z existující verze Galerie sdílených imagí:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>Oprávnění k přizpůsobení imagí v virtuální sítě

Azure image Builder nabízí možnost nasazení a používání stávající virtuální sítě ve vašem předplatném, což umožňuje přizpůsobení přístupu k připojeným prostředkům.

Pokud chcete nasadit virtuální počítač do existující virtuální sítě, nemusíte pro skupinu prostředků udělovat oprávnění přispěvatele spravovaných identit, která jste přiřadili uživateli. Spravovaná identita přiřazená uživatelem ale potřebuje následující `Actions` oprávnění Azure ve skupině prostředků virtuální sítě:

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>Vytvoření definice role Azure

V následujících příkladech se vytvoří definice role Azure z akcí popsaných v předchozích částech. Příklady jsou aplikovány na úrovni skupiny prostředků. Vyhodnotit a otestovat, jestli jsou příklady pro vaše požadavky dostatečně podrobné. V případě svého scénáře ho možná budete muset upravit na konkrétní sdílenou galerii imagí.

Akce obrázku umožňují čtení a zápis. Rozhodněte se, co je pro vaše prostředí vhodné. Můžete například vytvořit roli, která povolí službě Azure image Builder číst image ze skupiny prostředků *příklad – RG-1* a zapsat obrázky do skupiny prostředků *příklad – RG-2*.

### <a name="custom-image-azure-role-example"></a>Příklad role Azure vlastní image

Následující příklad vytvoří roli Azure pro použití a distribuci zdrojové vlastní image. Pak udělíte vlastní roli spravované identitě přiřazené uživateli pro Azure image Builder.

Chcete-li zjednodušit nahrazení hodnot v příkladu, nastavte nejprve následující proměnné. Chcete-li nastavit proměnné, nahraďte nastavení zástupných symbolů.

| Nastavení | Popis |
|---------|-------------|
| \<Subscription ID\> | ID vašeho předplatného Azure |
| \<Resource group\> | Skupina prostředků pro vlastní image |

```powershell-interactive
$sub_id = "<Subscription ID>"
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
$imageResourceGroup = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$role_definition="aibRoleImageCreation.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleImageCreation.json description file. 
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $imageRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $imageResourceGroup
}

New-AzRoleAssignment @parameters
```

### <a name="existing-vnet-azure-role-example"></a>Příklad stávající role Azure VNET

Následující příklad vytvoří roli Azure pro použití a distribuci existující image virtuální sítě. Pak udělíte vlastní roli spravované identitě přiřazené uživateli pro Azure image Builder.

Chcete-li zjednodušit nahrazení hodnot v příkladu, nastavte nejprve následující proměnné. Chcete-li nastavit proměnné, nahraďte nastavení zástupných symbolů.

| Nastavení | Popis |
|---------|-------------|
| \<Subscription ID\> | ID vašeho předplatného Azure |
| \<Resource group\> | Skupina prostředků VNET |

```powershell-interactive
$sub_id = "<Subscription ID>"
$res_group = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json"
$role_definition="aibRoleNetworking.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same AAD domain
$timeInt=$(get-date -UFormat "%s")
$networkRoleDefName="Azure Image Builder Network Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<vnetRgName>', $res_group) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Networking Role',$networkRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleNetworking.json description file
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Assign the custom role to the user-assigned managed identity for Azure Image Builder
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $networkRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $res_group
}

New-AzRoleAssignment @parameters
```

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Přehled nástroje Azure image Builder](image-builder-overview.md).