---
title: 'Rychlý Start: vytvoření podrobného plánu pomocí PowerShellu'
description: V tomto rychlém startu použijete Azure modrotisky k vytváření, definování a nasazování artefaktů pomocí PowerShellu.
ms.date: 08/27/2020
ms.topic: quickstart
ms.openlocfilehash: 339cd4628cda5f469a783db02c10f86259c93941
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89051487"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-powershell"></a>Rychlý Start: definování a přiřazení Azure Blueprint s využitím PowerShellu

Naučíte se vytvářet a přiřazovat podrobné plány a umožňují definici běžných vzorů pro vývoj opakovaně použitelných a rychlých nasazení konfigurací na základě Azure Resource Manager šablon (šablon ARM), zásad, zabezpečení a dalších. V tomto kurzu se naučíte používat podrobné plány Azure Blueprint k provádění nejčastějších úloh spojených s vytvářením, publikováním a přiřazením podrobného plánu v organizaci, jako je například:

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

Pokud ještě není nainstalovaná, nainstalujte a ověřte modul **AZ. detail** z Galerie prostředí PowerShell podle pokynů v tématu [Přidání modulu AZ. detail](./how-to/manage-assignments-ps.md#add-the-azblueprint-module) .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-blueprint"></a>Vytvoření podrobného plánu

Jako první krok při definování standardního vzoru pro dodržování předpisů je sestavení podrobného plánu z dostupných prostředků. Vytvoříme podrobný plán MyBlueprint, který pro předplatné nakonfiguruje přiřazení rolí a zásad. Potom do skupiny prostředků přidáme skupinu prostředků, šablonu ARM a přiřazení role.

> [!NOTE]
> Při použití PowerShellu se _vytvoří nejprve objekt_ podrobného plánu. Pro každý přidávaný _artefakt_, který má parametry, je potřeba předem definovat parametry v počátečním _podrobném plánu_.

1. Vytvořte počáteční objekt _blueprint_. Parametr **BlueprintFile** má soubor JSON, který obsahuje vlastnosti týkající se podrobného plánu, všech skupin prostředků, které se mají vytvořit, a všech parametrů úrovně podrobného plánu. Tyto parametry se nastaví při přiřazení a používají je artefakty přidané v dalších krocích.

   - Soubor JSON – blueprint.jsna

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
                     "defaultValue": "Standard_LRS",
                     "allowedValues": [
                         "Standard_LRS",
                         "Standard_GRS",
                         "Standard_ZRS",
                         "Premium_LRS"
                     ],
                     "metadata": {
                         "displayName": "storage account type.",
                         "description": null
                     }
                 },
                 "tagName": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The name of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "tagValue": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The value of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "contributors": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                         "strongType": "PrincipalId"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                         "strongType": "PrincipalId"
                     }
                 }
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

   - Příkaz PowerShellu

     ```azurepowershell-interactive
     # Login first with Connect-AzAccount if not using Cloud Shell

     # Get a reference to the new blueprint object, we'll use it in subsequent steps
     $blueprint = New-AzBlueprint -Name 'MyBlueprint' -BlueprintFile .\blueprint.json
     ```

     > [!NOTE]
     > Při vytváření definic podrobných plánů programově použijte název souboru _blueprint.js_ .
     > Tento název souboru se používá při volání metody [Import-AzBlueprintWithArtifact](/powershell/module/az.blueprint/import-azblueprintwithartifact).

     Ve výchozím nastavení je ve výchozím předplatném vytvořen objekt podrobného plánu. Chcete-li určit skupinu pro správu, použijte parametr **ManagementGroupId**. K určení předplatného použijte parametr **SubscriptionId**.

