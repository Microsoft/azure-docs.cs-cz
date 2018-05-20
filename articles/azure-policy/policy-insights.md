---
title: Prostřednictvím kódu programu vytvoření zásad a zobrazit data o dodržování předpisů se zásadami Azure
description: Tento článek vás provede prostřednictvím kódu programu vytváření a Správa zásad Azure zásady.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 5405566b5254c553eac584acc1653449b51ddffc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Prostřednictvím kódu programu vytvoření zásad a zobrazit data o dodržování předpisů

Tento článek vás provede prostřednictvím kódu programu vytváření a Správa zásad. Také ukazuje, jak zobrazit stavy kompatibility prostředků a zásady. Definice zásad vynucovat různá pravidla a efekty přes vaše prostředky. Vynucení zajišťuje, že prostředky zůstat kompatibilní s firemními standardy a smlouvy o úrovni služeb.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že byly splněny následující požadavky:

1. Pokud jste to ještě neudělali, nainstalujte [ARMClient](https://github.com/projectkudu/ARMClient). Je nástroj, který odešle požadavky HTTP využívající Azure Resource Manager rozhraní API.
2. Aktualizujte modul AzureRM PowerShellu na nejnovější verzi. Další informace o nejnovější verzi najdete v tématu [prostředí Azure PowerShell](https://github.com/Azure/azure-powershell/releases).
3. Zaregistrujte zprostředkovatele prostředků zásady statistiky použití prostředí Azure PowerShell, abyste zajistili, že vaše předplatné funguje s poskytovatelem prostředků. Když chcete registrovat poskytovatele prostředků, musíte mít oprávnění k provedení operace akce registrace pro poskytovatele prostředků. Tato operace je součástí rolí Přispěvatel a Vlastník. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků:

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Další informace o registraci a zobrazení zprostředkovatelé prostředků najdete v tématu [zprostředkovatelé prostředků a typy](../azure-resource-manager/resource-manager-supported-services.md).
4. Pokud jste to ještě neudělali, nainstalujte rozhraní příkazového řádku Azure. Můžete získat nejnovější verzi na [nainstalovat Azure CLI 2.0 v systému Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Vytvořte a přiřaďte definici zásady

Prvním krokem k lepší viditelnost vašich prostředků je vytvořit a přiřadit zásady přes vaše prostředky. Dalším krokem je se dozvíte, jak programově vytvořit a přiřadit zásady. Příklad zásady audity účty úložiště, které jsou otevřené pro všechny veřejné sítě pomocí prostředí PowerShell, rozhraní příkazového řádku Azure a požadavky HTTP.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Vytvořte a přiřaďte definici zásady v prostředí PowerShell

1. Vytvořte soubor JSON s názvem AuditStorageAccounts.json pomocí následujícího fragmentu kódu JSON.

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

  Další informace o vytváření definice zásad najdete v tématu [strukturu definice zásad Azure](policy-definition.md).
2. Spusťte následující příkaz k vytvoření definice zásady pomocí souboru AuditStorageAccounts.json.

  ```azurepowershell-interactive
  New-AzureRmPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
  ```

  Příkaz vytvoří definici zásady s názvem _auditu úložiště účtů otevřete k veřejným sítím_. Další informace o dalších parametrů, které můžete použít, najdete v části [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition).
3. Po vytvoření vaší definice zásady, můžete vytvořit přiřazení zásad spuštěním následujících příkazů:

  ```azurepowershell-interactive
  $rg = Get-AzureRmResourceGroup -Name 'ContosoRG'
  $Policy = Get-AzureRmPolicyDefinition -Name 'AuditStorageAccounts'
  New-AzureRmPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
  ```

  Nahraďte _ContosoRG_ s názvem vaší skupiny určený prostředků.

Další informace o správě zásad prostředků pomocí modulu Powershellu pro Azure Resource Manager najdete v tématu [AzureRM.Resources](/powershell/module/azurerm.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Vytvořte a přiřaďte definici zásady pomocí ARMClient

Použijte následující postup k vytvoření definice zásady.

1. Zkopírujte následující fragmentu kódu JSON vytvořte soubor JSON. Soubor v dalším kroku budete volat.

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

2. Vytvoření definice zásady pomocí následující volání:

  ```
  armclient PUT "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
  ```

  Nahradit předchozí &lt;subscriptionId&gt; s ID předplatného určený.

Další informace o struktuře dotaz najdete v tématu [definice zásady – vytvoření nebo aktualizace](/rest/api/resources/policydefinitions/createorupdate).

Použijte následující postup k vytvoření přiřazení zásady a přiřazení definice zásady na úrovni skupiny prostředků.

1. Zkopírujte následující fragmentu kódu JSON vytvořte soubor JSON zásady přiřazení. Nahradí informace z příkladu v &lt; &gt; symboly vlastními hodnotami.

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

2. Vytvoření přiřazení zásady pomocí následující volání:

  ```
  armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
  ```

  Nahradí informace z příkladu v &lt; &gt; symboly vlastními hodnotami.

  Další informace o tom, jak HTTP volání rozhraní REST API najdete v tématu [prostředky rozhraní API REST Azure](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Vytvořte a přiřaďte definici zásady pomocí rozhraní příkazového řádku Azure

K vytvoření definice zásady, použijte následující postup:

1. Zkopírujte následující fragmentu kódu JSON vytvořte soubor JSON zásady přiřazení.

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

2. Spusťte následující příkaz k vytvoření definice zásady:

  ```azurecli-interactive
az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
  ```

3. Použijte následující příkaz k vytvoření přiřazení zásady. Nahradí informace z příkladu v &lt; &gt; symboly vlastními hodnotami.

  ```azurecli-interactive
  az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
  ```

ID definice zásady můžete získat pomocí prostředí PowerShell pomocí následujícího příkazu:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

ID definice zásady pro definici zásady, kterou jste vytvořili by měl podobat následujícímu příkladu:

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Další informace o tom, jak můžete spravovat zásady prostředků pomocí rozhraní příkazového řádku Azure najdete v tématu [zásady prostředků Azure CLI](/cli/azure/policy?view=azure-cli-latest).

## <a name="identify-non-compliant-resources"></a>Identifikace prostředků, které nedodržují předpisy

V přiřazení prostředek je nevyhovující, pokud není postupujte podle zásad nebo iniciativy pravidla. Následující tabulka ukazuje, jak jinou zásadu důsledky pracovat vyhodnocení podmínky pro výsledný stav dodržování předpisů:

| Stav prostředků | Efekt | Vyhodnocení zásad | Stav dodržování předpisů |
| --- | --- | --- | --- |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Nevyhovující předpisům |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Odpovídající |
| Nová | Audit, AuditIfNotExist\* | True | Nevyhovující předpisům |
| Nová | Audit, AuditIfNotExist\* | False | Odpovídající |

\* Pokud příkaz tak, aby se vyžadovat připojení, DeployIfNotExist a AuditIfNotExist důsledky hodnotu TRUE. Důsledky také vyžadují existence podmínku, která má být FALSE jako nevyhovující. Pokud má hodnotu TRUE, aktivuje podmínka IF vyhodnocení podmínky existence pro související prostředky.

Abyste lépe pochopili, jak jsou prostředky označené jako nekompatibilní, použijeme v příkladu přiřazení zásad vytvořili výše.

Předpokládejme například, že máte skupinu prostředků – ContsoRG, s některé úložiště účty (zvýrazněné červeně), které jsou viditelné na veřejných sítích.

![Účty úložiště, které jsou zveřejněné na veřejných sítích](media/policy-insights/resource-group01.png)

V tomto příkladu potřebujete věnujte pozornost bezpečnostní rizika. Teď, když jste vytvořili přiřazení zásady, vyhodnotí se pro všechny účty úložiště ve skupině prostředků ContosoRG. Ho audity tři účty nevyhovující úložiště v důsledku toho změna stavy jejich k **nevyhovující.**

![Auditovat účty nevyhovující úložiště](media/policy-insights/resource-group03.png)

Prostředků ve skupině prostředků, které nejsou kompatibilní s přiřazení zásady pomocí následujícího postupu. V příkladu prostředky jsou účty úložiště ve skupině prostředků ContosoRG.

1. Získání ID přiřazení zásady tak, že spustíte následující příkazy:

  ```azurepowershell-interactive
  $policyAssignment = Get-AzureRmPolicyAssignment | Where-Object { $_.Properties.displayName -eq 'Audit Storage Accounts with Open Public Networks' }
  $policyAssignment.PolicyAssignmentId
  ```

  Další informace o získání ID přiřazení zásady najdete v tématu [Get-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/Get-AzureRmPolicyAssignment).

2. Spusťte následující příkaz, který mají ID prostředků nekompatibilní prostředky zkopírován do souboru JSON:

  ```
  armclient POST "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
  ```

3. Výsledky by měl podobat následujícímu příkladu:

  ```json
  {
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
      "@odata.count": 3,
      "value": [{
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount1Id>"
          },
          {
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount2Id>"
          },
          {
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount3ID>"
          }
      ]
  }
  ```

Výsledky jsou rovnocenná by obvykle zobrazené v části **nekompatibilní prostředky** v [Azure zobrazení portálu](assign-policy-definition.md#identify-non-compliant-resources).

Nekompatibilní prostředky jsou v současné době pouze identifikovat pomocí portálu Azure a u žádostí protokolu HTTP. Další informace o dotazování stavy zásad najdete v tématu [stav zásad](/rest/api/policy-insights/policystates) článku rozhraní API.

## <a name="view-policy-events"></a>Zobrazení událostí modulu zásad

Pokud prostředek se vytvoří nebo aktualizuje, generuje se výsledek vyhodnocení zásad. Výsledky se nazývají _události zásad_. Spuštěním následujícího dotazu zobrazíte všechny události zásad, které jsou přidružené k přiřazení zásady.

```
armclient POST "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2017-12-12-preview"
```

Vaše výsledky budou vypadat přibližně jako v následujícím příkladu:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 3
    }]
}
```

Jako stavy zásady můžete zobrazit jenom události zásad u žádostí protokolu HTTP. Další informace o dotazování události zásad najdete v tématu [události zásad](/rest/api/policy-insights/policyevents) článku.

## <a name="next-steps"></a>Další postup

Projděte si následující články pro další informace o příkazech a dotazy v tomto článku.

- [Prostředky Azure REST API](/rest/api/resources/)
- [Moduly prostředí Azure RM PowerShell](/powershell/module/azurerm.resources/#policies)
- [Příkazy zásad rozhraní příkazového řádku Azure](/cli/azure/policy?view=azure-cli-latest)
- [Poskytovatel prostředků Statistika zásad odkazu k REST API](/rest/api/policy-insights)