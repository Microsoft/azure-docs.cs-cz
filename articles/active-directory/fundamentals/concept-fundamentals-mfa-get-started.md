---
title: Povolení Multi-Factor Authentication pro vaši organizaci – Azure Active Directory
description: Povolte Azure MFA pro vaši organizaci na základě vaší licence.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe0d79f228ee2b84c00a35d4836cbda6a4847a42
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932447"
---
# <a name="enable-multi-factor-authentication-for-your-organization"></a>Povolení Multi-Factor Authentication pro vaši organizaci

Existuje několik způsobů, jak povolit Azure Multi-Factor Authentication (MFA) pro uživatele služby Azure Active Directory (AD) na základě licencí, které vaše organizace vlastní. 

![V případě potřeby prozkoumat signály a vynutilit MFA](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Na základě našich studií je váš účet o více než 99,9% méně pravděpodobný, pokud použijete MFA.

Jak vaše organizace zapíná vícefaktorové ověřování ještě zdarma, než se stane statistikou?

## <a name="free-option"></a>Možnost Free

Zákazníci, kteří využívají Bezplatné výhody Azure AD, můžou použít [výchozí nastavení zabezpečení](../fundamentals/concept-fundamentals-security-defaults.md) a povolit službu Multi-Factor Authentication ve svém prostředí.

## <a name="office-365"></a>Office 365

Pro zákazníky se sadou Office 365 jsou k dispozici dvě možnosti:

- [Výchozí nastavení zabezpečení](concept-fundamentals-security-defaults.md) je možné povolit prostřednictvím služby Azure AD a chránit tak všechny uživatele pomocí Azure Multi-Factor Authentication.
- Pokud vaše organizace vyžaduje více členitosti při poskytování služby Multi-Factor Authentication, zahrnují vaše licence [na uživatele možnosti vícefaktorového](../authentication/howto-mfa-userstates.md) ověřování. Vícefaktorové ověřování vázané na uživatele je povolené a v každém z nich se vynutilo pro každého uživatele zvlášť správcem.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Pro zákazníky s Azure AD Premium P1 nebo podobnými licencemi, které zahrnují tuto funkci, jako je Enterprise Mobility + Security E3, Microsoft 365 F1 nebo Microsoft 365 E3: 

Doporučením je použití [zásad podmíněného přístupu](../conditional-access/concept-conditional-access-policy-common.md) pro nejlepší uživatelské prostředí.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Pro zákazníky s Azure AD Premium P2 nebo podobnými licencemi, které zahrnují tuto funkci, jako je například Enterprise Mobility + Security E5 nebo Microsoft 365 E5: 

Doporučením je použití [zásad podmíněného přístupu](../conditional-access/concept-conditional-access-policy-common.md) společně se zásadami riziku [ochrany identity pro zajištění](../identity-protection/overview-v2.md) optimálního uživatelského prostředí a flexibility vynucení.

## <a name="authentication-methods"></a>Metody ověření

|   | Výchozí nastavení zabezpečení | Všechny ostatní metody |
| --- | --- | --- |
| Oznámení přes mobilní aplikaci | × | × |
| Ověřovací kód z mobilní aplikace nebo hardwarového tokenu |   | × |
| Textová zpráva na telefon |   | × |
| Telefonní hovor |   | × |
| Hesla aplikací |   | × * * |

\* * Hesla aplikací jsou k dispozici pouze v případě MFA pro jednotlivé uživatele se staršími scénáři ověřování, pokud jsou povolena správci.

## <a name="next-steps"></a>Další kroky

[Stránka s cenami služby Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