1. Přidejte do předplatného přiřazení role. **ArtifactFile** definuje _typ_ artefaktu, vlastnosti zarovnané na identifikátor definice role a hlavní identity jsou předány jako pole hodnot. V následujícím příkladu jsou identity instančního objektu s přiřazenou určenou rolí nakonfigurované na parametr nastavený při přiřazení podrobného plánu. V tomto příkladu se používá předdefinovaná role _Přispěvatel_ s identifikátorem GUID `b24988ac-6180-42a0-ab88-20f7382dd24c` .

   - Soubor JSON – \artifacts\roleContributor.jsna

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

   - Příkaz PowerShellu

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleContributor' -ArtifactFile .\artifacts\roleContributor.json
     ```

1. Přidejte do předplatného přiřazení zásad. **ArtifactFile** definuje _typ_ artefaktu, vlastnosti, které se zarovnají do definice zásady nebo iniciativy, a nakonfiguruje přiřazení zásady tak, aby se při přiřazení podrobného plánu použily definované parametry podrobného plánu. V tomto příkladu se používá _značka Apply a její výchozí hodnota pro předdefinované zásady skupin prostředků_ s identifikátorem GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` .

   - Soubor JSON – \artifacts\policyTags.jsna

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply tag and its default value to resource groups",
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

   - Příkaz PowerShellu

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyTags' -ArtifactFile .\artifacts\policyTags.json
     ```

1. Přidejte do předplatného další přiřazenou zásadu pro značku úložiště (znovu použijte parametr _storageAccountType_). Tento další artefakt přiřazené zásady ukazuje, že parametr definovaný v podrobném plánu může používat více artefaktů. V tomto příklad se parametr **storageAccountType** používá k nastavení značky u skupiny prostředků. Tato hodnota poskytuje informace o účtu úložiště, který se vytvoří v dalším kroku. V tomto příkladu se používá _značka Apply a její výchozí hodnota pro předdefinované zásady skupin prostředků_ s identifikátorem GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` .

   - Soubor JSON – \artifacts\policyStorageTags.jsna

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply storage tag to resource group",
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

   - Příkaz PowerShellu

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyStorageTags' -ArtifactFile .\artifacts\policyStorageTags.json
     ```

1. Přidejte pod skupinu prostředků šablonu. **TemplateFile** pro šablonu ARM obsahuje normální komponentu JSON šablony. Šablona také znovu používá parametry podrobného plánu **storageAccountType**, **tagName** a **tagValue**, které se šabloně předávají. Parametry podrobného plánu jsou k dispozici pro šablonu pomocí parametru **TemplateParameterFile** a uvnitř šablony JSON, kterou používá pár klíč-hodnota k vložení hodnoty. Název parametru podrobného plánu a šablony může být stejný.

   - Soubor šablony ARM formátu JSON – \artifacts\templateStorage.jsna

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
                 "metadata": {
                     "description": "Storage Account type"
                 }
             },
             "tagNameFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag name from blueprint"
                 }
             },
             "tagValueFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag value from blueprint"
                 }
             }
         },
         "variables": {
             "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
         },
         "resources": [{
             "type": "Microsoft.Storage/storageAccounts",
             "name": "[variables('storageAccountName')]",
             "apiVersion": "2016-01-01",
             "tags": {
                 "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
             },
             "location": "[resourceGroup().location]",
             "sku": {
                 "name": "[parameters('storageAccountTypeFromBP')]"
             },
             "kind": "Storage",
             "properties": {}
         }],
         "outputs": {
             "storageAccountSku": {
                 "type": "string",
                 "value": "[variables('storageAccountName')]"
             }
         }
     }
     ```

   - Soubor parametrů šablony ARM formátu JSON – \artifacts\templateStorageParams.js

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "value": "[parameters('storageAccountType')]"
             },
             "tagNameFromBP": {
                 "value": "[parameters('tagName')]"
             },
             "tagValueFromBP": {
                 "value": "[parameters('tagValue')]"
             }
         }
     }
     ```

   - Příkaz PowerShellu

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Type TemplateArtifact -Name 'templateStorage' -TemplateFile .\artifacts\templateStorage.json -TemplateParameterFile .\artifacts\templateStorageParams.json -ResourceGroupName storageRG
     ```

