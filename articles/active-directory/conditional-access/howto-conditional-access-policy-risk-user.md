---
title: Podmíněný přístup na základě rizika uživatele – Azure Active Directory
description: Vytvoření zásad podmíněného přístupu pomocí rizika uživatele ochrany identity
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5423a61c8febee72f32935f3dee4e9f9e868630
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89049073"
---
# <a name="conditional-access-user-risk-based-conditional-access"></a>Podmíněný přístup: podmíněný přístup na základě rizika uživatele

Společnost Microsoft spolupracuje s výzkumnými pracovníky, prosazováním právních předpisů, různými týmy zabezpečení v Microsoftu a dalšími důvěryhodnými zdroji pro hledání párů nevrácených uživatelských jmen a hesel. Organizace s Azure AD Premium licencemi P2 můžou vytvářet zásady podmíněného přístupu, které obsahují [Azure AD Identity Protection zjišťování rizik uživatelů](../identity-protection/concept-identity-protection-risks.md#user-risk). 

Existují dvě místa, kde je možné tuto zásadu přiřadit. Organizace by si měli vybrat jednu z následujících možností, aby bylo možné povolit zásady podmíněného přístupu na základě rizik uživatelů, které vyžadují zabezpečenou změnu hesla.

## <a name="enable-with-conditional-access-policy"></a>Povolit se zásadami podmíněného přístupu

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Vyhledejte **Azure Active Directory**  >    >  **podmíněný přístup** zabezpečení.
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard. Další informace najdete v [Nastavení standardů pojmenovávání zásad](./plan-conditional-access.md#set-naming-standards-for-your-policies).
1. V části **Přiřazení** vyberte **Uživatelé a skupiny**.
   1. V části **Zahrnout** vyberte **Všichni uživatelé**.
   1. V části **vyloučit** vyberte **Uživatelé a skupiny** a zvolte účty pro nouzový přístup nebo rozklad vaší organizace. 
   1. Vyberte **Hotovo**.
1. V části **cloudové aplikace nebo akce**  >  vyberte **všechny cloudové aplikace**.
1. V části **podmínky**  >  **rizika uživatele** nastavte **Konfigurovat** na **Ano**. V části **konfigurovat úrovně rizika uživatele potřebné pro vymáhání zásady** vyberte **Vysoká** a potom vyberte **Hotovo**.
1. V části **řízení přístupu**  >  **udělení** přístupu vyberte **udělit přístup**, **vyžadovat změnu hesla** a vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte tak, aby se zásady povolily.

## <a name="enable-through-identity-protection"></a>Povolit prostřednictvím Identity Protection

1. Přihlaste se na **Azure Portal**.
1. Vyberte **všechny služby** a pak vyhledejte **Azure AD Identity Protection**.
1. Vyberte **zásady rizik uživatelů**.
1. V části **přiřazení** vyberte **Uživatelé**.
   1. V části **Zahrnout** vyberte **Všichni uživatelé**.
   1. V části **vyloučit** vyberte **Vybrat vyloučené uživatele**, zvolte nouzový přístup nebo účty pro přerušení vaší organizace a vyberte **Vybrat**.
   1. Vyberte **Hotovo**.
1. V části **podmínky** vyberte **riziko pro uživatele** a pak zvolte **Vysoká**.
   1. Vyberte **Vybrat** a pak **Hotovo**.
1. V části **ovládací prvky**  >  **přístup** zvolte možnost **povolený přístup** a pak vyberte **vyžadovat změnu hesla**.
   1. Vyberte **Vybrat**.
1. Nastavte **zásadu Vynutilit** na **zapnuto**.
1. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Podmíněný přístup podle rizika přihlašování](howto-conditional-access-policy-risk.md)

[Určení dopadu pomocí režimu pouze sestavy podmíněného přístupu](howto-conditional-access-insights-reporting.md)

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)

[Co je Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)