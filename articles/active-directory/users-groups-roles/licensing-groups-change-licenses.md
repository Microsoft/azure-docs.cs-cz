---
title: Změna licenčních plánů pro uživatele a skupiny – Azure AD | Dokumenty společnosti Microsoft
description: Jak migrovat uživatele v rámci skupiny do různých plánů služeb pomocí skupinových licencí ve službě Azure Active Directory
services: active-directory
keywords: Licencování Azure AD
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf2f04e1728f94c89bddcc31c287cc017a79020f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025903"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Změna přiřazení licencí pro uživatele nebo skupinu ve službě Azure Active Directory

Tento článek popisuje, jak přesunout uživatele a skupiny mezi plány licencí služby ve službě Azure Active Directory (Azure AD). Cílem přístupu Azure AD je zajistit, že během změny licence nedojde ke ztrátě služby nebo dat. Uživatelé by měli bez problémů přepínat mezi službami. Postup přiřazení plánu licence v tomto článku popisuje změnu uživatele nebo skupiny v Office 365 E1 na Office 365 E3, ale tyto kroky platí pro všechny licenční plány. Při aktualizaci přiřazení licencí pro uživatele nebo skupinu jsou odebrání přiřazení licencí a nová přiřazení provedena současně, aby uživatelé neztratili přístup ke svým službám během změn licencí nebo se nezobrazí konflikty licencí mezi plány.

## <a name="before-you-begin"></a>Než začnete

Před aktualizací přiřazení licencí je důležité ověřit, zda jsou určité předpoklady pravdivé pro všechny uživatele nebo skupiny, které mají být aktualizovány. Pokud předpoklady nejsou pravdivé pro všechny uživatele ve skupině, migrace může selhat pro některé. V důsledku toho mohou někteří uživatelé ztratit přístup ke službám nebo datům. Zajistit:

- Uživatelé mají aktuální licenční plán (v tomto případě Office 365 E1), který je přiřazen ke skupině a zděděný uživatelem a není přiřazen přímo.

- Pro licenční plán, který přiřazujete, máte dostatek dostupných licencí. Pokud nemáte dostatek licencí, některým uživatelům nemusí být přiřazen nový licenční plán. Můžete zkontrolovat počet dostupných licencí.

- Uživatelé nemají jiné přiřazené licence služby, které by mohly být v konfliktu s požadovanou licencí nebo zabránit odebrání aktuální licence. Například licence ze služby, jako je Workplace Analytics nebo Project Online, která je závislá na jiných službách.

- Pokud spravujete skupiny místně a synchronizujete je do Azure AD přes Azure AD Connect, pak přidáte nebo odeberete uživatele pomocí místního systému. Může chvíli trvat, než se změny synchronizují s Azure AD, které se vyberou pomocí skupinové licence.

- Pokud používáte členství v dynamických skupinách Azure AD, přidáte nebo odeberete uživatele změnou jejich atributů, ale proces aktualizace pro přiřazení licencí zůstane stejný.

## <a name="change-user-license-assignments"></a>Změna přiřazení uživatelských licencí

Pokud na stránce **Aktualizovat přiřazení licencí** zjistíte, že některá zaškrtávací políčka nejsou k dispozici, znamená to služby, které nelze změnit, protože jsou zděděny ze skupinové licence.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) pomocí účtu správce licencí ve vaší organizaci Azure AD.
1. Vyberte**Uživatelé** **služby Azure Active Directory** > a otevřete stránku **Profil** pro uživatele.
1. Vyberte **možnost Licence**.
1. Vyberte **Přiřazení,** chcete-li upravit přiřazení licencí pro uživatele nebo skupinu. Na stránce **Přiřazení** můžete vyřešit konflikty přiřazení licencí.
1. Zaškrtněte políčko Office 366 E3 a ujistěte se, že jsou vybrány minimálně všechny služby E1 přiřazené uživateli.
1. Zrušte zaškrtnutí políčka Office 365 E1.

    ![Stránka přiřazení licencí pro uživatele s vypnutým Office 365 E1 a výběrem Office 365 E3](media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. Vyberte **Uložit**.

Azure AD použije nové licence a odebere staré licence současně poskytovat kontinuitu služeb.

## <a name="change-group-license-assignments"></a>Změna přiřazení skupinových licencí

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) pomocí účtu správce licencí ve vaší organizaci Azure AD.
1. Vyberte**Skupiny služby** **Azure Active Directory** > a otevřete stránku **Přehled** pro skupinu.
1. Vyberte **možnost Licence**.
1. Vyberte příkaz **Přiřazení** a upravte přiřazení licencí pro uživatele nebo skupinu.
1. Zaškrtněte políčko office 366 E3. Chcete-li zachovat kontinuitu služeb, ujistěte se, že vyberete všechny služby E1, které jsou již přiřazeny uživateli.
1. Zrušte zaškrtnutí políčka Office 365 E1.

    ![Výběr příkazu Přiřazení na stránce Licence pro uživatele nebo skupiny](media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. Vyberte **Uložit**.

K zajištění kontinuity služeb azure ad použije nové licence a odebere staré licence současně pro všechny uživatele ve skupině.

## <a name="next-steps"></a>Další kroky

Další informace o dalších scénářích správy licencí prostřednictvím skupin naleznete v následujících článcích:

- [Přiřazení licencí ke skupině v Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
- [Identifikace a řešení potíží s licencemi pro skupinu v Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
- [Jak migrovat jednotlivé licencované uživatele do skupinových licencí ve službě Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
- [Licencování dalších scénářů skupiny Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)
- [Příklady Prostředí PowerShell pro skupinové licencování ve službě Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
