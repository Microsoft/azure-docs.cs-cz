---
title: Udělení přístupu k vytváření předplatných Azure Enterprise
description: Zjistěte, jak dát uživateli nebo instančnímu objektu možnost programově vytvářet předplatná Azure Enterprise.
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: b77efd7e5cf7ff016605e0ba2e74cff9ea8dab89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478874"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Udělení přístupu k vytváření předplatných Azure Enterprise (preview)

Jako zákazník Azure ve [smlouvě Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)můžete udělit jinému uživateli nebo hlavnímu uživateli služby oprávnění k vytváření předplatných účtovaných na váš účet. V tomto článku se dozvíte, jak pomocí řízení [přístupu na základě rolí (RBAC)](../../active-directory/role-based-access-control-configure.md) sdílet možnost vytvářet odběry a jak auditovat vytváření předplatného. Na účtu, který chcete sdílet, musíte mít roli vlastníka.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Udělení přístupu

Chcete-li [vytvořit předplatná v rámci účtu registrace](programmatically-create-subscription.md), musí mít uživatelé roli [vlastníka RBAC](../../role-based-access-control/built-in-roles.md#owner) pro tento účet. Můžete udělit uživateli nebo skupině uživatelů roli vlastníka RBAC na účtu registrace pomocí následujících kroků:

1. Získání ID objektu účtu registrace, ke kterého chcete udělit přístup

    Chcete-li udělit ostatním roli vlastníka RBAC na účtu registrace, musíte být vlastníkem účtu nebo vlastníkem RBAC účtu.

    # <a name="rest"></a>[Odpočinku](#tab/rest)

    Žádost o seznam všech účtů registrace, ke které máte přístup:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Azure reaguje se seznamem všech účtů registrace, ke kterým máte přístup:

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

    Pomocí `principalName` této vlastnosti můžete identifikovat účet, ke kterému chcete udělit přístup vlastníka RBAC. Zkopírujte `name` tento účet. Pokud byste například chtěli udělit vlastníkovi SignUpEngineering@contoso.com RBAC přístup k ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```účtu registrace, zkopírovali byste . Toto je ID objektu účtu registrace. Tuto hodnotu vložte někam, abyste ji `enrollmentAccountObjectId`mohli použít v dalším kroku jako .

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    Pomocí rutiny [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) můžete vypsat všechny účty registrace, ke které máte přístup. Vyberte **Vyzkoušet** otevřete [Azure Cloud Shell](https://shell.azure.com/). Chcete-li kód vložit, klepněte pravým tlačítkem myši na okna prostředí a vyberte **vložit**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure reaguje se seznamem účtů registrace, ke kterým máte přístup:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Pomocí `principalName` této vlastnosti můžete identifikovat účet, ke kterém chcete udělit přístup vlastníka RBAC. Zkopírujte `ObjectId` tento účet. Pokud byste například chtěli udělit vlastníkovi SignUpEngineering@contoso.com RBAC přístup k ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```účtu registrace, zkopírovali byste . Toto ID objektu vložte někam, abyste ho `enrollmentAccountObjectId`mohli použít v dalším kroku jako .

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    Pomocí příkazu [az billing-enrollment-account](https://aka.ms/EASubCreationPublicPreviewCLI) příkaz upsat všechny účty registrace, ke které máte přístup. Vyberte **Vyzkoušet** otevřete [Azure Cloud Shell](https://shell.azure.com/). Chcete-li kód vložit, klepněte pravým tlačítkem myši na okna prostředí a vyberte **vložit**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure reaguje se seznamem účtů registrace, ke kterým máte přístup:

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

    Pomocí `principalName` této vlastnosti můžete identifikovat účet, ke kterému chcete udělit přístup vlastníka RBAC. Zkopírujte `name` tento účet. Pokud byste například chtěli udělit vlastníkovi SignUpEngineering@contoso.com RBAC přístup k ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```účtu registrace, zkopírovali byste . Toto je ID objektu účtu registrace. Tuto hodnotu vložte někam, abyste ji `enrollmentAccountObjectId`mohli použít v dalším kroku jako .

1. <a id="userObjectId"></a>Získat ID objektu uživatele nebo skupiny, které chcete přidělit roli vlastníka RBAC

    1. Na webu Azure Portal vyhledejte **službu Azure Active Directory**.
    1. Pokud chcete uživateli udělit přístup, klikněte v nabídce vlevo na **Uživatelé.** Pokud chcete skupině udělit přístup, klikněte na **Skupiny**.
    1. Vyberte uživatele nebo skupinu, které chcete dát roli vlastníka RBAC.
    1. Pokud jste vybrali uživatele, najdete ID objektu na stránce Profil. Pokud jste vybrali skupinu, ID objektu se zobrazí na stránce Přehled. Zkopírujte **ObjektID** kliknutím na ikonu vpravo od textového pole. Vložte to někam, abyste ji mohli `userObjectId`použít v dalším kroku jako .

1. Udělit uživateli nebo skupině roli vlastníka RBAC v účtu registrace

    Pomocí hodnot, které jste shromáždili v prvních dvou krocích, udělte uživateli nebo seskupte roli vlastníka RBAC na účtu registrace.

    # <a name="rest"></a>[Odpočinku](#tab/rest-2)

    Spusťte následující příkaz ```<enrollmentAccountObjectId>``` a `name` nahrazte jej```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```zkopírovaným v prvním kroku ( ). Nahraďte ```<userObjectId>``` ID objektu, které jste zkopírovali z druhého kroku.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Když je role Vlastník úspěšně přiřazena v oboru účtu registrace, Azure odpoví informacemi o přiřazení role:

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

    Spusťte následující příkaz [New-AzRoleAssignment,](../../active-directory/role-based-access-control-manage-access-powershell.md) který nahradí ```<enrollmentAccountObjectId>``` `ObjectId` shromážděné```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```v prvním kroku ( ). Nahraďte ```<userObjectId>``` id objektu shromážděné v druhém kroku.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-2)

    Spusťte následující příkaz vytvořit přiřazení ```<enrollmentAccountObjectId>``` role `name` [az](../../active-directory/role-based-access-control-manage-access-azure-cli.md) a nahrazte jej zkopírovaným v prvním kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Nahraďte ```<userObjectId>``` id objektu shromážděné v druhém kroku.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Jakmile se uživatel stane vlastníkem RBAC pro váš účet registrace, může [programově vytvářet odběry](programmatically-create-subscription.md) pod ním. Předplatné vytvořené delegovaným uživatelem má stále původního vlastníka účtu jako správce služby, ale ve výchozím nastavení má také delegovaného uživatele jako vlastníka RBAC.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Auditovat, kdo vytvořil odběry pomocí protokolů aktivit

Chcete-li sledovat odběry vytvořené prostřednictvím tohoto rozhraní API, použijte [rozhraní API protokolu aktivit klienta](/rest/api/monitor/tenantactivitylogs). Momentálně není možné ke sledování vytváření předplatného použít PowerShell, CLI nebo Portál Azure.

1. Jako správce tenanta Azure AD [zvyšte úroveň přístupu](../../active-directory/role-based-access-control-tenant-admin-access.md) uživateli provádějícímu audit a pak mu přiřaďte roli Čtenář v oboru `/providers/microsoft.insights/eventtypes/management`.
1. Jako uživatel auditování zavolejte [rozhraní API protokolu aktivit klienta,](/rest/api/monitor/tenantactivitylogs) abyste viděli aktivity vytváření předplatného. Příklad:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Pokud chcete toto rozhraní API pohodlně volat z příkazového řádku, vyzkoušejte [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Další kroky

* Teď, když uživatel nebo instanční objekt má oprávnění k vytvoření předplatného, můžete tuto identitu použít k [programovému vytvoření předplatných Azure Enterprise](programmatically-create-subscription.md).
* Příklad při vytváření odběrů pomocí rozhraní .NET najdete [v ukázkovém kódu na GitHubu](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Další informace o Azure Resource Manager a jeho api, najdete v [tématu Přehled Azure Resource Manager](overview.md).
* Další informace o správě velkého počtu předplatných pomocí skupin pro správu najdete v [tématu Uspořádání prostředků pomocí skupin pro správu Azure.](../../governance/management-groups/overview.md)
* Pokud se chcete podívat na komplexní pokyny pro osvědčené postupy pro velké organizace v oblasti zásad správného řízení předplatného, přečtěte si informace [o podnikovém uživatelském právu Azure – normativní zásady správného řízení předplatného.](/azure/architecture/cloud-adoption-guide/subscription-governance)
