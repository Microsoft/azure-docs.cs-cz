---
title: Vytvořit podrobný plán pomocí REST API
description: Pomocí Azure modrotisky můžete vytvářet, definovat a nasazovat artefakty pomocí REST API.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/04/2019
ms.topic: quickstart
ms.service: blueprints
ms.openlocfilehash: 16540441aa8bc5d115606e1c5b3bdc2302ae2c20
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978497"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-rest-api"></a>Rychlý Start: definování a přiřazení Azure Blueprint s REST API

Naučíte se vytvářet a přiřazovat podrobné plány a umožňují definici běžných vzorů pro vývoj opakovaně použitelných a rychlých nasazení konfigurací na základě Správce prostředků šablon, zásad, zabezpečení a dalších. V tomto kurzu se naučíte používat Azure modrotisky k provádění některých běžných úkolů souvisejících s vytvářením, publikováním a přiřazováním podrobného plánu v rámci vaší organizace, například:

> [!div class="checklist"]
> - Vytvořit nový podrobný plán a přidat různé podporované artefakty
> - Provedení změn v existujícím podrobném plánu ještě v **konceptu**
> - Označení podrobného plánu jako připraveného k přiřazení k **publikovanému**
> - Přiřazení podrobného plánu k existujícímu předplatnému
> - Zkontroluje stav a průběh přiřazeného podrobného plánu.
> - Odebrat podrobný plán, který byl přiřazen k předplatnému

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="getting-started-with-rest-api"></a>Začínáme s REST API

