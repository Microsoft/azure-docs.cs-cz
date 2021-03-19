---
title: Programové vytváření zásad
description: Tento článek vás provede programově vytvářením a správou zásad pro Azure Policy pomocí rozhraní příkazového řádku Azure, Azure PowerShell a REST API.
ms.date: 03/16/2021
ms.topic: how-to
ms.openlocfilehash: b4f15241799469da2e479cb758f648ec3131a149
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598486"
---
# <a name="programmatically-create-policies"></a>Programové vytváření zásad

Tento článek vás provede programově vytvářením a správou zásad. Azure Policy definice vynutila různá pravidla a vliv na vaše prostředky. Vynucování zajišťuje, aby prostředky zůstaly v souladu s vašimi podnikovými standardy a smlouvami o úrovni služeb.

Informace o dodržování předpisů najdete v tématu [získání dat o dodržování předpisů](get-compliance-data.md).

## <a name="prerequisites"></a>Předpoklady

Než začnete, ujistěte se, že jsou splněné následující předpoklady:

1. Pokud jste to ještě neudělali, nainstalujte si nástroj [ARMClient](https://github.com/projectkudu/ARMClient). Jedná se o nástroj, který posílá žádosti HTTPS do rozhraní API založených na Azure Resource Manageru.

1. Aktualizujte modul Azure PowerShell na nejnovější verzi. Podrobné informace najdete v tématu [instalace Azure PowerShell modulu](/powershell/azure/install-az-ps) . Další informace o nejnovější verzi najdete v tématu [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

1. Zaregistrujte poskytovatele prostředků služby Azure Policy Insights pomocí Azure PowerShell, abyste ověřili, že vaše předplatné spolupracuje s poskytovatelem prostředků. Chcete-li zaregistrovat poskytovatele prostředků, musíte mít oprávnění ke spuštění operace akce registrace pro poskytovatele prostředků. Tato operace je součástí rolí Přispěvatel a Vlastník. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Další informace o registraci a zobrazení poskytovatelů prostředků najdete v tématu [poskytovatelé a typy prostředků](../../../azure-resource-manager/management/resource-providers-and-types.md).

1. Pokud jste to ještě neudělali, nainstalujte rozhraní příkazového řádku Azure CLI. Nejnovější verzi můžete získat při instalaci rozhraní příkazového [řádku Azure CLI ve Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Vytvoření a přiřazení definice zásady

Prvním krokem k lepšímu přehledu vašich prostředků je vytváření a přiřazování zásad pro vaše prostředky. V dalším kroku se dozvíte, jak programově vytvářet a přiřazovat zásady. Ukázková zásada Audituje účty úložiště, které jsou otevřené pro všechny veřejné sítě pomocí PowerShellu, Azure CLI a požadavků HTTP.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Vytvoření a přiřazení definice zásady pomocí PowerShellu

1. Pomocí následujícího fragmentu kódu JSON vytvořte soubor JSON s názvem AuditStorageAccounts.jsv.

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

   Další informace o vytváření definice zásad najdete v tématu [Azure Policy struktura definice](../concepts/definition-structure.md).

1. Spuštěním následujícího příkazu vytvořte definici zásady pomocí AuditStorageAccounts.jsv souboru.

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   Příkaz vytvoří definici zásady s názvem _audit účty úložiště otevřená ve veřejných sítích_.
   Další informace o dalších parametrech, které můžete použít, najdete v části [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition).

   Když se volá bez parametrů umístění, `New-AzPolicyDefinition` ve výchozím nastavení se uloží definice zásady ve vybraném předplatném kontextu relace. Definici uložíte do jiného umístění pomocí následujících parametrů:

   - **SubscriptionId** – Uložit do jiného předplatného Vyžaduje hodnotu _GUID_ .
   - **ManagementGroupName** -Uložit do skupiny pro správu. Vyžaduje hodnotu typu _String_ .

1. Po vytvoření definice zásady můžete vytvořit přiřazení zásady spuštěním následujících příkazů:

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Nahraďte _ContosoRG_ názvem vaší zamýšlené skupiny prostředků.

   Parametr **Scope** v `New-AzPolicyAssignment` sadě funguje se skupinou pro správu, předplatným, skupinou prostředků nebo jedním prostředkem. Parametr používá úplnou cestu prostředku, která vrací vlastnost **ResourceID** `Get-AzResourceGroup` . Vzor pro **Rozsah** každého kontejneru je následující. Nahraďte `{rName}` ,, `{rgName}` a názvem `{subId}` `{mgName}` prostředku, názvem skupiny prostředků, ID předplatného a názvem skupiny pro správu v uvedeném pořadí.
   `{rType}` by se nahradilo **typem prostředku** prostředku, například `Microsoft.Compute/virtualMachines` pro virtuální počítač.

   - Partner `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Skupina prostředků – `/subscriptions/{subId}/resourceGroups/{rgName}`
   - Formě `/subscriptions/{subId}`
   - Skupina pro správu – `/providers/Microsoft.Management/managementGroups/{mgName}`

Další informace o správě zásad prostředků pomocí modulu Správce prostředků PowerShellu najdete v tématu [AZ. Resources](/powershell/module/az.resources/#policy).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Vytvoření a přiřazení definice zásady pomocí ARMClient

K vytvoření definice zásady použijte následující postup.

1. Zkopírujte následující fragment kódu JSON pro vytvoření souboru JSON. Tento soubor budete volat v dalším kroku.

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

1. Vytvořte definici zásady pomocí jednoho z následujících volání:

   ```console
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2019-09-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2019-09-01" @<path to policy definition JSON file>
   ```

   Nahraďte předchozí {subscriptionId} IDENTIFIKÁTORem vašeho předplatného nebo {managementGroupId} ID vaší [skupiny pro správu](../../management-groups/overview.md).

   Další informace o struktuře dotazu najdete v tématu [definice Azure Policy – vytvoření nebo aktualizace](/rest/api/resources/policydefinitions/createorupdate) a [definice zásad – vytvoření nebo aktualizace ve skupině pro správu](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) .

Pomocí následujícího postupu vytvořte přiřazení zásady a přiřaďte definici zásady na úrovni skupiny prostředků.

1. Zkopírujte následující fragment kódu JSON pro vytvoření souboru přiřazení zásad JSON. Nahraďte ukázkové informace v &lt; &gt; symbolech vlastními hodnotami.

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

1. Pomocí následujícího volání vytvořte přiřazení zásady:

   ```console
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2019-09-01" @<path to Assignment JSON file>
   ```

   Nahraďte ukázkové informace v &lt; &gt; symbolech vlastními hodnotami.

   Další informace o tom, jak provádět volání HTTP do REST API, najdete v tématu [prostředky Azure REST API](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Vytvoření a přiřazení definice zásady pomocí Azure CLI

Pokud chcete vytvořit definici zásady, použijte následující postup:

1. Zkopírujte následující fragment kódu JSON pro vytvoření souboru přiřazení zásad JSON.

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

   Další informace o vytváření definice zásad najdete v tématu [Azure Policy struktura definice](../concepts/definition-structure.md).

1. Spuštěním následujícího příkazu vytvořte definici zásady:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   Příkaz vytvoří definici zásady s názvem _audit účty úložiště otevřená ve veřejných sítích_.
   Další informace o dalších parametrech, které můžete použít, najdete v tématu [AZ Policy definition Create](/cli/azure/policy/definition#az_policy_definition_create).

   Když se volá bez parametrů umístění, `az policy definition creation` ve výchozím nastavení se uloží definice zásady ve vybraném předplatném kontextu relace. Definici uložíte do jiného umístění pomocí následujících parametrů:

   - **předplatné** – uložte si do jiného předplatného. Vyžaduje hodnotu _identifikátoru GUID_ pro ID předplatného nebo hodnotu _řetězce_ pro název předplatného.
   - **Správa-skupina** – uložení do skupiny pro správu. Vyžaduje hodnotu typu _String_ .

1. K vytvoření přiřazení zásady použijte následující příkaz. Nahraďte ukázkové informace v &lt; &gt; symbolech vlastními hodnotami.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   Parametr **Scope** v `az policy assignment create` sadě funguje se skupinou pro správu, předplatným, skupinou prostředků nebo jedním prostředkem. Parametr používá úplnou cestu prostředku. Vzor pro **Rozsah** každého kontejneru je následující. Nahraďte `{rName}` ,, `{rgName}` a názvem `{subId}` `{mgName}` prostředku, názvem skupiny prostředků, ID předplatného a názvem skupiny pro správu v uvedeném pořadí. `{rType}` by se nahradilo **typem prostředku** prostředku, například `Microsoft.Compute/virtualMachines` pro virtuální počítač.

   - Partner `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Skupina prostředků – `/subscriptions/{subID}/resourceGroups/{rgName}`
   - Formě `/subscriptions/{subID}`
   - Skupina pro správu – `/providers/Microsoft.Management/managementGroups/{mgName}`

ID definice Azure Policy můžete získat pomocí PowerShellu pomocí následujícího příkazu:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

ID definice zásad, které jste vytvořili, by mělo vypadat jako v následujícím příkladu:

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Další informace o tom, jak můžete spravovat zásady prostředků pomocí Azure CLI, najdete v tématu [zásady prostředků Azure CLI](/cli/azure/policy).

## <a name="next-steps"></a>Další kroky

Další informace o příkazech a dotazech v tomto článku najdete v následujících článcích.

- [Prostředky Azure REST API](/rest/api/resources/)
- [Moduly Azure PowerShell](/powershell/module/az.resources/#policy)
- [Příkazy zásad Azure CLI](/cli/azure/policy)
- [Referenční informace o REST API Azure Policy poskytovatele prostředků](/rest/api/policy)
- [Uspořádání prostředků s využitím skupin pro správu Azure.](../../management-groups/overview.md)
