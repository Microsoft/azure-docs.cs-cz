---
title: Oprava prostředků, které nevyhovují předpisům
description: Tato příručka vás provede opravou prostředků, které nedodržují zásady v Azure Policy.
ms.date: 09/09/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d263286a7504e7a8234ebd944bbbee69c5303
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74267361"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Opravit nekompatibilní prostředky službou Azure Policy

Prostředky, které nejsou kompatibilní se zásadami **deployIfNotExists** nebo **Modify** , je možné do stavu, který je v souladu s **nápravou**, předávat do odpovídajícího stavu. Nápravu je možné provést tak, že na základě pokynů Azure Policy spustíte efekt **deployIfNotExists** nebo **operace** značky přiřazené zásady u stávajících prostředků. Tento článek popisuje kroky potřebné k pochopení a provedení nápravy Azure Policy.

## <a name="how-remediation-security-works"></a>Jak funguje opravy zabezpečení

Když Azure Policy spustí šablonu v definici zásady **deployIfNotExists** , použije [spravovanou identitu](../../../active-directory/managed-identities-azure-resources/overview.md).
Azure Policy vytvoří spravovanou identitu pro každé přiřazení, ale musí obsahovat podrobnosti o rolích, které mají udělit spravovanou identitu. Pokud spravovaná identita chybí role, zobrazí se tato chyba během přiřazení zásady nebo iniciativa. Při použití portálu Azure Policy po spuštění přiřazení automaticky udělit spravované identitě uvedené role.

