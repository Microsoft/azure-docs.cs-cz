---
title: Jak používat Role-Based Access Control v Azure API Management | Microsoft Docs
description: Naučte se používat předdefinované role a vytvářet vlastní role v Azure API Management
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
ms.openlocfilehash: faef2721b48ffab12264c585d2dec55ab9334016
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87015285"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Použití řízení přístupu na základě role ve službě API Management

Azure API Management spoléhá na řízení přístupu na základě role Azure (Azure RBAC) a umožňuje jemně odstupňovanou správu přístupu pro API Management služby a entity (například rozhraní API a zásady). Tento článek poskytuje přehled předdefinovaných a vlastních rolí v API Management. Další informace o správě přístupu v Azure Portal najdete v tématu [Začínáme se správou přístupu v Azure Portal](../role-based-access-control/overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Vestavěné role

API Management v současné době nabízí tři předdefinované role a v blízké budoucnosti bude přidávat další dvě role. Tyto role se dají přiřadit v různých oborech, včetně předplatného, skupiny prostředků a jednotlivých instancí API Management. Pokud například přiřadíte roli "API Management Service Reader" uživateli na úrovni skupiny prostředků, bude mít uživatel oprávnění ke čtení všech API Management instancí v rámci skupiny prostředků. 

Následující tabulka uvádí stručný popis předdefinovaných rolí. Tyto role můžete přiřadit pomocí Azure Portal nebo jiných nástrojů, včetně Azure [PowerShellu](../role-based-access-control/role-assignments-powershell.md), [azure CLI](../role-based-access-control/role-assignments-cli.md)a [REST API](../role-based-access-control/role-assignments-rest.md). Podrobnosti o tom, jak přiřadit předdefinované role, najdete v tématu [použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md).

| Role          | Přístup pro čtení<sup>[1]</sup> | Přístup pro zápis<sup>[2]</sup> | Vytvoření, odstranění, škálování, síť VPN a konfigurace vlastní domény služby | Přístup k staršímu portálu vydavatele | Description
| ------------- | ---- | ---- | ---- | ---- | ---- 
| Přispěvatel API Management služby | ✓ | ✓ | ✓ | ✓ | Super uživatel. Má úplný přístup CRUD k API Management službám a entitám (například rozhraní API a zásady). Má přístup k webu starší verze portálu vydavatele. |
| Čtecí modul API Management služby | ✓ | | || Má přístup jen pro čtení k API Management službám a entitám. |
| Operátor API Management služby | ✓ | | ✓ | | Může spravovat API Management Services, ale ne entity.|
| Editor služby API Management<sup>*</sup> | ✓ | ✓ | |  | Může spravovat API Management entit, ale ne služeb.|
| API Management správce obsahu<sup>*</sup> | ✓ | | | ✓ | Může spravovat portál pro vývojáře. Přístup ke službám a entitám jen pro čtení.|

<sup>[1] přístup pro čtení k API Management službám a entitám (například rozhraní API a zásady).</sup>

<sup>[2] přístup pro zápis do API Management služeb a entit s výjimkou následujících operací: vytvoření, odstranění a škálování instance; Konfigurace sítě VPN; a vlastní nastavení domény.</sup>

<sup>\* Role Editor služeb bude k dispozici po migraci všech uživatelských rozhraní správce z existujícího portálu vydavatele na Azure Portal. Role správce obsahu bude k dispozici po refaktorování portálu vydavatele, aby obsahovala pouze funkce související se správou portálu pro vývojáře.</sup>  

## <a name="custom-roles"></a>Vlastní role

Pokud žádná z předdefinovaných rolí nevyhovuje vašim konkrétním potřebám, je možné vytvořit vlastní role a zajistit tak podrobnější správu přístupu pro API Management entit. Můžete například vytvořit vlastní roli, která má k API Management službě přístup jen pro čtení, ale má jenom přístup pro zápis do jednoho konkrétního rozhraní API. Další informace o vlastních rolích najdete [v tématu vlastní role v Azure RBAC](../role-based-access-control/custom-roles.md). 

> [!NOTE]
> Aby bylo možné v Azure Portal zobrazit instanci API Management, musí vlastní role zahrnovat tuto ```Microsoft.ApiManagement/service/read``` akci.

Když vytváříte vlastní roli, je snazší začít s jednou z vestavěných rolí. Upravte atributy pro přidání **akcí**, **NotActions**nebo **AssignableScopes**a pak změny uložte jako novou roli. Následující příklad začíná rolí "API Management Service Reader" a vytvoří vlastní roli s názvem "Editor rozhraní API pro kalkulačku". Vlastní roli můžete přiřadit konkrétnímu rozhraní API. V důsledku toho má tato role k tomuto rozhraní API přístup jenom. 

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

Článek o [operacích poskytovatele prostředků Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) obsahuje seznam oprávnění, která je možné udělit na úrovni API Management.

## <a name="video"></a>Video


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Další kroky

Další informace o Role-Based Access Control v Azure najdete v následujících článcích:
  * [Začínáme se správou přístupu na webu Azure Portal](../role-based-access-control/overview.md)
  * [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md)
  * [Vlastní role v Azure RBAC](../role-based-access-control/custom-roles.md)
  * [Operace poskytovatele prostředků Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)
