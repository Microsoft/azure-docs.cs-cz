---
title: Programové vytváření zásad
description: Tento článek vás provede programově vytváření a správu zásad pro zásady Azure s Azure CLI, Azure PowerShell a REST API.
ms.date: 01/31/2019
ms.topic: how-to
ms.openlocfilehash: 08ed43a464d1dd7de8220428dbc1c61ce9fc3ad6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264541"
---
# <a name="programmatically-create-policies"></a>Programové vytváření zásad

Tento článek vás provede programově vytváření a správu zásad. Definice zásad Azure vynucují různá pravidla a efekty oproti vašim prostředkům. Vynucení zajišťuje, že prostředky zůstanou v souladu s vašimi podnikovými standardy a smlouvami o úrovni služeb.

Informace o dodržování předpisů najdete v [tématu získávání dat o dodržování předpisů](get-compliance-data.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jsou splněny následující požadavky:

1. Pokud jste to ještě neudělali, nainstalujte si nástroj [ARMClient](https://github.com/projectkudu/ARMClient). Jedná se o nástroj, který posílá žádosti HTTPS do rozhraní API založených na Azure Resource Manageru.

1. Aktualizujte modul Azure PowerShell na nejnovější verzi. Podrobné informace [najdete v tématu Instalace modulu Azure PowerShell.](/powershell/azure/install-az-ps) Další informace o nejnovější verzi najdete v [tématu Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

1. Zaregistrujte poskytovatele prostředků Azure Policy Insights pomocí Azure PowerShellu a ověřte, že vaše předplatné funguje s poskytovatelem prostředků. Chcete-li zaregistrovat zprostředkovatele prostředků, musíte mít oprávnění ke spuštění operace akce registru pro poskytovatele prostředků. Tato operace je součástí rolí Přispěvatel a Vlastník. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Další informace o registraci a zobrazení zprostředkovatelů prostředků naleznete v tématu [Zprostředkovatelé a typy prostředků](../../../azure-resource-manager/management/resource-providers-and-types.md).

1. Pokud jste tak ještě neučinili, nainstalujte azure cli. Nejnovější verzi můžete získat na [webu Install Azure CLI v systému Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Vytvoření a přiřazení definice zásady

Prvním krokem k lepší viditelnosti prostředků je vytvoření a přiřazení zásad nad prostředky. Dalším krokem je naučit se programově vytvářet a přiřazovat zásady. Ukázkové zásady audituje účty úložiště, které jsou otevřené pro všechny veřejné sítě pomocí powershellu, azure CLI a http požadavků.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Vytvoření a přiřazení definice zásad pomocí PowerShellu

1. Pomocí následujícího fragmentu JSON vytvořte soubor JSON s názvem AuditStorageAccounts.json.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Další informace o vytváření definice zásad najdete v tématu [Azure Policy Definition Structure .](../concepts/definition-structure.md)

1. Spuštěním následujícího příkazu vytvořte definici zásad pomocí souboru AuditStorageAccounts.json.

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   Příkaz vytvoří definici zásad s názvem _Účty úložiště auditu otevřené pro veřejné sítě_.
   Další informace o dalších parametrech, které můžete použít, naleznete [v tématu New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition).

   Při volání bez parametrů `New-AzPolicyDefinition` umístění, výchozí uložení definice zásady ve vybraném předplatném kontextu relací. Chcete-li definici uložit do jiného umístění, použijte následující parametry:

   - **SubscriptionId** – uložit do jiného předplatného. Vyžaduje hodnotu _GUID._
   - **ManagementGroupName** - Uložit do skupiny pro správu. Vyžaduje hodnotu _řetězce._

1. Po vytvoření definice zásad můžete vytvořit přiřazení zásad spuštěním následujících příkazů:

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Nahraďte _contosoRG_ názvem zamýšlené skupiny prostředků.

   Scope **Scope** Parametr `New-AzPolicyAssignment` na pracuje se skupinou pro správu, předplatné, skupina prostředků nebo jeden prostředek. Parametr používá úplnou cestu prostředku, kterou Vlastnost `Get-AzResourceGroup` **ResourceId** na vrátí. Vzor pro **Scope** pro každý kontejner je následující. `{rName}`Nahraďte `{subId}`, `{mgName}` `{rgName}`, a s názvem prostředku, názvem skupiny prostředků, ID předplatného a názvem skupiny pro správu.
   `{rType}`by být **nahrazeny typu prostředku** prostředku, `Microsoft.Compute/virtualMachines` například pro virtuální hod.

   - Zdroj -`/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Skupina prostředků -`/subscriptions/{subId}/resourceGroups/{rgName}`
   - Předplatné -`/subscriptions/{subId}/`
   - Řídící skupina -`/providers/Microsoft.Management/managementGroups/{mgName}`

Další informace o správě zásad prostředků pomocí modulu PowerShell Azure Resource Manager najdete v [tématu Az.Resources](/powershell/module/az.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Vytvoření a přiřazení definice zásad pomocí klienta ARMClient

Pomocí následujícího postupu vytvořte definici zásady.

1. Zkopírujte následující fragment JSON a vytvořte soubor JSON. Soubor zavoláte v dalším kroku.

   ```json
   "properties": {
       "displayName": "Audit Storage Accounts Open to Public Networks",
       "policyType": "Custom",
       "mode": "Indexed",
       "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
       "parameters": {},
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Storage/storageAccounts"
                   },
                   {
                       "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                       "equals": "Allow"
                   }
               ]
           },
           "then": {
               "effect": "audit"
           }
       }
   }
   ```

1. Vytvořte definici zásad pomocí jednoho z následujících volání:

   ```console
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
   ```

   Nahraďte předchozí {subscriptionId} ID vašeho předplatného nebo {managementGroupId} ID vaší [skupiny pro správu](../../management-groups/overview.md).

   Další informace o struktuře dotazu najdete v tématu [Definice zásad Azure – Vytvoření nebo aktualizace](/rest/api/resources/policydefinitions/createorupdate) a definice zásad – vytvoření nebo aktualizace ve skupině pro [správu.](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

Pomocí následujícího postupu vytvořte přiřazení zásad a přiřaďte definici zásady na úrovni skupiny zdrojů.

1. Zkopírujte následující fragment JSON a vytvořte soubor přiřazení zásad JSON. Nahraďte &lt; &gt; ukázkové informace v symbolech vlastními hodnotami.

   ```json
   {
       "properties": {
           "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
           "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
           "parameters": {},
           "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
           "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
       }
   }
   ```

1. Vytvořte přiřazení zásad pomocí následujícího volání:

   ```console
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
   ```

   Nahraďte &lt; &gt; ukázkové informace v symbolech vlastními hodnotami.

   Další informace o volání protokolu HTTP do rozhraní REST API najdete v [tématu Prostředky rozhraní API Azure REST](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Vytvoření a přiřazení definice zásad pomocí azure cli

Chcete-li vytvořit definici zásad, použijte následující postup:

1. Zkopírujte následující fragment JSON a vytvořte soubor přiřazení zásad JSON.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Další informace o vytváření definice zásad najdete v tématu [Azure Policy Definition Structure .](../concepts/definition-structure.md)

1. Chcete-li vytvořit definici zásad, spusťte následující příkaz:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   Příkaz vytvoří definici zásad s názvem _Účty úložiště auditu otevřené pro veřejné sítě_.
   Další informace o dalších parametrech, které můžete použít, naleznete [v tématu vytvoření definice zásad az](/cli/azure/policy/definition#az-policy-definition-create).

   Při volání bez parametrů `az policy definition creation` umístění, výchozí uložení definice zásady ve vybraném předplatném kontextu relací. Chcete-li definici uložit do jiného umístění, použijte následující parametry:

   - **--subscription** - Uložit do jiného předplatného. Vyžaduje hodnotu _GUID_ pro ID předplatného nebo hodnotu _řetězce_ pro název předplatného.
   - **--management-group** - Uložit do skupiny pro správu. Vyžaduje hodnotu _řetězce._

1. K vytvoření přiřazení zásad použijte následující příkaz. Nahraďte &lt; &gt; ukázkové informace v symbolech vlastními hodnotami.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   Parametr **--scope** `az policy assignment create` pracuje se skupinou pro správu, předplatným, skupinou prostředků nebo jedním prostředkem. Parametr používá úplnou cestu prostředku. Vzor pro **--scope** pro každý kontejner je následující. `{rName}`Nahraďte `{subId}`, `{mgName}` `{rgName}`, a s názvem prostředku, názvem skupiny prostředků, ID předplatného a názvem skupiny pro správu. `{rType}`by být **nahrazeny typu prostředku** prostředku, `Microsoft.Compute/virtualMachines` například pro virtuální hod.

   - Zdroj -`/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Skupina prostředků -`/subscriptions/{subID}/resourceGroups/{rgName}`
   - Předplatné -`/subscriptions/{subID}`
   - Řídící skupina -`/providers/Microsoft.Management/managementGroups/{mgName}`

ID definice zásad Azure můžete získat pomocí PowerShellu s následujícím příkazem:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

ID definice zásad pro definici zásady, kterou jste vytvořili, by se mělo podobat následujícímu příkladu:

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Další informace o tom, jak můžete spravovat zásady prostředků pomocí Azure CLI, najdete v [tématu Zásady prostředků Azure CLI](/cli/azure/policy?view=azure-cli-latest).

## <a name="next-steps"></a>Další kroky

Další informace o příkazech a dotazech v tomto článku naleznete v následujících článcích.

- [Prostředky rozhraní AZURE REST API](/rest/api/resources/)
- [Moduly Azure PowerShellu](/powershell/module/az.resources/#policies)
- [Příkazy zásad Azure CLI](/cli/azure/policy?view=azure-cli-latest)
- [Odkaz na rozhraní REST API poskytovatele prostředků Azure Policy Insights](/rest/api/policy-insights)
- [Uspořádání prostředků s využitím skupin pro správu Azure.](../../management-groups/overview.md)