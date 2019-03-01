---
title: Použití řízení přístupu na základě Role ve službě Azure API Management | Dokumentace Microsoftu
description: Zjistěte, jak použít předdefinované role a vytvářet vlastní role ve službě Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 21e7406d566be5be73342d530a4ad66b80a1d23f
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990437"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Použití řízení přístupu na základě Role ve službě Azure API Management
Azure API Management se spoléhá na Azure Role-Based řízení přístupu (RBAC) umožňuje přesnou správu přístupu služby API Management a entity (například rozhraní API a zásady). Tento článek poskytuje přehled předdefinovaných a vlastních rolí ve službě API Management. Další informace o řízení přístupu na webu Azure Portal najdete v tématu [Začínáme se správou přístupu na webu Azure Portal](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Vestavěné role
API Management nyní poskytuje tři předdefinované role a přidá dvě další role v blízké budoucnosti. Pracovníci v těchto rolích mohou být přiřazeny v různých oborech, včetně předplatného, skupiny prostředků a jednotlivé instance služby API Management. Například pokud uživatel na úrovni skupiny prostředků přiřadíte role možnost Čtenář, služba Azure API Management", pak uživatel má přístup pro čtení všechny instance API Management v rámci skupiny prostředků. 

Následující tabulka obsahuje stručný popis předdefinované role. Tyto role můžete přiřadit pomocí webu Azure portal nebo jiné nástroje, včetně Azure [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli), a [rozhraní REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest). Podrobnosti o tom, jak přiřadit předdefinované role najdete v tématu [použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

| Role          | Oprávnění ke čtení<sup>[1]</sup> | Přístup pro zápis<sup>[2]</sup> | Vytvoření služby, odstranění, škálování, VPN a konfigurace vlastní domény | Přístup k portálu pro vydavatele starší verze | Popis
| ------------- | ---- | ---- | ---- | ---- | ---- | ---- |
| Přispěvatel služby Azure API Management | ✓ | ✓ | ✓ | ✓ | Superuživatel. Má plný přístup CRUD služby API Management a entity (například rozhraní API a zásady). Má přístup k portálu pro vydavatele starší verze. |
| Čtecí zařízení služby Azure API Management | ✓ | | || Má přístup jen pro čtení služby API Management a entity. |
| Azure API Management Service – operátor | ✓ | | ✓ | | Může spravovat služby API Management, ale ne entity.|
| Azure API Management Service Editor<sup>*</sup> | ✓ | ✓ | |  | Může spravovat entity API Management, ale ne služby.|
| Azure API Management Content Manager<sup>*</sup> | ✓ | | | ✓ | Můžete spravovat na portálu pro vývojáře. Přístup jen pro čtení ke službám a entity.|

<sup>[1] přístup pro čtení služby API Management a entity (například rozhraní API a zásady).</sup>

<sup>[2] k zápisu do služby API Management a entity s výjimkou následujících operací: instance vytváření, odstraňování a škálování; Konfiguraci sítě VPN. a nastavení vlastní domény.</sup>

<sup>\* Editor služby role bude k dispozici, poté, co jsme migraci všech uživatelského rozhraní pro správu z existující portál pro vydavatele na webu Azure portal. Role správce obsahu bude k dispozici, až portál vydavatele je teď vyčleněný tak, aby obsahovala pouze funkce související se správou portálu pro vývojáře.</sup>  

## <a name="custom-roles"></a>Vlastní role
Pokud žádná z předdefinovaných rolí nevyhovuje vašim konkrétním potřebám, můžete zajistit podrobnější řízení přístupu pro API Management entity vytvořit vlastní role. Například můžete vytvořit vlastní roli, která má přístup jen pro čtení ke službě API Management, ale má pouze oprávnění k zápisu do jednoho konkrétního rozhraní API. Další informace o vlastních rolích najdete v tématu [vlastní role Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

> [!NOTE]
> Aby bylo možné zobrazit instance služby API Management na webu Azure Portal, musí obsahovat vlastní role ```Microsoft.ApiManagement/service/read``` akce.

Když vytvoříte vlastní roli, je jednodušší začít s některou z předdefinovaných rolí. Upravit atributy pro přidání **akce**, **NotActions**, nebo **AssignableScopes**a následně změny uložte novou roli. V následujícím příkladu začíná roli možnost Čtenář, služba Azure API Management"a vytváří vlastní roli s názvem" Kalkulačka API Editor. " Vlastní role můžete přiřadit konkrétní rozhraní API. V důsledku toho tato role má přístup pouze rozhraní API. 

```
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

[Operace poskytovatele prostředků Azure Resource Manageru](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) článek obsahuje seznam oprávnění, která může být na úrovni API Management.

## <a name="video"></a>Video


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Další postup

Další informace o řízení přístupu na základě rolí v Azure, najdete v následujících článcích:
  * [Začínáme se správou přístupu na webu Azure Portal](../role-based-access-control/overview.md)
  * [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md)
  * [Vlastní role v Azure RBAC](../role-based-access-control/custom-roles.md)
  * [Operace poskytovatele prostředků Azure Resource Manageru](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)

