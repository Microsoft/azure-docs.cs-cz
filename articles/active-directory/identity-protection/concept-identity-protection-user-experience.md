---
title: Uživatelské prostředí se službou Azure AD Identity Protection
description: Uživatelské prostředí azure ad identity ochrany
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
ms.openlocfilehash: cc10fb4f9894a355c9eed024ae9f87747214999b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72886997"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Uživatelské prostředí se službou Azure AD Identity Protection

Pomocí ochrany identity služby Azure Active Directory můžete:

* Vyžadovat, aby se uživatelé registrovali pro vícefaktorové ověřování Azure (MFA)
* Automatizace nápravy rizikových přihlášení a ohrožených uživatelů

Všechny zásady ochrany identity mají vliv na přihlašovací prostředí pro uživatele. Povolení uživatelům zaregistrovat a používat nástroje, jako je Azure MFA a samoobslužné resetování hesla může zmírnit dopad. Tyto nástroje spolu s příslušnými volbami zásad poskytují uživatelům možnost samonápravy, když ji potřebují.

## <a name="multi-factor-authentication-registration"></a>Registrace vícefaktorového ověřování

Povolení zásad ochrany identity, které vyžadují registraci vícefaktorového ověřování a cílení na všechny uživatele, zajistí, že budou mít možnost používat Azure MFA k vlastní nápravě v budoucnu. Konfigurace této zásady poskytuje uživatelům 14denní období, kdy se mohou zaregistrovat a na konci jsou nuceni se zaregistrovat. Prostředí pro uživatele je uvedeno níže. Více informací naleznete v dokumentaci ke koncovým uživatelům v článku [Přehled dvoufaktorového ověření a vašemu pracovnímu nebo školnímu účtu](../user-help/user-help-two-step-verification-overview.md).

### <a name="registration-interrupt"></a>Přerušení registrace

1. Při přihlášení k jakékoli aplikaci integrované azure ad, uživatel dostane oznámení o požadavku na nastavení účtu pro vícefaktorové ověřování. Tato zásada se aktivuje také ve Windows 10 Out of Box Experience pro nové uživatele s novým zařízením.
   
    ![Další informace jsou požadovány](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. Dokončete kroky s asistencí, abyste se zaregistrovali pro azure multifaktorové ověřování a dokončete přihlášení.

## <a name="risky-sign-in-remediation"></a>Riskantní náprava přihlášení

Pokud správce nakonfiguroval zásadu pro rizika přihlášení, jsou ovlivnění uživatelé upozorněni, když se pokusí přihlásit a aktivovat úroveň rizika zásad. 

### <a name="risky-sign-in-self-remediation"></a>Riskantní samonáprava přihlášení

1. Uživatel je informován, že o jeho přihlášení bylo zjištěno něco neobvyklého, například přihlášení z nového umístění, zařízení nebo aplikace.
   
    ![Něco neobvyklého rychlého](./media/concept-identity-protection-user-experience/120.png)

1. Uživatel je povinen prokázat svou identitu dokončením Azure MFA s jedním z jejich dříve registrovaných metod. 

### <a name="risky-sign-in-administrator-unblock"></a>Rizikový přihlašovací správce odblokování

Správci se mohou rozhodnout blokovat uživatele při přihlášení v závislosti na jejich úrovni rizika. Chcete-li se odblokovat, musí koncoví uživatelé kontaktovat své pracovníky IT nebo se mohou pokusit přihlásit ze známého umístění nebo zařízení. Vlastní náprava provedením vícefaktorového ověřování není v tomto případě možností.

![Blokováno zásadami rizika přihlášení](./media/concept-identity-protection-user-experience/200.png)

Pracovníci IT mohou postupovat podle pokynů v části [Odblokování uživatelů,](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) aby se uživatelé mohli přihlásit zpět.

## <a name="risky-user-remediation"></a>Riskantní náprava uživatelů

Pokud byla nakonfigurována zásada rizika uživatele, uživatelé, kteří splňují pravděpodobnost ohrožení ohrožení ohrožení úrovně rizika uživatele, musí před přihlášením projít tokem obnovení ohrožení uživatele. 

### <a name="risky-user-self-remediation"></a>Riziková samonáprava uživatelů

1. Uživatel je informován, že zabezpečení účtu je ohroženo z důvodu podezřelé aktivity nebo uniklých přihlašovacích údajů.
   
    ![Odstranění rizika](./media/concept-identity-protection-user-experience/101.png)

1. Uživatel je povinen prokázat svou identitu dokončením Azure MFA s jedním z jejich dříve registrovaných metod. 
1. Nakonec je uživatel nucen změnit své heslo pomocí samoobslužného resetování hesla, protože někdo jiný mohl mít přístup ke svému účtu.

## <a name="risky-sign-in-administrator-unblock"></a>Rizikový přihlašovací správce odblokování

Správci se mohou rozhodnout blokovat uživatele při přihlášení v závislosti na jejich úrovni rizika. Chcete-li získat odblokování, koncoví uživatelé musí kontaktovat své IT pracovníky. Samonáprava provedením vícefaktorového ověřování a samoobslužným resetováním hesla není v tomto případě možné.

![Blokováno zásadami rizika uživatele](./media/concept-identity-protection-user-experience/104.png)

Pracovníci IT mohou postupovat podle pokynů v části [Odblokování uživatelů,](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) aby se uživatelé mohli přihlásit zpět.

## <a name="see-also"></a>Viz také

- [Náprava rizik a odblokování uživatelů](howto-identity-protection-remediate-unblock.md)

- [Ochrana identit služby Azure Active Directory](../active-directory-identityprotection.md) 
