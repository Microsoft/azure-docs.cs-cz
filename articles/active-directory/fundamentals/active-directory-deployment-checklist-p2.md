---
title: Kontrolní seznam pro nasazení Azure AD
description: Azure Active Directory kontrolní seznam nasazení funkcí
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92d1e5b8ac6492b0b1d819431e4616d32a092cc8
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836916"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Průvodce nasazením funkcí Azure Active Directory

Může se zdát, že těžké nasadit Azure Active Directory (Azure AD) pro vaši organizaci a zajistit jejich zabezpečení. Tento článek popisuje běžné úkoly, které zákazníci vyhledají ve fázích, a to v průběhu 30, 60, 90 dnů nebo víc, aby zvýšili jejich stav zabezpečení. I organizace, které už mají nasazenou službu Azure AD, můžou pomocí tohoto průvodce zajistit, aby jejich investice byly na maximum.

Dobře plánovaná a spuštěná infrastruktura identity PAVES způsob zabezpečení přístupu k vašim úlohám produktivity a datům jenom známými uživateli a zařízeními.

Zákazníci navíc můžou ověřit své [zabezpečené skóre identity](identity-secure-score.md) a zjistit, jak se budou zarovnávat podle osvědčených postupů Microsoftu. Před a po implementaci těchto doporučení Zkontrolujte své zabezpečené skóre, abyste viděli, jak dobře pracujete v porovnání s ostatními uživateli ve vašem odvětví a s jinými organizacemi vaší velikosti.

## <a name="prerequisites"></a>Předpoklady

Mnohé z doporučení v tomto průvodci můžete implementovat pomocí Azure AD Free nebo bez licence vůbec. Tam, kde jsou licence požadovány, je pro splnění této smlouvy nutné zadat, jaká licence je vyžadována minimálně.

Další informace o licencování najdete na následujících stránkách:

* [Licencování Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Ceny služby Azure AD External identity](../external-identities/external-identities-pricing.md)

## <a name="phase-1-build-a-foundation-of-security"></a>Fáze 1: sestavení základu zabezpečení

V této fázi můžou správci ve službě Azure AD před importem nebo vytvořením normálních uživatelských účtů vytvořit bezpečnější a snadno ovladatelné základní funkce zabezpečení. Tato základní fáze vám zajistí, aby se od začátku nastavilější stav a aby se koncoví uživatelé museli do nových konceptů zavádět jenom jednou.