![Spravovaná identita - chybějící role](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Pokud se prostředek upravený pomocí **deployIfNotExists** nebo **Upravit** nachází mimo rozsah přiřazení zásady nebo šablona přistupuje k vlastnostem na prostředcích mimo obor přiřazení zásady, musí se spravované identitě přiřazení [ručně udělit přístup](#manually-configure-the-managed-identity) , jinak se nasazení opravy nezdaří.

## <a name="configure-policy-definition"></a>Nakonfigurovat definici zásad

Prvním krokem je definování rolí, které **deployIfNotExists** a **mění** v definici zásady, aby se úspěšně nasadil obsah šablony, která je k dispozici. V části **podrobnosti** vlastnost, přidejte **roleDefinitionIds** vlastnost. Tato vlastnost je pole řetězců, které odpovídají role ve vašem prostředí. Úplný příklad najdete v [příkladech deployIfNotExists](../concepts/effects.md#deployifnotexists-example) nebo v příkladech pro [Úpravy](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Vlastnost **roleDefinitionIds** používá úplný identifikátor prostředku a nebere v úvahu krátký **roleName** role. Chcete-li získat ID pro roli "Přispěvatel" ve vašem prostředí, použijte následující kód:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Ručně nakonfigurovat spravované identity

Při vytváření přiřazení pomocí portálu Azure Policy generuje spravovanou identitu a udělí jí role definované v **roleDefinitionIds**. Za těchto podmínek je třeba provést postup pro vytvoření spravované identity a přiřadit oprávnění ručně:

- Při používání sady SDK (jako je Azure PowerShell)
- Když se upraví prostředek mimo rozsah přiřazení pomocí šablony
- Pokud je prostředek mimo rozsah přiřazení čteného šablonou

> [!NOTE]
> Azure PowerShell a rozhraní .NET jsou pouze sady SDK, které aktuálně podporují tuto funkci.

### <a name="create-managed-identity-with-powershell"></a>Vytvořte spravovanou identitu pomocí Powershellu

Chcete-li vytvořit spravovanou identitu při přiřazování zásady, **umístění** musí být definován a **AssignIdentity** použít. Následující příklad získá definic předdefinovaných zásad **nasadit SQL DB transparentní šifrování dat**, nastaví cílová skupina prostředků a pak vytvoří přiřazení.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

`$assignment` Proměnná nyní obsahuje ID objektu zabezpečení spravovanou identitu spolu se standardní hodnoty vrácená při vytváření přiřazení zásad. Je přístupný prostřednictvím `$assignment.Identity.PrincipalId`.

### <a name="grant-defined-roles-with-powershell"></a>Udělení definované role pomocí prostředí PowerShell

Nové spravovanou identitu, musíte dokončit replikace prostřednictvím Azure Active Directory lze udělit potřebná role. Po dokončení replikace následující příklad provede iteraci definice zásady v `$policyDef` pro **roleDefinitionIds** a pomocí [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) udělí nové spravované identitě role.

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

### <a name="grant-defined-roles-through-portal"></a>Udělení definované role prostřednictvím portálu

Existují dva způsoby, jak udělit definované role pomocí portálu, pomocí spravované identity přiřazení **řízení přístupu (IAM)** nebo úpravou přiřazení zásady nebo iniciativa a kliknutím na **Uložit**.

Přidání role pro toto přiřazení spravovanou identitu, postupujte podle těchto kroků:

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

1. Na levé straně stránky služby Azure Policy vyberte **Přiřazení**.

1. Vyhledejte přiřazení, která má spravovanou identitu a klikněte na název.

1. Najít **ID přiřazení** vlastnost na stránky pro úpravu. ID přiřazení bude vypadat:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   Název spravované identity je poslední část přiřazení ID prostředku, který je `2802056bfc094dfb95d4d7a5` v tomto příkladu. Zkopírujte tuto část ID přiřazení prostředku.

1. Přejděte na prostředek nebo prostředky nadřazeného kontejneru (skupinu prostředků, předplatné, skupina pro správu), který se musí ručně přidat definice role.

1. Klikněte na tlačítko **řízení přístupu (IAM)** odkaz na stránce prostředků a klikněte na tlačítko **+ přidat přiřazení role** v horní části stránku řízení přístupu.

1. Vyberte vhodnou roli, která odpovídá **roleDefinitionIds** z definice zásady.
   Ponechte **přiřadit přístup k** nastavenou na výchozí hodnotu "Azure AD uživatele, skupiny nebo aplikace". V **vyberte** pole, vložte nebo napište část ID prostředku přiřazení dříve nachází. Po dokončení hledání, klikněte na objekt se stejným názvem vyberte ID a klikněte na **Uložit**.

## <a name="create-a-remediation-task"></a>Vytvořte úlohu nápravy

### <a name="create-a-remediation-task-through-portal"></a>Vytvoření úlohy nápravy prostřednictvím portálu

Během hodnocení určuje přiřazení zásad s **deployIfNotExists** nebo **úpravou** efektů, jestli existují nekompatibilní prostředky. Po nalezení nekompatibilní prostředky jsou podrobné informace jsou k dispozici na **nápravy** stránky. Společně se seznamem zásad, které mají nekompatibilní prostředky je možnosti k aktivaci **úloha opravy**. Tato možnost vytvoří nasazení ze šablony **deployIfNotExists** nebo operace **Úpravy** .

Chcete-li vytvořit **úloha opravy**, postupujte podle těchto kroků:

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

   ![Vyhledat zásady ve všech službách](../media/remediate-resources/search-policy.png)

1. Vyberte **nápravy** na levé straně na stránku služby Azure Policy.

   ![Výběr nápravy na stránce zásad](../media/remediate-resources/select-remediation.png)

1. Všechna přiřazení zásad **deployIfNotExists** a **Upravit** s nekompatibilními prostředky jsou obsažená v **zásadách, které se mají opravit** na kartě a v tabulce dat. Klikněte na příslušnou zásadu s prostředky, které jsou nekompatibilní. **Nová úloha opravy** otevře se stránka.

   > [!NOTE]
   > Alternativní způsob, jak otevírat **úloha opravy** stránky je najít a klikněte na zásadu z **dodržování předpisů** stránce a potom klikněte na **vytvořit úlohu nápravy** tlačítko.

1. Na **nová úloha opravy** stránce, filtrovat prostředky pomocí řešení **oboru** tři tečky a vyberte podřízené prostředky z kde přiřazené zásady (včetně na jednotlivé prostředky objekty). Kromě toho pomocí **umístění** rozevíracího seznamu a dále filtrovat prostředky. Pouze prostředky uvedené v tabulce bude opraven.

   ![Opravit – vyberte prostředky, které se mají opravit.](../media/remediate-resources/select-resources.png)

1. Začátek nápravy úlohy po prostředků se vyfiltrovaly kliknutím **napravit**. Otevře se stránka zásad dodržování předpisů pro **nápravy úlohy** kartu k zobrazení stavu průběh úlohy.

   ![Opravit – průběh úloh nápravy](../media/remediate-resources/task-progress.png)

1. Klikněte na **úloha opravy** ze stránky zásad dodržování předpisů se získat podrobnosti o průběhu. Filtrování používá pro úlohy se zobrazí spolu s seznam prostředků je opravit.

1. Z **úloha opravy** stránky, klikněte pravým tlačítkem na prostředek můžete zobrazit buď nasazení úloh opravy nebo prostředek. Na konci řádku, klikněte na **událostí souvisejících s** zobrazíte podrobnosti, jako je chybová zpráva.

   ![Napravit - prostředků úkolu kontextové nabídky](../media/remediate-resources/resource-task-context-menu.png)

Prostředky nasazené prostřednictvím **úloha opravy** jsou přidány do **nasazené prostředky** karty na stránce zásady dodržování předpisů.

### <a name="create-a-remediation-task-through-azure-cli"></a>Vytvoření úlohy nápravy pomocí Azure CLI

Pokud chcete vytvořit **úlohu nápravy** pomocí Azure CLI, použijte příkazy `az policy remediation`. Nahraďte `{subscriptionId}` IDENTIFIKÁTORem předplatného a `{myAssignmentId}` **deployIfNotExists** nebo **upravte** ID přiřazení zásad.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Další příkazy a příklady pro nápravu najdete v tématu [AZ Policy reoprava](/cli/azure/policy/remediation) .

### <a name="create-a-remediation-task-through-azure-powershell"></a>Vytvoření úlohy nápravy pomocí Azure PowerShell

Chcete-li vytvořit **úlohu nápravy** pomocí Azure PowerShell, použijte příkazy `Start-AzPolicyRemediation`. Nahraďte `{subscriptionId}` IDENTIFIKÁTORem předplatného a `{myAssignmentId}` **deployIfNotExists** nebo **upravte** ID přiřazení zásad.

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
- Přečtěte si, jak [získat data o dodržování předpisů](get-compliance-data.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
