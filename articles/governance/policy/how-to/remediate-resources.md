---
title: Oprava prostředků, které nevyhovují předpisům
description: Tato příručka vás provede opravou prostředků, které nedodržují zásady v Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 219a3c56f9e4e4c9e132fa759b017fac63ade766
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71977993"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Napravit nekompatibilní prostředky pomocí Azure Policy

Prostředky, které nejsou kompatibilní se zásadami **deployIfNotExists** nebo **Modify** , je možné do stavu, který je v souladu s **nápravou**, předávat do odpovídajícího stavu. Nápravu je možné provést tak, že na základě pokynů Azure Policy spustíte efekt **deployIfNotExists** nebo **operace** značky přiřazené zásady u stávajících prostředků. Tento článek popisuje kroky potřebné k pochopení a provedení nápravy Azure Policy.

## <a name="how-remediation-security-works"></a>Jak funguje Oprava zabezpečení

Když Azure Policy spustí šablonu v definici zásady **deployIfNotExists** , použije [spravovanou identitu](../../../active-directory/managed-identities-azure-resources/overview.md).
Azure Policy vytvoří spravovanou identitu pro každé přiřazení, ale musí obsahovat podrobnosti o rolích, které mají udělit spravovanou identitu. Pokud ve spravované identitě chybí role, zobrazí se tato chyba při přiřazování zásady nebo k iniciativě. Při použití portálu Azure Policy po spuštění přiřazení automaticky udělit spravované identitě uvedené role.

