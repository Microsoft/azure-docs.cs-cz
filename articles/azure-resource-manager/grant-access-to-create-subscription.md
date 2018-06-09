---
title: Udělení přístupu k vytvoření předplatného Azure Enterprise | Microsoft Docs
description: Zjistěte, jak poskytnout uživateli nebo instanční objekt možnost vytváření prostřednictvím kódu programu předplatných Azure Enterprise.
services: azure-resource-manager
author: jlian
manager: jlian
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: jlian
ms.openlocfilehash: 4c5d505f431ef684b73adc04629464883d336a5b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238258"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Udělení přístupu k vytvoření předplatného Azure Enterprise (preview)

Jako Azure odběratele na [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), můžete udělit jiného uživatele nebo službu hlavní oprávnění k vytvoření odběrů účtují ke svému účtu. V tomto článku se dozvíte, jak používat [řízení přístupu na základě Role (RBAC)](../active-directory/role-based-access-control-configure.md) sdílet schopnost vytvářet odběry a auditování vytváření předplatného.

Vytvoření odběru naleznete v tématu [prostřednictvím kódu programu vytvářet odběry Azure Enterprise (preview)](programmatically-create-subscription.md).

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>Delegovat přístup k účtu registrace pomocí RBAC

Umožnit jiný uživatel nebo instanční objekt schopnost vytvářet odběry proti určitého účtu, [jim poskytnout roli vlastníka RBAC v oboru účet zápisu](../active-directory/role-based-access-control-manage-access-rest.md). Následující příklad obsahuje uživatele v klientovi s `principalId` z `<userObjectId>` (pro SignUpEngineering@contoso.com) roli vlastníka na účet pro zápis. Registrace účtu ID a ID objektu zabezpečení, najdete v tématu [prostřednictvím kódu programu vytvářet odběry Azure Enterprise (preview)](programmatically-create-subscription.md).

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
Když jsou v rozsahu účtu registrace úspěšně přiřazeny roli vlastníka, odpoví Azure informace o přiřazení role:

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

Použití [New-AzureRmRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) k přidělit jinému uživateli vlastníka přístup ke svému účtu registrace.

```azurepowershell-interactive
New-AzureRmRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Použití [vytvořit přiřazení role az](../active-directory/role-based-access-control-manage-access-azure-cli.md) k přidělit jinému uživateli vlastníka přístup ke svému účtu registrace.

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

----

Jakmile uživatel změní na vlastníka RBAC pro váš účet pro zápis, mohou prostřednictvím kódu programu vytvářet odběry v něm. Předplatné vytvořené delegovaný uživatel má stále původního vlastníka účtu jako správce služby, ale má také delegovaný uživatel jako vlastníka ve výchozím nastavení. 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Audit, který vytvořil odběry pomocí protokoly aktivity

Chcete-li sledovat odběry vytvořené prostřednictvím toto rozhraní API, použijte [rozhraní API pro klienty aktivity protokolu](/rest/api/monitor/tenantactivitylogs). Aktuálně není možné použít PowerShell, rozhraní příkazového řádku nebo portálu Azure ke sledování vytváření odběru.

1. Jako správce klienta klienta Azure AD [zvýšení přístupu](../active-directory/role-based-access-control-tenant-admin-access.md) pak auditování uživateli podle oboru přiřadit role Čtenář `/providers/microsoft.insights/eventtypes/management`.
1. Jako uživatel auditování, volání [rozhraní API pro klienty aktivity protokolu](/rest/api/monitor/tenantactivitylogs) zobrazíte předplatné Vytvoření aktivity. Příklad:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> Toto rozhraní API pohodlně volat z příkazového řádku, zkuste [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Další postup

* Teď, když uživatel nebo instanční objekt má oprávnění k vytvoření odběru, můžete použít tuto identitu do [prostřednictvím kódu programu vytvářet odběry Azure Enterprise](programmatically-create-subscription.md).
* Příklad k vytvoření odběrů pomocí rozhraní .NET, naleznete v části [ukázkový kód na Githubu](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Další informace o Azure Resource Manager a jejích rozhraní API najdete v tématu [přehled Azure Resource Manageru](resource-group-overview.md).
* Další informace o správě velkého počtu odběry pomocí skupin pro správu najdete v tématu [uspořádání prostředků s skupin pro správu Azure](management-groups-overview.md)
* Komplexní osvědčených postupů pokyny pro velké organizace na řízení předplatného najdete v tématu [Azure enterprise vygenerované uživatelské rozhraní – zásady správného řízení doporučený předplatného](/azure/architecture/cloud-adoption-guide/subscription-governance)
