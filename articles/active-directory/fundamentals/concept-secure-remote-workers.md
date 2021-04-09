---
title: Rychlá reakce na zabezpečené identity pomocí Azure Active Directory
description: Urychlení reakce na hrozby s využitím cloudových identit Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: davidspo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c4301a61a79ab2351c18af0c76cccc3d07dd202
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836678"
---
# <a name="rapidly-respond-to-secure-identities-with-azure-ad"></a>Rychlá reakce na zabezpečení identit pomocí Azure AD

Může se zdát, že těžké se snaží zabezpečit vaše pracovní procesy v dnešním světě, zejména v případě, že potřebujete rychle reagovat a rychle zajistit přístup k mnoha službám. Tento článek je určený k poskytnutí stručného seznamu všech akcí, které je třeba provést, a pomůže vám identifikovat a stanovit prioritu pro nasazení funkcí služby Azure AD na základě typu licence, kterou vlastníte. Azure AD nabízí spoustu funkcí a poskytuje mnoho vrstev zabezpečení pro vaše identity, což znamená, že se v některých případech může přecházet. Mnoho organizací už je v cloudu nebo se rychle přesouvá do cloudu. Tento dokument je určený k tomu, aby bylo možné rychle nasazovat služby a zabezpečit své identity jako primární aspekt. 

Každá tabulka poskytuje konzistentní doporučení zabezpečení a chrání identitu správců i uživatelů před hlavními útoky zabezpečení (přerušování a přerušování hesla) a současně minimalizuje dopad na uživatele a zlepšuje činnost koncového uživatele. 

Doprovodné materiály taky správcům umožní nakonfigurovat přístup k SaaS a místním aplikacím zabezpečeným a chráněným způsobem, který se vztahuje buď na cloudové, tak i na hybridní (synchronizované) identity a platí pro uživatele, kteří pracují vzdáleně nebo v kanceláři.

Tento kontrolní seznam vám pomůže rychle nasadit kritické doporučené akce pro zajištění ochrany vaší organizace hned pomocí vysvětlení, jak:

- Posílit své přihlašovací údaje.
- Snižte prostor pro útoky.
- Automatizujte reakci na hrozby.
- Využijte cloudové funkce Cloud Intelligence.
- Povolit samoobslužnou službu koncového uživatele.

## <a name="prerequisites"></a>Požadavky

V tomto průvodci se předpokládá, že už jste v Azure AD navázali jenom cloudové nebo hybridní identity. Nápovědu k výběru typu identity najdete v článku. [Zvolte správnou metodu ověřování pro Azure Active Directory řešení hybridní identity](../hybrid/choose-ad-authn.md) . 

## <a name="summary"></a>Souhrn

Existuje mnoho aspektů zabezpečené infrastruktury identity, ale tento kontrolní seznam se zaměřuje na bezpečnou a zabezpečenou infrastrukturu identity, která umožňuje uživatelům pracovat vzdáleně. Zabezpečení vaší identity je jenom součástí vašeho bezpečnostního scénáře, měli byste taky zvážit ochranu dat, aplikací a zařízení.

### <a name="guidance-for-azure-ad-free-office-365-or-microsoft-365-customers"></a>Pokyny pro Azure AD Free, Office 365 nebo zákazníky Microsoft 365.

Existuje několik doporučení, která Azure AD Free, sada Office 365 nebo zákazníci Microsoft 365 aplikací by měli mít možnost chránit své identity uživatelů. v následující tabulce je třeba zvýraznit klíčové akce pro následující licenční předplatné:

- Office 365 (Office 365 E1, E3, E5, F1, a1, a3, A5)
- Microsoft 365 (Business Basic, aplikace pro firmy, Business Standard, Business Premium, a1)
- Azure AD Free (součástí Azure, Dynamics 365, Intune a Power Platform)

