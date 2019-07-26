---
title: Jak nakonfigurovat zásady registrace Multi-Factor Authentication v Azure Active Directory Identity Protection | Microsoft Docs
description: Naučte se konfigurovat zásady registrace Azure AD Identity Protection Multi-Factor Authentication.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27ff7512bb3f9422ed4c8edd7ab50fce23f0ed07
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499563"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Jak: Konfigurace zásad registrace Azure Multi-Factor Authentication

Azure AD Identity Protection vám pomůže spravovat zavedení registrace vícefaktorového ověřování (MFA) konfigurací zásady podmíněného přístupu, která vyžaduje registraci MFA, bez ohledu na to, ke které aplikaci moderního ověřování se přihlašujete. V tomto článku se dozvíte, co je možné použít pro a jak ji nakonfigurovat.



## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Co je zásada registrace Azure Multi-Factor Authentication?

Azure Multi-Factor Authentication poskytuje způsob, jak ověřit, kdo používáte více než jenom uživatelské jméno a heslo. Poskytuje druhou vrstvu zabezpečení pro přihlášení uživatelů. Aby uživatelé mohli reagovat na výzvy MFA, musí se nejdřív zaregistrovat pro Azure Multi-Factor Authentication.

Doporučujeme, abyste pro přihlášení uživatelů vyžadovali službu Azure Multi-Factor Authentication, protože:

- Poskytuje silné ověřování s využitím široké škály možností jednoduchého ověřování.
- Hraje klíčovou roli při přípravě vaší organizace, aby chránila a obnovila rizikové události v rámci Identity Protection.

Další podrobnosti o MFA najdete v tématu [co je Azure Multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="how-do-i-access-the-registration-policy"></a>Návody přístup k zásadám registrace?

Zásady registrace MFA jsou v části **Konfigurace** na [stránce Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).

![Zásada MFA](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>Nastavení zásad

Když konfigurujete zásady registrace MFA, musíte provést následující změny konfigurace:

- Uživatelé a skupiny, na které se zásady vztahují. Nezapomeňte vyloučit [účty pro nouzový přístup](../users-groups-roles/directory-emergency-access.md)vaší organizace.

    ![Uživatelé a skupiny](./media/howto-mfa-policy/11.png)

- Ovládací prvek, který chcete vynutit – **vyžadovat registraci Azure MFA**

    ![Access](./media/howto-mfa-policy/12.png)

- Zásada vyhovět zásadám musí být nastavená na zapnuto.

    ![Vyhovět zásadám](./media/howto-mfa-policy/14.png)

- **Uložit** zásadu

## <a name="user-experience"></a>Činnost koncového uživatele

Azure Active Directory Identity Protection vyzve uživatele, aby se zaregistrovali příště, když se přihlásí interaktivně.

Přehled souvisejícího uživatelského prostředí najdete v těchto tématech:

- [Tok registrace Multi-Factor Authentication](flows.md#multi-factor-authentication-registration).  
- [Prostředí pro přihlašování pomocí Azure AD Identity Protection](flows.md).  

## <a name="next-steps"></a>Další postup

Přehled Azure AD Identity Protection najdete v tématu [Azure AD Identity Protection Overview](overview.md).
