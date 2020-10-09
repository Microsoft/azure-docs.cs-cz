---
title: Zobrazení sestav & protokolů ve správě nároků – Azure AD
description: Naučte se, jak zobrazit sestavu přiřazení uživatelů a protokoly auditu v Azure Active Directory správu nároků.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: barclayn
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86f2d5202a9b5439fcacca549659e4e181ffeca4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85078126"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>Zobrazení sestav a protokolů v Azure AD – Správa nároků

Sestavy správy opravňujících k Azure AD a protokolu auditu Azure AD poskytují další podrobnosti o tom, k jakým prostředkům mají uživatelé přístup. Jako správce můžete zobrazit balíčky pro přístup a přiřazení prostředků pro uživatele a zobrazit protokoly žádostí pro účely auditování nebo určit stav požadavku uživatele. Tento článek popisuje, jak používat sestavy správy oprávnění a protokoly auditu Azure AD.

Podívejte se na následující video, kde se dozvíte, jak zobrazit prostředky, ke kterým mají uživatelé přístup v rámci správy nároků:

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>Zobrazit balíčky pro přístup pro uživatele

Tato sestava umožňuje zobrazit seznam všech přístupových balíčků, které může uživatel požadovat, a balíčky pro přístup, které jsou aktuálně přiřazeny uživateli.

**Požadovaná role:** Globální správce nebo Správce uživatelů

1. Klikněte na **Azure Active Directory** a pak na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **sestavy**.

1. Klikněte na **přístupové balíčky pro uživatele**.

1. Kliknutím na **Vybrat uživatele** otevřete podokno vybrat uživatele.

1. Vyhledejte uživatele v seznamu a klikněte na tlačítko **Vybrat**.

    Karta **může vyžadovat** zobrazení seznamu balíčků přístupu, které si uživatel může vyžádat. Tento seznam je určený zásadami pro [žádosti](entitlement-management-access-package-request-policy.md#for-users-in-your-directory) definovanými pro balíčky přístupu. 

    ![Přístup k balíčkům pro uživatele](./media/entitlement-management-reports/access-packages-report.png)

1. Pokud existuje více než jedna role nebo zásady prostředků pro balíček přístupu, zobrazí se podrobnosti výběru kliknutím na položku role nebo zásady prostředků.

1. Kliknutím na kartu **přiřazeno** zobrazíte seznam balíčků pro přístup, které jsou aktuálně přiřazeny uživateli. Když se k uživateli přiřadí balíček pro přístup, znamená to, že uživatel má přístup ke všem rolím prostředků v balíčku pro přístup.

## <a name="view-resource-assignments-for-a-user"></a>Zobrazení přiřazení prostředků pro uživatele

Tato sestava umožňuje zobrazit seznam prostředků, které jsou aktuálně přiřazeny uživateli v rámci správy oprávnění. Všimněte si, že tato sestava je určena pro prostředky spravované pomocí správy nároků. Uživatel může mít přístup k jiným prostředkům ve vašem adresáři mimo správu nároků.

**Požadovaná role:** Globální správce nebo Správce uživatelů

1. Klikněte na **Azure Active Directory** a pak na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **sestavy**.

1. Klikněte na **přiřazení prostředků pro uživatele**.

1. Kliknutím na **Vybrat uživatele** otevřete podokno vybrat uživatele.

1. Vyhledejte uživatele v seznamu a klikněte na tlačítko **Vybrat**.

    Zobrazí se seznam prostředků, které jsou aktuálně přiřazeny uživateli. V seznamu se taky zobrazuje balíček pro přístup a zásady, ze kterých se role prostředků dostaly, spolu s počátečním a koncovým datem pro přístup.
    
    Pokud má uživatel přístup ke stejnému prostředku ve dvou nebo více balíčcích, můžete kliknutím na šipku zobrazit jednotlivé balíčky a zásady.

    ![Přiřazení prostředků pro uživatele](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Určení stavu požadavku uživatele

Pokud chcete získat další podrobnosti o tom, jak si uživatel vyžádal a přijal přístup k balíčku přístupu, můžete použít protokol auditu Azure AD. Konkrétně můžete použít záznamy protokolu v `EntitlementManagement` kategorii a a `UserManagement` získat další podrobnosti o krocích zpracování pro jednotlivé požadavky.  

1. Klikněte na **Azure Active Directory** a pak klikněte na **protokoly auditu**.

1. V horní části změňte **kategorii** na buď `EntitlementManagement` nebo `UserManagement` , v závislosti na záznamu auditu, který hledáte.  

1. Klikněte na **Použít**.

1. Chcete-li stáhnout protokoly, klikněte na tlačítko **Stáhnout**.

Když Azure AD obdrží novou žádost, zapíše záznam auditu, ve kterém je **kategorie** , `EntitlementManagement` a **aktivita** se obvykle nachází `User requests access package assignment` .  V případě přímého přiřazení vytvořeného v Azure Portal je pole **aktivita** záznamu auditu `Administrator directly assigns user to access package` a uživatel, který provádí přiřazení, je identifikován funkcí **ActorUserPrincipalName**.

Služba Azure AD bude během žádosti zapisovat další záznamy auditu, včetně:

| Kategorie | Aktivita | Stav žádosti |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Požadavek nevyžaduje schválení. |
| `UserManagement` | `Create request approval` | Požadavek vyžaduje schválení. |
| `UserManagement` | `Add approver to request approval` | Požadavek vyžaduje schválení. |
| `EntitlementManagement` | `Approve access package assignment request` | Žádost schválena |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Žádost schválena nebo nevyžaduje schválení |

Když se uživateli přiřadí přístup, služba Azure AD zapíše záznam auditu pro `EntitlementManagement` kategorii s **aktivitou** `Fulfill access package assignment` .  Uživatel, který obdržel přístup, identifikuje pole **ActorUserPrincipalName** .

Pokud se přístup nepřiřadil, služba Azure AD zapíše záznam auditu pro `EntitlementManagement` kategorii s **aktivitou** `Deny access package assignment request` , pokud žádost zamítl schvalovatel, nebo `Access package assignment request timed out (no approver action taken)` Pokud časový limit požadavku vypršel před schválením schvalovatelem.

Když platnost přiřazení balíčku přístupu uživatele vyprší, zruší ho uživatel nebo ho odebral správce a pak Azure AD zapíše záznam auditu pro `EntitlementManagement` kategorii s **aktivitou** `Remove access package assignment` .

## <a name="next-steps"></a>Další kroky

- [Archivace sestav a protokolů](entitlement-management-logs-and-reporting.md)
- [Řešení potíží se správou nároků Azure AD](entitlement-management-troubleshoot.md)
- [Typické scénáře](entitlement-management-scenarios.md)
