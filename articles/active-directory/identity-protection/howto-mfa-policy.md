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
ms.openlocfilehash: e626260dba3155ef56ee4a784aab2c6fd6897295
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45581360"
---
# <a name="how-to-configure-the-multi-factor-authentication-registration-policy"></a>Postupy: Konfigurace zásady registrace pro vícefaktorové ověřování

Služba Azure AD Identity Protection umožňuje správu zavedení registracích vícefaktorového ověřování (MFA) tím, že nakonfigurujete zásady. Tento článek vysvětluje, co zásady je možné pro příslušný jeho konfiguraci.

## <a name="what-is-the-multi-factor-authentication-registration-policy"></a>Co jsou zásady registrace služby Multi-Factor authentication?

Azure Multi-Factor authentication je metodu ověřování, který se vyžaduje použití víc než jenom uživatelské jméno a heslo. Poskytuje druhou vrstvu zabezpečení, které uživatelská přihlášení a transakce.  
Doporučujeme, abyste vyžadují ověřování Azure Multi-Factor Authentication pro přihlášení uživatelů, protože ho:

* Nabízí silné ověřování s řadou jednoduchých možností
* Hrají klíčovou roli při přípravě vaší organizaci, aby ochrana a obnovení z účtu ohrožení

![Zásady rizik uživatelů](./media/howto-mfa-policy/1019.png "zásady rizik uživatelů")

Další podrobnosti najdete v tématu [co je Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md)

## <a name="configuration"></a>Konfigurace

**Chcete-li otevřít dialogové okno Konfigurace související**:

- Na **Azure AD Identity Protection** okno v **konfigurovat** klikněte na tlačítko **registracích vícefaktorového ověřování**.

    ![Zásady vícefaktorového ověřování](./media/howto-mfa-policy/1019.png "zásad vícefaktorového ověřování")

### <a name="settings"></a>Nastavení

* Nastavte uživatele a skupiny, které zásady platí pro:

    ![Zásady vícefaktorového ověřování](./media/howto-mfa-policy/1020.png "zásad vícefaktorového ověřování")
* Můžete nastavit řízení vynucení při aktivaci zásady::  

    ![Zásady vícefaktorového ověřování](./media/howto-mfa-policy/1021.png "zásad vícefaktorového ověřování")
* Přepnutí stavu zásad:

    ![Zásady vícefaktorového ověřování](./media/howto-mfa-policy/403.png "zásad vícefaktorového ověřování")
* Zobrazte aktuální stav registrace:

    ![Zásady vícefaktorového ověřování](./media/howto-mfa-policy/1022.png "zásad vícefaktorového ověřování")

Přehled související uživatelské prostředí naleznete v tématu:

* [Tok ověřování službou Multi-Factor Authentication registrace](flows.md#multi-factor-authentication-registration).  
* [Přihlašovací prostředí v Azure AD Identity Protection](flows.md).  



## <a name="next-steps"></a>Další postup

Získejte přehled o Azure AD Identity Protection, najdete v článku [Přehled služby Azure AD Identity Protection](overview.md).
