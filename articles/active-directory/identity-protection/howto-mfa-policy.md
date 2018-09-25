---
title: Jak nakonfigurovat zásady registrace pro vícefaktorové ověřování ve službě Azure Active Directory Identity Protection | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat zásady registrace pro vícefaktorové ověřování Azure AD Identity Protection.
services: active-directory
keywords: Azure active directory identity protection, zjišťování cloudových aplikací, Správa aplikací, zabezpečení, rizika, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 792a1fc2403e672c973577efd7a05c9c81d45ad4
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47054077"
---
# <a name="how-to-configure-the-multi-factor-authentication-registration-policy"></a>Postupy: Konfigurace zásady registrace pro vícefaktorové ověřování

Služba Azure AD Identity Protection umožňuje správu zavedení registracích vícefaktorového ověřování (MFA) tím, že nakonfigurujete zásady. Tento článek vysvětluje, co zásady je možné pro příslušný jeho konfiguraci.

## <a name="what-is-the-multi-factor-authentication-registration-policy"></a>Co jsou zásady registrace služby Multi-Factor authentication?

Azure Multi-Factor authentication je metodu ověřování, který se vyžaduje použití víc než jenom uživatelské jméno a heslo. Poskytuje druhou vrstvu zabezpečení, které uživatelská přihlášení a transakce.  

Doporučujeme, abyste vyžadují ověřování Azure Multi-Factor Authentication pro přihlášení uživatelů, protože ho:

- Nabízí silné ověřování s řadou jednoduchých možností

- Hrají klíčovou roli při přípravě vaší organizaci, aby ochrana a obnovení z účtu ohrožení


Další podrobnosti najdete v tématu [co je Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md)


## <a name="how-do-i-access-the-mfa-registration-policy"></a>Jak získám přístup do zásady registrace MFA?
   
Zásady registrace MFA probíhá **konfigurovat** části na [stránku služby Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Zásady vícefaktorového ověřování](./media/howto-mfa-policy/1014.png)




## <a name="policy-settings"></a>Nastavení zásad

Když konfigurujete zásady rizik přihlašování, budete muset nastavit:

- Uživatelé a skupiny, které zásady platí pro:

    ![Uživatelé a skupiny](./media/howto-mfa-policy/11.png)

- Typ přístupu, kterou chcete vynutit:  

    ![Uživatelé a skupiny](./media/howto-mfa-policy/12.png)

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
