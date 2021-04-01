---
title: Zobrazení, přidání a odebrání přiřazení balíčku pro přístup ve správě nároků Azure AD – Azure Active Directory
description: Naučte se zobrazovat, přidávat a odebírat přiřazení pro balíček přístupu v Azure Active Directory správě nároků.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18bc7ca9e108a35fd27f7b3155f186221a8caae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90980065"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Zobrazení, přidání a odebrání přiřazení balíčku pro přístup ve správě nároků Azure AD

V části Správa opravňujících k Azure AD se můžete podívat, kdo byl přiřazený k balíčkům, zásadám a stavům. Pokud má přístupový balíček odpovídající zásady, můžete také přímo přiřadit uživatele k balíčku pro přístup. Tento článek popisuje, jak zobrazit, přidat a odebrat přiřazení pro balíčky přístupu.

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat správu nároků Azure AD a přiřazovat uživatelům přístup k balíčkům, musíte mít jednu z těchto licencí:

- Azure AD Premium P2
- Licence Enterprise Mobility + Security (EMS) E5

## <a name="view-who-has-an-assignment"></a>Zobrazit, kdo má přiřazení

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu, přístup správce balíčků nebo správce přiřazení balíčku pro přístup

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Kliknutím na **přiřazení** zobrazíte seznam aktivních přiřazení.

    ![Seznam přiřazení k balíčku pro přístup](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Kliknutím na konkrétní přiřazení zobrazíte další podrobnosti.

1. Pokud chcete zobrazit seznam přiřazení, která neobsahovala správně zřízené všechny role prostředků, klikněte na stav filtru a vyberte **doručování**.

    Na stránce **žádosti** můžete zobrazit další podrobnosti o chybách doručení hledáním odpovídající žádosti uživatele.

1. Pokud chcete zobrazit vypršení platnosti, klikněte na stav filtru a vyberte **konec platnosti**.

1. Pokud chcete stáhnout soubor CSV s filtrovaným seznamem, klikněte na **Stáhnout**.

### <a name="viewing-assignments-programmatically"></a>Zobrazení přiřazení prostřednictvím kódu programu

Můžete také načíst přiřazení v balíčku přístupu pomocí Microsoft Graph.  Uživatel v příslušné roli s aplikací, která má delegované `EntitlementManagement.ReadWrite.All` oprávnění, může volat rozhraní API k [vypsání accessPackageAssignments](/graph/api/accesspackageassignment-list?view=graph-rest-beta).

## <a name="directly-assign-a-user"></a>Přímo přiřadit uživatele

V některých případech můžete chtít přímo přiřadit konkrétní uživatele k balíčku pro přístup, aby uživatelé nemuseli projít procesem vyžadování balíčku pro přístup. Aby bylo možné přiřazovat uživatele přímo, musí mít přístupový balíček zásadu, která umožňuje přímé přiřazení správců.

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu, přístup správce balíčků nebo správce přiřazení balíčku pro přístup

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. V nabídce vlevo klikněte na **přiřazení**.

1. Kliknutím na **nové přiřazení** otevřete přidat uživatele pro přístup k balíčku.

    ![Přiřazení – přidat uživatele do balíčku pro přístup](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Klikněte na **Přidat uživatele** a vyberte uživatele, kterým chcete přiřadit tento balíček přístupu.

1. V seznamu **Vybrat zásadu** vyberte zásadu, kterou budou řídit a sledovat budoucí požadavky uživatelů a životní cyklus. Pokud chcete, aby vybraní uživatelé měli jiná nastavení zásad, můžete kliknutím na **vytvořit novou zásadu** přidat novou zásadu.

1. Nastavte datum a čas, kdy má přiřazení vybraných uživatelů začít a končit. Pokud není zadané koncové datum, použije se nastavení životního cyklu zásad.

1. Volitelně můžete zadat odůvodnění pro přímé přiřazení záznamu.

1. Kliknutím na tlačítko **Přidat** můžete vybraným uživatelům přiřadit přímý přístup k balíčku přístupu.

    Po chvíli klikněte na **aktualizovat** , aby se uživatelé mohli zobrazit v seznamu přiřazení.

### <a name="directly-assigning-users-programmatically"></a>Přímé přiřazování uživatelů prostřednictvím kódu programu

Můžete také přímo přiřadit uživatele k balíčku přístupu pomocí Microsoft Graph.  Uživatel v příslušné roli s aplikací, která má delegované `EntitlementManagement.ReadWrite.All` oprávnění, může volat rozhraní API a [vytvořit accessPackageAssignmentRequest](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta).

## <a name="remove-an-assignment"></a>Odebrání přiřazení

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu, přístup správce balíčků nebo správce přiřazení balíčku pro přístup

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. V nabídce vlevo klikněte na **přiřazení**.
 
1. Zaškrtněte políčko vedle uživatele, jehož přiřazení chcete odebrat z balíčku pro přístup. 

1. V horní části levého podokna klikněte na tlačítko **Odebrat** . 
 
    ![Přiřazení – odebrat uživatele z balíčku přístupu](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Zobrazí se oznámení o odebrání přiřazení. 

## <a name="next-steps"></a>Další kroky

- [Žádost o změnu a nastavení balíčku pro přístup](entitlement-management-access-package-request-policy.md)
- [Zobrazení sestav a protokolů](entitlement-management-reports.md)