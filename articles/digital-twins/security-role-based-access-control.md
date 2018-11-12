---
title: Principy řízení přístupu na základě rolí Azure digitální dvojče | Dokumentace Microsoftu
description: Další ověřování v digitální dvojče s řízením přístupu na základě rolí.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: b9ccdb9030a24520be8f24f757c279241f3a07e1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014784"
---
# <a name="role-based-access-control"></a>Řízení přístupu na základě role

Azure digitální dvojče umožňuje řízení přístupu na přesné ke konkrétní data, prostředkům a akcím v prostorových grafu. Dělá to pomocí granulární role a oprávnění správy nazývá řízení přístupu na základě role (RBAC). RBAC se skládá z _role_ a _přiřazení rolí_. Role určete úroveň oprávnění. Přiřazení rolí roli přidružit uživateli nebo zařízení.

Pomocí RBAC, oprávnění lze udělit práva:

- Uživatel.
- Zařízení.
- Objekt služby.
- Uživatelem definované funkce. 
- Všichni uživatelé, kteří patří do nějaké domény. 
- Tenant.
 
Úroveň přístupu také možné podrobně nastavit.

RBAC je jedinečný v tom, že se oprávnění dědí dolů na prostorový graf.

## <a name="what-can-i-do-with-rbac"></a>Co mi RBAC umožňuje?

Vývojáři mohou využít RBAC pro:

* Udělte schopnost spravovat zařízení pro celé sestavení, nebo jenom pro konkrétní místnost nebo dolní mez.
* Udělení globální přístup správce ke všem uzlům prostorový graf pro celý graf nebo pouze pro části grafu.
* Udělte přístup k podpoře specialista pro čtení do grafu, s výjimkou přístupové klíče.
* Udělení přístupu k doméně pro čtení ke všem objektům grafu každého člena.

## <a name="rbac-best-practices"></a>Osvědčené postupy RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Role

### <a name="role-definitions"></a>Definice rolí

Definice role je soubor oprávnění a se někdy označuje jako role. Seznamy definice role, které povolené operace, mezi které patří vytvoření, čtení, aktualizace a odstraňování. Určuje také na typy objektů, které se vztahují tato oprávnění.

Následující role jsou k dispozici v digitální dvojče Azure:

* **Místo správce**: vytvořit, číst, aktualizovat a odstranit oprávnění pro zadané místo a všechny uzly pod. Globální oprávnění.
* **Správce uživatelů**: vytvořit, číst, aktualizovat a odstranit oprávnění pro uživatele a objekty související s uživatelem. Oprávnění ke čtení pro mezery.
* **Správce zařízení**: vytvořit, číst, aktualizovat a odstranit oprávnění pro zařízení a zařízení související objekty. Oprávnění ke čtení pro mezery.
* **Klíč správce**: vytvořit, číst, aktualizovat a odstranit oprávnění pro přístupové klíče. Oprávnění ke čtení pro mezery.
* **Token správce**: oprávnění pro čtení a aktualizace pro přístupové klíče. Oprávnění ke čtení pro mezery.
* **Uživatel**: oprávnění ke čtení pro mezery, senzory a uživatelů, což zahrnuje odpovídající související objekty.
* **Podpora odborných**: oprávnění ke čtení pro všechno, co s výjimkou přístupové klíče.
* **Instalační program zařízení**: oprávnění pro čtení a aktualizace pro zařízení a senzorů, které obsahuje odpovídající související objekty. Oprávnění ke čtení pro mezery.
* **Zařízení brány**: vytvořit oprávnění pro senzory. Oprávnění ke čtení pro zařízení a senzorů, která obsahuje odpovídající související objekty.

>[!NOTE]
> K načtení úplné definice pro předchozí role, dotaz rozhraní API systému/rolí.

### <a name="object-types"></a>Typy objektů

`ObjectIdType` Odkazuje na typ identitu, která udělil roli. Kromě `DeviceId` a `UserDefinedFunctionId` typy, typy odpovídají vlastnost objektu služby Azure Active Directory (Azure AD):
  
* `UserId` Typ přiřadí roli uživatele.
* `DeviceId` Typ role přiřadí k zařízení.
* `DomainName` Typ přiřadí roli na název domény. Přístupová práva odpovídající roli má každý uživatel pomocí zadaného názvu domény.
* `TenantId` Typ přiřadí roli do tenanta. Každý uživatel, který patří do zadané ID tenanta Azure AD má přístupová práva odpovídající roli.
* `ServicePrincipalId` Typ přiřadí ID objektu zabezpečení služby role
* `UserDefinedFunctionId` Typ přiřadí roli uživatelem definované funkce (UDF).

> [!div class="nextstepaction"]
> [Dotaz nebo ID objektu uživatele](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)

> [!div class="nextstepaction"]
> [Získání ID objektu pro objekt zabezpečení služby](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.8.1)

> [!div class="nextstepaction"]
> [Načtení ID objektu pro tenanta služby Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

## <a name="role-assignments"></a>Přiřazení rolí

Udělení oprávnění pro příjemce, vytvořte přiřazení role. Odvolat oprávnění, odeberte přiřazení role. Přiřazení role Azure digitální dvojče objektu, například uživatele nebo tenantem Azure AD přidruží roli a mezerou. Oprávnění jsou udělena pro všechny objekty, které patří do tohoto místa. Místo zahrnuje celý prostorový graf pod ním.

Například uživatel dostane přiřazení role s rolí `DeviceInstaller` pro kořenový uzel prostorový graf, který představuje budovy. Uživatel pak číst a aktualizovat zařízení pro tento uzel a všechny ostatní podřízené prostory v budově.

## <a name="next-steps"></a>Další postup

Další informace o zabezpečení Azure digitální dvojče [vytvořit a spravovat přiřazení rolí](./security-create-manage-role-assignments.md).
