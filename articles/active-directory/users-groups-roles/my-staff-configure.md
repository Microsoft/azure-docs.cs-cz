---
title: Delegovat správu uživatelů (preview) – Azure AD | Dokumenty společnosti Microsoft
description: Delegování správy uživatelů pomocí složek Moje zaměstnanci a administrativních jednotek
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 3f7c12612dbe37de6b08cb05a64af460296ade93
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394212"
---
# <a name="manage-your-users-with-my-staff-preview"></a>Správa uživatelů pomocí funkce Moje zaměstnanci (preview)

Moji zaměstnanci vám umožňují delegovat na autoritu, jako je například vedoucí obchodu nebo vedoucí týmu, oprávnění k zajištění, že jejich zaměstnanci mají přístup ke svým účtům Azure AD. Namísto spoléhání se na centrální helpdesk mohou organizace delegovat běžné úkoly, jako je resetování hesel nebo změna telefonních čísel, na manažera týmu. Díky aplikaci My Staff může uživatel, který nemá přístup ke svému účtu, znovu získat přístup pouhými několika kliknutími, bez potřeby helpdesku nebo it personálu.

Před konfigurací funkce Moje zaměstnanci pro vaši organizaci doporučujeme, abyste si tuto dokumentaci a [uživatelskou dokumentaci](../user-help/my-staff-team-manager.md) prověřili, abyste porozuměli funkcím a dopadu této funkce na uživatele. Můžete využít uživatelskou dokumentaci k trénování a přípravě uživatelů na nové prostředí a pomoci zajistit úspěšné zavedení.

## <a name="how-my-staff-works"></a>Jak můj personál funguje

Moji zaměstnanci jsou založeny na administrativních jednotkách (AU), které jsou kontejnerem zdrojů, které lze použít k omezení rozsahu administrativníkontroly přiřazení role. V části Moje zaměstnanci se au používají k definování podmnožiny uživatelů organizace, například obchodu nebo oddělení. Potom například manažer týmu může být přiřazen k roli, jejíž obor je jedna nebo více au. V níže uvedeném příkladu byla uživateli udělena role správy ověřování a tři au jsou rozsah emitovali. Další informace o jednotkách pro správu naleznete [v tématu Správa administrativních jednotek ve službě Azure Active Directory](directory-administrative-units.md).

## <a name="how-to-enable-my-staff"></a>Jak povolit můj personál

Po konfiguraci řízení au, můžete použít tento obor pro uživatele, kteří mají přístup k moje zaměstnanci. Ke funkci Moji zaměstnanci mají přístup pouze uživatelé, kterým je přiřazena role pro správu. Chcete-li povolit funkci Moje zaměstnanci, proveďte následující kroky:

1. Přihlaste se k portálu Azure jako správce uživatele.
2. Přejděte na**Azure** >  **Active Directory** > Nastavení uživatele**Uživatelské funkce náhledy** > **Spravovat nastavení náhledu uživatelských funkcí**.
3. V části **Správci mají přístup k funkci Zaměstnanci**, můžete povolit přístup pro všechny uživatele, vybrané uživatele nebo žádný přístup uživatelů.

> [!Note]
> Ke funkci Moje zaměstnanci mají přístup pouze uživatelé, kterým byla přiřazena role správce. Pokud povolíte funkci Moje zaměstnanci pro uživatele, kterému není přiřazena role správce, nebude mít přístup k funkci Moje zaměstnanci.

## <a name="using-my-staff"></a>Použití funkce Moje zaměstnanci

Když uživatel přejde do služby Moje zaměstnanci, zobrazí se jim názvy [administrativních jednotek,](directory-administrative-units.md) nad nimiž má oprávnění správce. V [uživatelské dokumentaci My Staff](../user-help/my-staff-team-manager.md)používáme termín "umístění" pro odkaz ování administrativních jednotek. Pokud oprávnění správce nemají obor AU, oprávnění platí v celé organizaci. Po povolení funkce Moje zaměstnanci k ní mohou uživatelé, kterým byla [https://mystaff.microsoft.com](https://mystaff.microsoft.com)přidělena role správy, získat přístup prostřednictvím aplikace . Mohou vybrat AU pro zobrazení uživatelů v tomto AU a vybrat uživatele, který otevře svůj profil.

## <a name="licenses"></a>Licence

Každý uživatel, který je povolen v části Moje zaměstnanci, musí mít licenci, i když nepoužívá portál Moje zaměstnanci. Každý povolený uživatel musí mít jednu z následujících licencí Azure AD nebo Microsoft 365:

- Azure AD Premium P1 nebo P2
- Microsoft 365 F1 nebo F3

## <a name="reset-a-users-password"></a>Resetování hesla uživatele

Následující role mají oprávnění k resetování hesla uživatele:

- [Správce ověřování](directory-assign-admin-roles.md#authentication-administrator)
- [Správce privilegovaného ověřování](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Globální správce](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [Správce technické podpory](directory-assign-admin-roles.md#helpdesk-administrator)
- [Správce uživatele](directory-assign-admin-roles.md#user-administrator)
- [Správce hesel](directory-assign-admin-roles.md#password-administrator)

V **stránce Moji zaměstnanci**otevřete profil uživatele. Vyberte **možnost Obnovit heslo**.

- Pokud je uživatel pouze cloud, můžete zobrazit dočasné heslo, které můžete dát uživateli.
- Pokud je uživatel synchronizován z místní služby Active Directory, můžete zadat heslo, které splňuje místní zásady služby AD. Potom můžete dát toto heslo uživateli.

    ![Indikátor průběhu resetování hesla a oznámení o úspěchu](media/my-staff-configure/reset-password.png)

Uživatel je povinen změnit své heslo při příštím přihlášení.

## <a name="manage-a-phone-number"></a>Správa telefonního čísla

V **stránce Moji zaměstnanci**otevřete profil uživatele.

- Výběrem části **Přidat telefonní číslo** přidáte telefonní číslo pro uživatele.
- Chcete-li změnit telefonní číslo, vyberte **možnost Upravit telefonní číslo.**
- Výběrem **možnosti Odebrat telefonní číslo** odeberete telefonní číslo uživatele.

V závislosti na vašem nastavení pak může uživatel použít telefonní číslo, které jste nastavili pro přihlášení pomocí sms, provádění vícefaktorového ověřování a samoobslužné resetování hesla.

Chcete-li spravovat telefonní číslo uživatele, musíte být přiřazeni k jedné z následujících rolí:

- [Správce ověřování](directory-assign-admin-roles.md#authentication-administrator)
- [Správce privilegovaného ověřování](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Globální správce](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>Search

AU a uživatele ve vaší organizaci můžete vyhledat pomocí vyhledávacího panelu Moje zaměstnanci. Můžete prohledávat všechny aua uživatele ve vaší organizaci, ale můžete provádět změny pouze uživatelům, kteří jsou v AU, přes který jste dostali oprávnění správce.

Můžete také vyhledat uživatele v rámci AU. Chcete-li to provést, použijte vyhledávací panel v horní části seznamu uživatelů.

## <a name="audit-logs"></a>Protokoly auditu

Protokoly auditu můžete zobrazit pro akce provedené v části Moji zaměstnanci na portálu Azure Active Directory. Pokud byl protokol auditu vygenerován akcí přijatou v části Můj zaměstnanec, zobrazí se v události auditu v části Další podrobnosti.

## <a name="next-steps"></a>Další kroky

[Dokumentace uživatele](../user-help/my-staff-team-manager.md)
My Staff[Dokumentace administrativních jednotek](directory-administrative-units.md)
