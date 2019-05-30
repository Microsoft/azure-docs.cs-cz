---
title: Udělení přístupu k vytvoření předplatného Azure Enterprise | Dokumentace Microsoftu
description: Zjistěte, jak umožnit uživatel nebo instanční objekt můžete programově vytvořit odběry Azure Enterprise.
services: azure-resource-manager
author: jureid
manager: jureid
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: 81f3edcfcffad1b9b6d0ab4b49769601196049ad
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238062"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Udělení přístupu k vytvoření předplatného Azure Enterprise (preview)

Jako zákazník Azure na [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), můžete udělit jiný uživatel nebo objekt instančního objektu oprávnění k vytvoření předplatného účtuje na příslušný účet. V tomto článku se dozvíte, jak používat [řízení přístupu na základě Role (RBAC)](../active-directory/role-based-access-control-configure.md) sdílet možnost vytvářet předplatná a jak auditovat vytvoření předplatného. Musíte mít roli vlastníka účtu, který chcete sdílet.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Udělení přístupu

K [vytvářet předplatná v rámci registrace účtu](programmatically-create-subscription.md), uživatelé musí mít [role RBAC vlastníka](../role-based-access-control/built-in-roles.md#owner) na tento účet. Můžete udělit uživateli nebo skupině uživatelů role RBAC vlastníka účtu registrace pomocí následujících kroků:

1. Získání ID objektu, který chcete udělit přístup k registračnímu účtu

    K udělení ostatní role RBAC vlastníka na účet pro zápis, musí být buď vlastník účtu nebo RBAC vlastníka účtu.

    # <a name="resttabrest"></a>[REST](#tab/rest)

    Požadavek na výpis všech registračních účtů, ke kterým máte přístup k:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Azure jako odpověď vrátí seznam všech registračních účtů, ke kterým máte přístup k:

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

    Použití `principalName` identifikovat účet, který chcete udělit přístup RBAC vlastníka pro vlastnost. Kopírovat `name` tohoto účtu. Například, pokud chcete udělit přístup RBAC vlastníka SignUpEngineering@contoso.com účet pro zápis, budou zkopírovány ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. To je ID objektu registrace účtu. Vložte tuto hodnotu někde, takže ho můžete použít v dalším kroku jako `enrollmentAccountObjectId`.

    # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

    Použití [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) rutiny pro zobrazení seznamu všechny registračních účtů, kterým máte přístup. Vyberte **vyzkoušet** otevřete [Azure Cloud Shell](https://shell.azure.com/). Vložte kód, kliknete pravým tlačítkem na prostředí windows a vyberte položku **vložte**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure jako odpověď vrátí seznam registračních účtů, ke kterým máte přístup k:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Použití `principalName` identifikovat účet, kterým chcete udělit přístup RBAC vlastníka pro vlastnost. Kopírovat `ObjectId` tohoto účtu. Například, pokud chcete udělit přístup RBAC vlastníka SignUpEngineering@contoso.com účet pro zápis, budou zkopírovány ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Vložte toto ID objektu někde, takže ho můžete použít v dalším kroku jako `enrollmentAccountObjectId`.

    # <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

    Použití [az fakturační účet registrace seznamu](https://aka.ms/EASubCreationPublicPreviewCLI) seznam všech registračních účtů, máte přístup k příkazu. Vyberte **vyzkoušet** otevřete [Azure Cloud Shell](https://shell.azure.com/). Vložte kód, kliknete pravým tlačítkem na prostředí windows a vyberte položku **vložte**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure jako odpověď vrátí seznam registračních účtů, ke kterým máte přístup k:

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

    Použití `principalName` identifikovat účet, který chcete udělit přístup RBAC vlastníka pro vlastnost. Kopírovat `name` tohoto účtu. Například, pokud chcete udělit přístup RBAC vlastníka SignUpEngineering@contoso.com účet pro zápis, budou zkopírovány ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. To je ID objektu registrace účtu. Vložte tuto hodnotu někde, takže ho můžete použít v dalším kroku jako `enrollmentAccountObjectId`.

1. <a id="userObjectId"></a>Získání ID objektu uživatele nebo skupiny, kterou chcete přidělit roli RBAC vlastníka

    1. Na webu Azure Portal, hledání **Azure Active Directory**.
    1. Pokud chcete udělit přístup uživatelům, klikněte na **uživatelé** v nabídce na levé straně. Pokud chcete udělit přístup do skupiny, klikněte na tlačítko **skupiny**.
    1. Vyberte uživatele nebo skupinu, kterou chcete přidělit roli RBAC vlastníka.
    1. Pokud jste vybrali uživatele, najdete na stránce profil ID objektu. Pokud vyberete skupinu, ID objektu bude na stránce Přehled. Kopírovat **ObjectID** kliknutím na ikonu vedle textového pole. Vložte tento někam tak, aby ho můžete použít v dalším kroku jako `userObjectId`.

1. Udělit uživateli nebo skupině role RBAC vlastníka na účet pro zápis

    Pomocí hodnoty, které jste shromáždili v první dva kroky, udělit uživateli nebo skupině role RBAC vlastníka na účet pro zápis.

    # <a name="resttabrest-2"></a>[REST](#tab/rest-2)

    Spusťte následující příkaz, nahrazení ```<enrollmentAccountObjectId>``` s `name` jste zkopírovali v prvním kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Nahraďte ```<userObjectId>``` s ID objektu, který jste zkopírovali v druhém kroku.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Když v rozsahu účtu registrace úspěšně přiřazení role vlastníka, Azure jako odpověď vrátí informace o přiřazení role:

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

    Spusťte následující příkaz [New-AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) příkaz a nahraďte ```<enrollmentAccountObjectId>``` s `ObjectId` shromážděných v prvním kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Nahraďte ```<userObjectId>``` s objektem ID shromážděných v druhém kroku.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-clitabazure-cli-2"></a>[Azure CLI](#tab/azure-cli-2)

    Spusťte následující příkaz [vytvořit přiřazení role az](../active-directory/role-based-access-control-manage-access-azure-cli.md) příkaz a nahraďte ```<enrollmentAccountObjectId>``` s `name` jste zkopírovali v prvním kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Nahraďte ```<userObjectId>``` s objektem ID shromážděných v druhém kroku.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Jakmile uživatel se stane vlastníkem RBAC pro váš účet pro zápis, mohou [programově vytvořit odběry](programmatically-create-subscription.md) pod ním. Předplatné vytvořené delegovaný uživatel má stále původního vlastníka účtu jako správce služby, ale má také delegovaný uživatel jako vlastníka RBAC ve výchozím nastavení.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Audit, který vytvořil předplatných, pomocí protokolů aktivit

Chcete-li sledovat předplatná vytvořená přes toto rozhraní API, použijte [rozhraní API pro klienty aktivitu protokolu](/rest/api/monitor/tenantactivitylogs). Aktuálně není možné pomocí Powershellu, rozhraní příkazového řádku nebo portálu Azure portal ke sledování vytváření odběru.

1. Jako správce tenanta Azure AD [zvyšte úroveň přístupu](../active-directory/role-based-access-control-tenant-admin-access.md) uživateli provádějícímu audit a pak mu přiřaďte roli Čtenář v oboru `/providers/microsoft.insights/eventtypes/management`.
1. Jako uživatel, auditování, zavolejte [rozhraní API pro klienty aktivitu protokolu](/rest/api/monitor/tenantactivitylogs) zobrazíte předplatné Vytvoření aktivity. Příklad:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Pokud chcete toto rozhraní API pohodlně volat z příkazového řádku, vyzkoušejte [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Další postup

* Teď, když uživatel nebo instanční objekt služby má oprávnění k vytvoření předplatného, můžete použít tuto identitu do [programově vytvářet předplatná Azure Enterprise](programmatically-create-subscription.md).
* Příklad vytvoření předplatného pomocí rozhraní .NET, naleznete v tématu [ukázkový kód na Githubu](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Další informace o Azure Resource Manageru a jeho rozhraní API najdete v tématu [přehled Azure Resource Manageru](resource-group-overview.md).
* Další informace o správě velkého počtu předplatných pomocí skupin pro správu najdete v tématu [uspořádání prostředků se skupinami pro správu Azure](management-groups-overview.md)
* Komplexní osvědčených postupů pokyny pro velké organizace na předplatné zásady správného řízení najdete v tématu [Základní kostra Azure enterprise – zásady správného řízení předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance)
