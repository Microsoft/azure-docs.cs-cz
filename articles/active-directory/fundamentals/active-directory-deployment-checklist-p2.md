---
title: Kontrolní seznam pro nasazení Azure AD
description: Kontrolní seznam nasazení funkcí služby Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: ''
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: f84226a631014b51338d47887fe3bafc969dc571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063641"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Průvodce nasazením funkcí služby Azure Active Directory

Může se zdát skličující nasadit Azure Active Directory (Azure AD) pro vaši organizaci a udržet ji v bezpečí. Tento článek identifikuje běžné úkoly, které zákazníci považují za užitečné pro dokončení ve fázích, v průběhu 30, 60, 90 dnů nebo více, aby se zlepšilo jejich stav zabezpečení. Dokonce i organizace, které už nasadily Azure AD můžete použít tuto příručku k zajištění, že jsou co nejvíce z jejich investice.

Dobře naplánovaná a provedená infrastruktura identit dláždí cestu k bezpečnému přístupu k vašim pracovním zátěžím a datům pouze známými uživateli a zařízeními.

Zákazníci mohou navíc zkontrolovat [své skóre zabezpečení identity](identity-secure-score.md) a zjistit, jak jsou zarovnáni s doporučenými postupy společnosti Microsoft. Zkontrolujte své bezpečné skóre před a po implementaci těchto doporučení, abyste zjistili, jak dobře si vedete ve srovnání s ostatními ve vašem oboru a s jinými organizacemi vaší velikosti.

## <a name="prerequisites"></a>Požadavky

Mnoho doporučení v této příručce lze implementovat s Azure AD free nebo bez licence vůbec. Tam, kde jsou vyžadovány licence, uvádíme, která licence je vyžadována minimálně k provedení úkolu.

Další informace o licencích naleznete na následujících stránkách:

* [Licencování Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Pokyny k licencování Azure AD B2B](../b2b/licensing-guidance.md)

## <a name="phase-1-build-a-foundation-of-security"></a>Fáze 1: Vybudovat základ bezpečnosti

V této fázi správci umožňují funkce zabezpečení směrného plánu vytvořit bezpečnější a snadno použitelný základ ve službě Azure AD před importem nebo vytvořením běžných uživatelských účtů. Tato základní fáze zajišťuje, že jste od začátku v bezpečnějším stavu a že koncoví uživatelé musí být jednou představeni novým konceptům.

