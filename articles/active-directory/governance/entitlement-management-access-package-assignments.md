---
title: Zobrazení, přidání a odebrání přiřazení pro přístupový balíček ve správě nároků Azure AD – Azure Active Directory
description: Zjistěte, jak zobrazit, přidat a odebrat přiřazení pro přístupový balíček ve správě nároků služby Azure Active Directory.
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
ms.openlocfilehash: 12e3b86f41e7188778393ab717554907ef5d44ec
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631749"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Zobrazení, přidání a odebrání přiřazení pro přístupový balíček ve správě oprávnění Azure AD

Ve správě nároků Azure AD uvidíte, kdo byl přiřazen k přístupu k balíčkům, jejich zásadám a stavu. Pokud přístupový balíček obsahuje příslušnou zásadu, můžete také přímo přiřadit uživatele k přístupu balíčku. Tento článek popisuje, jak zobrazit, přidat a odebrat přiřazení pro přístupové balíčky.

## <a name="prerequisites"></a>Požadavky

Chcete-li používat správu nároků Azure AD a přiřazovat uživatelům přístup k balíčkům, musíte mít jednu z následujících licencí:

- Azure AD Premium P2
- Licence Enterprise Mobility + Security (EMS) E5

## <a name="view-who-has-an-assignment"></a>Zobrazit, kdo má přiřazení

**Předpokladová role:** Globální správce, správce uživatele, vlastník katalogu nebo správce balíčků aplikace Access

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **accessové balíčky** a potom otevřete přístupový balíček.

1. Kliknutím na **Přiřazení** zobrazíte seznam aktivních přiřazení.

    ![Seznam přiřazení k přístupovému balíčku](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Kliknutím na konkrétní přiřazení zobrazíte další podrobnosti.

1. Chcete-li zobrazit seznam přiřazení, která neměla správně zřízené všechny role zdrojů, klepněte na stav filtru a vyberte **možnost Doručení**.

    Další podrobnosti o chybách doručení můžete zobrazit vyhledáním odpovídajícího požadavku uživatele na stránce **Požadavky.**

1. Chcete-li zobrazit přiřazení s ukončenou platností, klepněte na stav filtru a vyberte **možnost Vypršela platnost**.

1. Chcete-li stáhnout soubor CSV z filtrovaného seznamu, klepněte na tlačítko **Stáhnout**.

### <a name="viewing-assignments-programmatically"></a>Programové zobrazení přiřazení

Můžete také načíst přiřazení v balíčku přístupu pomocí aplikace Microsoft Graph.  Uživatel v příslušné roli s aplikací, která `EntitlementManagement.ReadWrite.All` má delegované oprávnění, může volat rozhraní API do [seznamu accessPackageAssignments](https://docs.microsoft.com/graph/api/accesspackageassignment-list?view=graph-rest-beta).

## <a name="directly-assign-a-user"></a>Přímá přiřazení uživatele

V některých případech můžete chtít přímo přiřadit konkrétní uživatele k balíčku přístupu tak, aby uživatelé nemuseli projít procesem vyžádání přístupového balíčku. Chcete-li přímo přiřadit uživatele, musí mít balíček přístupu zásadu, která umožňuje přímé přiřazení správce.

**Předpokladová role:** Globální správce, správce uživatele, vlastník katalogu nebo správce balíčků aplikace Access

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **accessové balíčky** a potom otevřete přístupový balíček.

1. V levé nabídce klepněte na **položku Úlohy**.

1. Kliknutím na **Nové přiřazení** otevřete tlačítko Přidat pro přístup k balíčku.

    ![Přiřazení – přidání uživatele pro přístup k balíčku](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Kliknutím na **Přidat uživatele** vyberte uživatele, kterým chcete přiřadit tento přístupový balíček.

1. V seznamu **Vybrat zásady** vyberte zásady, které budou řídit a sledovat budoucí požadavky uživatelů a životní cyklus. Pokud chcete, aby vybraní uživatelé měli různá nastavení zásad, můžete klepnutím na **tlačítko Vytvořit novou zásadu** přidat novou zásadu.

1. Nastavte datum a čas, kdy má přiřazení vybraných uživatelů začínat a končit. Pokud není k dispozici koncové datum, použije se nastavení životního cyklu zásady.

1. Volitelně uveďte odůvodnění pro vaše přímé přiřazení pro vedení záznamů.

1. Chcete-li přímo přiřadit vybrané uživatele k přístupovému balíčku, klepněte na tlačítko **Přidat.**

    Po chvíli klikněte na **Aktualizovat** a zozobrazujete uživatele v seznamu Přiřazení.

### <a name="directly-assigning-users-programmatically"></a>Přímé přiřazení uživatelů programově

Můžete také přímo přiřadit uživatele k přístupu balíček pomocí microsoft graphu.  Uživatel v příslušné roli s aplikací, která `EntitlementManagement.ReadWrite.All` má delegované oprávnění můžete volat rozhraní API [k vytvoření accessPackageAssignmentRequest](https://docs.microsoft.com/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta).

## <a name="remove-an-assignment"></a>Odebrání přiřazení

**Předpokladová role:** Globální správce, správce uživatele, vlastník katalogu nebo správce balíčků aplikace Access

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **accessové balíčky** a potom otevřete přístupový balíček.

1. V levé nabídce klepněte na **položku Úlohy**.
 
1. Zaškrtněte políčko vedle uživatele, jehož přiřazení chcete odebrat z přístupového balíčku. 

1. Klikněte na tlačítko **Odebrat** v horní části levého podokna. 
 
    ![Přiřazení – odebrání uživatele z přístupového balíčku](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Zobrazí se oznámení informující o odebrání přiřazení. 

## <a name="next-steps"></a>Další kroky

- [Změnit požadavek a nastavení přístupového balíčku](entitlement-management-access-package-request-policy.md)
- [Zobrazení sestav a protokolů](entitlement-management-reports.md)
