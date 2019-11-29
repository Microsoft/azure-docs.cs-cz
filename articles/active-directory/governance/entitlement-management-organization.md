---
title: Přidání propojené organizace ve správě nároků Azure AD – Azure Active Directory
description: Naučte se, jak umožnit lidem mimo vaši organizaci žádat o přístup k balíčkům, abyste mohli spolupracovat na projektech.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dec950f5475a8a64cfecfac1fb25246d6a7aa29
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561922"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Přidání propojené organizace v Azure AD – Správa nároků

Správa opravňujících k Azure AD umožňuje spolupracovat s lidmi mimo vaši organizaci. Pokud často spolupracujete s uživateli v externím adresáři služby Azure AD nebo v doméně, můžete je přidat jako připojenou organizaci. Tento článek popisuje, jak přidat propojenou organizaci, abyste mohli uživatelům mimo vaši organizaci umožnit vyžádat si prostředky v adresáři.

## <a name="what-is-a-connected-organization"></a>Co je propojená organizace?

Připojená organizace je externí adresář nebo doména služby Azure AD, ke kterým máte relaci.

Předpokládejme například, že pracujete s Woodgrove bankou a chcete spolupracovat se dvěma externími organizacemi: grafický design Institute a contoso. Vaše kontaktní osoba pracovala s grafickým institutem pro vytváření grafiky, kterou používají Azure AD, a uživatelé tohoto institutu pro grafický design Institute mají hlavní název uživatele, který končí `graphicdesigninstitute.com`. A Vy jste na svém místě od společnosti Contoso dozvěděli, že ještě nepoužívají službu Azure AD, ale uživatelé společnosti Contoso mají hlavní název uživatele, který končí `contoso.com`.

Můžete nakonfigurovat dvě připojené organizace – jednu pro Graphics design Institute s doménovou `graphicdesigninstitute.com`a jednu pro contoso s doménovou `contoso.com`. Pokud pak přidáte tyto dvě připojené organizace do zásady, můžou uživatelé z každé organizace, které mají hlavní název uživatele odpovídající zásadám, vyžadovat přístup k balíčkům. Vzhledem k tomu, že grafický úřad pro vytváření grafiky byl identifikován jako při použití služby Azure AD, `graphicdesigninstitute.example`pak budou mít uživatelé s tímto hlavním názvem uživatele také přístup k balíčkům, které používají stejné zásady.

![Příklad propojené organizace](./media/entitlement-management-organization/connected-organization-example.png)

Jak budou uživatelé z adresáře nebo domény služby Azure AD ověřeni, závisí na typu ověřování. Typy ověřování pro připojené organizace jsou následující:

- Azure AD
- [Přímá federace](../b2b/direct-federation.md)
- [Jednorázové heslo](../b2b/one-time-passcode.md) (doména)

Ukázku, jak přidat propojenou organizaci, najdete v následujícím videu:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Přidání připojené organizace

Pomocí těchto kroků přidáte externí adresář služby Azure AD nebo doménu jako připojenou organizaci.

**Požadovaná role:** Globální správce, Správce uživatelů nebo Pozvánka hosta

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **propojené organizace** a pak klikněte na **Přidat připojenou organizaci**.

    ![Řízení identit – připojené organizace – přidat připojenou organizaci](./media/entitlement-management-organization/connected-organization.png)

1. Na kartě **základy** zadejte zobrazovaný název a popis organizace.

    ![Přidat kartu s informacemi o připojené organizaci](./media/entitlement-management-organization/organization-basics.png)

1. Na kartě **adresář + doména** kliknutím na **Přidat adresář a doménu** otevřete podokno vybrat adresáře a domény.

1. Zadejte název domény, ve které chcete vyhledat adresář nebo doménu služby Azure AD. Je nutné zadat celý název domény.

1. Ověřte, že se jedná o správnou organizaci podle zadaného názvu a typu ověřování. Způsob, jakým se uživatelé přihlásí, závisí na typu ověřování.

    ![Přidat propojenou organizaci – výběr adresářů a domén](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Kliknutím na **Přidat** Přidejte adresář nebo doménu služby Azure AD. V současné době můžete pro každou propojenou organizaci přidat jenom jeden adresář nebo doménu služby Azure AD.

    > [!NOTE]
    > Všichni uživatelé z adresáře nebo domény služby Azure AD budou moci požádat o tento balíček přístupu. To zahrnuje uživatele v Azure AD ze všech subdomén přidružených k adresáři, pokud tyto domény nejsou blokované seznamem povolených a zakázaných aplikací Azure B2B. Další informace najdete v tématu [Povolení nebo blokování pozvánek uživatelům B2B z konkrétních organizací](../b2b/allow-deny-list.md).

1. Až přidáte adresář nebo doménu služby Azure AD, klikněte na **Vybrat**.

    V seznamu se zobrazí organizace.

    ![Přidat připojenou organizaci – karta adresáře](./media/entitlement-management-organization/organization-directory-domain.png)

1. Na kartě **sponzoři** přidejte volitelné sponzoři pro tuto připojenou organizaci.

    Sponzoři jsou interní nebo externí uživatelé, kteří jsou již ve vašem adresáři, které jsou kontaktním bodem pro vztah s touto propojenou organizací. Interní sponzoři jsou členské uživatele ve vašem adresáři. Externí sponzoři jsou uživatelé typu host z připojené organizace, kteří byli dříve pozváni a jsou již ve vašem adresáři. Sponzory můžou být využívány jako schvalovatelé, když uživatelé v této připojené organizaci požadují přístup k tomuto balíčku přístupu. Informace o tom, jak pozvat uživatele typu Host do adresáře, najdete [v tématu přidání Azure Active Directory uživatelů spolupráce B2B v Azure Portal](../b2b/add-users-administrator.md).

    Po kliknutí na tlačítko **Přidat nebo odebrat**se zobrazí podokno pro výběr interních nebo externích sponzorů. V podokně se zobrazí nefiltrovaný seznam uživatelů a skupin ve vašem adresáři.

    ![Přístup k balíčku – zásady – přidat připojenou organizaci – karta sponzoři](./media/entitlement-management-organization/organization-sponsors.png)

1. Na kartě **Revize + vytvořit** zkontrolujte nastavení organizace a pak klikněte na **vytvořit**.

    ![Přístup k balíčku – zásady – přidat připojenou organizaci – revize + vytvořit kartu](./media/entitlement-management-organization/organization-review-create.png)

## <a name="delete-a-connected-organization"></a>Odstranění propojené organizace

Pokud už nebudete mít relaci s externím adresářem nebo doménou služby Azure AD, můžete propojenou organizaci odstranit.

**Požadovaná role:** Globální správce, Správce uživatelů nebo Pozvánka hosta

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **propojené organizace** a pak kliknutím otevřete propojenou organizaci.

1. Na stránce Přehled klikněte na **Odstranit** a odstraňte připojenou organizaci.

    V současné době můžete propojenou organizaci odstranit jenom v případě, že neexistují žádní připojení uživatelé.

    ![Zásady správného řízení identity – připojení organizace – odstranění připojené organizace](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Další kroky

- [Řízení přístupu pro externí uživatele](entitlement-management-organization.md)
- [Pro uživatele, kteří nejsou ve vašem adresáři](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