| Úkol | Podrobnosti | Požadovaná licence |
| ---- | ------ | ---------------- |
| [Určení více než jednoho globálního správce](../users-groups-roles/directory-emergency-access.md) | Přiřaďte alespoň dva účty trvalého globálního správce pouze pro cloud, které se používají v případě nouze. Tyto účty se nepoužívají denně a měly by mít dlouhá a složitá hesla. | Azure AD Free |
| [Pokud je to možné, používejte neglobální role správy](../users-groups-roles/directory-assign-admin-roles.md) | Ušetujte správcům pouze přístup, který potřebují, pouze k oblastem, ke kterým potřebují přístup. Ne všichni správci musí být globální správci. | Azure AD Free |
| [Povolení správy privilegovaných identit pro sledování použití role správce](../privileged-identity-management/pim-getting-started.md) | Povolte správu privilegovaných identit a začněte sledovat využití rolí správy. | Azure AD Premium P2 |
| [Zavedení samoobslužného resetování hesla](../authentication/howto-sspr-deployment.md) | Snižte volání technické podpory pro resetování hesla tím, že zaměstnancům umožníte resetovat vlastní hesla pomocí zásad, které jste jako ovládací prvek správce. | |
| [Vytvoření seznamu vlastních zakázaných hesel specifických pro organizaci](../authentication/howto-password-ban-bad-configure.md) | Zabraňte uživatelům ve vytváření hesel, která obsahují běžná slova nebo fráze z vaší organizace nebo oblasti. | |
| [Povolení místní integrace pomocí ochrany heslem Azure AD](../authentication/concept-password-ban-bad-on-premises.md) | Rozšiřte seznam zakázaných hesel do místního adresáře, abyste zajistili, že hesla nastavená místně jsou také v souladu s globálními seznamy zakázaných hesel specifickými pro klienta. | Azure AD Premium P1 |
| [Povolení pokynů společnosti Microsoft pro hesla](https://www.microsoft.com/research/publication/password-guidance/) | Přestaňte vyžadovat, aby uživatelé měnili své heslo podle nastaveného plánu, zakažte požadavky na složitost a uživatelé mají větší možnost zapamatovat si svá hesla a uchovávat je v bezpečí. | Azure AD Free |
| [Zakázání pravidelných resetování hesla pro cloudové uživatelské účty](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | Pravidelná resetování hesla povzbuzují uživatele, aby nastoňovali svá stávající hesla. Použijte pokyny v dokumentu microsoft u směrů pro hesla a zrcadlíte místní zásady pro uživatele pouze pro cloud. | Azure AD Free |
| [Přizpůsobení inteligentního uzamčení služby Azure Active Directory](../authentication/howto-password-smart-lockout.md) | Zastavení replikace uzamčení uživatelů na shlukech do místních uživatelů služby Active Directory | |
| [Povolení inteligentního uzamčení extranetu pro ad FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | AD FS extranet uzamčení chrání před hrubou silou heslo hádání útoků, zatímco umožňuje platné ad FS uživatelé i nadále používat své účty. | |
| [Nasazení vícefaktorového ověřování Azure AD pomocí zásad podmíněného přístupu](../authentication/howto-mfa-getstarted.md) | Vyžadovat, aby uživatelé prováděli dvoustupňové ověření při přístupu k citlivým aplikacím pomocí zásad podmíněného přístupu. | Azure AD Premium P1 |
| [Povolení ochrany identity služby Azure Active Directory](../identity-protection/overview-identity-protection.md) | Povolte sledování rizikových přihlášení a ohrožení přihlašovacích údajů pro uživatele ve vaší organizaci. | Azure AD Premium P2 |
| [Použití detekce rizik ke spuštění vícefaktorového ověřování a změn hesel](../authentication/tutorial-risk-based-sspr-mfa.md) | Povolte automatizaci, která může aktivovat události, jako je vícefaktorové ověřování, resetování hesla a blokování přihlášení na základě rizika. | Azure AD Premium P2 |
| [Povolení konvergované registrace pro samoobslužné resetování hesla a vícefaktorové ověřování Azure AD (preview)](../authentication/concept-registration-mfa-sspr-converged.md) | Umožněte uživatelům zaregistrovat se z jednoho společného prostředí pro azure multifaktorové ověřování i samoobslužné resetování hesla. | Azure AD Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>Fáze 2: Import uživatelů, povolení synchronizace a správa zařízení

Dále přidáme k základům stanoveným ve fázi 1 importem našich uživatelů a povolením synchronizace, plánováním přístupu hosta a přípravou na podporu dalších funkcí.

| Úkol | Podrobnosti | Požadovaná licence |
| ---- | ------ | ---------------- |
| [Instalace služby Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md) | Připravte se na synchronizaci uživatelů z existujícího místního adresáře do cloudu. | Azure AD Free |
| [Implementace synchronizace hash hesla](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md) | Synchronizace hash hesel umožňuje replikovat změny hesel, chybné zjišťování a nápravu hesel a neuniklé sestavy pověření. | Azure AD Free |
| [Implementace zpětného zápisu hesla](../authentication/howto-sspr-writeback.md) | Povolit zápis změn hesel v cloudu zpět do místního prostředí služby Windows Server Active Directory. | Azure AD Premium P1 |
| [Implementace stavu Azure AD Connect](../connect-health/active-directory-aadconnect-health.md) | Povolte monitorování klíčových statistik stavu pro servery Azure AD Connect, servery služby AD FS a řadiče domény. | Azure AD Premium P1 |
| [Přiřazení licencí uživatelům podle členství ve skupině Azure Active Directory](../users-groups-roles/licensing-groups-assign.md) | Ušetřete čas a úsilí vytvořením skupin licencí, které povolují nebo zakazují funkce podle skupin namísto nastavení pro jednotlivé uživatele. | |
| [Vytvoření plánu pro přístup uživatele typu Host](../b2b/what-is-b2b.md) | Spolupracujte s hostujícími uživateli tím, že jim umožníte přihlásit se k vašim aplikacím a službám pomocí vlastní pracovní, školní nebo sociální identity. | [Pokyny k licencování Azure AD B2B](../b2b/licensing-guidance.md) |
| [Rozhodněte se o strategii správy zařízení](../devices/overview.md) | Rozhodněte se, co vaše organizace umožňuje, pokud jde o zařízení. Registrace vs spojení, Přineste si vlastní zařízení vs společnost za předpokladu. | |
| [Nasazení Windows Hello pro firmy ve vaší organizaci](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Příprava na ověření bez hesla pomocí Windows Hello | |
| [Nasazení metod ověřování bez hesla pro uživatele](../authentication/concept-authentication-passwordless.md) | Poskytněte uživatelům pohodlné metody ověřování bez hesla | Azure AD Premium P1 |

## <a name="phase-3-manage-applications"></a>Fáze 3: Správa aplikací

Jak budeme pokračovat v navazování na předchozí fáze, identifikujeme kandidátské aplikace pro migraci a integraci s Azure AD a dokončíme nastavení těchto aplikací.

| Úkol | Podrobnosti | Požadovaná licence |
| ---- | ------ | ---------------- |
| Identifikujte své aplikace | Identifikujte aplikace, které se používají ve vaší organizaci: místní, aplikace SaaS v cloudu a další obchodní aplikace. Zjistěte, jestli tyto aplikace mohou a měly by být spravovány pomocí Azure AD. | Není vyžadována žádná licence |
| [Integrace podporovaných aplikací SaaS v galerii](../manage-apps/add-application-portal.md) | Azure AD má galerii, která obsahuje tisíce předem integrovaných aplikací. Některé aplikace, které vaše organizace používá, jsou pravděpodobně v galerii přístupné přímo z webu Azure Portal. | Azure AD Free |
| [Integrace místních aplikací pomocí proxy aplikace](../manage-apps/application-proxy-add-on-premises-application.md) | Proxy aplikace umožňuje uživatelům přístup k místním aplikacím po přihlášení pomocí svého účtu Azure AD. | |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>Fáze 4: Auditování privilegovaných identit, dokončení kontroly přístupu a správa životního cyklu uživatele

Fáze 4 vidí správci vynucovat zásady nejnižší oprávnění pro správu, dokončení jejich první kontroly přístupu a povolení automatizace běžných úloh životního cyklu uživatele.

| Úkol | Podrobnosti | Požadovaná licence |
| ---- | ------ | ---------------- |
| [Vynucení používání správy privilegovaných identit](../privileged-identity-management/pim-security-wizard.md) | Odeberte role správy z běžných každodenních uživatelských účtů. Povolit uživatelům s právoem administrativních uživatelů používat svou roli po úspěšné kontrole vícefaktorového ověřování, poskytnutí obchodního odůvodnění nebo vyžádání schválení od určených schvalovatelů. | Azure AD Premium P2 |
| [Dokončení kontroly přístupu pro role adresáře Azure AD v PIM](../privileged-identity-management/pim-how-to-start-security-review.md) | Spolupracujte se svými týmy zabezpečení a vedení a vytvořte zásady kontroly přístupu ke kontrole přístupu pro správu na základě zásad vaší organizace. | Azure AD Premium P2 |
| [Implementace zásad dynamického členství ve skupinách](../users-groups-roles/groups-dynamic-membership.md) | Dynamické skupiny slouží k automatickému přiřazení uživatelů ke skupinám na základě jejich atributů z hr (nebo zdroje pravdy), jako je oddělení, název, oblast a další atributy. |  |
| [Implementace zřizování aplikací založených na skupině](../manage-apps/what-is-access-management.md) | Pomocí zřizování správy přístupu na základě skupiny automaticky zřažete uživatele pro aplikace SaaS. |  |
| [Automatizace zřizování a zrušení zřizování uživatelů](../app-provisioning/user-provisioning.md) | Odeberte ruční kroky z životního cyklu účtu zaměstnance, abyste zabránili neoprávněnému přístupu. Synchronizujte identity ze zdroje pravdy (HR System) do Azure AD. |  |

## <a name="next-steps"></a>Další kroky

[Podrobnosti o licencování azure a ad a cenách](https://azure.microsoft.com/pricing/details/active-directory/)

[Konfigurace identit a přístupu k zařízením](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Běžné doporučené zásady identity a přístupu k zařízením](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)