| Úkol | Podrobnosti | Požadovaná licence |
| ---- | ------ | ---------------- |
| [Určení více než jednoho globálního správce](../roles/security-emergency-access.md) | Pokud je to možné, přiřaďte alespoň dva trvalé účty globálního správce jenom v cloudu. Tyto účty se nepoužívají denně a měly by mít dlouhá a složitá hesla. | Azure AD Free |
| [Pokud je to možné, používejte jiné než globální role správy](../roles/permissions-reference.md) | Poskytněte správcům jenom přístup, který potřebují jenom pro oblasti, ke kterým potřebují přístup. Ne všichni správci musí být globální správci. | Azure AD Free |
| [Povolit Privileged Identity Management pro sledování použití role správce](../privileged-identity-management/pim-getting-started.md) | Povolí Privileged Identity Management zahájení sledování využití role správy. | Azure AD Premium P2 |
| [Zavedení samoobslužného resetování hesla](../authentication/howto-sspr-deployment.md) | Omezení výzvy helpdesku pro resetování hesel tím, že zaměstnancům umožní resetovat vlastní hesla pomocí zásad, které jste jako ovládací prvek správce. | |
| [Vytvořit vlastní seznam zakázaných hesel pro konkrétní organizaci](../authentication/tutorial-configure-custom-password-protection.md) | Uživatelům zabránit ve vytváření hesel, která budou obsahovat běžná slova nebo fráze z vaší organizace nebo oblasti. | |
| [Povolit místní integraci s ochranou hesel Azure AD](../authentication/concept-password-ban-bad-on-premises.md) | Rozšíříte seznam zakázaných hesel na váš místní adresář, abyste měli jistotu, že jsou nastavená místní hesla taky v souladu se seznamy zakázaných hesel a globálních seznamů. | Azure AD Premium P1 |
| [Povolit pokyny pro heslo Microsoftu](https://www.microsoft.com/research/publication/password-guidance/) | Zastavení vyžadování uživatelů ke změně hesla podle nastaveného plánu, zakázání požadavků na složitost a vašim uživatelům je více apt, aby si zapamatovali hesla a zajistili, že jsou zabezpečená. | Azure AD Free |
| [Zakázat pravidelná resetování hesla pro cloudové uživatelské účty](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | Periodické resetování hesla připomáhají uživatelům zvyšovat stávající hesla. Použijte pokyny v dokumentu pokyny pro heslo od Microsoftu a zrcadlte své místní zásady na uživatele jenom pro Cloud. | Azure AD Free |
| [Přizpůsobení Azure Active Directoryho inteligentního uzamčení](../authentication/howto-password-smart-lockout.md) | Zastavení uzamčení od cloudových uživatelů při jejich replikaci do místních uživatelů Active Directory | |
| [Povolit extranetové inteligentní uzamčení pro AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | AD FS uzamčení extranetu chrání před útoky hrubou silou hesla, zatímco umožňuje platným AD FSm uživatelům nadále používat své účty. | |
| [Blokování staršího ověřování do Azure AD s podmíněným přístupem](../conditional-access/block-legacy-authentication.md) | Zablokuje starší protokoly ověřování jako POP, SMTP, IMAP a MAPI, které nemůžou vyhovět Multi-Factor Authentication, takže jim vyhovuje vstupnímu bodu pro nežádoucí osoby. | Azure AD Premium P1 |
| [Nasazení Multi-Factor Authentication Azure AD pomocí zásad podmíněného přístupu](../authentication/howto-mfa-getstarted.md) | Vyžaduje, aby uživatelé prováděli dvoustupňové ověřování při přístupu k citlivým aplikacím pomocí zásad podmíněného přístupu. | Azure AD Premium P1 |
| [Povolit Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md) | Povolte sledování rizikových přihlášení a napadených přihlašovacích údajů uživatelům ve vaší organizaci. | Azure AD Premium P2 |
| [Použití zjišťování rizik ke spuštění vícefaktorového ověřování a změn hesel](../authentication/tutorial-risk-based-sspr-mfa.md) | Povolte automatizaci, která může aktivovat události, jako je vícefaktorové ověřování, resetování hesla a blokování přihlášení na základě rizika. | Azure AD Premium P2 |
| [Povolení kombinované registrace pro Samoobslužné resetování hesla a službu Azure AD Multi-Factor Authentication](../authentication/concept-registration-mfa-sspr-combined.md) | Umožněte uživatelům, aby se zaregistrovali z jednoho společného prostředí pro Azure AD Multi-Factor Authentication a Samoobslužné resetování hesla. | Azure AD Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>Fáze 2: import uživatelů, povolení synchronizace a Správa zařízení

V dalším kroku přidáme do základu, která je ve fázi 1, importem uživatelů a povolením synchronizace, plánováním přístupu hostů a přípravou k podpoře dalších funkcí.

| Úkol | Podrobnosti | Požadovaná licence |
| ---- | ------ | ---------------- |
| [Instalace služby Azure AD Connect](../hybrid/how-to-connect-install-select-installation.md) | Připravte se na synchronizaci uživatelů ze stávajícího místního adresáře do cloudu. | Azure AD Free |
| [Implementace synchronizace hodnot hash hesel](../hybrid/how-to-connect-password-hash-synchronization.md) | Synchronizuje hodnoty hash hesel, aby bylo možné replikovat změny hesel, chybnou detekci a nápravu hesla a nevrácené zprávy o přihlašovacích údajích. | Azure AD Free |
| [Implementace zpětného zápisu hesla](../authentication/tutorial-enable-sspr-writeback.md) | Povolí zpětný zápis změn hesel v cloudu do místního prostředí Active Directory Windows serveru. | Azure AD Premium P1 |
| [Implementovat Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) | Povolte monitorování statistik stavu klíčů pro Azure AD Connect servery, servery AD FS a řadiče domény. | Azure AD Premium P1 |
| [Přiřazení licencí uživatelům podle členství ve skupině v Azure Active Directory](../enterprise-users/licensing-groups-assign.md) | Ušetřete čas a úsilí vytvořením skupin licencí, které umožňují povolit nebo zakázat funkce podle skupin namísto nastavení na uživatele. | |
| [Vytvoření plánu pro přístup uživatele typu Host](../external-identities/what-is-b2b.md) | Spolupracujte s uživateli typu Host tím, že jim umožníte přihlašovat se k vašim aplikacím a službám s vlastními pracovními, školními nebo sociálními identitami. | [Ceny služby Azure AD External identity](../external-identities/external-identities-pricing.md) |
| [Rozhodnutí o strategii správy zařízení](../devices/overview.md) | Rozhodněte se, co vaše organizace povoluje pro zařízení. Při registraci vs se spojí vaše vlastní zařízení a společnost. | |
| [Nasazení Windows Hello pro firmy ve vaší organizaci](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Příprava ověřování pomocí hesla ve Windows Hello | |
| [Nasazení metod ověřování neheslem pro vaše uživatele](../authentication/concept-authentication-passwordless.md) | Poskytněte uživatelům praktické metody ověřování bez hesla | Azure AD Premium P1 |

## <a name="phase-3-manage-applications"></a>Fáze 3: Správa aplikací

Jak pokračujeme v sestavách v předchozích fázích, identifikujeme kandidátské aplikace pro migraci a integraci s Azure AD a dokončíte nastavení těchto aplikací.

| Úkol | Podrobnosti | Požadovaná licence |
| ---- | ------ | ---------------- |
| Identifikujte své aplikace | Identifikujte aplikace používané ve vaší organizaci: místní, SaaS aplikace v cloudu a další obchodní aplikace. Určete, jestli tyto aplikace můžou a by měly být spravované pomocí Azure AD. | Není vyžadována žádná licence. |
| [Integrace podporovaných aplikací SaaS v galerii](../manage-apps/add-application-portal.md) | Azure AD obsahuje galerii obsahující tisíce předem integrovaných aplikací. Některé aplikace, které vaše organizace používá, jsou pravděpodobně v galerii přístupné přímo z Azure Portal. | Azure AD Free |
| [Použití proxy aplikací k integraci místních aplikací](../manage-apps/application-proxy-add-on-premises-application.md) | Proxy aplikací umožňuje uživatelům přístup k místním aplikacím přihlášením pomocí svého účtu služby Azure AD. | |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>Fáze 4: Auditovat privilegované identity, dokončit kontrolu přístupu a spravovat životní cyklus uživatele

Fáze 4 se dohlíží k správcům, kteří vynucují zásady minimálního oprávnění pro správu, dokončují první kontroly přístupu a povolují automatizaci běžných úkolů životního cyklu uživatele.

| Úkol | Podrobnosti | Požadovaná licence |
| ---- | ------ | ---------------- |
| [Vynutilo použití Privileged Identity Management](../privileged-identity-management/pim-security-wizard.md) | Odeberte role pro správu z normálního denního uživatelského účtu. Uživatelé s právy pro správu mají nárok na používání své role po úspěšném ověření služby Multi-Factor Authentication, poskytování obchodního odůvodnění nebo žádosti o schválení od určených schvalovatelů. | Azure AD Premium P2 |
| [Dokončení kontroly přístupu pro role adresáře Azure AD v PIM](../privileged-identity-management/pim-how-to-start-security-review.md) | Spolupracujte se svými týmy zabezpečení a vedoucími k vytvoření zásady kontroly přístupu ke kontrole přístupu pro správu na základě zásad vaší organizace. | Azure AD Premium P2 |
| [Implementace zásad členství v dynamické skupině](../enterprise-users/groups-dynamic-membership.md) | Pomocí dynamických skupin můžete automaticky přiřazovat uživatele do skupin na základě jejich atributů z HR (nebo ze zdroje pravdy), jako je oddělení, název, oblast a další atributy. |  |
| [Implementace zřizování aplikace založené na skupinách](../manage-apps/what-is-access-management.md) | Pro Automatické zřizování uživatelů pro aplikace SaaS použijte zřizování skupinového přístupu na základě skupin. |  |
| [Automatizace zřizování a rušení zřizování uživatelů](../app-provisioning/user-provisioning.md) | Odebrání ručních kroků z životního cyklu účtu zaměstnance, aby nedocházelo k neoprávněnému přístupu Synchronizujte identity ze zdroje pravdy (systému HR) do Azure AD. |  |

## <a name="next-steps"></a>Další kroky

[Podrobnosti o licencování a cenách Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)

[Konfigurace identit a přístupu k zařízením](/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Běžné Doporučené zásady pro identitu a přístup k zařízením](/microsoft-365/enterprise/identity-access-policies)