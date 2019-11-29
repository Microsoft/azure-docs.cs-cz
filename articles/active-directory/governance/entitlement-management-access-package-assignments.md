---
title: Zobrazení, přidání a odebrání přiřazení balíčku pro přístup ve správě nároků Azure AD – Azure Active Directory
description: Naučte se zobrazovat, přidávat a odebírat přiřazení pro balíček přístupu v Azure Active Directory správě nároků.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0501da153567f3b95804d8a8a6576d8cf199762c
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2019
ms.locfileid: "74554000"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Zobrazení, přidání a odebrání přiřazení balíčku pro přístup ve správě nároků Azure AD

V části Správa opravňujících k Azure AD se můžete podívat, kdo byl přiřazený k balíčkům, zásadám a stavům. Pokud má přístupový balíček odpovídající zásady, můžete také přímo přiřadit uživatele k balíčku pro přístup. Tento článek popisuje, jak zobrazit, přidat a odebrat přiřazení pro balíčky přístupu.

## <a name="view-who-has-an-assignment"></a>Zobrazit, kdo má přiřazení

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Kliknutím na **přiřazení** zobrazíte seznam aktivních přiřazení.

    ![Seznam přiřazení k balíčku pro přístup](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Kliknutím na konkrétní přiřazení zobrazíte další podrobnosti.

1. Pokud chcete zobrazit seznam přiřazení, která neobsahovala správně zřízené všechny role prostředků, klikněte na stav filtru a vyberte **doručování**.

    Na stránce **žádosti** můžete zobrazit další podrobnosti o chybách doručení hledáním odpovídající žádosti uživatele.

1. Pokud chcete zobrazit vypršení platnosti, klikněte na stav filtru a vyberte **konec platnosti**.

1. Pokud chcete stáhnout soubor CSV s filtrovaným seznamem, klikněte na **Stáhnout**.

## <a name="directly-assign-a-user"></a>Přímo přiřadit uživatele

V některých případech můžete chtít přímo přiřadit konkrétní uživatele k balíčku pro přístup, aby uživatelé nemuseli projít procesem vyžadování balíčku pro přístup. Aby bylo možné přiřazovat uživatele přímo, musí mít přístupový balíček zásadu, která umožňuje přímé přiřazení správců.

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

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

## <a name="remove-an-assignment"></a>Odebrání přiřazení

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

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
