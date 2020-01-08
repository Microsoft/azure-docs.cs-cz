---
title: Udělení přístupu k vytváření předplatných Azure Enterprise
description: Naučte se, jak dát uživateli nebo instančnímu objektu možnost programově vytvářet předplatná Azure Enterprise.
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: b77efd7e5cf7ff016605e0ba2e74cff9ea8dab89
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75478874"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Udělení přístupu k vytvoření předplatných Azure Enterprise (Preview)

Jako zákazník Azure on [smlouva Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)můžete jinému uživateli nebo instančnímu objektu udělit oprávnění k vytváření předplatných, která se fakturují k vašemu účtu. V tomto článku se dozvíte, jak pomocí [Access Control na základě rolí (RBAC)](../../active-directory/role-based-access-control-configure.md) sdílet možnost vytvářet předplatná a jak auditovat vytváření předplatných. Na účtu, který chcete sdílet, musíte mít roli vlastníka.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Udělení přístupu

Aby bylo možné [v rámci účtu pro registraci vytvořit předplatná](programmatically-create-subscription.md), musí mít uživatelé na daném účtu [roli vlastníka RBAC](../../role-based-access-control/built-in-roles.md#owner) . Můžete uživateli nebo skupině uživatelů udělit roli vlastníka RBAC na účtu pro zápis pomocí následujících kroků:

1. Získat ID objektu účtu pro registraci, kterému chcete udělit přístup

    Chcete-li udělit ostatním rolím vlastníka RBAC na účtu pro zápis, musíte být buď vlastníkem účtu, nebo vlastníkem role RBAC účtu.

    # <a name="resttabrest"></a>[REST](#tab/rest)

    Požadavek na výpis všech účtů pro zápis, ke kterým máte přístup:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Azure odpoví seznamem všech účtů zápisu, ke kterým máte přístup:

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

    Pomocí vlastnosti `principalName` Identifikujte účet, ke kterému chcete udělit přístup vlastníka RBAC. Zkopírujte `name` tohoto účtu. Pokud byste například chtěli udělit účtu pro zápis k SignUpEngineering@contoso.com přístup vlastníka RBAC, měli byste zkopírovat ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Toto je ID objektu registračního účtu. Vložte tuto hodnotu někam, abyste ji mohli použít v dalším kroku jako `enrollmentAccountObjectId`.

    # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

    Pomocí rutiny [Get-AzEnrollmentAccount Zobrazte](/powershell/module/az.billing/get-azenrollmentaccount) seznam všech registračních účtů, ke kterým máte přístup. Vyberte **zkusit** pro otevření [Azure Cloud Shell](https://shell.azure.com/). Kód vložíte tak, že kliknete pravým tlačítkem myši na okna prostředí a vyberete **Vložit**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure odpoví seznamem účtů pro zápis, ke kterým máte přístup:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Pomocí vlastnosti `principalName` Identifikujte účet, ke kterému chcete udělit přístup vlastníka RBAC. Zkopírujte `ObjectId` tohoto účtu. Pokud byste například chtěli udělit účtu pro zápis k SignUpEngineering@contoso.com přístup vlastníka RBAC, měli byste zkopírovat ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Vložte toto ID objektu někam, abyste ho mohli použít v dalším kroku jako `enrollmentAccountObjectId`.

    # <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

    K vypsání všech registračních účtů, ke kterým máte přístup, použijte příkaz [AZ disenrollment-Account list](https://aka.ms/EASubCreationPublicPreviewCLI) . Vyberte **zkusit** pro otevření [Azure Cloud Shell](https://shell.azure.com/). Kód vložíte tak, že kliknete pravým tlačítkem myši na okna prostředí a vyberete **Vložit**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure odpoví seznamem účtů pro zápis, ke kterým máte přístup:

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

    Pomocí vlastnosti `principalName` Identifikujte účet, ke kterému chcete udělit přístup vlastníka RBAC. Zkopírujte `name` tohoto účtu. Pokud byste například chtěli udělit účtu pro zápis k SignUpEngineering@contoso.com přístup vlastníka RBAC, měli byste zkopírovat ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Toto je ID objektu registračního účtu. Vložte tuto hodnotu někam, abyste ji mohli použít v dalším kroku jako `enrollmentAccountObjectId`.

1. <a id="userObjectId"></a>Získat ID objektu uživatele nebo skupiny, kterým chcete přidělit roli vlastníka RBAC

    1. V Azure Portal vyhledejte **Azure Active Directory**.
    1. Pokud chcete udělit přístup pro uživatele, klikněte na tlačítko **Uživatelé** v nabídce na levé straně. Pokud chcete udělit přístup ke skupině, klikněte na **skupiny**.
    1. Vyberte uživatele nebo skupinu, kterým chcete přidělit roli vlastníka RBAC.
    1. Pokud jste vybrali uživatele, najdete ID objektu na stránce profilu. Pokud jste vybrali skupinu, ID objektu se zobrazí na stránce Přehled. Zkopírujte **identifikátor objectID** kliknutím na ikonu napravo od textového pole. Vložte to někam, abyste ho mohli použít v dalším kroku jako `userObjectId`.

1. Udělit uživateli nebo skupině roli vlastníka RBAC v účtu pro zápis

    Pomocí hodnot, které jste shromáždili v prvním dvou krocích, udělte uživateli nebo skupině roli vlastníka RBAC v účtu pro zápis.

    # <a name="resttabrest-2"></a>[REST](#tab/rest-2)

    Spusťte následující příkaz a nahraďte ```<enrollmentAccountObjectId>``` `name` jste si zkopírovali v prvním kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Nahraďte ```<userObjectId>``` číslem ID objektu, který jste zkopírovali z druhého kroku.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Když se role vlastníka úspěšně přiřadí v oboru účtu registrace, Azure odpoví informacemi o přiřazení role:

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

    # <a name="powershelltabazure-powershell-2"></a>[PowerShell](#tab/azure-powershell-2)

    Spusťte následující příkaz [New-AzRoleAssignment](../../active-directory/role-based-access-control-manage-access-powershell.md) , který nahradí ```<enrollmentAccountObjectId>``` `ObjectId` shromážděnými v prvním kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Nahraďte ```<userObjectId>``` číslem ID objektu shromážděným v druhém kroku.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-clitabazure-cli-2"></a>[Azure CLI](#tab/azure-cli-2)

    Spusťte následující příkaz [AZ role Assignment Create](../../active-directory/role-based-access-control-manage-access-azure-cli.md) a nahraďte ```<enrollmentAccountObjectId>``` `name` jste si zkopírovali v prvním kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Nahraďte ```<userObjectId>``` číslem ID objektu shromážděným v druhém kroku.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Jakmile se uživatel pro váš registrační účet stal vlastníkem RBAC, může [programově vytvořit předplatná](programmatically-create-subscription.md) . Předplatné vytvořené delegovaným uživatelem má nadále původní vlastníka účtu jako správce služeb, ale ve výchozím nastavení má delegovaného uživatele jako vlastníka RBAC.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Audit, který vytvořil odběry pomocí protokolů aktivit

Pokud chcete sledovat odběry vytvořené přes toto rozhraní API, použijte [rozhraní API protokolu aktivit klienta](/rest/api/monitor/tenantactivitylogs). V současné době není možné pomocí PowerShellu, CLI ani Azure Portal sledovat vytváření předplatného.

1. Jako správce tenanta Azure AD [zvyšte úroveň přístupu](../../active-directory/role-based-access-control-tenant-admin-access.md) uživateli provádějícímu audit a pak mu přiřaďte roli Čtenář v oboru `/providers/microsoft.insights/eventtypes/management`.
1. Jako uživatel s auditem zavolejte [rozhraní API protokolu aktivit tenanta](/rest/api/monitor/tenantactivitylogs) , abyste viděli aktivity vytváření předplatných. Příklad:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Pokud chcete toto rozhraní API pohodlně volat z příkazového řádku, vyzkoušejte [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Další kroky

* Když má uživatel nebo instanční objekt oprávnění k vytvoření předplatného, můžete tuto identitu použít k [programovému vytváření předplatných Azure Enterprise](programmatically-create-subscription.md).
* Příklad vytváření předplatných pomocí .NET najdete v tématu [vzorový kód na GitHubu](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Další informace o Azure Resource Manager a jeho rozhraních API najdete v tématu [Azure Resource Manager Overview](overview.md).
* Další informace o správě velkého počtu předplatných pomocí skupin pro správu najdete v tématu [uspořádání prostředků pomocí skupin pro správu Azure](../../governance/management-groups/overview.md) .
* Podrobné pokyny k osvědčeným postupům pro velké organizace týkající se zásad správného řízení předplatného najdete v tématu zásady [správného řízení předplatného Azure Enterprise](/azure/architecture/cloud-adoption-guide/subscription-governance) .
