---
title: Azure Multi-Factor Authentication pro vaši organizaci – Azure Active Directory
description: Informace o dostupných funkcích azure multifaktorového ověřování pro vaši organizaci na základě licenčního modelu
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: febdb708c637ac322c0ca884eae627da9bd5904c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530389"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>Přehled azure multifaktorového ověřování pro vaši organizaci

Existuje několik způsobů, jak povolit vícefaktorové ověřování Azure pro uživatele služby Azure Active Directory (AD) na základě licencí, které vaše organizace vlastní. 

![Prozkoumejte signály a vpřípadě potřeby vynucujte vícefaktorové vynucovat vícefaktorové vy](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Na základě našich studií je u vašeho účtu o více než 99,9 % menší pravděpodobnost ohrožení zabezpečení, pokud používáte vícefaktorové ověřování (MFA).

Tak jak se vaše organizace zapnout MFA i zdarma, než se stane statistika?

## <a name="free-option"></a>Možnost zdarma

Zákazníci, kteří využívají bezplatné výhody Azure AD můžete použít [výchozí zabezpečení](../fundamentals/concept-fundamentals-security-defaults.md) povolit vícefaktorové ověřování ve svém prostředí.

## <a name="office-365-business-premium-e3-or-e5"></a>Office 365 Business Premium, E3 nebo E5

Pro zákazníky s Office 365 existují dvě možnosti:

* Azure Multi-Factor Authentication je povolená nebo zakázaná pro všechny uživatele, pro všechny události přihlášení. Neexistuje možnost povolit pouze vícefaktorové ověřování pro podmnožinu uživatelů nebo pouze v určitých scénářích. Správa probíhá prostřednictvím portálu Office 365. 
* Pro lepší uživatelské prostředí upgradujte na Azure AD Premium P1 nebo P2 a použijte podmíněný přístup. Další informace najdete v tématu zabezpečení prostředků Office 365 s vícefaktorovým ověřováním.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Pro zákazníky s Azure AD Premium P1 nebo podobnými licencemi, které obsahují tyto funkce, jako jsou Enterprise Mobility + Security E3, Microsoft 365 F1 nebo Microsoft 365 E3: 

Pomocí [podmíněného přístupu Azure AD](../conditional-access/overview.md) můžete uživatele vyzvat k vícefaktorovému ověřování během určitých scénářů nebo událostí tak, aby vyhovovaly vašim obchodním požadavkům.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Pro zákazníky s Azure AD Premium P2 nebo podobnými licencemi, které obsahují tyto funkce, jako je enterprise mobility + zabezpečení E5 nebo Microsoft 365 E5: 

Poskytuje nejsilnější pozici zabezpečení a lepší uživatelské prostředí. Přidává [podmíněný přístup založený na rizicích](../conditional-access/howto-conditional-access-policy-risk.md) na funkce Azure AD Premium P1, které se přizpůsobí vzorcům uživatele a minimalizují výzvy k vícefaktorovému ověřování.

## <a name="authentication-methods"></a>Metody ověřování

|   | Výchozí nastavení zabezpečení | Všechny ostatní metody |
| --- | --- | --- |
| Oznámení prostřednictvím mobilní aplikace | × | × |
| Ověřovací kód z mobilní aplikace nebo hardwarového tokenu |   | × |
| Textová zpráva do telefonu |   | × |
| Volání na telefon |   | × |

## <a name="next-steps"></a>Další kroky

Další informace najdete v kurzu k [zabezpečení událostí přihlášení uživatelů pomocí Azure Multi-Factor Authentication](../authentication/tutorial-enable-azure-mfa.md).

Další informace o licencování najdete [v tématu Funkce a licence pro Azure Multi-Factor Authentication](../authentication/concept-mfa-licensing.md).
