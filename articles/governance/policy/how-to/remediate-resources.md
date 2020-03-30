---
title: Oprava prostředků, které nevyhovují předpisům
description: Tato příručka vás provede nápravou prostředků, které nejsou kompatibilní se zásadami v zásadách Azure.
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 71af5c81e0dce4d5c0a0461534f634db36bd66a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471383"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Navádění nekompatibilních prostředků pomocí zásad Azure

Prostředky, které nejsou kompatibilní s **deployIfNotExists** nebo **upravit** zásady lze umístit do stavu kompatibilní prostřednictvím **nápravy**. Náprava se provádí pokynem zásad Azure ke spuštění efektu **deployIfNotExists** nebo **operací** značek přiřazených zásad na vašich existujících prostředcích, ať už je toto přiřazení pro skupinu pro správu, předplatné, skupinu prostředků nebo jednotlivé prostředky. Tento článek ukazuje kroky potřebné k pochopení a provedení nápravy pomocí zásad Azure.

## <a name="how-remediation-security-works"></a>Jak funguje zabezpečení nápravy

Když Azure Policy spustí šablonu v definici zásad **deployIfNotExists,** provádí to pomocí [spravované identity](../../../active-directory/managed-identities-azure-resources/overview.md).
Azure Policy vytvoří spravovanou identitu pro každé přiřazení, ale musí obsahovat podrobnosti o tom, jaké role udělit spravované identity. Pokud spravovaná identita chybí role, tato chyba se zobrazí během přiřazení zásady nebo iniciativy. Při použití portálu Azure Policy automaticky udělí spravovanou identitu uvedené role po spuštění přiřazení. _Umístění_ spravované identity nemá vliv na jeho provoz s Azure Policy.

