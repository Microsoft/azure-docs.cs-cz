---
title: Zobrazení sestav a protokolů ve správě nároků ve službě Azure AD (Preview) – Azure Active Directory
description: Naučte se, jak zobrazit sestavu přiřazení uživatelů a protokoly auditu v Azure Active Directory správu nároků (Preview).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: ajburnle
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2420fc25795ec74939649fb8a17ead7c8cfdd1df
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69032446"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>Zobrazení sestav a protokolů ve správě nároků ve službě Azure AD (Preview)

> [!IMPORTANT]
> Správa opravňujících k Azure Active Directory (Azure AD) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sestavy přiřazení uživatelů a protokol auditu Azure Active Directory poskytují další podrobnosti o uživatelích v adresáři. Jako správce můžete zobrazit prostředky, ke kterým má uživatel přístup, a zobrazit protokoly žádostí pro účely auditování nebo určit stav požadavku uživatele. Tento článek popisuje, jak používat sestavu přiřazení uživatelů a protokoly auditu Azure AD.

Podívejte se na následující video, kde se dozvíte, jak používat správu nároků ke správě přístupu pro uživatele v Azure Active Directory:

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-resources-a-user-has-access-to"></a>Zobrazit prostředky, ke kterým má uživatel přístup

1. Klikněte na **Azure Active Directory** a pak na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **sestava přiřazení uživatelů**.

1. Kliknutím na **Vybrat uživatele** otevřete podokno vybrat uživatele.

1. Vyhledejte uživatele v seznamu, ke kterému chcete zobrazit prostředky, ke kterým mají přístup.

1. Klikněte na uživatele a pak klikněte na **Vybrat**.

    Zobrazí se seznam prostředků, ke kterým má uživatel přístup. Zahrnuje balíček přístupu, zásady a data.

    ![Sestava přiřazení uživatelů](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Určení stavu požadavku uživatele

Pokud chcete získat další podrobnosti o tom, jak si uživatel vyžádal a přijal přístup k balíčku přístupu, můžete použít protokol auditu Azure AD. Konkrétně můžete použít záznamy protokolu v `EntitlementManagement` kategorii a a `UserManagement` získat další podrobnosti o krocích zpracování pro jednotlivé požadavky.  

1. Klikněte na **Azure Active Directory** a pak klikněte na **protokoly auditu**.

1. V horní části změňte **kategorii** na buď `EntitlementManagement` nebo `UserManagement`, v závislosti na záznamu auditu, který hledáte.  

1. Klikněte na tlačítko **Použít**.

1. Chcete-li stáhnout protokoly, klikněte na tlačítko **Stáhnout**.

Když Azure AD obdrží novou žádost, zapíše záznam auditu, ve kterém `EntitlementManagement` je **kategorie** , a **aktivita** se obvykle `User requests access package assignment`nachází.  V případě přímého přiřazení vytvořeného v Azure Portal je `Administrator directly assigns user to access package`pole **aktivita** záznamu auditu a uživatel, který provádí přiřazení, je identifikován funkcí **ActorUserPrincipalName**.

Služba Azure AD bude během žádosti zapisovat další záznamy auditu, včetně:

| Kategorie | Aktivita | Stav žádosti |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Požadavek nevyžaduje schválení. |
| `UserManagement` | `Create request approval` | Požadavek vyžaduje schválení. |
| `UserManagement` | `Add approver to request approval` | Požadavek vyžaduje schválení. |
| `EntitlementManagement` | `Approve access package assignment request` | Žádost schválena |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Žádost schválena nebo nevyžaduje schválení |

Když se uživateli přiřadí přístup, služba Azure AD zapíše záznam auditu pro `EntitlementManagement` kategorii s **aktivitou** `Fulfill access package assignment`.  Uživatel, který obdržel přístup, identifikuje pole **ActorUserPrincipalName** .

Pokud se přístup nepřiřadil, služba `EntitlementManagement` Azure AD zapíše záznam auditu pro kategorii s **aktivitou** `Deny access package assignment request`, pokud žádost zamítl schvalovatel, nebo `Access package assignment request timed out (no approver action taken)`Pokud časový limit požadavku vypršel před schválením schvalovatelem.

Když platnost přiřazení balíčku přístupu uživatele vyprší, zruší ho uživatel nebo ho odebral správce a pak Azure AD zapíše záznam auditu pro `EntitlementManagement` kategorii s **aktivitou** . `Remove access package assignment`

## <a name="next-steps"></a>Další postup

- [Řešení potíží se správou nároků Azure AD](entitlement-management-troubleshoot.md)
- [Obvyklé scénáře](entitlement-management-scenarios.md)
