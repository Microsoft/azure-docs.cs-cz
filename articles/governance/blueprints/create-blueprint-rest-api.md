---
title: 'Rychlý Start: vytvoření podrobného plánu pomocí REST API'
description: V tomto rychlém startu použijete Azure modrotisky k vytváření, definování a nasazování artefaktů pomocí REST API.
ms.date: 10/14/2020
ms.topic: quickstart
ms.openlocfilehash: aa25a65b20b295045b52a49c8c47fb8849c3cba3
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056970"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-rest-api"></a>Rychlý Start: definování a přiřazení Azure Blueprint s REST API

Naučíte se vytvářet a přiřazovat podrobné plány a umožňují definici běžných vzorů pro vývoj opakovaně použitelných a rychlých nasazení konfigurací na základě Azure Resource Manager šablon (šablon ARM), zásad, zabezpečení a dalších. V tomto kurzu se naučíte používat podrobné plány Azure Blueprint k provádění nejčastějších úloh spojených s vytvářením, publikováním a přiřazením podrobného plánu v organizaci, jako je například:

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.
- Zaregistrujte `Microsoft.Blueprint` poskytovatele prostředků. Pokyny najdete v tématu [poskytovatelé a typy prostředků](../../azure-resource-manager/management/resource-providers-and-types.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>Začínáme s REST API

Pokud rozhraní REST API neznáte, nejprve si přečtěte [referenční informace k rozhraní Azure REST API](/rest/api/azure/), kde najdete obecný přehled rozhraní REST API, zejména identifikátoru URI požadavku a textu požadavku. V tomto článku se tyto pojmy používají v pokynech při práci s podrobnými plány Azure, a proto se předpokládá, že jste se s nimi prakticky seznámili. Začátečníkům se doporučuje používat k automatické autorizaci nástroje, jako je [ARMClient](https://github.com/projectkudu/ARMClient) a další.

Specifikace Azure modrotisky najdete v tématu [Azure modrotisky REST API](/rest/api/blueprints/).

### <a name="rest-api-and-powershell"></a>REST API a PowerShell

Pokud nemáte nástroj na volání REST API, můžete k zadání těchto pokynů použít PowerShell. V následujícím příkladu je ukázková hlavička pro ověřování pomocí Azure. Vygenerujte ověřovací hlavičku, která je někdy označovaná jako **nosný token**, a zadejte identifikátor URI v REST API pro připojení s libovolnými parametry nebo **text žádosti**:

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

Pokud chcete získat informace o předplatném, nahraďte hodnotu `{subscriptionId}` proměnné **$restUri**. Proměnná $response obsahuje výsledek rutiny `Invoke-RestMethod`, který můžete parsovat rutinami, jako je [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json). Pokud koncový bod služby REST API očekává **text žádosti**, zadejte do parametru `-Body` proměnnou `Invoke-RestMethod` ve formátu JSON.

## <a name="create-a-blueprint"></a>Vytvoření podrobného plánu

Jako první krok při definování standardního vzoru pro dodržování předpisů je sestavení podrobného plánu z dostupných prostředků. Vytvoříme podrobný plán MyBlueprint, který pro předplatné nakonfiguruje přiřazení rolí a zásad. Potom do skupiny prostředků přidáme skupinu prostředků, šablonu ARM a přiřazení role.

> [!NOTE]
> Při použití REST API se nejprve vytvoří objekt _blueprint_. Pro každý přidávaný _artefakt_, který má parametry, je potřeba předem definovat parametry v počátečním _podrobném plánu_.

Každý identifikátor URI v REST API používá proměnné, které je potřeba nahradit vašimi vlastními hodnotami:

- `{YourMG}` – Nahraďte ID vaší skupiny pro správu.
- Proměnnou `{subscriptionId}` nahraďte ID předplatného.

> [!NOTE]
> Plány lze také vytvořit na úrovni předplatného. Příklad najdete v tématu vytvoření podrobného plánu [v předplatném](/rest/api/blueprints/blueprints/createorupdate#subscriptionblueprint).

1. Vytvořte počáteční objekt _blueprint_. **Text požadavku** obsahuje vlastnosti podrobného plánu, vytvářené skupiny prostředků a všechny parametry na úrovni podrobného plánu. Tyto parametry se nastaví při přiřazení a používají je artefakty přidané v dalších krocích.

   - Identifikátor URI v REST API

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
     ```

   - Text požadavku

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
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
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group"
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

1. Přidejte do předplatného přiřazení role. **Text žádosti** definuje _druh_ artefaktu, vlastnosti odpovídající identifikátoru definice role a hlavní identity předávané jako pole hodnot. V následujícím příkladu jsou identity instančního objektu s přiřazenou určenou rolí nakonfigurované na parametr nastavený při přiřazení podrobného plánu. V tomto příkladu se používá předdefinovaná role _Přispěvatel_ s identifikátorem GUID `b24988ac-6180-42a0-ab88-20f7382dd24c` .

   - Identifikátor URI v REST API

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleContributor?api-version=2018-11-01-preview
     ```

   - Text požadavku

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

1. Přidejte do předplatného přiřazení zásad. **Text požadavku** definuje _druh_ artefaktu, vlastnosti odpovídající definici zásady nebo iniciativy a konfiguruje přiřazení zásady na použití definovaných parametrů podrobného plánu konfigurovaných při přiřazení podrobného plánu. V tomto příkladu se používá _značka Apply a její výchozí hodnota pro předdefinované zásady skupin prostředků_ s identifikátorem GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` .

   - Identifikátor URI v REST API

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyTags?api-version=2018-11-01-preview
     ```

   - Text požadavku

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
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

1. Přidejte do předplatného další přiřazenou zásadu pro značku úložiště (znovu použijte parametr _storageAccountType_). Tento další artefakt přiřazené zásady ukazuje, že parametr definovaný v podrobném plánu může používat více artefaktů. V tomto příklad se parametr **storageAccountType** používá k nastavení značky u skupiny prostředků. Tato hodnota poskytuje informace o účtu úložiště, který se vytvoří v dalším kroku. V tomto příkladu se používá _značka Apply a její výchozí hodnota pro předdefinované zásady skupin prostředků_ s identifikátorem GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` .

   - Identifikátor URI v REST API

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
     ```

   - Text požadavku

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
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

1. Přidejte pod skupinu prostředků šablonu. **Text žádosti** pro šablonu ARM obsahuje normální komponentu JSON šablony a definuje cílovou skupinu prostředků s **vlastnostmi.** skupina zdrojů. Šablona také znovu používá parametry podrobného plánu **storageAccountType**, **tagName** a **tagValue**, které se šabloně předávají. Parametry podrobného plánu jsou šabloně k dispozici díky definování **properties.parameters**. Uvnitř kódu JSON šablony se k vložení hodnoty použije odpovídající pár klíč-hodnota. Názvy parametrů podrobného plánu a šablony sice můžou být stejné, ale my jsme je odlišili, abychom ilustrovali jejich předávání z podrobného plánu do artefaktu šablony.

   - Identifikátor URI v REST API

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/templateStorage?api-version=2018-11-01-preview
     ```

   - Text požadavku

     ```json
     {
         "kind": "template",
         "properties": {
             "template": {
                 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                 "contentVersion": "1.0.0.0",
                 "parameters": {
                     "storageAccountTypeFromBP": {
                         "type": "string",
                         "defaultValue": "Standard_LRS",
                         "allowedValues": [
                             "Standard_LRS",
                             "Standard_GRS",
                             "Standard_ZRS",
                             "Premium_LRS"
                         ],
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
                     "location": "[resourceGroups('storageRG').location]",
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
             },
             "resourceGroup": "storageRG",
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
     }
     ```

1. Přidejte pod skupinu prostředků přiřazení role. Jde o podobné přiřazení jako při předchozím přiřazení role. V následujícím příkladu se identifikátor definice použije pro roli **Owner** (Vlastník), který jí z podrobného plánu nabídne jiný parametr. V tomto příkladu se používá předdefinovaná role _Owner_ s identifikátorem GUID `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` .

   - Identifikátor URI v REST API

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
     ```

   - Text požadavku

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

## <a name="publish-a-blueprint"></a>Publikování podrobného plánu

Po přidání artefaktů do podrobného plánu ho můžeme publikovat. Publikováním se zpřístupní, aby bylo možné ho přiřadit k předplatnému.

- Identifikátor URI v REST API

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/versions/{BlueprintVersion}?api-version=2018-11-01-preview
  ```

Hodnota `{BlueprintVersion}` představuje řetězec složený z písmen, číslic a spojovníků (bez mezer a speciálních znaků) o maximální délce 20 znaků. Použijte jedinečnou hodnotu, která má vypovídací schopnost, třeba **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Přiřazení podrobného plánu

Po publikování podrobného plánu pomocí rozhraní REST API je možné ho přiřadit k předplatnému. Přiřaďte vytvořený podrobný plán některému z předplatných v hierarchii skupiny pro správu. Pokud je podrobný plán uložen v předplatném, může být přiřazen pouze k tomuto předplatnému. **Text požadavku** určuje přiřazovaný podrobný plán, poskytuje název a umístění všech skupin prostředků v definici podrobného plánu a nabízí také všechny parametry definované v podrobném plánu a používané jedním nebo několika připojenými artefakty.

Každý identifikátor URI v REST API používá proměnné, které je potřeba nahradit vašimi vlastními hodnotami:

- `{tenantId}` – Nahraďte ID tenanta.
- `{YourMG}` – Nahraďte ID vaší skupiny pro správu.
- Proměnnou `{subscriptionId}` nahraďte ID předplatného.

1. Poskytněte instančnímu objektu Azure Blueprint roli **Owner** (Vlastník) cílového předplatného. AppId je static ( `f71766dc-90d9-4b7d-bd9d-4499c4331c3f` ), ale ID instančního objektu se liší podle tenanta. K vyžádání podrobností týkajících se tenanta použijte následující REST API. Používá [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md), která má jinou autorizaci.

   - Identifikátor URI v REST API

     ```http
     GET https://graph.windows.net/{tenantId}/servicePrincipals?api-version=1.6&$filter=appId eq 'f71766dc-90d9-4b7d-bd9d-4499c4331c3f'
     ```

1. Spusťte nasazení podrobného plánu tím, že ho přiřadíte k předplatnému. Parametry **contributors** a **owners** vyžadují pole identifikátorů objectId objektů, kterým bude udělena přiřazená role. Ke shromáždění identifikátorů objectId, které se použijí v **textu žádosti** pro vlastní uživatele, skupiny nebo instanční objekty, použijte [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md).

   - Identifikátor URI v REST API

     ```http
     PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
     ```

   - Text požadavku

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

   - Spravovaná identita přiřazená uživatelem

     Přiřazení podrobného plánu může také používat [uživatelsky přiřazenou spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md).
     V tomto případě se v těle požadavku mění část **identity** . Nahraďte `{yourRG}` a `{userIdentity}` názvem vaší skupiny prostředků a názvem vaší uživatelsky přiřazené spravované identity.

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

Podrobný plán můžete odebrat z předplatného. Odebrání se často provádí v případě, že už nepotřebujete prostředky artefaktů. Po odebrání podrobného plánu zůstanou přiřazené artefakty, které byly jeho součástí. K odebrání přiřazeného podrobného plánu použijte následující operaci REST API:

- Identifikátor URI v REST API

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

### <a name="delete-a-blueprint"></a>Smazání podrobného plánu

K odebrání samotného podrobného plánu použijte následující operaci REST API:

- Identifikátor URI v REST API

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili, přiřadili a odebrali podrobný plán s REST API. Další informace o plánech Azure najdete v článku o životním cyklu podrobného plánu.

> [!div class="nextstepaction"]
> [Další informace o životním cyklu podrobného plánu](./concepts/lifecycle.md)