![Spravovaná identita – chybějící role](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Pokud prostředek změněný **podle deployIfNotExists** nebo **modify** je mimo rozsah přiřazení zásad y nebo šablona přistupuje k vlastnostem na prostředky mimo rozsah přiřazení zásad, musí být spravované identitě přiřazení [ručně udělen přístup](#manually-configure-the-managed-identity) nebo se nezdaří nasazení nápravy.

## <a name="configure-policy-definition"></a>Konfigurace definice zásad

Prvním krokem je definovat role, které **nasaditIfNotExists** a **upravit** potřeby v definici zásad úspěšně nasadit obsah zahrnuté šablony. Pod **vlastnost podrobnosti** přidejte **vlastnost roleDefinitionIds.** Tato vlastnost je pole řetězců, které odpovídají rolím ve vašem prostředí. Úplný příklad naleznete v [příkladu deployIfNotExists](../concepts/effects.md#deployifnotexists-example) nebo [v příkladech úprav](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Vlastnost **roleDefinitionIds** používá úplný identifikátor prostředku a nepřevezme krátký **název role.** Chcete-li získat ID pro roli přispěvatele ve vašem prostředí, použijte následující kód:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Ruční konfigurace spravované identity

Při vytváření přiřazení pomocí portálu Azure Policy vygeneruje spravovanou identitu a udělí jí role definované v **roleDefinitionIds**. Za následujících podmínek je nutné ručně provést kroky k vytvoření spravované identity a přiřazení oprávnění:

- Při používání sady SDK (například Azure PowerShell)
- Pokud je zdroj mimo obor přiřazení změněn šablonou
- Při čtení zdroje mimo obor přiřazení šablonou

> [!NOTE]
> Azure PowerShell a .NET jsou jediné sady SDK, které tuto funkci aktuálně podporují.

### <a name="create-managed-identity-with-powershell"></a>Vytvoření spravované identity pomocí PowerShellu

Chcete-li vytvořit spravovanou identitu během přiřazení zásady, musí být **definováno umístění** a **použita funkce AssignIdentity.** Následující příklad získá definici integrované zásady **Nasazení transparentního šifrování dat SQL DB**, nastaví cílovou skupinu prostředků a potom vytvoří přiřazení.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

Proměnná `$assignment` nyní obsahuje id jistiny spravované identity spolu se standardními hodnotami vrácenými při vytváření přiřazení zásad. To je možné `$assignment.Identity.PrincipalId`přistupovat prostřednictvím .

### <a name="grant-defined-roles-with-powershell"></a>Udělit definované role pomocí PowerShellu

Nová spravovaná identita musí dokončit replikaci prostřednictvím služby Azure Active Directory, aby jí mohly být uděleny potřebné role. Po dokončení replikace následující příklad iteruje definici zásadv `$policyDef` pro **roleDefinitionIds** a používá [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) udělit nové spravované identity role.

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

### <a name="grant-defined-roles-through-portal"></a>Udělit definované role prostřednictvím portálu

Existují dva způsoby, jak udělit spravované identitě přiřazení definované role pomocí portálu, pomocí **řízení přístupu (IAM)** nebo úpravou přiřazení zásady nebo iniciativy a klepnutím na tlačítko **Uložit**.

Chcete-li přidat roli ke spravované identitě přiřazení, postupujte takto:

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

1. Na levé straně stránky služby Azure Policy vyberte **Přiřazení**.

1. Vyhledejte přiřazení, které má spravovanou identitu, a klikněte na název.

1. Nastránce úprav vyhledejte vlastnost **ID přiřazení.** ID přiřazení bude něco jako:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   Název spravované identity je poslední část ID prostředku přiřazení, `2802056bfc094dfb95d4d7a5` která je v tomto příkladu. Zkopírujte tuto část ID zdroje přiřazení.

1. Přejděte na prostředek nebo nadřazený kontejner prostředků (skupina prostředků, předplatné, skupina pro správu), který potřebuje ručně přidat definici role.

1. Klikněte na odkaz **Řízení přístupu (IAM)** na stránce prostředků a klikněte na **+ Přidat přiřazení role** v horní části stránky řízení přístupu.

1. Vyberte příslušnou roli, která odpovídá **roleDefinitionIds** z definice zásady.
   Ponechat **Přiřaďte přístup k** nastavení na výchozí nastavení "Uživatel, skupina nebo aplikace Azure AD". Do pole **Vybrat** vložte nebo zadejte část ID zdroje přiřazení, která byla umístěna dříve. Po dokončení hledání klepněte na objekt se stejným názvem, vyberte ID a klepněte na tlačítko **Uložit**.

## <a name="create-a-remediation-task"></a>Vytvoření nápravné úlohy

### <a name="create-a-remediation-task-through-portal"></a>Vytvoření nápravné úlohy prostřednictvím portálu

Během hodnocení přiřazení zásad s **deployIfNotExists** nebo **upravit** účinky určuje, pokud existují nekompatibilní prostředky. Pokud jsou nalezeny nekompatibilní prostředky, podrobnosti jsou uvedeny na stránce **náprava.** Spolu se seznamem zásad, které mají nekompatibilní prostředky je možnost spustit **nápravu úkolu**. Tato možnost je to, co vytvoří nasazení z **šablony deployIfNotExists** nebo **operace změny.**

Chcete-li vytvořit **úkol nápravy**, postupujte takto:

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

   ![Hledání zásad ve všech službách](../media/remediate-resources/search-policy.png)

1. Na levé straně stránky Zásady Azure vyberte **Náprava.**

   ![Vybrat nápravu na stránce Zásady](../media/remediate-resources/select-remediation.png)

1. Všechny **deployIfNotExists** a **upravit** přiřazení zásad s nekompatibilní prostředky jsou zahrnuty na **zásady k nápravě** kartu a tabulku dat. Klikněte na zásadu s prostředky, které nejsou kompatibilní. Otevře se stránka **Úkol nové nápravy.**

   > [!NOTE]
   > Alternativním způsobem otevření stránky **úkolu nápravy** je najít zásadu na stránce **Dodržování předpisů** a kliknout na ni a potom kliknout na tlačítko **Vytvořit úlohu nápravy.**

1. Na stránce **Nový úkol nápravy** vyfiltrujte prostředky, které chcete napravit, pomocí elips **oboru** k výběru podřízených prostředků, ze kterých je zásada přiřazena (včetně jednotlivých objektů prostředků). Kromě toho použijte **umístění** rozevírací rozbalovací dále filtrovat prostředky. Budou opraveny pouze prostředky uvedené v tabulce.

   ![Náprava – vyberte, které prostředky chcete napravit](../media/remediate-resources/select-resources.png)

1. Sčínejte s úkolem nápravy po filtrování zdrojů klepnutím na tlačítko **Opravovat**. Stránka dodržování zásad se otevře na kartě **Úkoly nápravy** a zobrazí se stav průběhu úkolů. Nasazení vytvořená nápravným úkolem začínají okamžitě.

   ![Náprava - průběh sanačních úkolů](../media/remediate-resources/task-progress.png)

1. Kliknutím na **úkol nápravy** na stránce dodržování zásad získáte podrobnosti o průběhu. Filtrování použité pro úkol se zobrazí spolu se seznamem prostředků, které jsou remnovovány.

1. Na stránce **úkolu nápravy** klikněte pravým tlačítkem myši na prostředek a zobrazte nasazení nápravné úlohy nebo prostředek. Na konci řádku klikněte na **související události,** abyste viděli podrobnosti, jako je například chybová zpráva.

   ![Kontextová nabídka Úkol nápravy – zdroj](../media/remediate-resources/resource-task-context-menu.png)

Prostředky nasazené prostřednictvím **nápravné úlohy** jsou přidány na kartu **Nasazené prostředky** na stránce dodržování zásad.

### <a name="create-a-remediation-task-through-azure-cli"></a>Vytvoření úkolu nápravy pomocí azure cli

Chcete-li vytvořit **úlohu nápravy** pomocí `az policy remediation` příkazového příkazu Azure, použijte příkazy. Nahraďte `{subscriptionId}` id `{myAssignmentId}` předplatného a **nasazením IfNotExists** nebo **upravte** ID přiřazení zásad.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Další příkazy a příklady nápravných opatření naleznete v příkazech [pro nápravu zásad az.](/cli/azure/policy/remediation)

### <a name="create-a-remediation-task-through-azure-powershell"></a>Vytvoření úkolu nápravy prostřednictvím Azure PowerShellu

Pokud chcete vytvořit **nápravnou úlohu** pomocí `Start-AzPolicyRemediation` Azure PowerShellu, použijte příkazy. Nahraďte `{subscriptionId}` id `{myAssignmentId}` předplatného a **nasazením IfNotExists** nebo **upravte** ID přiřazení zásad.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Další sanační rutiny a příklady naleznete v modulu [Az.PolicyInsights.](/powershell/module/az.policyinsights/#policy_insights)

## <a name="next-steps"></a>Další kroky

- Projděte si příklady na [ukázkách zásad Azure](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Pochopit, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](get-compliance-data.md).
- Zkontrolujte, co je skupina pro správu [pomocí organizace Uspořádat prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
