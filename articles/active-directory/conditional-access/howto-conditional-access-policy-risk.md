---
title: Podmíněný přístup – podmíněný přístup založený na riziku – Služba Azure Active Directory
description: Vytvoření zásad podmíněného přístupu, které umožní vylepšení zásad ochrany identity
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8054d8985596095db32d9262322d7fb0f4aab8c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295155"
---
# <a name="conditional-access-risk-based-conditional-access"></a>Podmíněný přístup: Podmíněný přístup založený na riziku

Organizace s licencemi Azure AD Premium P2 můžou vytvářet zásady podmíněného přístupu zahrnující detekci rizik azure aidentity ochrany identity. Existují tři výchozí zásady, které lze povolit po vybalení z krabice. 

* Vyžadovat, aby se všichni uživatelé zaregistrovali pro azure vícefaktorové ověřování.
* Vyžadovat změnu hesla pro uživatele, kteří jsou vysoce rizikové.
* Vyžadovat vícefaktorové ověřování pro uživatele se středním nebo vysokým rizikem přihlášení.

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>Vyžadovat, aby se všichni uživatelé zaregistrovali pro vícefaktorové ověřování Azure

Povolení této zásady bude vyžadovat, aby se všichni uživatelé zaregistrovali pro azure multifaktorové ověřování do 14 dnů. 

1. Přihlaste se k **portálu Azure**.
1. Klikněte na **Všechny služby** a přejděte na **Azure AD Identity Protection**.
1. Klikněte na **Registrace MFA**.
1. V části **Přiřazení**vyberte **možnost Uživatelé**.
   1. V části **Zahrnout**vyberte **možnost Všichni uživatelé**.
   1. V části **Vyloučit**vyberte **vybrat vyloučené uživatele**, zvolte účty pro nouzový přístup nebo přetržení vaší organizace a vyberte **Vybrat**. 
   1. Vyberte **Done** (Hotovo).
1. Nastavte **zásady vynucení** na **zapnuto.**
1. Klikněte na **Uložit**.

## <a name="require-a-password-change-high-risk-users"></a>Vyžadovat změnu hesla vysoce rizikových uživatelů

Microsoft spolupracuje při vyhledávání dvojic uživatelských jmen a hesel s výzkumnými pracovníky, orgány zajišťujícími vymáhání zákona, různými týmy zabezpečení v Microsoftu a dalšími důvěryhodnými zdroji. Když některá dvojice odpovídá účtu ve vašem prostředí, může se následující zásadou aktivovat změna hesla na základě rizikové události.

1. Přihlaste se k **portálu Azure**.
1. Klikněte na **Všechny služby** a přejděte na **Azure AD Identity Protection**.
1. Klikněte na **zásady rizika uživatele**.
1. V části **Přiřazení**vyberte **Možnost Uživatelé.**
   1. V části **Zahrnout**vyberte **možnost Všichni uživatelé**.
   1. V části **Vyloučit**vyberte **vybrat vyloučené uživatele**, zvolte účty pro nouzový přístup nebo přetržení vaší organizace a vyberte **Vybrat**.
   1. Vyberte **Done** (Hotovo).
1. V **části Podmínky**vyberte Uživatelské **riziko**a pak zvolte **Vysoká**.
   1. Klepněte na **tlačítko Vybrat** **a**hotovo .
1. V **části Controls** > **Access**zvolte **Allow access**a pak vyberte Vyžadovat **změnu hesla**.
   1. Klepněte na **tlačítko Vybrat**.
1. Nastavte **zásady vynucení** na **zapnuto.**
1. Klikněte na **Uložit**.

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>Vyžadovat středně nebo vysoce rizikové uživatele vícefaktorového faktoru

Většina uživatelů se chová běžným způsobem, který je možné sledovat. Když se mimo tento běžný způsob vychýlí, může být nebezpečné jim povolit se normálně přihlásit. Možná budete chtít zablokovat, že uživatel, nebo možná jen požádat je, aby provedla vícefaktorové ověřování dokázat, že jsou opravdu, kdo říkají, že jsou. Vyžádání vícefaktorového ověření při zjištění rizikového přihlášení zapnete povolením následující zásady.

1. Přihlaste se k **portálu Azure**.
1. Klikněte na **Všechny služby** a přejděte na **Azure AD Identity Protection**.
1. Klikněte na **zásady rizika přihlášení.**
1. V části **Přiřazení**vyberte **Možnost Uživatelé.**
   1. V části **Zahrnout**vyberte **možnost Všichni uživatelé**.
   1. V části **Vyloučit**vyberte **vybrat vyloučené uživatele**, zvolte účty pro nouzový přístup nebo přetržení vaší organizace a vyberte **Vybrat**.
   1. Vyberte **Done** (Hotovo).
1. V **části Podmínky**vyberte Riziko **přihlášení**a pak zvolte Střední **a vyšší**.
   1. Klepněte na **tlačítko Vybrat** **a**hotovo .
1. V **části Controls** > **Access**zvolte Allow **access**a pak vyberte **Vyžadovat vícefaktorové ověřování**.
   1. Klepněte na **tlačítko Vybrat**.
1. Nastavte **zásady vynucení** na **zapnuto.**
1. Klikněte na **Uložit**.

## <a name="next-steps"></a>Další kroky

[Podmíněné přístupové běžné zásady](concept-conditional-access-policy-common.md)

[Určení dopadu pomocí režimu pouze pro sestavu podmíněného přístupu](howto-conditional-access-report-only.md)

[Simulovat chování přihlášení pomocí nástroje Co-li podmíněného přístupu](troubleshoot-conditional-access-what-if.md)

[Jak to funguje: Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)

[Co je Azure Active Directory Identity Protection?](../identity-protection/overview.md)
