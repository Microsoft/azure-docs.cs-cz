---
title: Přiřazení nebo odebrání licence – Azure Active Directory | Dokumentace Microsoftu
description: Pokyny ohledně toho, jak přiřadit nebo odebrání uživatele nebo skupiny licencí Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: b7abcb4820dde221f17efa1fcded05df41fa7bed
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449482"
---
# <a name="assign-or-remove-licenses-using-the-azure-active-directory-portal"></a>Přiřazení nebo odebrání licencí pomocí portálu Azure Active Directory
Mnoho služeb Azure Active Directory (Azure AD) vyžadují aktivaci produktů Azure AD a licencování jednotlivých uživatelů nebo skupin (a související členy) pro tento produkt. Uživatelé s licencemi na aktivní budou moci přistupovat a používat licencovaný jenom služby Azure AD.

## <a name="available-product-editions"></a>Edice k dispozici produktu
K dispozici několik edice produktu Azure AD.

- Azure AD Free

- Azure AD Basic

- Azure AD Premium 1 (Azure AD P1)

- Azure AD Premium 2 (Azure AD P2)

Konkrétní informace o každé edici produktu a související podrobnosti o licencování najdete v tématu [jaká licence potřebuji?](../authentication/concept-sspr-licensing.md).

## <a name="view-your-product-edition-and-license-details"></a>Zobrazit vaše edice a licenční podrobnosti o produktu
Můžete zobrazit vaši dostupný produkty Microsoftu, včetně jednotlivých licencí, kontrolují všechna data vypršení a počet přiřazení k dispozici.

### <a name="to-find-your-product-and-license-details"></a>Najít podrobnosti o vašich produktů a licence
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte **licence**.

    **Licence** se zobrazí stránka.

    ![Licence stránce zobrazující počet zakoupených produktů a přiřazených licencí](media/license-users-groups/license-details-blade.png)
    
3. Vyberte **zakoupených produktů** odkaz zobrazíte **produkty** stránky a zobrazíte **přiřazeno**, **dostupné**, a  **Zanedlouho vyprší platnost** podrobnosti pro jednotlivé edice konkrétního produktu.

    ![Stránka produkty v rámci edicí produktu a informace o přidružené licenci](media/license-users-groups/license-products-blade-with-products.png)

4. Vyberte název edice produktu a zjistěte jeho licencovaných uživatelů a skupin.

## <a name="assign-licenses-to-users-or-groups"></a>Přiřazení licencí uživatelům nebo skupinám
Ujistěte se, že kdokoli by bylo potřeba použít licencovanou služby Azure AD má příslušnou licenci. To je na vás, jestli chcete přidat licenční práva pro jednotlivé uživatele nebo celé skupiny.

>[!Note]
>Licencování na základě skupiny je funkce ve verzi public preview služby Azure AD a je k dispozici žádné placené licenční plán Azure AD. Další informace o verzích Preview najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).<br><br>Podrobné informace o tom, jak přidat uživatele najdete v tématu [postup přidání nebo odstranění uživatelů ve službě Azure Active Directory](add-users-azure-active-directory.md). Podrobné informace o tom, jak vytvářet skupiny a přidávat členy, naleznete v tématu [vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md).

