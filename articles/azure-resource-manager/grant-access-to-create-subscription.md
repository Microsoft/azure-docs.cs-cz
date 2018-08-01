---
title: Udělení přístupu k vytvoření předplatného Azure Enterprise | Dokumentace Microsoftu
description: Zjistěte, jak umožnit uživatel nebo instanční objekt můžete programově vytvořit odběry Azure Enterprise.
services: azure-resource-manager
author: adpick
manager: adpick
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: adpick
ms.openlocfilehash: 86e457cf553c84386937c35bab1ab0fd20518bed
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39369055"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Udělení přístupu k vytvoření předplatného Azure Enterprise (preview)

Jako zákazník Azure na [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), můžete udělit jiný uživatel nebo objekt instančního objektu oprávnění k vytvoření předplatného účtuje na příslušný účet. V tomto článku se dozvíte, jak používat [řízení přístupu na základě Role (RBAC)](../active-directory/role-based-access-control-configure.md) sdílet možnost vytvářet předplatná a jak auditovat vytvoření předplatného. Musíte mít roli vlastníka účtu, který chcete sdílet.

Jak vytvoříte odběr, naleznete v tématu [programově vytvářet předplatná Azure Enterprise (preview)](programmatically-create-subscription.md).

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>Delegování přístupu k účtu služby registrace pomocí RBAC

Udělit jinému uživateli nebo instančnímu objektu možnost vytvářet předplatná na konkrétní účet [přiřadit roli RBAC vlastníka v oboru registračnímu účtu](../active-directory/role-based-access-control-manage-access-rest.md). Následující příklad uděluje uživateli v tenantovi se `principalId` z `<userObjectId>` (pro SignUpEngineering@contoso.com) roli vlastníka na účet pro zápis. Registrace ID účtu a ID objektu zabezpečení najdete v tématu [programově vytvářet předplatná Azure Enterprise (preview)](programmatically-create-subscription.md).

# <a name="resttabrest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

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

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Použití [New-AzureRmRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) přidělit jinému uživateli přístup vlastníka účtu registrace.

```azurepowershell-interactive
New-AzureRmRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Použití [vytvořit přiřazení role az](../active-directory/role-based-access-control-manage-access-azure-cli.md) přidělit jinému uživateli přístup vlastníka účtu registrace.

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

----

Jakmile uživatel se stane vlastníkem RBAC pro váš účet pro zápis, může prostřednictvím kódu programu vytvořit odběry pod ním. Předplatné vytvořené delegovaný uživatel má stále původního vlastníka účtu jako správce služby, ale má také delegovaný uživatel jako vlastníka ve výchozím nastavení. 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Audit, který vytvořil předplatných, pomocí protokolů aktivit

Chcete-li sledovat předplatná vytvořená přes toto rozhraní API, použijte [rozhraní API pro klienty aktivitu protokolu](/rest/api/monitor/tenantactivitylogs). Aktuálně není možné pomocí Powershellu, rozhraní příkazového řádku nebo portálu Azure portal ke sledování vytváření odběru.

1. Jako správce tenanta Azure AD tenanta [zvýšení úrovně přístupu](../active-directory/role-based-access-control-tenant-admin-access.md) pak auditování uživateli přes obor přiřadit role Čtenář `/providers/microsoft.insights/eventtypes/management`.
1. Jako uživatel, auditování, zavolejte [rozhraní API pro klienty aktivitu protokolu](/rest/api/monitor/tenantactivitylogs) zobrazíte předplatné Vytvoření aktivity. Příklad:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> Jednoduše volat toto rozhraní API z příkazového řádku, zkuste [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Další postup

* Teď, když uživatel nebo instanční objekt služby má oprávnění k vytvoření předplatného, můžete použít tuto identitu do [programově vytvářet předplatná Azure Enterprise](programmatically-create-subscription.md).
* Příklad vytvoření předplatného pomocí rozhraní .NET, naleznete v tématu [ukázkový kód na Githubu](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Další informace o Azure Resource Manageru a jeho rozhraní API najdete v tématu [přehled Azure Resource Manageru](resource-group-overview.md).
* Další informace o správě velkého počtu předplatných pomocí skupin pro správu najdete v tématu [uspořádání prostředků se skupinami pro správu Azure](management-groups-overview.md)
* Komplexní osvědčených postupů pokyny pro velké organizace na předplatné zásady správného řízení najdete v tématu [Základní kostra Azure enterprise – zásady správného řízení předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance)
