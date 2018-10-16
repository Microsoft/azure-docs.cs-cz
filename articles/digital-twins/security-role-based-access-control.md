---
title: Principy Azure digitální dvojče na základě rolí řízení přístupu | Dokumentace Microsoftu
description: Další ověřování v digitální dvojče s řízením přístupu na základě rolí.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: 7a6d8565a0f85b4cb81d9f5f23b04fe6b2edc53e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324063"
---
# <a name="role-based-access-control"></a>Řízení přístupu na základě role

Azure digitální dvojče umožňuje řízení přístupu na přesné ke konkrétní data, prostředkům a akcím v prostorových grafu. Provádí se prostřednictvím detailní role a oprávnění správy volá _řízení přístupu na základě Role_. Řízení přístupu na základě role se skládá z _role_, nebo úroveň oprávnění, a _přiřazení rolí_, nebo přidružení roli uživatele nebo zařízení.

Pomocí řízení přístupu na základě Role, oprávnění lze udělit práva uživatele, zařízení, instanční objekt služby, uživatelem definované funkce, všichni uživatelé náležející k doméně nebo tenanta. Kromě toho stupeň přístup také možné podrobně nastavit.

Řízení přístupu na základě role je jedinečný v tom, že se oprávnění dědí dolů na prostorový graf.

## <a name="what-can-i-do-with-role-based-access-control"></a>Co můžu dělat s řízením přístupu na základě rolí?

Vývojáři mohou využít řízení přístupu na základě rolí pro:

* Udělte schopnost spravovat zařízení pro celé sestavení, nebo jenom pro konkrétní místnost nebo dolní mez.
* Udělení globální přístup správce ke všem uzlům prostorový graf pro celý graf nebo pouze pro části grafu.
* Udělte přístup k podpoře specialista pro čtení do grafu, s výjimkou přístupové klíče.
* Udělení přístupu k doméně pro čtení ke všem objektům grafu každého člena.

## <a name="role-based-access-control-best-practices"></a>Osvědčené postupy pro řízení přístupu na základě role

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Role

### <a name="role-definitions"></a>Definice rolí

A **definice role** je kolekce oprávnění a se někdy označuje jako **role**. Definice role obsahuje seznam povolených operací včetně *vytvořit*, *čtení*, *aktualizovat*, a *odstranit*. Určuje také na typy objektů, které se vztahují tato oprávnění.

Následující role jsou k dispozici v digitální dvojče Azure:

* **Místo správce**: vytvořit, číst, aktualizovat a odstranit oprávnění pro zadané místo a všechny uzly pod. Globální oprávnění.
* **Správce uživatelů**: vytvořit, číst, aktualizovat a odstranit oprávnění pro uživatele a objekty související s uživatelem. Oprávnění ke čtení pro mezery.
* **Správce zařízení**: vytvořit, číst, aktualizovat a odstranit oprávnění pro zařízení a zařízení související objekty. Oprávnění ke čtení pro mezery.
* **Klíč správce**: vytvořit, číst, aktualizovat a odstranit oprávnění pro přístupové klíče. Oprávnění ke čtení pro mezery.
* **Token správce**: oprávnění pro čtení a aktualizace pro přístupové klíče. Oprávnění ke čtení pro mezery.
* **Uživatel**: oprávnění ke čtení pro mezery, senzory a uživatelů, včetně jejich odpovídající souvisejících objektů.
* **Podpora odborných**: oprávnění ke čtení pro všechno, co s výjimkou přístupové klíče.
* **Instalační program zařízení**: oprávnění pro čtení a aktualizace pro zařízení a snímačů, včetně jejich odpovídajících související objekty. Oprávnění ke čtení pro mezery.
* **Zařízení brány**: vytvořit oprávnění pro senzory. Oprávnění ke čtení pro zařízení a senzorů, včetně jejich odpovídajících souvisejících objektů.

>[!NOTE]
> *Úplná definice za položky uvedené výše se dá načíst pomocí dotazu rozhraní API systému/rolí.*

### <a name="object-types"></a>Typy objektů

`ObjectIdType` Odkazuje na typ identity, který je právě přiřazen do role. Kromě `DeviceId` a `UserDefinedFunctionId` typy, typy odpovídají vlastnost objektu služby Azure Active Directory (Azure AD):
  
* `UserId` Typ přiřadí roli uživatele.
* `DeviceId` Typ role přiřadí k zařízení.
* `DomainName` Typ přiřadí roli na název domény. Každý uživatel pomocí zadaného názvu domény bude mít přístupová práva odpovídající roli.
* `TenantId` Typ přiřadí roli do tenanta. Každý uživatel, který patří do zadané ID tenanta Azure AD bude mít přístupová práva odpovídající roli.
* `ServicePrincipalId` Typ přiřadí ID objektu zabezpečení služby role
* `UserDefinedFunctionId` Typ přiřadí roli pro uživatelsky definovaná funkce (UDF).

> [!div class="nextstepaction"]
> [Dotaz nebo ID objektu uživatele](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)

> [!div class="nextstepaction"]
> [Získání ID objektu pro objekt zabezpečení služby](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.8.1)

> [!div class="nextstepaction"]
> [Načtení ID objektu pro tenanta služby Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

## <a name="role-assignments"></a>Přiřazení rolí

Oprávnění jsou udělena příjemci vytvořením přiřazení role a odvolat tak, že odeberete přiřazení role. Přiřazení role Azure digitální dvojče přidruží objektu (uživatel, tenanta Azure AD, atd.), role a mezerou. Potom jsou udělena oprávnění ke všem objektům, které patří do tohoto místa, včetně celý prostorový graf pod ním.

Například uživatel dostane přiřazení role s rolí `DeviceInstaller` pro kořenový uzel prostorový graf, který představuje budovy. Uživatel bude moct číst a aktualizovat zařízení s nejen k tomuto uzlu, ale všechny ostatní podřízené prostory v budově.

## <a name="next-steps"></a>Další postup

Další informace o zabezpečení Azure digitální dvojče [vytvořit a spravovat přiřazení rolí](./security-create-manage-role-assignments.md).
