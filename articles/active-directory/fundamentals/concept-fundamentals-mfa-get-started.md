---
title: Multi-Factor Authentication Azure AD pro vaši organizaci – Azure Active Directory
description: Přečtěte si o dostupných funkcích služby Azure AD Multi-Factor Authentication ve vaší organizaci na základě vašeho licenčního modelu.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: daveba
author: daveba
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85e7f02f8cbda6218396bf4a9a4654a113b7817c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94836738"
---
# <a name="overview-of-azure-ad-multi-factor-authentication-for-your-organization"></a>Přehled Multi-Factor Authentication Azure AD pro vaši organizaci

Existuje několik způsobů, jak povolit Multi-Factor Authentication služby Azure AD pro uživatele služby Azure Active Directory (AD) na základě licencí, které vaše organizace vlastní. 

![V případě potřeby prozkoumat signály a vynutilit MFA](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Na základě našich studií je váš účet více než 99,9% méně pravděpodobný, pokud použijete službu Multi-Factor Authentication (MFA).

Jak vaše organizace zapnete vícefaktorové ověřování, i když je to zadarmo, než se stanete statistikou?

## <a name="free-option"></a>Možnost Free

Zákazníci, kteří využívají Bezplatné výhody Azure AD, můžou použít [výchozí nastavení zabezpečení](../fundamentals/concept-fundamentals-security-defaults.md) a povolit službu Multi-Factor Authentication ve svém prostředí.

## <a name="microsoft-365-business-e3-or-e5"></a>Microsoft 365 Business, E3 nebo E5

Pro zákazníky, kteří používají Microsoft 365, jsou k dispozici dvě možnosti:

* Pro všechny přihlašovací události je Azure AD Multi-Factor Authentication buď povolený, nebo zakázaný pro všechny uživatele. Pro podmnožinu uživatelů není možné povolit ověřování pomocí služby Multi-Factor Authentication nebo jenom v některých případech. Správa je prostřednictvím portálu Office 365. 
* Pro lepší uživatelské prostředí upgradujte na Azure AD Premium P1 nebo P2 a použijte podmíněný přístup. Další informace najdete v tématu zabezpečení Microsoft 365ch prostředků pomocí služby Multi-Factor Authentication.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Pro zákazníky s Azure AD Premium P1 nebo podobnými licencemi, které zahrnují tuto funkci, jako je Enterprise Mobility + Security E3, Microsoft 365 F1 nebo Microsoft 365 E3: 

Pomocí [podmíněného přístupu Azure AD](../authentication/tutorial-enable-azure-mfa.md) můžete vyzvat uživatele k ověřování službou Multi-Factor Authentication během určitých scénářů nebo událostí, aby vyhovovaly vašim obchodním požadavkům.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Pro zákazníky s Azure AD Premium P2 nebo podobnými licencemi, které zahrnují tuto funkci, jako je například Enterprise Mobility + Security E5 nebo Microsoft 365 E5: 

Poskytuje nejsilnější umístění zabezpečení a vylepšené uživatelské prostředí. Přidá [podmíněný přístup založený na rizikech](../conditional-access/howto-conditional-access-policy-risk.md) k funkcím Azure AD Premium P1, které se přizpůsobí uživatelským vzorům a minimalizuje výzvy k ověření službou Multi-Factor Authentication.

## <a name="authentication-methods"></a>Metody ověřování

| Metoda | Výchozí nastavení zabezpečení | Všechny ostatní metody |
| --- | --- | --- |
| Oznámení prostřednictvím mobilní aplikace | × | × |
| Ověřovací kód z mobilní aplikace nebo hardwarového tokenu |   | × |
| Textová zpráva na telefon |   | × |
| Zavolat na telefon |   | × |

## <a name="next-steps"></a>Další kroky

Informace o tom, jak začít, najdete v kurzu [zabezpečení událostí přihlašování uživatelů pomocí Multi-Factor Authentication služby Azure AD](../authentication/tutorial-enable-azure-mfa.md).

Další informace o licencování najdete v tématu [funkce a licence pro Azure AD Multi-Factor Authentication](../authentication/concept-mfa-licensing.md).