| Doporučená akce | Podrobnosti |
| --- | --- |
| [Povolit výchozí nastavení zabezpečení](concept-fundamentals-security-defaults.md) | Chraňte všechny identity a aplikace uživatelů povolením MFA a blokováním starší verze ověřování. |
| [Povolit synchronizaci hodnot hash hesel](../hybrid/how-to-connect-password-hash-synchronization.md) (Pokud používáte hybridní identity) | Poskytněte redundanci pro ověřování a zlepšení zabezpečení (včetně inteligentního uzamčení, uzamčení protokolu IP a možnosti zjišťování nevrácených přihlašovacích údajů). |
| [Povolit inteligentní zámek ADFS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (Pokud je k dispozici) | Chrání uživatele před tím, než se zlými úmysly přestanou uzamknout extranet účtu. |
| [Povolit Azure Active Directory inteligentní uzamčení](../authentication/howto-password-smart-lockout.md) (Pokud používáte spravované identity) | Inteligentní zamykání pomáhá uzamknout chybné aktéry, které se pokoušejí uhodnout hesla uživatelů, nebo používat metody hrubou silou k získání. |
| [Zakázání souhlasu koncových uživatelů k aplikacím](../manage-apps/configure-user-consent.md) | Pracovní postup pro vyjádření souhlasu správce poskytuje správcům zabezpečený způsob, jak udělit přístup k aplikacím, které vyžadují schválení správcem, aby koncoví uživatelé nezveřejnili podniková data. Společnost Microsoft doporučuje zakázat budoucí operace souhlasu s uživatelem, aby se snížila plocha a zmírnila toto riziko. |
| [Integrace podporovaných aplikací SaaS z Galerie do Azure AD a povolení jednotného přihlašování](../manage-apps/add-application-portal.md) | Azure AD obsahuje galerii obsahující tisíce předem integrovaných aplikací. Některé aplikace, které vaše organizace používá, jsou pravděpodobně v galerii přístupné přímo z Azure Portal. Zajištění vzdáleného a zabezpečeného přístupu k podnikovým aplikacím SaaS pomocí vylepšeného uživatelského prostředí (SSO) |
| [Automatizace zřizování a rušení zřizování uživatelů v aplikacích SaaS](../app-provisioning/user-provisioning.md) (Pokud je k dispozici) | Automaticky Vytvářejte identity a role uživatelů v cloudových aplikacích (SaaS), ke kterým uživatelé potřebují přístup. Automatické zřizování zahrnuje kromě vytváření identit uživatelů taky údržbu a odebírání identit uživatelů při změně stavu nebo rolí, což zvyšuje zabezpečení vaší organizace. |
| [Povolit zabezpečený hybridní přístup: zabezpečení starších verzí aplikací pomocí stávajících řadičů pro doručování aplikací a sítí](../manage-apps/secure-hybrid-access.md) (Pokud je k dispozici) | Publikujte a Chraňte své místní a cloudové aplikace ověřování pomocí připojení ke službě Azure AD s existujícím řadičem pro doručování aplikací nebo sítí. |
| [Povolit Samoobslužné resetování hesla](../authentication/tutorial-enable-sspr.md) (týká se pouze cloudových účtů) | Tato možnost omezuje volání helpdesku a ztrátu produktivity, když se uživatel nemůže přihlásit k zařízení nebo aplikaci. |
| [Pokud je to možné, používejte jiné než globální role správy](../roles/permissions-reference.md) | Poskytněte správcům jenom přístup, který potřebují jenom pro oblasti, ke kterým potřebují přístup. Ne všichni správci musí být globální správci. |
| [Povolit pokyny pro heslo Microsoftu](https://www.microsoft.com/research/publication/password-guidance/) | Zastavení vyžadování uživatelů ke změně hesla podle nastaveného plánu, zakázání požadavků na složitost a vašim uživatelům je více apt, aby si zapamatovali hesla a zajistili, že jsou zabezpečená. |


### <a name="guidance-for-azure-ad-premium-plan-1-customers"></a>Pokyny pro zákazníky s plánem Azure AD Premium Plan 1.

Následující tabulka má zvýraznit klíčové akce pro následující licenční předplatné:

- Azure Active Directory Premium P1 (Azure AD P1)
- Enterprise Mobility + Security (EMS E3)
- Microsoft 365 (M365 E3, a3, F1, F3)

| Doporučená akce | Podrobnosti |
| --- | --- |
| [Povolit kombinované možnosti registrace pro Azure AD MFA a SSPR a zjednodušit tak možnosti registrace uživatelů](../authentication/howto-registration-mfa-sspr-combined.md) | Umožněte uživatelům, aby se zaregistrovali z jednoho společného prostředí pro Azure AD Multi-Factor Authentication a Samoobslužné resetování hesla. |
| [Konfigurace nastavení vícefaktorového ověřování pro vaši organizaci](../authentication/howto-mfa-getstarted.md) | Zajištění ochrany účtů před ohrožením službou Multi-Factor Authentication |
| [Povolení samoobslužného resetování hesel](../authentication/tutorial-enable-sspr.md) | Tato možnost omezuje volání helpdesku a ztrátu produktivity, když se uživatel nemůže přihlásit k zařízení nebo aplikaci. |
| [Implementace zpětného zápisu hesla](../authentication/tutorial-enable-sspr-writeback.md) (Pokud se používají hybridní identity) | Povolí zpětný zápis změn hesel v cloudu do místního prostředí Active Directory Windows serveru. |
| Vytvoření a povolení zásad podmíněného přístupu | [MFA pro správce k ochraně účtů, kterým jsou přiřazena oprávnění správce.](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Zablokuje starší protokoly ověřování z důvodu zvýšeného rizika spojeného se staršími protokoly ověřování.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [Vícefaktorové ověřování pro všechny uživatele a aplikace pro vytváření vyrovnaných zásad vícefaktorového ověřování pro vaše prostředí, zabezpečení uživatelů a aplikací.](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) <br><br> [Vyžadovat VÍCEFAKTOROVÉ ověřování pro správu Azure, aby chránila vaše privilegované prostředky tím, že vyžaduje vícefaktorové ověřování pro všechny uživatele přistupující k prostředkům Azure.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Povolit synchronizaci hodnot hash hesel](../hybrid/how-to-connect-password-hash-synchronization.md) (Pokud používáte hybridní identity) | Poskytněte redundanci pro ověřování a zlepšení zabezpečení (včetně inteligentního uzamčení, uzamčení protokolu IP a možnosti zjišťování nevrácených přihlašovacích údajů). |
| [Povolit inteligentní zámek ADFS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (Pokud je k dispozici) | Chrání uživatele před tím, než se zlými úmysly přestanou uzamknout extranet účtu. |
| [Povolit Azure Active Directory inteligentní uzamčení](../authentication/howto-password-smart-lockout.md) (Pokud používáte spravované identity) | Inteligentní zamykání pomáhá uzamknout chybné aktéry, které se pokoušejí uhodnout hesla uživatelů, nebo používat metody hrubou silou k získání. |
| [Zakázání souhlasu koncových uživatelů k aplikacím](../manage-apps/configure-user-consent.md) | Pracovní postup pro vyjádření souhlasu správce poskytuje správcům zabezpečený způsob, jak udělit přístup k aplikacím, které vyžadují schválení správcem, aby koncoví uživatelé nezveřejnili podniková data. Společnost Microsoft doporučuje zakázat budoucí operace souhlasu s uživatelem, aby se snížila plocha a zmírnila toto riziko. |
| [Povolení vzdáleného přístupu k místním starším aplikacím pomocí proxy aplikací](../manage-apps/application-proxy-add-on-premises-application.md) | Povolte Azure Proxy aplikací služby AD a integrujte se staršími aplikacemi, aby uživatelé mohli zabezpečeně přistupovat k místním aplikacím přihlášením pomocí svého účtu Azure AD. |
| [Povolit zabezpečený hybridní přístup: Zabezpečte starší verze aplikací pomocí stávajících řadičů pro doručování aplikací a sítí](../manage-apps/secure-hybrid-access.md) (Pokud je k dispozici). | Publikujte a Chraňte své místní a cloudové aplikace ověřování pomocí připojení ke službě Azure AD s existujícím řadičem pro doručování aplikací nebo sítí. |
| [Integrace podporovaných aplikací SaaS z Galerie do Azure AD a povolení jednotného přihlašování](../manage-apps/add-application-portal.md) | Azure AD obsahuje galerii obsahující tisíce předem integrovaných aplikací. Některé aplikace, které vaše organizace používá, jsou pravděpodobně v galerii přístupné přímo z Azure Portal. Zajištění vzdáleného a zabezpečeného přístupu k podnikovým aplikacím SaaS pomocí vylepšeného uživatelského prostředí (SSO) |
| [Automatizace zřizování a rušení zřizování uživatelů v aplikacích SaaS](../app-provisioning/user-provisioning.md) (Pokud je k dispozici) | Automaticky Vytvářejte identity a role uživatelů v cloudových aplikacích (SaaS), ke kterým uživatelé potřebují přístup. Automatické zřizování zahrnuje kromě vytváření identit uživatelů taky údržbu a odebírání identit uživatelů při změně stavu nebo rolí, což zvyšuje zabezpečení vaší organizace. |
| [Povolit podmíněný přístup – na základě zařízení](../conditional-access/require-managed-devices.md) | Vylepšete zabezpečení a uživatelské prostředí pomocí podmíněného přístupu na základě zařízení. Tento krok zajišťuje uživatelům přístup pouze ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů. Tato zařízení se také označují jako spravovaná zařízení. Spravovaná zařízení můžou být kompatibilní s Intune nebo hybridní zařízení připojená k Azure AD. |
| [Povolení ochrany heslem](../authentication/howto-password-ban-bad-on-premises-deploy.md) | Ochrana uživatelů před použitím slabých a snadno použitelných hesel |
| [Určení více než jednoho globálního správce](../roles/security-emergency-access.md) | Pokud je to možné, přiřaďte alespoň dva trvalé účty globálního správce jenom v cloudu. Tyto účty se nepoužívají denně a měly by mít dlouhá a složitá hesla. Účty pro oddělitelné sklo zajistí, že budete mít k této službě přístup v nouzi. |
| [Pokud je to možné, používejte jiné než globální role správy](../roles/permissions-reference.md) | Poskytněte správcům jenom přístup, který potřebují jenom pro oblasti, ke kterým potřebují přístup. Ne všichni správci musí být globální správci. |
| [Povolit pokyny pro heslo Microsoftu](https://www.microsoft.com/research/publication/password-guidance/) | Zastavení vyžadování uživatelů ke změně hesla podle nastaveného plánu, zakázání požadavků na složitost a vašim uživatelům je více apt, aby si zapamatovali hesla a zajistili, že jsou zabezpečená. |
| [Vytvoření plánu pro přístup uživatele typu Host](../external-identities/what-is-b2b.md) | Spolupracujte s uživateli typu Host tím, že jim umožníte přihlásit své aplikace a služby s vlastními pracovními, školními nebo sociálními identitami. |

### <a name="guidance-for-azure-ad-premium-plan-2-customers"></a>Pokyny pro zákazníky s Azure AD Premium Plan 2.

Následující tabulka má zvýraznit klíčové akce pro následující licenční předplatné:

- Azure Active Directory Premium P2 (Azure AD P2)
- Enterprise Mobility + Security (EMS E5)
- Microsoft 365 (M365 E5, A5)

| Doporučená akce | Podrobnosti |
| --- | --- |
| [Povolit kombinované možnosti registrace pro Azure AD MFA a SSPR a zjednodušit tak možnosti registrace uživatelů](../authentication/howto-registration-mfa-sspr-combined.md) | Umožněte uživatelům, aby se zaregistrovali z jednoho společného prostředí pro Azure AD Multi-Factor Authentication a Samoobslužné resetování hesla. |
| [Konfigurace nastavení vícefaktorového ověřování pro vaši organizaci](../authentication/howto-mfa-getstarted.md) | Zajištění ochrany účtů před ohrožením službou Multi-Factor Authentication |
| [Povolení samoobslužného resetování hesel](../authentication/tutorial-enable-sspr.md) | Tato možnost omezuje volání helpdesku a ztrátu produktivity, když se uživatel nemůže přihlásit k zařízení nebo aplikaci. |
| [Implementace zpětného zápisu hesla](../authentication/tutorial-enable-sspr-writeback.md) (Pokud se používají hybridní identity) | Povolí zpětný zápis změn hesel v cloudu do místního prostředí Active Directory Windows serveru. |
| [Povolením zásad ochrany identit vynutili registraci MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) | Spravujte zavedení služby Azure AD Multi-Factor Authentication (MFA). |
| [Povolení zásad pro uživatele a přihlašování k riziku Identity Protection](../identity-protection/howto-identity-protection-configure-risk-policies.md) | Povolte zásady uživatele a přihlášení identity. Doporučené zásady přihlašování jsou cílené na středně rizikové přihlášení a vyžadují MFA. Pro zásady uživatele by se mělo zaměřit na uživatele s vysokým rizikem, který vyžaduje akci změny hesla. |
| Vytvoření a povolení zásad podmíněného přístupu | [MFA pro správce k ochraně účtů, kterým jsou přiřazena oprávnění správce.](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Zablokuje starší protokoly ověřování z důvodu zvýšeného rizika spojeného se staršími protokoly ověřování.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [Vyžadovat VÍCEFAKTOROVÉ ověřování pro správu Azure, aby chránila vaše privilegované prostředky tím, že vyžaduje vícefaktorové ověřování pro všechny uživatele přistupující k prostředkům Azure.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Povolit synchronizaci hodnot hash hesel](../hybrid/how-to-connect-password-hash-synchronization.md) (Pokud používáte hybridní identity) | Poskytněte redundanci pro ověřování a zlepšení zabezpečení (včetně inteligentního uzamčení, uzamčení protokolu IP a možnosti zjišťování nevrácených přihlašovacích údajů). |
| [Povolit inteligentní zámek ADFS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (Pokud je k dispozici) | Chrání uživatele před tím, než se zlými úmysly přestanou uzamknout extranet účtu. |
| [Povolit Azure Active Directory inteligentní uzamčení](../authentication/howto-password-smart-lockout.md) (Pokud používáte spravované identity) | Inteligentní zamykání pomáhá uzamknout chybné aktéry, které se pokoušejí uhodnout hesla uživatelů, nebo používat metody hrubou silou k získání. |
| [Zakázání souhlasu koncových uživatelů k aplikacím](../manage-apps/configure-user-consent.md) | Pracovní postup pro vyjádření souhlasu správce poskytuje správcům zabezpečený způsob, jak udělit přístup k aplikacím, které vyžadují schválení správcem, aby koncoví uživatelé nezveřejnili podniková data. Společnost Microsoft doporučuje zakázat budoucí operace souhlasu s uživatelem, aby se snížila plocha a zmírnila toto riziko. |
| [Povolení vzdáleného přístupu k místním starším aplikacím pomocí proxy aplikací](../manage-apps/application-proxy-add-on-premises-application.md) | Povolte Azure Proxy aplikací služby AD a integrujte se staršími aplikacemi, aby uživatelé mohli zabezpečeně přistupovat k místním aplikacím přihlášením pomocí svého účtu Azure AD. |
| [Povolit zabezpečený hybridní přístup: Zabezpečte starší verze aplikací pomocí stávajících řadičů pro doručování aplikací a sítí](../manage-apps/secure-hybrid-access.md) (Pokud je k dispozici). | Publikujte a Chraňte své místní a cloudové aplikace ověřování pomocí připojení ke službě Azure AD s existujícím řadičem pro doručování aplikací nebo sítí. |
| [Integrace podporovaných aplikací SaaS z Galerie do Azure AD a povolení jednotného přihlašování](../manage-apps/add-application-portal.md) | Azure AD obsahuje galerii obsahující tisíce předem integrovaných aplikací. Některé aplikace, které vaše organizace používá, jsou pravděpodobně v galerii přístupné přímo z Azure Portal. Zajištění vzdáleného a zabezpečeného přístupu k podnikovým aplikacím SaaS pomocí vylepšeného uživatelského prostředí (SSO) |
| [Automatizace zřizování a rušení zřizování uživatelů v aplikacích SaaS](../app-provisioning/user-provisioning.md) (Pokud je k dispozici) | Automaticky Vytvářejte identity a role uživatelů v cloudových aplikacích (SaaS), ke kterým uživatelé potřebují přístup. Automatické zřizování zahrnuje kromě vytváření identit uživatelů taky údržbu a odebírání identit uživatelů při změně stavu nebo rolí, což zvyšuje zabezpečení vaší organizace. |
| [Povolit podmíněný přístup – na základě zařízení](../conditional-access/require-managed-devices.md) | Vylepšete zabezpečení a uživatelské prostředí pomocí podmíněného přístupu na základě zařízení. Tento krok zajišťuje uživatelům přístup pouze ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů. Tato zařízení se také označují jako spravovaná zařízení. Spravovaná zařízení můžou být kompatibilní s Intune nebo hybridní zařízení připojená k Azure AD. |
| [Povolení ochrany heslem](../authentication/howto-password-ban-bad-on-premises-deploy.md) | Ochrana uživatelů před použitím slabých a snadno použitelných hesel |
| [Určení více než jednoho globálního správce](../roles/security-emergency-access.md) | Pokud je to možné, přiřaďte alespoň dva trvalé účty globálního správce jenom v cloudu. Tyto účty se nepoužívají denně a měly by mít dlouhá a složitá hesla. Účty pro oddělitelné sklo zajistí, že budete mít k této službě přístup v nouzi. |
| [Pokud je to možné, používejte jiné než globální role správy](../roles/permissions-reference.md) | Poskytněte správcům jenom přístup, který potřebují jenom pro oblasti, ke kterým potřebují přístup. Ne všichni správci musí být globální správci. |
| [Povolit pokyny pro heslo Microsoftu](https://www.microsoft.com/research/publication/password-guidance/) | Zastavení vyžadování uživatelů ke změně hesla podle nastaveného plánu, zakázání požadavků na složitost a vašim uživatelům je více apt, aby si zapamatovali hesla a zajistili, že jsou zabezpečená. |
| [Vytvoření plánu pro přístup uživatele typu Host](../external-identities/what-is-b2b.md) | Spolupracujte s uživateli typu Host tím, že jim umožníte přihlásit své aplikace a služby s vlastními pracovními, školními nebo sociálními identitami. |
| [Povolit Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Umožňuje spravovat, řídit a monitorovat přístup k důležitým prostředkům ve vaší organizaci, takže správci mají přístup jenom v případě potřeby a se schválením. |

## <a name="next-steps"></a>Další kroky

- Podrobné pokyny k nasazení jednotlivých funkcí služby Azure AD najdete v [plánech nasazení projektu služby Azure AD](active-directory-deployment-plans.md).

- Kompletní kontrolní seznam nasazení služby Azure AD najdete v článku [Průvodce nasazením funkcí Azure Active Directory](active-directory-deployment-checklist-p2.md) .