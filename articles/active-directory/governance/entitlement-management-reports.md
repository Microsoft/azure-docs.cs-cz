---
title: Prohlížet si protokoly a sestavy ve službě Azure AD oprávnění management (Preview) – Azure Active Directory
description: Zjistěte, jak zobrazit sestavu přiřazení uživatelů a protokolů auditu v Azure Active Directory oprávnění správy (Preview).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a61a581574c77a57939ea23fdadc7b060b82af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64541537"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>Zobrazit sestavy a protokoly ve správě služby Azure AD oprávnění (Preview)

> [!IMPORTANT]
> Správa nároků Azure Active Directory (Azure AD) je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="view-resources-a-user-has-access-to"></a>Zobrazit prostředky, které mají uživatelé přístup k

1. Klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **sestava přiřazení uživatele**.

1. Klikněte na tlačítko **vybraným uživatelům** a otevřete tak podokno vybraným uživatelům.

1. Vyhledejte uživatele, v seznamu, který chcete zobrazit prostředky, které mají přístup.

1. Klepněte na uživatele a pak klikněte na tlačítko **vyberte**.

    Zobrazí se seznam prostředků, které má uživatel přístup k. Zahrnuje přístup k balíčku, zásad a data.

    ![Sestava uživatelů spadajících pod přiřazení](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Zjistit stav požadavku na uživatele

Pokud chcete získat další podrobnosti o jak uživatel požaduje a přijaté přístup k balíčku přístup, můžete použít protokol auditu Azure AD. Konkrétně můžete použít záznamů protokolu v `EntitlementManagement` a `UserManagement` kategorie zobrazíte další podrobnosti o krocích zpracování pro každý požadavek.  

1. Klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **protokoly auditu**.

1. V horní části stránky, změnit **kategorie** buď `EntitlementManagement` nebo `UserManagement`, v závislosti na záznam auditu, které hledáte.  

1. Klikněte na tlačítko **Použít**.

1. Pro stahování protokolů, klikněte na tlačítko **Stáhnout**.

Novou žádost o přijetí služby Azure AD zapíše záznamu auditu, ve kterém **kategorie** je `EntitlementManagement` a **aktivity** je obvykle `User requests access package assignment`.  V případě přímého přiřazení vytvořili na webu Azure Portal **aktivity** pole záznamu auditu je `Administrator directly assigns user to access package`, a uživatel provádějící přiřazení je identifikován **ActorUserPrincipalName**.

Azure AD se zapište záznamy auditu, probíhá požadavek, včetně:

| Category | Aktivita | Stav žádosti |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Žádost o nevyžaduje schválení |
| `UserManagement` | `Create request approval` | Požadavek vyžaduje schválení |
| `UserManagement` | `Add approver to request approval` | Požadavek vyžaduje schválení |
| `EntitlementManagement` | `Approve access package assignment request` | Žádost se schválila. |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Žádost o schválení nebo nevyžaduje schválení |

Když se uživatel je přiřazený přístup, Azure AD zapíše záznam auditu pro `EntitlementManagement` kategorie s **aktivity** `Fulfill access package assignment`.  Uživatel, který obdržel přístup je identifikován **ActorUserPrincipalName** pole.

Pokud nebyla přiřazena přístup a pak zapíše záznam auditu pro Azure AD `EntitlementManagement` kategorie s **aktivity** buď `Deny access package assignment request`v případě, že žádost byla zamítnuta schvalovatelem, nebo `Access package assignment request timed out (no approver action taken)`, pokud vypršel před schvalovatel může schválit.

Když vyprší platnost přiřazení balíčku přístupu uživatele, je zrušena uživatelem nebo odebrat správce, pak vypíše záznam auditu pro Azure AD `EntitlementManagement` kategorie s **aktivity** z `Remove access package assignment`.

## <a name="next-steps"></a>Další postup

- [Řešení potíží se správou oprávnění Azure AD](entitlement-management-troubleshoot.md)
- [Obvyklé scénáře](entitlement-management-scenarios.md)
