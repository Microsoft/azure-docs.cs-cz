---
title: Podmíněný přístup – podmíněný přístup na základě rizik – Azure Active Directory
description: Vytvoření zásad podmíněného přístupu pro povolení vylepšení Identity Protection na zásady
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcdd89ba7741257443cf5e7567c59a6986e43c20
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424831"
---
# <a name="conditional-access-risk-based-conditional-access"></a>Podmíněný přístup: podmíněný přístup na základě rizik

Organizace s Azure AD Premium licencemi P2 můžou vytvářet zásady podmíněného přístupu, které obsahují Azure AD Identity Protection detekce rizik. Existují tři výchozí zásady, které mohou být povoleny v poli. 

* Vyžaduje, aby se všichni uživatelé zaregistrovali pro Azure Multi-Factor Authentication.
* Vyžaduje změnu hesla pro uživatele s vysokým rizikem.
* Vyžadovat službu Multi-Factor Authentication pro uživatele se středním nebo vysokým rizikem přihlášení.

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>Vyžadovat, aby se všichni uživatelé zaregistrovali pro Azure Multi-Factor Authentication

Povolení této zásady bude vyžadovat, aby se všichni uživatelé zaregistrovali pro Azure Multi-Factor Authentication do 14 dnů. 

1. Přihlaste se na web **Azure Portal**.
1. Klikněte na **Všechny služby** a přejděte na **Azure AD Identity Protection**.
1. Klikněte na **Registrace MFA**.
1. V části **přiřazení**vyberte **Uživatelé**.
   1. V části **Zahrnout**vyberte **Všichni uživatelé**.
   1. V části **vyloučit**vyberte **Vybrat vyloučené uživatele**, zvolte nouzový přístup nebo účty pro přerušení vaší organizace a vyberte **Vybrat**. 
   1. Vyberte **Done** (Hotovo).
1. Nastavte **zásadu Vynutilit** na **zapnuto**.
1. Klikněte na možnost **Uložit**.

## <a name="require-a-password-change-high-risk-users"></a>Vyžadovat změnu hesla s vysokým rizikem uživatelů

Microsoft spolupracuje při vyhledávání dvojic uživatelských jmen a hesel s výzkumnými pracovníky, orgány zajišťujícími vymáhání zákona, různými týmy zabezpečení v Microsoftu a dalšími důvěryhodnými zdroji. Když některá dvojice odpovídá účtu ve vašem prostředí, může se následující zásadou aktivovat změna hesla na základě rizikové události.

1. Přihlaste se na web **Azure Portal**.
1. Klikněte na **Všechny služby** a přejděte na **Azure AD Identity Protection**.
1. Klikněte na **zásady rizika pro uživatele**.
1. V části **přiřazení**vyberte **Uživatelé** .
   1. V části **Zahrnout**vyberte **Všichni uživatelé**.
   1. V části **vyloučit**vyberte **Vybrat vyloučené uživatele**, zvolte nouzový přístup nebo účty pro přerušení vaší organizace a vyberte **Vybrat**.
   1. Vyberte **Done** (Hotovo).
1. V části **podmínky**vyberte **riziko pro uživatele**a pak zvolte **Vysoká**.
   1. Klikněte na **Vybrat** a potom na **Hotovo**.
1. V části **ovládací prvky** > **přístup**zvolte možnost **Povolení přístupu**a pak vyberte možnost **vyžadovat změnu hesla**.
   1. Klikněte na **Vybrat**.
1. Nastavte **zásadu Vynutilit** na **zapnuto**.
1. Klikněte na možnost **Uložit**.

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>Vyžadovat, aby se pro rizikové uživatele MFA nebo vysokého přihlašování

Většina uživatelů se chová běžným způsobem, který je možné sledovat. Když se mimo tento běžný způsob vychýlí, může být nebezpečné jim povolit se normálně přihlásit. Je možné, že budete chtít tohoto uživatele zablokovat nebo ho můžete jednoduše požádat, aby provedl ověřování pomocí vícefaktorového ověřování, aby prokázal, že jsou ve skutečnosti, na kterých říkají. Vyžádání vícefaktorového ověření při zjištění rizikového přihlášení zapnete povolením následující zásady.

1. Přihlaste se na web **Azure Portal**.
1. Klikněte na **Všechny služby** a přejděte na **Azure AD Identity Protection**.
1. Klikněte na **zásady rizik přihlašování** .
1. V části **přiřazení**vyberte **Uživatelé** .
   1. V části **Zahrnout**vyberte **Všichni uživatelé**.
   1. V části **vyloučit**vyberte **Vybrat vyloučené uživatele**, zvolte nouzový přístup nebo účty pro přerušení vaší organizace a vyberte **Vybrat**.
   1. Vyberte **Done** (Hotovo).
1. V části **podmínky**vyberte možnost **riziko přihlášení**a pak zvolte **střední a vyšší**.
   1. Klikněte na **Vybrat** a potom na **Hotovo**.
1. V části **ovládací prvky** > **přístup**zvolte možnost **Povolení přístupu**a pak vyberte možnost **vyžadovat vícefaktorové ověřování**.
   1. Klikněte na **Vybrat**.
1. Nastavte **zásadu Vynutilit** na **zapnuto**.
1. Klikněte na možnost **Uložit**.

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Určení dopadu pomocí režimu pouze sestavy podmíněného přístupu](howto-conditional-access-report-only.md)

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)

[Jak to funguje: Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)

[Co je Azure Active Directory Identity Protection?](../identity-protection/overview.md)
