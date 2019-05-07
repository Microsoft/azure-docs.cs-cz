---
title: Jak nakonfigurovat zásady registrace pro vícefaktorové ověřování ve službě Azure Active Directory Identity Protection | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat zásady registrace pro vícefaktorové ověřování Azure AD Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f4083ddf849842358f7699badca6598e56e4dee
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65139367"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Jak: Konfigurace zásady registrace pro Azure Multi-Factor Authentication

Azure AD Identity Protection vám pomůže se správou zavádění registracích vícefaktorového ověřování (MFA) tím, že nakonfigurujete zásadu podmíněného přístupu, která vyžaduje registrace MFA bez ohledu na to, jaké aplikace se přihlašujete. Tento článek vysvětluje, co zásady je možné pro a jeho konfiguraci.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Co je Azure Multi-Factor Authentication zásady registrace?

Azure Multi-Factor Authentication poskytuje způsob ověření, který používáte více než jenom uživatelské jméno a heslo. Poskytuje druhou vrstvu zabezpečení, které uživatel přihlásil. Aby uživatelé mohli reagovat na výzvy MFA musí nejprve registrovat pro ověřování Azure Multi-Factor Authentication.

Doporučujeme, abyste vyžadují ověřování Azure Multi-Factor Authentication pro přihlášení uživatelů, protože ho:

- Nabízí silné ověřování s řadou jednoduchých možností
- Hrají klíčovou roli při přípravě vaší organizaci, aby ochrana a obnovení z rizikových událostí ve službě Identity Protection

Podrobné informace o MFA naleznete v tématu [co je Azure Multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="how-do-i-access-the-registration-policy"></a>Jak získám přístup do zásady registrace?

Zásady registrace MFA probíhá **konfigurovat** části na [stránku služby Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).

![Zásady vícefaktorového ověřování](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>Nastavení zásad

Když konfigurujete zásady registrace MFA, budete muset provést následující změny konfigurace:

- Uživatelé a skupiny, které se zásady vztahují. Mějte na paměti k vyloučení vaší organizace [účtů pro nouzový přístup](../users-groups-roles/directory-emergency-access.md).

    ![Uživatelé a skupiny](./media/howto-mfa-policy/11.png)

- Ovládací prvek chcete vynutit - **registrace vyžadují Azure MFA**

    ![Access](./media/howto-mfa-policy/12.png)

- Vynucení zásad musí být nastavená na **na**.

    ![Vynucení zásad](./media/howto-mfa-policy/14.png)

- **Uložit** zásady

## <a name="user-experience"></a>Činnost koncového uživatele

Přehled související uživatelské prostředí naleznete v tématu:

- [Tok ověřování službou Multi-Factor Authentication registrace](flows.md#multi-factor-authentication-registration).  
- [Přihlašovací prostředí v Azure AD Identity Protection](flows.md).  

## <a name="next-steps"></a>Další postup

Získejte přehled o Azure AD Identity Protection, najdete v článku [Přehled služby Azure AD Identity Protection](overview.md).