Pokud nejste obeznámeni s REST API, začněte kontrolou [Azure REST API Reference](/rest/api/azure/) , abyste získali Obecné informace o REST API, konkrétně žádosti identifikátoru URI a textu žádosti. Tento článek používá tyto koncepty k poskytnutí pokynů pro práci s plány Azure a předpokládá, že jsou jejich provozní znalosti. Nástroje, jako je [ARMClient](https://github.com/projectkudu/ARMClient) a jiné, můžou autorizovat automaticky a jsou doporučené pro začátečníky.

Specifikace modrotisky najdete v tématu [Azure modrotisky REST API](/rest/api/blueprints/).

### <a name="rest-api-and-powershell"></a>REST API a PowerShell

Pokud ještě nemáte k dispozici nástroj pro volání REST API, zvažte použití prostředí PowerShell pro tyto pokyny. Následuje ukázka hlavičky pro ověřování pomocí Azure. Vygenerujte ověřovací hlavičku, někdy označovanou jako **nosný token**, a zadejte REST API identifikátor URI pro připojení k jakýmkoli parametrům nebo **textu žádosti**:

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
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2016-06-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

Pokud chcete získat informace o vašem předplatném, nahraďte `{subscriptionId}` v proměnné **$restUri** výše. Proměnná $response obsahuje výsledek rutiny `Invoke-RestMethod`, která se dá analyzovat pomocí rutin, jako je [ConvertFrom-JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json). Pokud koncový bod služby REST API očekává **tělo požadavku**, zadejte proměnnou FORMÁTOVANOU jako JSON pro parametr `-Body` `Invoke-RestMethod`.

## <a name="create-a-blueprint"></a>Vytvořit podrobný plán

Prvním krokem při definování standardního vzoru pro dodržování předpisů je vytvoření podrobného plánu z dostupných prostředků. Vytvoříme podrobný plán s názvem "MyBlueprint" a nakonfigurujeme přiřazení rolí a zásad pro předplatné. Potom do skupiny prostředků přidáme skupinu prostředků, šablonu Správce prostředků a přiřazení role.

> [!NOTE]
> Při použití _REST API se nejprve vytvoří objekt_ podrobného plánu. Pro každý _artefakt_ , který má být přidán s parametry, musí být parametry předem definovány na počáteční podrobný _plán_.

V každém REST API identifikátor URI existují proměnné, které se používají, je třeba nahradit vlastními hodnotami:

- `{YourMG}` – nahraďte ID vaší skupiny pro správu.
- `{subscriptionId}` – nahraďte ID předplatného.

> [!NOTE]
> Plány lze také vytvořit na úrovni předplatného. Příklad najdete v tématu vytvoření podrobného plánu [v předplatném](/rest/api/blueprints/blueprints/createorupdate#subscriptionblueprint).

1. Vytvořte _počáteční objekt_ podrobného plánu. **Tělo žádosti** zahrnuje vlastnosti podrobného plánu, všech skupin prostředků, které se mají vytvořit, a všech parametrů úrovně podrobného plánu. Parametry jsou nastaveny během přiřazování a použity artefakty přidanými v pozdějších krocích.

   - REST API IDENTIFIKÁTOR URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
     ```

   - Text žádosti

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

1. Přidejte přiřazení role v předplatném. **Text žádosti** definuje _typ_ artefaktu, vlastnosti, které jsou zarovnané na identifikátor definice role a hlavní identity jsou předány jako pole hodnot. V následujícím příkladu jsou hlavní identity udělené zadané roli nakonfigurované na parametr, který se nastaví během přiřazení podrobného plánu. V tomto příkladu se používá předdefinovaná role _přispěvatele_ s identifikátorem GUID `b24988ac-6180-42a0-ab88-20f7382dd24c`.

   - REST API IDENTIFIKÁTOR URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleContributor?api-version=2018-11-01-preview
     ```

   - Text žádosti

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

1. Přidat přiřazení zásad v předplatném **Text žádosti** definuje _druh_ artefaktu, vlastnosti, které se zarovnají do definice zásady nebo iniciativy, a nakonfiguruje přiřazení zásady tak, aby se při přiřazení podrobného plánu použily definované parametry podrobného plánu. V tomto příkladu se používá _značka Apply a její výchozí hodnota pro předdefinované zásady skupin prostředků_ s identifikátorem GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - REST API IDENTIFIKÁTOR URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyTags?api-version=2018-11-01-preview
     ```

   - Text žádosti

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

1. Přidejte další přiřazení zásady pro značku úložiště (znovu použijte parametr _storageAccountType_ ) v předplatném. Tento artefakt přiřazení dodatečné zásady ukazuje, že parametr definovaný v podrobném plánu je použitelný více než jedním artefaktem. V tomto příkladu se **storageAccountType** používá k nastavení značky pro skupinu prostředků. Tato hodnota poskytuje informace o účtu úložiště, který je vytvořený v dalším kroku. V tomto příkladu se používá _značka Apply a její výchozí hodnota pro předdefinované zásady skupin prostředků_ s identifikátorem GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - REST API IDENTIFIKÁTOR URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
     ```

   - Text žádosti

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

1. Přidejte šablonu do skupiny prostředků. **Tělo žádosti** pro šablonu správce prostředků obsahuje normální komponentu JSON šablony a definuje cílovou skupinu prostředků s **vlastnostmi.** Šablona také znovu používá parametry podrobného plánu **storageAccountType**, **TagName**a **tagValue** předáním každého do šablony. Parametry podrobného plánu jsou k dispozici pro šablonu definováním **vlastností. Parameters** a uvnitř šablony JSON, kterou používá pár klíč-hodnota k vložení hodnoty. Názvy parametrů v podrobném plánu a šabloně by mohly být stejné, ale byly odlišné pro ilustraci toho, jak každý z podrobných plánů do artefaktu šablony prochází.

   - REST API IDENTIFIKÁTOR URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/templateStorage?api-version=2018-11-01-preview
     ```

   - Text žádosti

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

1. Přidejte přiřazení role do skupiny prostředků. Podobně jako u položky přiřazení předchozí role používá níže uvedený příklad identifikátor definice role **vlastníka** a poskytuje mu jiný parametr než podrobný plán. V tomto příkladu se používá předdefinovaná role _Owner_ s identifikátorem GUID `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`.

   - REST API IDENTIFIKÁTOR URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
     ```

   - Text žádosti

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

## <a name="publish-a-blueprint"></a>Publikovat podrobný plán

Teď, když jsou artefakty přidané do podrobného plánu, je čas je publikovat. Publikování umožňuje přiřazení k předplatnému.

- REST API IDENTIFIKÁTOR URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/versions/{BlueprintVersion}?api-version=2018-11-01-preview
  ```

Hodnota parametru `{BlueprintVersion}` je řetězec písmen, číslic a spojovníků (bez mezer nebo jiných speciálních znaků) s maximální délkou 20 znaků. Použijte něco jedinečného a informativní, jako je například **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Přiřadit podrobný plán

Po publikování plánu pomocí REST API ho můžete přiřadit k předplatnému. Přiřaďte podrobný plán, který jste vytvořili, do některého z předplatných v rámci vaší hierarchie skupin pro správu. Pokud je podrobný plán uložen v předplatném, může být přiřazen pouze k tomuto předplatnému. **Tělo žádosti** určuje podrobný plán, který má být přiřazen, poskytuje název a umístění do všech skupin prostředků v definici podrobného plánu a poskytuje všechny parametry definované v podrobném plánu a používané jedním nebo více připojenými artefakty.

V každém REST API identifikátor URI existují proměnné, které se používají, je třeba nahradit vlastními hodnotami:

- `{tenantId}` – nahraďte ID tenanta.
- `{YourMG}` – nahraďte ID vaší skupiny pro správu.
- `{subscriptionId}` – nahraďte ID předplatného.

1. Zadejte Azure Blueprint instančního objektu roli **vlastníka** v cílovém předplatném. AppId je static (`f71766dc-90d9-4b7d-bd9d-4499c4331c3f`), ale ID instančního objektu se liší podle tenanta. Podrobnosti můžete požadovat pro vašeho tenanta pomocí následujících REST API. Používá [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) , které mají jinou autorizaci.

   - REST API IDENTIFIKÁTOR URI

     ```http
     GET https://graph.windows.net/{tenantId}/servicePrincipals?api-version=1.6&$filter=appId eq 'f71766dc-90d9-4b7d-bd9d-4499c4331c3f'
     ```

1. Spusťte nasazení podrobného plánu tím, že ho přiřadíte k předplatnému. Jelikož parametry **přispěvatelé** a **vlastníci** vyžadují, aby bylo přiřazení role uděleno pole objectID objektů zabezpečení, použijte [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) pro shromáždění identifikátorů objectID pro použití v **textu žádosti** pro váš vlastní uživatelé, skupiny nebo instanční objekty.

   - REST API IDENTIFIKÁTOR URI

     ```http
     PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
     ```

   - Text žádosti

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

     Přiřazení podrobného plánu může také používat [uživatelsky přiřazenou spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md). V tomto případě se v těle požadavku mění část **identity** .  V uvedeném pořadí nahraďte `{yourRG}` a `{userIdentity}` názvem skupiny prostředků a názvem vaší spravované identity přiřazené uživatelem.

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
     > Modrotisky nespravují spravovanou identitu přiřazenou uživatelem. Uživatelé zodpovídají za přiřazení dostatečných rolí a oprávnění nebo přiřazení podrobného plánu se nezdaří.

## <a name="unassign-a-blueprint"></a>Zrušení přiřazení podrobného plánu

Z předplatného můžete odebrat plán. Odebrání se často provádí, když už prostředky artefaktů nepotřebujete. Po odebrání podrobného plánu jsou artefakty přiřazené v rámci tohoto podrobného plánu ponecháni za. Chcete-li odebrat přiřazení podrobného plánu, použijte následující operaci REST API:

- REST API IDENTIFIKÁTOR URI

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

## <a name="delete-a-blueprint"></a>Odstranit podrobný plán

Chcete-li odebrat samotný podrobný plán, použijte následující operaci REST API:

- REST API IDENTIFIKÁTOR URI

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

## <a name="next-steps"></a>Další kroky

- Přečtěte si informace o [životním cyklu](./concepts/lifecycle.md)podrobného plánu.
- Naučte se používat [statické a dynamické parametry](./concepts/parameters.md).
- Naučte se přizpůsobit [pořadí klasifikace](./concepts/sequencing-order.md)podrobného plánu.
- Zjistěte, jak využít [uzamykání prostředků](./concepts/resource-locking.md)podrobného plánu.
- Přečtěte si, jak [aktualizovat existující přiřazení](./how-to/update-existing-assignments.md).
- Řešení problémů při přiřazování podrobného plánu s [obecným řešením potíží](./troubleshoot/general.md).