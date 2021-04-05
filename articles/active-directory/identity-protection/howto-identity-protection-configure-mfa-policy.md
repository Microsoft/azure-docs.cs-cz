---
title: Konfigurace zásad registrace MFA – Azure Active Directory Identity Protection
description: Naučte se konfigurovat zásady registrace Azure AD Identity Protection Multi-Factor Authentication.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 072db1d47abd95844075aeedfeddc4f8cf6bf936
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835862"
---
# <a name="how-to-configure-the-azure-ad-multi-factor-authentication-registration-policy"></a>Postupy: Konfigurace zásad registrace Multi-Factor Authentication služby Azure AD

Azure AD Identity Protection vám pomůže spravovat registraci služby Azure AD Multi-Factor Authentication (MFA) pomocí konfigurace zásady podmíněného přístupu, která bude vyžadovat registraci MFA bez ohledu na to, k jakou aplikaci moderního ověřování se přihlašujete.

## <a name="what-is-the-azure-ad-multi-factor-authentication-registration-policy"></a>Jaké jsou zásady registrace Multi-Factor Authentication služby Azure AD?

Azure AD Multi-Factor Authentication poskytuje způsob, jak ověřit, kdo používáte víc než jenom uživatelské jméno a heslo. Poskytuje druhou vrstvu zabezpečení pro přihlášení uživatelů. Aby uživatelé mohli reagovat na výzvy MFA, musí se nejdřív zaregistrovat pro Azure AD Multi-Factor Authentication.

Doporučujeme, abyste pro přihlášení uživatelů vyžadovali Multi-Factor Authentication Azure AD, protože:

- Poskytuje silné ověřování prostřednictvím řady možností ověřování.
- Hraje klíčovou roli v rámci přípravy vaší organizace na automatickou nápravu z detekce rizik v rámci Identity Protection.

Další informace o Multi-Factor Authentication Azure AD najdete v tématu [co je Azure ad Multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Konfigurace zásad

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Přejděte do **Azure Active Directory**  >  **zabezpečení**  >  **Identity Protection**–  >  **zásady registrace ověřování MFA**.
   1. V části **přiřazení**
      1. **Uživatelé** – zvolte možnost **Všichni uživatelé** nebo **Vyberte jednotlivce a skupiny,** Pokud chcete omezit zavedení.
         1. Volitelně můžete vybrat možnost vyloučení uživatelů ze zásad.
   1. Pod **ovládacími prvky**
      1. Zkontrolujte, jestli je zaškrtnuté políčko **vyžadovat registraci Azure AD MFA** , a zvolte **Vybrat**.
   1. **Vyhovět zásadám**  -  **Zapnuto**
   1. **Uložit**

## <a name="user-experience"></a>Uživatelské prostředí

Azure Active Directory Identity Protection vyzve uživatele, aby se zaregistrovali při příštím přihlášení, a k dokončení registrace budou mít 14 dní. Během období 14 dní můžou obejít registraci, ale na konci období se budou muset zaregistrovat, aby mohli dokončit proces přihlašování.

Přehled souvisejícího uživatelského prostředí najdete v těchto tématech:

- [Prostředí pro přihlašování pomocí Azure AD Identity Protection](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Další kroky

- [Povolit přihlašování a zásady rizik uživatelů](howto-identity-protection-configure-risk-policies.md)

- [Povolit Samoobslužné resetování hesla služby Azure AD](../authentication/howto-sspr-deployment.md)

- [Povolení vícefaktorového ověřování Azure AD](../authentication/howto-mfa-getstarted.md)
