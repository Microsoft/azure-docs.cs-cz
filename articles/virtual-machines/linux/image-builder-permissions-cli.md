---
title: Konfigurace oprávnění služby Azure image Builder pomocí Azure CLI
description: Konfigurace požadavků pro službu Azure VM Image Builder, včetně oprávnění a oprávnění pomocí Azure CLI
author: cynthn
ms.author: danis
ms.date: 04/02/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: eb4fe102407bf519c9253ac7da39178ad8cacb0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104607513"
---
# <a name="configure-azure-image-builder-service-permissions-using-azure-cli"></a>Konfigurace oprávnění služby Azure image Builder pomocí Azure CLI

Při registraci pro (AIB) udělí služba AIB oprávnění vytvořit, spravovat a odstranit pracovní skupinu prostředků (IT_ *) a mít práva k přidávání prostředků do této služby, které jsou požadovány pro sestavení bitové kopie. K tomu slouží AIB hlavní název služby (SPN), který je ve vašem předplatném dostupný během úspěšné registrace.

Pokud chcete, aby tvůrce imagí virtuálních počítačů Azure mohl distribuovat image do spravovaných imagí nebo do sdílené Galerie imagí, budete muset vytvořit uživatelem přiřazenou identitu Azure, která má oprávnění ke čtení a zápisu imagí. Pokud přistupujete k Azure Storage, budete potřebovat oprávnění ke čtení privátních nebo veřejných kontejnerů.

Před vytvořením image musíte nastavit oprávnění a oprávnění. Následující části podrobně popisují, jak nakonfigurovat možné scénáře pomocí Azure CLI.

> [!IMPORTANT]
> Azure image Builder je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>Registrace funkcí

Nejdřív se musíte zaregistrovat pro službu Azure image Builder. Registrace uděluje službě oprávnění vytvořit, spravovat a odstranit pracovní skupinu prostředků. Tato služba má také práva k přidání prostředků do skupiny, která je potřebná pro sestavení image.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem Azure

Azure image Builder vyžaduje, abyste vytvořili [spravovanou identitu přiřazenou uživatelem Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Azure image Builder používá spravovanou identitu přiřazenou uživatelem ke čtení imagí, zápisu imagí a přístupu k účtům Azure Storage. Udělíte oprávnění identitě v rámci svého předplatného ke konkrétním akcím.

> [!NOTE]
> V minulosti používal Azure image Builder hlavní název služby (SPN) Azure image Builder k udělení oprávnění skupinám prostředků imagí. Používání hlavního názvu služby (SPN) bude zastaralé. Místo toho použijte spravovanou identitu přiřazenou uživatelem.

Následující příklad ukazuje, jak vytvořit spravovanou identitu přiřazenou uživateli Azure. Chcete-li nastavit proměnné, nahraďte nastavení zástupných symbolů.

| Nastavení | Popis |
|---------|-------------|
| \<Resource group\> | Skupina prostředků, ve které se má vytvořit spravovaná identita přiřazená uživatelem |

```azurecli-interactive
identityName="aibIdentity"
imageResourceGroup=<Resource group>

az identity create \
    --resource-group $imageResourceGroup \
    --name $identityName
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

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
imageResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# Create a custom role from the sample aibRoleImageCreation.json description file.
az role definition create --role-definition ./aibRoleImageCreation.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

### <a name="existing-vnet-azure-role-example"></a>Příklad stávající role Azure VNET

Následující příklad vytvoří roli Azure pro použití a distribuci existující image virtuální sítě. Pak udělíte vlastní roli spravované identitě přiřazené uživateli pro Azure image Builder.

Chcete-li zjednodušit nahrazení hodnot v příkladu, nastavte nejprve následující proměnné. Chcete-li nastavit proměnné, nahraďte nastavení zástupných symbolů.

| Nastavení | Popis |
|---------|-------------|
| \<Subscription ID\> | ID vašeho předplatného Azure |
| \<Resource group\> | Skupina prostředků VNET |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
VnetResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json

# Create a unique role name to avoid clashes in the same domain
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json

# Create a custom role from the aibRoleNetworking.json description file.
az role definition create --role-definition ./aibRoleNetworking.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$VnetResourceGroup
```

## <a name="using-managed-identity-for-azure-storage-access"></a>Použití spravované identity pro přístup k Azure Storage

Pokud chcete bezproblémové ověřování pomocí Azure Storage a používání privátních kontejnerů, Azure image Builder a Azure potřebuje spravovanou identitu přiřazenou uživatelem. Azure image Builder používá identitu k ověřování pomocí Azure Storage.

> [!NOTE]
> Azure image Builder používá pouze identitu na základě času odeslání šablony obrázku. Virtuální počítač sestavení nemá přístup k identitě během sestavení bitové kopie.

Pomocí Azure CLI vytvořte spravovanou identitu přiřazenou uživatelem.

```azurecli
az role assignment create \
    --assignee <Image Builder client ID> \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/<Storage account container>
```

V šabloně tvůrce imagí musíte zadat spravovanou identitu přiřazenou uživatelem.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
    "location": "<Region>",
    ..
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<Image Builder ID>": {}     
        }
```

Nahraďte následující zástupné nastavení:

| Nastavení | Popis |
|---------|-------------|
| \<Region\> | Oblast šablony |
| \<Resource group\> | Skupina prostředků |
| \<Storage account container\> | Název kontejneru účtu úložiště |
| \<Subscription ID\> | Předplatné Azure |

Další informace o uživatelsky přiřazené spravované identitě najdete v tématu [Vytvoření vlastní image, která bude používat spravovanou identitu Azure User-Assigned k bezproblémové přístupu k souborům Azure Storage](./image-builder-user-assigned-identity.md). Tento rychlý Start vás provede vytvořením a konfigurací spravované identity přiřazené uživatelem pro přístup k účtu úložiště.

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Přehled nástroje Azure image Builder](../image-builder-overview.md).