1. Přidejte pod skupinu prostředků přiřazení role. Jde o podobné přiřazení jako při předchozím přiřazení role. V následujícím příkladu se identifikátor definice použije pro roli **Owner** (Vlastník), který jí z podrobného plánu nabídne jiný parametr. V tomto příkladu se používá předdefinovaná role _Owner_ s identifikátorem GUID `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` .

   - Soubor JSON – \artifacts\roleOwner.jsna

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

   - Příkaz PowerShellu

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleOwner' -ArtifactFile .\artifacts\roleOwner.json
     ```

## <a name="publish-a-blueprint"></a>Publikování podrobného plánu

Po přidání artefaktů do podrobného plánu ho můžeme publikovat. Publikováním se zpřístupní, aby bylo možné ho přiřadit k předplatnému.

```azurepowershell-interactive
# Use the reference to the new blueprint object from the previous steps
Publish-AzBlueprint -Blueprint $blueprint -Version '{BlueprintVersion}'
```

Hodnota `{BlueprintVersion}` představuje řetězec složený z písmen, číslic a spojovníků (bez mezer a speciálních znaků) o maximální délce 20 znaků. Použijte jedinečnou hodnotu, která má vypovídací schopnost, třeba **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Přiřazení podrobného plánu

Po publikování podrobného plánu pomocí PowerShellu ho můžete přiřadit k předplatnému. Přiřaďte vytvořený podrobný plán některému z předplatných v hierarchii skupiny pro správu. Pokud je podrobný plán uložen v předplatném, může být přiřazen pouze k tomuto předplatnému. Parametr podrobného plánu **určuje plán,** který se má přiřadit. K zadání parametrů název, umístění, identita, Lock a podrobného plánu použijte odpovídajícího parametru PowerShellu v `New-AzBlueprintAssignment` rutině nebo je poskytněte v souboru JSON s parametrem **AssignmentFile** .

1. Spusťte nasazení podrobného plánu tím, že ho přiřadíte k předplatnému. Protože parametry **přispěvatelé** a **vlastníci** vyžadují, aby bylo přiřazení role uděleno pole objectID objektů zabezpečení, použijte [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) pro shromáždění identifikátorů objectID pro použití v **AssignmentFile** pro vlastní uživatele, skupiny nebo instanční objekty.

   - Soubor JSON – blueprintAssignment.jsna

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
                 "storageAccountType": {
                     "value": "Standard_GRS"
                 },
                 "tagName": {
                     "value": "CostCenter"
                 },
                 "tagValue": {
                     "value": "ContosoIT"
                 },
                 "contributors": {
                     "value": [
                         "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                         "38833b56-194d-420b-90ce-cff578296714"
                     ]
                 },
                 "owners": {
                     "value": [
                         "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                         "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                     ]
                 }
             }
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

   - Příkaz PowerShellu

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintAssignment -Blueprint $blueprint -Name 'assignMyBlueprint' -AssignmentFile .\blueprintAssignment.json
     ```

   - Spravovaná identita přiřazená uživatelem

     Přiřazení podrobného plánu může také používat [uživatelsky přiřazenou spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md).
     V takovém případě se část **identity** souboru přiřazení JSON mění takto. V `{tenantId}` uvedeném pořadí nahraďte, `{subscriptionId}` , `{yourRG}` a `{userIdentity}` pomocí tenantId, SubscriptionId, názvu skupiny prostředků a názvu vaší spravované identity přiřazené uživatelem.

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     **Spravovaná identita přiřazená uživatelem** může být v jakémkoli předplatném a skupině prostředků, ke které má uživatel přihlášený podrobný plán oprávnění.

     > [!IMPORTANT]
     > Azure modrotisky nespravuje spravovanou identitu přiřazenou uživatelem. Uživatelé zodpovídají za přiřazení dostatečných rolí a oprávnění nebo přiřazení podrobného plánu se nezdaří.

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="unassign-a-blueprint"></a>Zrušení přiřazení plánu

Podrobný plán můžete odebrat z předplatného. Odebrání se často provádí v případě, že už nepotřebujete prostředky artefaktů. Po odebrání podrobného plánu zůstanou přiřazené artefakty, které byly jeho součástí. Chcete-li odebrat přiřazení podrobného plánu, použijte `Remove-AzBlueprintAssignment` rutinu:

assignMyBlueprint

```azurepowershell-interactive
Remove-AzBlueprintAssignment -Name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili, přiřadili a odebrali podrobný plán pomocí PowerShellu. Další informace o plánech Azure najdete v článku o životním cyklu podrobného plánu.

> [!div class="nextstepaction"]
> [Další informace o životním cyklu podrobného plánu](./concepts/lifecycle.md)