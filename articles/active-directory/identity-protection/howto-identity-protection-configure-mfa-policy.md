---
title: Konfigurace zásad registrace MFA – Azure Active Directory Identity Protection
description: Naučte se konfigurovat zásady registrace Azure AD Identity Protection Multi-Factor Authentication.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fa6a4cf184b426355f62117ea51642127eee529
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74382143"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Postupy: Konfigurace zásad registrace Azure Multi-Factor Authentication

Azure AD Identity Protection vám pomůže spravovat zavedení registrace Azure Multi-Factor Authentication (MFA) konfigurací zásady podmíněného přístupu, která vyžaduje registraci MFA, bez ohledu na to, ke které aplikaci moderního ověřování se přihlašujete.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Co je zásada registrace v Azure Multi-Factor Authentication?

Azure Multi-Factor Authentication poskytuje způsob, jak ověřit, kdo používáte víc než jenom uživatelské jméno a heslo. Poskytuje druhou vrstvu zabezpečení pro přihlášení uživatelů. Aby uživatelé mohli reagovat na výzvy MFA, musí se nejdřív zaregistrovat pro Azure Multi-Factor Authentication.

Doporučujeme, abyste pro přihlášení uživatelů vyžadovali Multi-Factor Authentication Azure, protože:

- Poskytuje silné ověřování prostřednictvím řady možností ověřování.
- Hraje klíčovou roli v rámci přípravy vaší organizace na automatickou nápravu z detekce rizik v rámci Identity Protection.

Další informace o Azure Multi-Factor Authentication najdete v tématu [co je azure Multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Konfigurace zásad

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Přejděte do **Azure Active Directory** > **zabezpečení** > **Identity Protection** > –**zásady registrace ověřování MFA**.
   1. V části **přiřazení**
      1. **Uživatelé** – zvolte možnost **Všichni uživatelé** nebo **Vyberte jednotlivce a skupiny,** Pokud chcete omezit zavedení.
         1. Volitelně můžete vybrat možnost vyloučení uživatelů ze zásad.
   1. Pod **ovládacími prvky**
      1. Zajistěte, aby bylo zaškrtnuté políčko **vyžadovat registraci Azure MFA** , a zvolte **Vybrat**.
   1. **Vyhovět zásadám** - **On**
   1. **Uložit**

## <a name="user-experience"></a>Uživatelské prostředí

Azure Active Directory Identity Protection vyzve uživatele, aby se zaregistrovali při příštím přihlášení, a k dokončení registrace budou mít 14 dní. Během období 14 dní můžou obejít registraci, ale na konci období se budou muset zaregistrovat, aby mohli dokončit proces přihlašování.

Přehled souvisejícího uživatelského prostředí najdete v těchto tématech:

- [Prostředí pro přihlašování pomocí Azure AD Identity Protection](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Další kroky

- [Povolit přihlašování a zásady rizik uživatelů](howto-identity-protection-configure-risk-policies.md)

- [Povolit Samoobslužné resetování hesla služby Azure AD](../authentication/howto-sspr-deployment.md)

- [Povolení služby Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
