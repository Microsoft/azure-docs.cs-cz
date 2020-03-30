---
title: Zobrazení sestav & protokolů ve správě nároků – Azure AD
description: Přečtěte si, jak zobrazit sestavu přiřazení uživatelů a protokoly auditování ve správě nároků služby Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 514f8e86d6bd28cc5212e0f0058f00e270f43e35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128418"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>Zobrazení sestav a protokolů ve správě oprávnění Azure AD

Sestavy správy nároků Azure AD a protokol auditu Azure AD poskytují další podrobnosti o tom, k jakým prostředkům mají uživatelé přístup. Jako správce můžete zobrazit přístupové balíčky a přiřazení prostředků pro uživatele a zobrazit protokoly požadavků pro účely auditování nebo určit stav požadavku uživatele. Tento článek popisuje, jak používat sestavy správy nároků a protokoly auditu Azure AD.

Následující video se dozvíte, jak zobrazit, k jakým prostředkům mají uživatelé při správě nároků přístup:

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>Zobrazení přístupových balíčků pro uživatele

Tato sestava umožňuje vypsat všechny přístupové balíčky, které může uživatel požadovat, a přístupové balíčky, které jsou aktuálně přiřazeny uživateli.

**Předpokladová role:** Globální správce nebo správce uživatelů

1. Klikněte na **Azure Active Directory** a potom klikněte na **Zásadsprávné řízení identit .**

1. V levé nabídce klikněte na **Sestavy**.

1. Klikněte na **položku Přístup k balíčkům pro uživatele**.

1. Kliknutím na **Vybrat uživatele** otevřete podokno Vybrat uživatele.

1. Vyhledejte uživatele v seznamu a klepněte na tlačítko **Vybrat**.

    Na kartě **Může požadovat** zobrazí seznam přístupových balíčků, které může uživatel požadovat. Tento seznam je určen [zásadami požadavků](entitlement-management-access-package-request-policy.md#for-users-in-your-directory) definovanými pro přístupové balíčky. 

    ![Přístup k balíčkům pro uživatele](./media/entitlement-management-reports/access-packages-report.png)

1. Pokud existuje více než jednu roli prostředků nebo zásady pro balíček přístupu, klikněte na role prostředků nebo položku zásad zobrazíte podrobnosti výběru.

1. Kliknutím na kartu **Přiřazeno** zobrazíte seznam přístupových balíčků aktuálně přiřazených uživateli. Pokud je uživateli přiřazen přístupový balíček, znamená to, že uživatel má přístup ke všem rolím prostředků v balíčku přístupu.

## <a name="view-resource-assignments-for-a-user"></a>Zobrazení přiřazení zdrojů pro uživatele

Tato sestava umožňuje vypsat zdroje aktuálně přiřazené uživateli ve správě nároků. Všimněte si, že tato sestava je pro prostředky spravované se správou nároků. Uživatel může mít přístup k jiným prostředkům ve vašem adresáři mimo správu nároků.

**Předpokladová role:** Globální správce nebo správce uživatelů

1. Klikněte na **Azure Active Directory** a potom klikněte na **Zásadsprávné řízení identit .**

1. V levé nabídce klikněte na **Sestavy**.

1. Klepněte na **položku Přiřazení zdrojů pro uživatele**.

1. Kliknutím na **Vybrat uživatele** otevřete podokno Vybrat uživatele.

1. Vyhledejte uživatele v seznamu a klepněte na tlačítko **Vybrat**.

    Zobrazí se seznam zdrojů aktuálně přiřazených uživateli. Seznam také zobrazuje balíček přístupu a zásady, ze kterých získali roli prostředku, spolu s počátečním a koncovým datem pro přístup.
    
    Pokud uživatel získal přístup ke stejnému prostředku ve dvou nebo více balíčcích, můžete klepnutím na šipku zobrazit každý balíček a zásady.

    ![Přiřazení zdrojů pro uživatele](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Určení stavu požadavku uživatele

Chcete-li získat další podrobnosti o tom, jak uživatel požadoval a přijal přístup k balíčku přístupu, můžete použít protokol auditu Azure AD. Zejména můžete použít záznamy protokolu v `EntitlementManagement` `UserManagement` a kategorie získat další podrobnosti o postupech zpracování pro každý požadavek.  

1. Klikněte na **službu Azure Active Directory** a potom na **položku Protokoly auditování**.

1. V horní části **Category** změňte kategorii `EntitlementManagement` `UserManagement`na jednu nebo , v závislosti na záznamu auditu, který hledáte.  

1. Klikněte na **Použít**.

1. Chcete-li protokoly stáhnout, klepněte na tlačítko **Stáhnout**.

Když Azure AD obdrží nový požadavek, zapíše záznam auditu, ve kterém `User requests access package assignment`je `EntitlementManagement` **kategorie** a **aktivita** je obvykle .  V případě přímého přiřazení vytvořeného na portálu Azure je `Administrator directly assigns user to access package`pole **Aktivita** záznamu auditu a uživatel provádějící přiřazení je identifikován **actoruserprincipalname**.

Azure AD bude psát další záznamy auditu, zatímco žádost probíhá, včetně:

| Kategorie | Aktivita | Stav požadavku |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Požadavek nevyžaduje schválení |
| `UserManagement` | `Create request approval` | Žádost vyžaduje schválení |
| `UserManagement` | `Add approver to request approval` | Žádost vyžaduje schválení |
| `EntitlementManagement` | `Approve access package assignment request` | Žádost schválena |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Žádost schválena nebo nevyžaduje schválení |

Když je uživateli přiřazen přístup, Azure AD `EntitlementManagement` zapíše záznam auditu pro kategorii s **aktivitou** `Fulfill access package assignment`.  Uživatel, který obdržel přístup, je identifikován polem **ActorUserPrincipalName.**

Pokud přístup nebyl přiřazen, pak Azure AD `EntitlementManagement` zapíše záznam `Deny access package assignment request`auditu pro kategorii s **aktivitou** `Access package assignment request timed out (no approver action taken)`buď, pokud byl požadavek zamítnut schvalovatelem, nebo , pokud časový čas požadavku před schvalovatel mohl schválit.

Když vyprší přiřazení balíčku přístupu uživatele, je zrušena uživatelem nebo odebrána správcem, pak Azure `EntitlementManagement` AD zapíše `Remove access package assignment`záznam auditu pro kategorii s **Activity** of .

## <a name="next-steps"></a>Další kroky

- [Archivovat sestavy a protokoly](entitlement-management-logs-and-reporting.md)
- [Poradce při potížích se správou nároků Azure AD](entitlement-management-troubleshoot.md)
- [Obvyklé scénáře](entitlement-management-scenarios.md)