### <a name="to-assign-a-license-to-a-specific-user"></a>Chcete-li přiřadit licenci pro konkrétního uživatele
1. Na **produkty** stránky, vyberte název edice, kterou chcete uživateli přiřadit ručně. Například _Azure Active Directory Premium plán 2_.

    ![Stránka produktů s edici zvýrazněné produktu](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. Na **Azure Active Directory Premium plán 2** stránce **přiřadit**.

    ![Stránka produktů se zvýrazněnou možností přiřadit](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. Na **přiřadit** stránce **uživatelů a skupin**a poté vyhledejte a vyberte uživateli přiřazujete licenci. Například _Mary Parker_.

    ![Přiřazení licencí stránky s zvýrazněný hledaný a zvolit možnosti](media/license-users-groups/assign-license-blade-with-highlight.png)

4. Vyberte **možnosti přiřazení**, ujistěte se, že máte příslušné licence funkcí zapnutou a pak vyberte **OK**.

    ![Licence možnost stránky zobrazující všechny možnosti, které jsou k dispozici v edici](media/license-users-groups/license-option-blade-assignments.png)

    **Přiřadit licence** stránka se aktualizuje a zobrazí, že je vybraný uživatel a zda jsou nakonfigurovány na přiřazení.

    >[!NOTE]
    >Ne všechny služby společnosti Microsoft jsou k dispozici ve všech umístěních. Předtím, než je možné přiřadit licence pro uživatele, je nutné zadat **místo využívání**. Tuto hodnotu lze nastavit **Azure Active Directory &gt; uživatelé &gt; profilu &gt; nastavení** oblasti ve službě Azure AD.

5. Vyberte **Přiřadit**.

    Uživatel se přidá do seznamu licencovaných uživatelů s přístupem ke zahrnutou služby Azure AD.

### <a name="to-assign-a-license-to-an-entire-group"></a>Chcete-li přiřadit licenci pro celou skupinu
1. Na **produkty** stránky, vyberte název edice, kterou chcete uživateli přiřadit ručně. Například _Azure Active Directory Premium plán 2_.

    ![Okno produkty s edici zvýrazněné produktu](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. Na **Azure Active Directory Premium plán 2** stránce **přiřadit**.

    ![Stránka produktů se zvýrazněnou možností přiřadit](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. Na **přiřadit** stránce **uživatelů a skupin**a poté vyhledejte a vyberte skupinu, kterou přiřazujete licenci. Například _zásady MDM - západní_.

    ![Přiřazení licencí stránky s zvýrazněný hledaný a zvolit možnosti](media/license-users-groups/assign-group-license-blade-with-highlight.png)

4. Vyberte **možnosti přiřazení**, ujistěte se, že máte příslušné licence funkcí zapnutou a pak vyberte **OK**.

    ![Licence možnost stránky zobrazující všechny možnosti, které jsou k dispozici v edici](media/license-users-groups/license-option-blade-group-assignments.png)

    **Přiřadit licence** stránka se aktualizuje a zobrazí, že je vybraný uživatel a zda jsou nakonfigurovány na přiřazení.

    >[!NOTE]
    >Ne všechny služby společnosti Microsoft jsou k dispozici ve všech umístěních. Předtím, než je možné přiřadit licenci ke skupině, je nutné zadat **místo využívání** pro všechny členy. Tuto hodnotu lze nastavit **Azure Active Directory &gt; uživatelé &gt; profilu &gt; nastavení** oblasti ve službě Azure AD. Každý uživatel, jehož umístění využití nezadáte dědí umístění tenanta.

5. Vyberte **Přiřadit**.

    Skupinu se přidá do seznamu skupin licencí a všichni členové mají přístup k zahrnutou služby Azure AD.


## <a name="remove-a-license"></a>Odebrání licence
Můžete odebrat licenci z uživatele nebo skupiny z **licence** stránky.

### <a name="to-remove-a-license-from-a-specific-user"></a>Chcete-li odebrat licenci od konkrétního uživatele
1. Na **licencovaní uživatelé** stránky pro edici produktu, vyberte uživatele, který už má licence. Například _Alain Charon_.

2. Vyberte **odebrat licenci**.

    ![Licencovaní uživatelé stránky se zvýrazněnou možností odebrat licenci](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

### <a name="to-remove-a-license-from-a-group"></a>Chcete-li odebrat licenci ze skupiny
1. Na **licencované skupiny** stránky pro edici produktu, vyberte skupinu, která už má licence. Například _zásady MDM - západní_.

2. Vyberte **odebrat licenci**.

    ![Licencované skupiny stránky se zvýrazněnou možností odebrat licenci](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)

>[!Important]
>Licence děděné uživatele ze skupiny nelze odebrat přímo. Místo toho budete muset odebrat uživatele ze skupiny, ze kterého se máte dědění licence.

## <a name="next-steps"></a>Další postup
Po přiřazení licence, můžete provádět následující procesy:

- [Identifikovat a vyřešit problémy přiřazení licence](../users-groups-roles/licensing-groups-resolve-problems.md)

- [Licencovaní uživatelé přidat do skupiny pro licencování](../users-groups-roles/licensing-groups-migrate-users.md)

- [Scénáře, omezeních a známých problémech použití skupin pro správu licencování v Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)

- [Přidání nebo změně informací profilu](active-directory-users-profile-azure-portal.md)
