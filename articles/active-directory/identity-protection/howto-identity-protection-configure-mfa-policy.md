---
title: Konfigurace zásad registrace vícefaktorové registrace – Ochrana identity služby Azure Active Directory
description: Zjistěte, jak nakonfigurovat zásady registrace vícefaktorového ověřování Azure AD Identity Protection.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382143"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Postup: Konfigurace zásad registrace azure vícefaktorového ověřování

Azure AD Identity Protection vám pomůže spravovat zavedení registrace Azure Multi-Factor Authentication (MFA) konfigurací zásad podmíněného přístupu tak, aby vyžadovaly registraci vícefaktorové ověřování bez ohledu na to, k jaké moderní ověřovací aplikaci se přihlašujete.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Co je zásada registrace azure vícefaktorového ověřování?

Azure Multi-Factor Authentication poskytuje prostředky k ověření, kdo používáte více než jen uživatelské jméno a heslo. Poskytuje druhou vrstvu zabezpečení pro přihlášení uživatelů. Aby uživatelé mohli reagovat na výzvy vícefaktorové ověřování, musí se nejprve zaregistrovat pro Azure Multi-Factor Authentication.

Doporučujeme, abyste pro přihlášení uživatelů k Azure vyžadovali vícefaktorové ověřování, protože:

- Poskytuje silné ověřování prostřednictvím řady možností ověření.
- Hraje klíčovou roli při přípravě vaší organizace na vlastní nápravu od detekce rizik v identity protection.

Další informace o vícefaktorovém ověřování Azure najdete v tématu [Co je azure vícefaktorové ověřování?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Konfigurace zásad

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Přejděte na**zásady registrace mfa**ochrany > **Security** >  > **identity zabezpečení služby** **Azure Active Directory**.
   1. V části **Přiřazení**
      1. **Uživatelé** – pokud omezíte zavedení, zvolte **Všichni uživatelé** **nebo Vybrat jednotlivce a skupiny.**
         1. Volitelně můžete vyloučit uživatele ze zásady.
   1. V části **Ovládací prvky**
      1. Ujistěte se, že zaškrtávací políčko **Vyžadovat registraci Azure MFA** je zaškrtnuto a zvolte **Vybrat**.
   1. **Vynutit zásady** - **na**
   1. **Uložit**

## <a name="user-experience"></a>Uživatelské prostředí

Azure Active Directory Identity Protection vyzve uživatele k registraci při příštím přihlášení interaktivně a budou mít 14 dní na dokončení registrace. Během tohoto 14denního období mohou registraci obejít, ale na konci období se budou muset před dokončením procesu přihlášení zaregistrovat.

Přehled souvisejícího uživatelského prostředí naleznete v následujících tématech:

- [Přihlášení pomocí azure ad identity ochrany](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Další kroky

- [Povolení zásad přihlášení a rizik uživatelů](howto-identity-protection-configure-risk-policies.md)

- [Povolení samoobslužného resetování hesla azure ad](../authentication/howto-sspr-deployment.md)

- [Povolení služby Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
