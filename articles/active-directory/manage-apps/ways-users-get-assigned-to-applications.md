---
title: Pochopení způsobu přiřazování uživatelů k aplikacím v Azure Active Directory
description: Seznamte se s tím, jak se uživatelé přiřadí k aplikaci, která používá Azure Active Directory pro správu identit.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: kenwith
ms.openlocfilehash: 161df0446c9478ca0f2b135c1e426f3786b164fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257436"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Pochopení způsobu přiřazování uživatelů k aplikacím v Azure Active Directory
Tento článek vám pomůže pochopit, jak se uživatelé přiřadí k aplikaci ve vašem tenantovi.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Jak se uživatelé přiřadí k aplikaci ve službě Azure AD?
Pro uživatele, kteří mají přístup k aplikaci, je nutné je nejprve přiřadit jiným způsobem. Přiřazení může provádět správce, obchodní delegát nebo občas samotný uživatel. Níže popisuje způsoby, kterými se uživatelé můžou přiřazovat k aplikacím:

*  Správce [přiřadí uživatele](./assign-user-or-group-access-portal.md) k aplikaci přímo.
*  Správce [přiřadí skupinu](./assign-user-or-group-access-portal.md) , které je uživatel členem aplikace, včetně:
    * Skupina, která byla synchronizovaná z místního prostředí
    * Statická skupina zabezpečení vytvořená v cloudu
    * [Dynamická skupina zabezpečení](../enterprise-users/groups-dynamic-membership.md) vytvořená v cloudu
    * Skupina Microsoft 365 vytvořená v cloudu
    * Skupina [Všichni uživatelé](../fundamentals/active-directory-groups-create-azure-portal.md)
*  Správce umožňuje [Samoobslužný přístup k aplikacím](./manage-self-service-access.md) povolit uživateli přidání aplikace pomocí funkce [Moje aplikace](../user-help/my-apps-portal-end-user-access.md) **Přidat funkci aplikace** **bez schválení firmy** .
*  Správce umožňuje [Samoobslužný přístup k aplikacím](./manage-self-service-access.md) povolit uživateli přidání aplikace pomocí funkce [Moje aplikace](../user-help/my-apps-portal-end-user-access.md) **Přidat funkci aplikace** , ale jenom **s předchozím schválením od vybrané sady obchodních schvalovatelů** .
*  Správce umožňuje [samoobslužné správě skupin](../enterprise-users/groups-self-service-management.md) dovolit uživateli připojit se ke skupině, ke které je aplikace přiřazená **bez souhlasu s obchodním schválením** .
*  Správce umožňuje [samoobslužné správě skupin](../enterprise-users/groups-self-service-management.md) dovolit uživateli připojit se ke skupině, ke které je aplikace přiřazená, ale jenom **s předchozím schválením od vybrané sady obchodních schvalovatelů** .
*  Správce přiřadí licenci uživateli přímo pro aplikaci první strany, například [Microsoft 365](https://products.office.com/)
*  Správce přiřadí licenci ke skupině, které je uživatel členem, do aplikace první strany, například [Microsoft 365](https://products.office.com/)
*  [Správce souhlasí s aplikací](../develop/howto-convert-app-to-be-multi-tenant.md) , kterou bude používat všichni uživatelé, a pak se uživatel přihlásí k aplikaci.
* Uživatel [souhlasí s aplikací](../develop/howto-convert-app-to-be-multi-tenant.md) , která se přihlašuje k aplikaci sami.

## <a name="next-steps"></a>Další kroky
* [Série rychlý Start při správě aplikací](view-applications-portal.md)
* [Co je správa aplikací?](what-is-application-management.md)
* [Co je jednotné přihlašování?](what-is-single-sign-on.md)