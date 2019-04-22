---
title: Jak nakonfigurovat zásady registrace pro vícefaktorové ověřování ve službě Azure Active Directory Identity Protection | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat zásady registrace pro vícefaktorové ověřování Azure AD Identity Protection.
services: active-directory
keywords: Azure active directory identity protection, zjišťování cloudových aplikací, Správa aplikací, zabezpečení, rizika, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: b563b0fb8dc12cbdcdd256a97134f9ff02988915
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698958"
---
# <a name="how-to-configure-the-multi-factor-authentication-registration-policy"></a>Jak: Konfigurace zásady registrace pro vícefaktorové ověřování

Služba Azure AD Identity Protection umožňuje správu zavedení registracích vícefaktorového ověřování (MFA) tím, že nakonfigurujete zásadu, která vyžaduje registrace MFA. Tento článek vysvětluje, co zásady je možné pro příslušný jeho konfiguraci.

## <a name="what-is-the-multi-factor-authentication-registration-policy"></a>Co jsou zásady registrace služby Multi-Factor authentication?

Azure Multi-Factor authentication je metodu ověřování, který se vyžaduje použití víc než jenom uživatelské jméno a heslo. Poskytuje druhou vrstvu zabezpečení, které uživatelská přihlášení a transakce. Aby uživatelé mohli reagovat na výzvy MFA musí nejprve registrovat pro MFA. 

Doporučujeme, abyste vyžadují ověřování Azure Multi-Factor Authentication pro přihlášení uživatelů, protože ho:

- Nabízí silné ověřování s řadou jednoduchých možností

- Hrají klíčovou roli při přípravě vaší organizaci, aby ochrana a obnovení z účtu ohrožení


Podrobné informace o MFA naleznete v tématu [co je Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md)


## <a name="how-do-i-access-the-mfa-registration-policy"></a>Jak získám přístup do zásady registrace MFA?
   
Zásady registrace MFA probíhá **konfigurovat** části na [stránku služby Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Zásady vícefaktorového ověřování](./media/howto-mfa-policy/1014.png)




## <a name="policy-settings"></a>Nastavení zásad

Když konfigurujete zásady registrace MFA, budete muset nastavit:

- Uživatelé a skupiny, které zásady platí pro:

    ![Uživatelé a skupiny](./media/howto-mfa-policy/11.png)

- Ovládací prvek, který chcete vynutit požadavek registrace vícefaktorového ověřování:  

    ![Access](./media/howto-mfa-policy/12.png)

- Stav zásad:

    ![Vynucení zásad](./media/howto-mfa-policy/14.png)


Dialogové okno Konfigurace zásad vám poskytne možnost odhad dopadu vaší konfigurace.

![Odhadovaný dopad](./media/howto-mfa-policy/15.png)




## <a name="user-experience"></a>Činnost koncového uživatele


Přehled související uživatelské prostředí naleznete v tématu:

* [Tok ověřování službou Multi-Factor Authentication registrace](flows.md#multi-factor-authentication-registration).  
* [Přihlašovací prostředí v Azure AD Identity Protection](flows.md).  



## <a name="next-steps"></a>Další postup

Získejte přehled o Azure AD Identity Protection, najdete v článku [Přehled služby Azure AD Identity Protection](overview.md).
