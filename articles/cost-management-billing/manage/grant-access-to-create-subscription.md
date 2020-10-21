---
title: Udělení přístupu pro vytváření předplatných Azure Enterprise
description: Zjistěte, jak dát instančnímu objektu nebo uživateli možnost programově vytvářet předplatná Azure Enterprise.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.reviewer: amberb
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: banders
ms.openlocfilehash: 02919cf2e35fdd4d981f3fde53085c2174f11bd1
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132563"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Udělení přístupu pro vytváření předplatných Azure Enterprise (Preview)

Jako zákazník Azure se [smlouvou Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) můžete jinému uživateli nebo instančnímu objektu poskytnout oprávnění pro vytváření předplatných fakturovaných na váš účet. V tomto článku se dozvíte, jak používat [řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) ke sdílení možností vytváření předplatných a jak auditovat vytváření předplatných. Pro účet, který chcete sdílet, musíte mít roli vlastníka.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Udělení přístupu

Pokud uživatelé chtějí [vytvářet předplatná v rámci registračního účtu](programmatically-create-subscription.md), musí pro tento účet mít [roli vlastníka Azure RBAC](../../role-based-access-control/built-in-roles.md#owner). Uživateli nebo skupině uživatelů můžete udělit roli vlastníka Azure RBAC pro registrační účet pomocí následujících kroků:

1. Získejte ID objektu registračního účtu, pro který chcete udělit přístup:

    Abyste mohli ostatním udělit roli vlastníka Azure RBAC v rámci registračního účtu, musíte mít pro tento účet roli vlastníka účtu nebo vlastníka Azure RBAC.

    # <a name="rest"></a>[REST](#tab/rest)

    Požádejte o výpis všech registračních účtů, ke kterým máte přístup:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Azure odpoví výpisem všech registračních účtů, ke kterým máte přístup:

    ```json
    {
      "value": [
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "SignUpEngineering@contoso.com"
          }
        },
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "BillingPlatformTeam@contoso.com"
          }
        }
      ]
    }
    ```

    K určení účtu, ke kterému chcete přidělit roli vlastníka Azure RBAC, použijte vlastnost `principalName`. Zkopírujte `name` tohoto účtu. Pokud například chcete přístup vlastníka Azure RBAC udělit registračnímu účtu SignUpEngineering@contoso.com, zkopírujete ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Je to ID objektu registračního účtu. Tuto hodnotu někam vložte, abyste ji mohli použít v dalším kroku jako `enrollmentAccountObjectId`.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    K zobrazení seznamu všech registračních účtů, ke kterým máte přístup, použijte rutinu [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount). Výběrem **Vyzkoušet** otevřete [Azure Cloud Shell](https://shell.azure.com/). Pokud chcete vložit kód, klikněte pravým tlačítkem myši na okno Shellu a vyberte **vložit**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure odpoví výpisem registračních účtů, ke kterým máte přístup:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    K určení účtu, ke kterému chcete přidělit roli vlastníka Azure RBAC, použijte vlastnost `principalName`. Zkopírujte `ObjectId` tohoto účtu. Pokud například chcete přístup vlastníka Azure RBAC udělit registračnímu účtu SignUpEngineering@contoso.com, zkopírujete ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Toto ID objektu někam vložte, abyste ho mohli použít v dalším kroku jako `enrollmentAccountObjectId`.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    Pomocí příkazu [az billing enrollment-account list](/cli/azure/billing) zobrazte výpis všech registračních účtů, ke kterým máte přístup. Výběrem **Vyzkoušet** otevřete [Azure Cloud Shell](https://shell.azure.com/). Pokud chcete vložit kód, klikněte pravým tlačítkem myši na okno Shellu a vyberte **vložit**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure odpoví výpisem registračních účtů, ke kterým máte přístup:

    ```json
    [
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "SignUpEngineering@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      },
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "BillingPlatformTeam@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      }
    ]
    ```

    ---

    K určení účtu, ke kterému chcete přidělit roli vlastníka Azure RBAC, použijte vlastnost `principalName`. Zkopírujte `name` tohoto účtu. Pokud například chcete přístup vlastníka Azure RBAC udělit registračnímu účtu SignUpEngineering@contoso.com, zkopírujete ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Je to ID objektu registračního účtu. Tuto hodnotu někam vložte, abyste ji mohli použít v dalším kroku jako `enrollmentAccountObjectId`.

1. <a id="userObjectId"></a>Získejte ID objektu uživatele nebo skupiny, kterým chcete poskytnout roli vlastníka Azure RBAC:

    1. Na webu Azure Portal hledejte v **Azure Active Directory**.
    1. Pokud chcete udělit přístup pro uživatele, v nabídce na levé straně vyberte **Uživatelé**. Pokud chcete udělit přístup ke skupině, vyberte **Skupiny**.
    1. Vyberte uživatele nebo skupinu, kterým chcete poskytnout roli vlastníka Azure RBAC.
    1. Pokud jste vybrali uživatele, najdete ID objektu na stránce profilu. Pokud jste vybrali skupinu, ID objektu bude uvedené na stránce přehledu. Výběrem ikony napravo od textového pole zkopírujte **ObjectID**. Někam ho vložte, abyste ho mohli použít v dalším kroku jako `userObjectId`.

1. Uživateli nebo skupině udělte roli vlastníka Azure RBAC pro registrační účet:

    Pomocí hodnot, které jste shromáždili v prvních dvou krocích, udělte uživateli nebo skupině roli vlastníka Azure RBAC pro registrační účet.

    # <a name="rest"></a>[REST](#tab/rest-2)

    Spusťte následující příkaz a nahraďte přitom ```<enrollmentAccountObjectId>``` hodnotou `name`, kterou jste zkopírovali v prvním kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Místo ```<userObjectId>``` použijte ID objektu, které jste zkopírovali v druhém kroku.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Když se role vlastníka úspěšně přiřadí v rozsahu registračního účtu, Azure odpoví informacemi o přiřazení role:

    ```json
    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>",
        "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "createdOn": "2018-03-05T08:36:26.4014813Z",
        "updatedOn": "2018-03-05T08:36:26.4014813Z",
        "createdBy": "<assignerObjectId>",
        "updatedBy": "<assignerObjectId>"
      },
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "<roleAssignmentGuid>"
    }
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell-2)

    Spusťte následující příkaz [New-AzRoleAssignment](../../role-based-access-control/role-assignments-powershell.md) a místo ```<enrollmentAccountObjectId>``` použijte hodnotu `ObjectId` zjištěnou v prvním kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Místo ```<userObjectId>``` použijte ID objektu, které jste získali v druhém kroku.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-2)

    Spusťte následující příkaz [az role assignment create](../../role-based-access-control/role-assignments-cli.md) a nahraďte přitom ```<enrollmentAccountObjectId>``` hodnotou `name`, kterou jste zkopírovali v prvním kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Místo ```<userObjectId>``` použijte ID objektu, které jste získali v druhém kroku.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Jakmile se uživatel stane vlastníkem Azure RBAC pro váš registrační účet, může v něm [programově vytvářet předplatná](programmatically-create-subscription.md). Předplatné vytvořené delegovaným uživatelem má jako správce služeb nadále původního vlastníka účtu, ale ve výchozím nastavení má také delegovaného uživatele jako vlastníka Azure RBAC.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Audit, kdo vytvořil odběry, s využitím protokolů aktivit

Pokud chcete sledovat předplatná vytvořená přes toto rozhraní API, použijte [rozhraní API pro protokoly aktivit tenanta](/rest/api/monitor/tenantactivitylogs). Ke sledování vytváření předplatných není možné použít PowerShell, rozhraní příkazového řádku ani Azure Portal.

1. Jako správce tenanta Azure AD [zvyšte úroveň přístupu](../../role-based-access-control/elevate-access-global-admin.md) uživateli provádějícímu audit a pak mu přiřaďte roli Čtenář v oboru `/providers/microsoft.insights/eventtypes/management`. Tento přístup je k dispozici v rámci rolí [Čtenář](../../role-based-access-control/built-in-roles.md#reader) a [Přispěvatel monitorování](../../role-based-access-control/built-in-roles.md#monitoring-contributor) a [vlastních rolí](../../role-based-access-control/custom-roles.md).
1. Jako uživatel provádějící audit můžete aktivity vytváření předplatných zobrazit voláním [rozhraní API pro protokoly aktivit tenanta](/rest/api/monitor/tenantactivitylogs). Příklad:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Pokud chcete toto rozhraní API pohodlně volat z příkazového řádku, vyzkoušejte [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Další kroky

* Teď když má uživatel nebo instanční objekt oprávnění k vytvoření předplatného, můžete tuto identitu použít k [programovému vytváření předplatných Azure Enterprise](programmatically-create-subscription.md).
* Příklad vytváření předplatných pomocí rozhraní .NET najdete v [ukázkovém kódu na GitHubu](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Další informace o Azure Resource Manageru a jeho rozhraních API najdete v tématu [Přehled Azure Resource Manageru](../../azure-resource-manager/management/overview.md).
* Další informace o správě velkého počtu předplatných pomocí skupin pro správu najdete v tématu věnovaném [uspořádání prostředků pomocí skupin pro správu v Azure](../../governance/management-groups/overview.md).
* Pokud si chcete projít kompletní doprovodné materiály k osvědčeným postupům pro zásady správného řízení pro předplatná ve velkých organizacích, přečtěte si téma [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance)