---
title: 'Úvodní příručka: Vytvoření podrobného plánu pomocí PowerShellu'
description: V tomto rychlém startu použijete Azure Blueprints k vytváření, definování a nasazování artefaktů pomocí Prostředí PowerShell.
ms.date: 11/21/2019
ms.topic: quickstart
ms.openlocfilehash: 8b418fa2b5244c42b8597bbbe7ed4773133d03a8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75436642"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-powershell"></a>Úvodní příručka: Definování a přiřazení azure blueprintu pomocí PowerShellu

Seznamte se s principy vytváření a přiřazování podrobných plánů, abyste mohli definovat běžné vzory a vyvíjet opakovaně použitelné a rychle nasaditelné konfigurace založené na šablonách Resource Manageru, zásadách, zabezpečení a dalších. V tomto kurzu se naučíte používat podrobné plány Azure Blueprint k provádění nejčastějších úloh spojených s vytvářením, publikováním a přiřazením podrobného plánu v organizaci, jako je například:

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free) než začnete.

Pokud ještě není nainstalovaný, postupujte podle pokynů v [přidat modul Az.Blueprint](./how-to/manage-assignments-ps.md#add-the-azblueprint-module) k instalaci a ověření modulu **Az.Blueprint** z Galerie prostředí PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-blueprint"></a>Vytvoření podrobného plánu

Jako první krok při definování standardního vzoru pro dodržování předpisů je sestavení podrobného plánu z dostupných prostředků. Vytvoříme podrobný plán MyBlueprint, který pro předplatné nakonfiguruje přiřazení rolí a zásad. Potom přidáme skupinu prostředků, šablonu Resource Manageru a přiřazení role ke skupině prostředků.

> [!NOTE]
> Při použití prostředí PowerShell je nejprve vytvořen objekt _podrobného plánu._ Pro každý přidávaný _artefakt_, který má parametry, je potřeba předem definovat parametry v počátečním _podrobném plánu_.

1. Vytvořte počáteční objekt _blueprint_. **BlueprintFile** Parametr trvá soubor JSON, který obsahuje vlastnosti o podrobný plán, všechny skupiny prostředků k vytvoření a všechny parametry úrovně podrobného plánu. Tyto parametry se nastaví při přiřazení a používají je artefakty přidané v dalších krocích.

   - Soubor JSON - blueprint.json

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
     > Při programovém vytváření definic podrobného plánu použijte soubor _blueprint.json._
     > Tento název souboru se používá při volání [Import-AzBlueprintWithArtifact](/powershell/module/az.blueprint/import-azblueprintwithartifact).

     Objekt podrobného plánu je vytvořen ve výchozím předplatném ve výchozím nastavení. Chcete-li určit skupinu pro správu, použijte parametr **ManagementGroupId**. Chcete-li zadat odběr, použijte parametr **SubscriptionId**.

1. Přidejte do předplatného přiřazení role. **Soubor artefaktdefinuje** _druh_ artefaktu, vlastnosti zarovnávají identifikátor definice role a hlavní identity jsou předány jako pole hodnot. V následujícím příkladu jsou identity instančního objektu s přiřazenou určenou rolí nakonfigurované na parametr nastavený při přiřazení podrobného plánu. Tento příklad používá předdefinovanou roli _přispěvatele_ s identifikátorem GUID aplikace `b24988ac-6180-42a0-ab88-20f7382dd24c`.

   - Soubor JSON - \artifacts\roleContributor.json

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

1. Přidejte do předplatného přiřazení zásad. **Soubor artefaktů** definuje _druh_ artefaktu, vlastnosti, které jsou zarovnány s definicí zásady nebo iniciativy, a nakonfiguruje přiřazení zásad tak, aby používalo definované parametry podrobného plánu ke konfiguraci během přiřazení podrobného plánu. Tento příklad používá _značku Apply a její výchozí hodnotu pro předdefinované zásady skupin prostředků_ s identifikátorem GUID aplikace `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - Soubor JSON - \artifacts\policyTags.json

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

1. Přidejte do předplatného další přiřazenou zásadu pro značku úložiště (znovu použijte parametr _storageAccountType_). Tento další artefakt přiřazené zásady ukazuje, že parametr definovaný v podrobném plánu může používat více artefaktů. V tomto příklad se parametr **storageAccountType** používá k nastavení značky u skupiny prostředků. Tato hodnota poskytuje informace o účtu úložiště, který se vytvoří v dalším kroku. Tento příklad používá _značku Apply a její výchozí hodnotu pro předdefinované zásady skupin prostředků_ s identifikátorem GUID aplikace `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - Soubor JSON - \artifacts\policyStorageTags.json

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

1. Přidejte pod skupinu prostředků šablonu. Šablona **Soubor** pro šablonu Správce prostředků obsahuje normální součást JSON šablony. Šablona také znovu používá parametry podrobného plánu **storageAccountType**, **tagName** a **tagValue**, které se šabloně předávají. Podrobné parametry jsou k dispozici pro šablonu pomocí parametru **TemplateParameterFile** a uvnitř šablony JSON, že klíč-hodnota dvojice se používá k vložení hodnoty. Názvy parametrů podrobného plánu a šablony mohou být stejné.

   - Soubor šablony Správce prostředků Služby JSON Azure - \artifacts\templateStorage.json

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

   - Soubor parametrů šablony Správce prostředků Služby JSON Azure - \artifacts\templateStorageParams.json

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

1. Přidejte pod skupinu prostředků přiřazení role. Jde o podobné přiřazení jako při předchozím přiřazení role. V následujícím příkladu se identifikátor definice použije pro roli **Owner** (Vlastník), který jí z podrobného plánu nabídne jiný parametr. Tento příklad používá předdefinovanou roli _Vlastníka_ s identifikátorem GUID aplikace `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`.

   - Soubor JSON - \artifacts\roleOwner.json

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

Jakmile se podrobný plán publikuje pomocí PowerShellu, je přiřaditelný k předplatnému. Přiřaďte vytvořený podrobný plán některému z předplatných v hierarchii skupiny pro správu. Pokud je podrobný plán uložen do předplatného, lze jej přiřadit pouze k tomuto předplatnému. Blueprint **Blueprint** Parametr určuje podrobný plán přiřadit. Chcete-li zadat parametry name, location, identity, lock a blueprint, `New-AzBlueprintAssignment` použijte odpovídající parametry prostředí PowerShell na rutině nebo je zadejte v souboru Parametr UassignmentFile JSON. **AssignmentFile**

1. Spusťte nasazení podrobného plánu tím, že ho přiřadíte k předplatnému. Vzhledem k tomu, **že parametry přispěvatelů** a **vlastníků** vyžadují pole objektu Id objektů, kterým má být přiřazení role uděleno, použijte [rozhraní Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) pro shromažďování objektů Idpro použití v **assignmentfile** pro vlastní uživatele, skupiny nebo objekty zabezpečení služby.

   - Soubor JSON - blueprintAssignment.json

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

     Přiřazení podrobného plánu můžete také použít [uživatelem přiřazenou spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md).
     V tomto případě se **identita** část souboru přiřazení JSON změní takto. `{tenantId}`Nahraďte `{yourRG}`, `{userIdentity}` `{subscriptionId}`, , a s tenantId, subscriptionId, název skupiny prostředků a název uživatelem přiřazené spravované identity, v uvedeném pořadí.

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     **Uživatelem přiřazená spravovaná identita** může být v libovolném předplatném a skupině prostředků, ke které má uživatel, který přiřazuje podrobný plán, oprávnění.

     > [!IMPORTANT]
     > Podrobné plány nespravuje spravovanou identitu přiřazenou uživatelem. Uživatelé jsou zodpovědní za přiřazení dostatečných rolí a oprávnění nebo přiřazení podrobného plánu se nezdaří.

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="unassign-a-blueprint"></a>Zrušení přiřazení plánu

Podrobný plán můžete odebrat z předplatného. Odebrání se často provádí v případě, že už nepotřebujete prostředky artefaktů. Po odebrání podrobného plánu zůstanou přiřazené artefakty, které byly jeho součástí. Chcete-li odebrat přiřazení `Remove-AzBlueprintAssignment` podrobného plánu, použijte rutinu:

přiřadit myblueprint

```azurepowershell-interactive
Remove-AzBlueprintAssignment -Name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili, přiřadili a odebrali podrobný plán pomocí Prostředí PowerShell. Další informace o Azure Blueprints, pokračujte v článku životního cyklu podrobného plánu.

> [!div class="nextstepaction"]
> [Další informace o životním cyklu podrobného plánu](./concepts/lifecycle.md)