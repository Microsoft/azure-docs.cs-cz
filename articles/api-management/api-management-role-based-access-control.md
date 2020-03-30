---
title: Jak používat řízení přístupu na základě rolí ve správě rozhraní AZURE API | Dokumenty společnosti Microsoft
description: Naučte se používat integrované role a vytvářet vlastní role ve správě rozhraní Azure API
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: ed0cd51fc686735f2d9c110ce46d5904107cafc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430619"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Použití řízení přístupu na základě role ve službě API Management

Azure API Management spoléhá na Azure Role-Based Řízení přístupu (RBAC) povolit jemně odstupňovanou správu přístupu pro služby a entity správy rozhraní API (například rozhraní API a zásady). Tento článek poskytuje přehled předdefinovaných a vlastních rolí ve správě rozhraní API. Další informace o správě přístupu na webu Azure Portal najdete v tématu [Začínáme se správou přístupu na webu Azure Portal](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Vestavěné role

Správa rozhraní API aktuálně poskytuje tři předdefinované role a v blízké budoucnosti přidá další dvě role. Tyto role lze přiřadit v různých oborech, včetně předplatného, skupiny prostředků a jednotlivých instancí správy rozhraní API. Pokud například přiřadíte roli "Čtečka služby api pro správu" uživateli na úrovni skupiny prostředků, má uživatel přístup pro čtení ke všem instancím správy rozhraní API uvnitř skupiny prostředků. 

Následující tabulka obsahuje stručný popis předdefinovaných rolí. Tyto role můžete přiřadit pomocí portálu Azure nebo jiných nástrojů, včetně Azure [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)a [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest). Podrobnosti o tom, jak přiřadit předdefinované role, najdete v tématu [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

| Role          | Přístup pro čtení<sup>[1]</sup> | Přístup pro zápis<sup>[2]</sup> | Vytváření, odstraňování, škálování, vpn a konfigurace vlastní domény | Přístup ke staršímu portálu vydavatelů | Popis
| ------------- | ---- | ---- | ---- | ---- | ---- 
| Přispěvatel služby správy rozhraní API | ✓ | ✓ | ✓ | ✓ | Super uživatel. Má úplný přístup CRUD ke službám a entitám správy rozhraní API (například rozhraní API a zásady). Má přístup k portálu staršího vydavatele. |
| Čtečka služeb pro správu rozhraní API | ✓ | | || Má přístup jen pro čtení ke službám a entitám správy rozhraní API. |
| Operátor služby správy rozhraní API | ✓ | | ✓ | | Může spravovat služby správy rozhraní API, ale ne entity.|
| API Management Service Editor<sup>*</sup> | ✓ | ✓ | |  | Může spravovat entity správy rozhraní API, ale ne služby.|
| Správce obsahu správy rozhraní API<sup>*</sup> | ✓ | | | ✓ | Můžete spravovat portál pro vývojáře. Přístup jen pro čtení ke službám a entitám.|

<sup>[1] Přístup ke čtení ke službám a entitám správy rozhraní API (například rozhraní API a zásady).</sup>

<sup>[2] Přístup pro zápis ke službám a entitám správy rozhraní API s výjimkou následujících operací: vytváření, odstraňování a škálování instancí; Konfigurace VPN; a vlastní nastavení domény.</sup>

<sup>\*Role Editor služeb bude dostupná po migraci veškerého uživatelského rozhraní správce z existujícího portálu vydavatele na portál Azure. Role Správce obsahu bude k dispozici po refaktored portálu vydavatele obsahovat pouze funkce související se správou portálu pro vývojáře.</sup>  

## <a name="custom-roles"></a>Vlastní role

Pokud žádná z předdefinovaných rolí nesplňuje vaše specifické potřeby, lze vytvořit vlastní role, které poskytují podrobnější správu přístupu pro entity správy rozhraní API. Můžete například vytvořit vlastní roli, která má přístup jen pro čtení ke službě api Management, ale má pouze přístup pro zápis do jednoho konkrétního rozhraní API. Další informace o vlastních rolích najdete [v tématu Vlastní role v Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

> [!NOTE]
> Aby bylo možné zobrazit instanci správy rozhraní API na ```Microsoft.ApiManagement/service/read``` webu Azure Portal, musí vlastní role obsahovat akci.

Když vytvoříte vlastní roli, je jednodušší začít s jednou z předdefinovaných rolí. Upravte atributy pro přidání **akcí**, **NotActions**nebo **AssignableScopes**a uložte změny jako novou roli. Následující příklad začíná rolí "API Management Service Reader" a vytvoří vlastní roli nazvanou Editor rozhraní API kalkulačky. Vlastní roli můžete přiřadit ke konkrétnímu rozhraní API. V důsledku toho tato role má pouze přístup k tomuto rozhraní API. 

```powershell
$role = Get-AzRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzRoleDefinition -Role $role
New-AzRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

Článek [operace zprostředkovatele prostředků Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) obsahuje seznam oprávnění, která lze udělit na úrovni správy rozhraní API.

## <a name="video"></a>Video


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Další kroky

Další informace o řízení přístupu na základě rolí v Azure najdete v následujících článcích:
  * [Začínáme se správou přístupu na webu Azure Portal](../role-based-access-control/overview.md)
  * [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md)
  * [Vlastní role v Azure RBAC](../role-based-access-control/custom-roles.md)
  * [Operace zprostředkovatele prostředků Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)