![Spravovaná identita – chybějící role](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Pokud se prostředek upravený pomocí **deployIfNotExists** nebo **Upravit** nachází mimo rozsah přiřazení zásady nebo šablona přistupuje k vlastnostem na prostředcích mimo obor přiřazení zásady, spravovaná identita přiřazení musí být [. Ruční udělení přístupu](#manually-configure-the-managed-identity) nebo nasazení opravy se nezdaří.

## <a name="configure-policy-definition"></a>Konfigurace definice zásad

Prvním krokem je definování rolí, které **deployIfNotExists** a **mění** v definici zásady, aby se úspěšně nasadil obsah šablony, která je k dispozici. Ve vlastnosti **Details** přidejte vlastnost **roleDefinitionIds** . Tato vlastnost je pole řetězců, které se shodují s rolemi ve vašem prostředí. Úplný příklad najdete v [příkladech deployIfNotExists](../concepts/effects.md#deployifnotexists-example) nebo v příkladech pro [Úpravy](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Vlastnost **roleDefinitionIds** používá úplný identifikátor prostředku a nebere v úvahu krátký **roleName** role. K získání ID pro roli Přispěvatel ve vašem prostředí použijte následující kód:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Ruční konfigurace spravované identity

Při vytváření přiřazení pomocí portálu Azure Policy generuje spravovanou identitu a udělí jí role definované v **roleDefinitionIds**. V následujících podmínkách je nutné provést kroky pro vytvoření spravované identity a přiřazení oprávnění k tomuto účelu ručně:

- Při použití sady SDK (například Azure PowerShell)
- Když šablona upraví prostředek mimo obor přiřazení
- Když šablona čte prostředek mimo obor přiřazení

> [!NOTE]
> Azure PowerShell a .NET jsou jediné sady SDK, které tuto funkci aktuálně podporují.

### <a name="create-managed-identity-with-powershell"></a>Vytvoření spravované identity pomocí PowerShellu

Aby bylo možné vytvořit spravovanou identitu během přiřazování zásady, **umístění** musí být definováno a **AssignIdentity** použito. Následující příklad získá definici předdefinované zásady **nasadit transparentní šifrování dat SQL DB**, nastaví cílovou skupinu prostředků a pak vytvoří přiřazení.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

Proměnná `$assignment` nyní obsahuje ID objektu zabezpečení spravované identity společně se standardními hodnotami vrácenými při vytváření přiřazení zásady. Dá se k němu přistupovat prostřednictvím `$assignment.Identity.PrincipalId`.

### <a name="grant-defined-roles-with-powershell"></a>Udělení definovaných rolí pomocí PowerShellu

Nová spravovaná identita musí dokončit replikaci prostřednictvím Azure Active Directory před tím, než bude možné udělit potřebné role. Po dokončení replikace následující příklad provede iteraci definice zásady v `$policyDef` pro **roleDefinitionIds** a pomocí [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) udělí nové spravované identitě role.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Udělení definovaných rolí prostřednictvím portálu

Existují dva způsoby, jak udělit spravované identitě přiřazení definované role pomocí portálu, pomocí **řízení přístupu (IAM)** nebo úpravou přiřazení zásad nebo iniciativ a kliknutím na **Uložit**.

Chcete-li přidat roli ke spravované identitě přiřazení, postupujte podle následujících kroků:

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

1. Na levé straně stránky služby Azure Policy vyberte **Přiřazení**.

1. Vyhledejte přiřazení, které má spravovanou identitu, a klikněte na název.

1. Na stránce pro úpravy vyhledejte vlastnost **ID přiřazení** . ID přiřazení bude vypadat nějak takto:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   Název spravované identity je poslední částí ID prostředku přiřazení, která je v tomto příkladu `2802056bfc094dfb95d4d7a5`. Zkopírujte tuto část ID prostředku přiřazení.

1. Přejděte do prostředku nebo do nadřazeného kontejneru prostředky (skupina prostředků, předplatné, skupina pro správu), která potřebuje ruční přidání definice role.

1. Klikněte na odkaz **řízení přístupu (IAM)** na stránce prostředky a v horní části stránky řízení přístupu klikněte na **+ Přidat přiřazení role** .

1. Vyberte odpovídající roli, která odpovídá **roleDefinitionIds** z definice zásady.
   Ponechte možnost **přiřadit přístup k** nastavení na výchozí hodnotu uživatel, skupina nebo aplikace služby Azure AD. Do pole **Vybrat** vložte nebo zadejte část ID prostředku přiřazení, která se nachází dříve. Po dokončení hledání klikněte na objekt se stejným názvem a vyberte položku ID a klikněte na **Uložit**.

## <a name="create-a-remediation-task"></a>Vytvoření úlohy nápravy

### <a name="create-a-remediation-task-through-portal"></a>Vytvoření úlohy nápravy prostřednictvím portálu

Během hodnocení určuje přiřazení zásad s **deployIfNotExists** nebo **úpravou** efektů, jestli existují nekompatibilní prostředky. Pokud jsou nalezeny nekompatibilní prostředky, podrobnosti jsou k dispozici na stránce **nápravy** . Společně se seznamem zásad, které mají nekompatibilní prostředky, je možnost aktivovat **úlohu nápravy**. Tato možnost vytvoří nasazení ze šablony **deployIfNotExists** nebo operace **Úpravy** .

Chcete-li vytvořit **úlohu nápravy**, postupujte podle následujících kroků:

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

   ![Vyhledat zásady ve všech službách](../media/remediate-resources/search-policy.png)

1. Na levé straně stránky Azure Policy vyberte **náprava** .

   ![Výběr nápravy na stránce zásad](../media/remediate-resources/select-remediation.png)

1. Všechna přiřazení zásad **deployIfNotExists** a **Upravit** s nekompatibilními prostředky jsou obsažená v **zásadách, které se mají opravit** na kartě a v tabulce dat. Klikněte na zásadu s prostředky, které nedodržují předpisy. Otevře se stránka **Nová úloha nápravy** .

   > [!NOTE]
   > Alternativní způsob, jak otevřít stránku **nápravné úlohy** , je najít a kliknout na zásadu na stránce **dodržování předpisů** a pak kliknout na tlačítko **vytvořit úlohu nápravy** .

1. Na stránce **Nová úloha nápravy** vyfiltrujte prostředky, které se mají opravit, pomocí teček **oboru** a vyberte podřízené prostředky, ze kterých je zásada přiřazena (včetně směrem k jednotlivým objektům prostředku). Kromě toho použijte rozevírací seznam **umístění** k dalšímu filtrování prostředků. Budou opraveny pouze prostředky uvedené v tabulce.

   ![Opravit – vyberte prostředky, které se mají opravit.](../media/remediate-resources/select-resources.png)

1. Spusťte úlohu nápravy, jakmile se prostředky vyfiltrují kliknutím na **opravit**. Na kartě **úlohy opravy** se otevře stránka kompatibilita zásad, ve které se zobrazí stav průběhu úkolů.

   ![Opravit – průběh úloh nápravy](../media/remediate-resources/task-progress.png)

1. Kliknutím na **úlohu nápravy** ze stránky dodržování zásad získáte podrobnosti o průběhu. Filtrování použité pro úlohu se zobrazí spolu se seznamem prostředků, které jsou opraveny.

1. Na stránce **úloha nápravy** klikněte pravým tlačítkem na prostředek a zobrazte buď nasazení úlohy opravy, nebo prostředek. Kliknutím na **související události** na konci řádku zobrazíte podrobnosti, jako je například chybová zpráva.

   ![Opravit – kontextová nabídka úkolu prostředku](../media/remediate-resources/resource-task-context-menu.png)

Prostředky nasazené prostřednictvím **nápravné úlohy** se přidají na kartě **nasazené prostředky** na stránce dodržování zásad.

### <a name="create-a-remediation-task-through-azure-cli"></a>Vytvoření úlohy nápravy pomocí Azure CLI

Pokud chcete vytvořit **úlohu nápravy** pomocí Azure CLI, použijte příkazy `az policy remediation`. Hodnotu `{subscriptionId}` nahraďte ID předplatného a `{myAssignmentId}` s vaším **deployIfNotExists** nebo **upravte** ID přiřazení zásad.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Další příkazy a příklady pro nápravu najdete v tématu [AZ Policy reoprava](/cli/azure/policy/remediation) .

### <a name="create-a-remediation-task-through-azure-powershell"></a>Vytvoření úlohy nápravy pomocí Azure PowerShell

Chcete-li vytvořit **úlohu nápravy** pomocí Azure PowerShell, použijte příkazy `Start-AzPolicyRemediation`. Hodnotu `{subscriptionId}` nahraďte ID předplatného a `{myAssignmentId}` s vaším **deployIfNotExists** nebo **upravte** ID přiřazení zásad.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Další rutiny a příklady pro nápravu najdete v tématu [AZ. PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) Module.

## <a name="next-steps"></a>Další kroky

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Zjistěte, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](getting-compliance-data.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).