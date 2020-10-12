---
title: Přehled ověření vydavatele – Microsoft Identity Platform | Azure
description: Poskytuje přehled programu pro ověřování vydavatele pro platformu Microsoft identity. Obsahuje seznam výhod, požadavků programu a nejčastějších dotazů. Pokud je aplikace označena jako ověřená vydavatelem, znamená to, že vydavatel ověřil svoji identitu pomocí účtu Microsoft Partner Network, který dokončil proces ověření a přidružil tento účet MPN k registraci aplikace.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 53861bbff9ba328ca97063df59cfedc6e886212d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91258145"
---
# <a name="publisher-verification"></a>Ověření vydavatele

Ověřování vydavatele pomáhá správcům a koncovým uživatelům pochopit pravost vývojářů aplikací, které jsou integrovány s platformou Microsoft identity. Pokud je aplikace označena jako ověřená vydavatelem, znamená to, že vydavatel ověřil svoji identitu pomocí účtu [Microsoft Partner Network](https://partner.microsoft.com/membership) , který dokončil proces [ověření](/partner-center/verification-responses) a přidružil tento účet MPN k registraci aplikace. 

Modrá "ověřená" výzva se zobrazí na výzvu k vyjádření souhlasu služby Azure AD a na dalších obrazovkách: ![ výzva k vyjádření souhlasu](./media/publisher-verification-overview/consent-prompt.png)

Tato funkce je primárně určená pro vývojáře, kteří sestavují víceklientské aplikace, které využívají [OAuth 2,0 a OpenID Connect](active-directory-v2-protocols.md) s [platformou Microsoft Identity](v2-overview.md). Tyto aplikace můžou podepisovat uživatele pomocí OpenID Connect nebo můžou pomocí OAuth 2,0 požádat o přístup k datům pomocí rozhraní API, jako je [Microsoft Graph](https://developer.microsoft.com/graph/).

## <a name="benefits"></a>Výhody
Ověřování vydavatele nabízí následující výhody:
- **Zvýšení transparentnosti a snížení rizik pro zákazníky**– Tato možnost pomáhá zákazníkům pochopit, které aplikace používané ve svých organizacích jsou publikovány vývojáři, kterým důvěřují. 

- **Vylepšené branding**– "ověřené" označení se zobrazí na stránce s [výzvou k vyjádření souhlasu](application-consent-experience.md)služby Azure AD, na stránce podnikové aplikace a na dalších plochách uživatelského prostředí, které používají koncoví uživatelé a správci. 

- Zajištění **plynulejšího podnikání**– správci můžou konfigurovat [zásady pro vyjádření souhlasu s uživatelem](../manage-apps/configure-user-consent.md), přičemž stav ověření vydavatele je jedno z primárních kritérií zásad.

> [!NOTE]
> Od listopadu 2020 již koncoví uživatelé nebudou moci udělit souhlas s nově registrovanými aplikacemi pro více tenantů bez ověřených vydavatelů. Tato akce bude platit pro aplikace, které jsou zaregistrované po 8. listopadu 2020, pomocí OAuth 2.0 vyžádá oprávnění mimo základní přihlášení a čtení profilu uživatele a vyžádá si souhlas od uživatelů v různých tenantůch, než je ta, ve které je aplikace zaregistrovaná. Na obrazovce pro vyjádření souhlasu se zobrazí upozornění informující o uživatelích, že tyto aplikace jsou riskantní a že jsou od neověřených vydavatelů.    

## <a name="requirements"></a>Požadavky
Je k dispozici několik požadavků na ověření vydavatele, některé z nich již byly dokončeny mnoha partnery společnosti Microsoft. Jsou to tyto: 

-  ID MPN pro platný účet [Microsoft Partner Network](https://partner.microsoft.com/membership) , který dokončil proces [ověření](/partner-center/verification-responses) . Tento účet MPN musí být [globální účet partnera (PGA)](/partner-center/account-structure#the-top-level-is-the-partner-global-account-pga) pro vaši organizaci. 

-  Aplikace registrovaná v tenantovi Azure AD s nakonfigurovanou [doménou vydavatele](howto-configure-publisher-domain.md)

-  Doména e-mailové adresy použité během ověřování účtu MPN musí být buď shodná s doménou vydavatele nakonfigurovanou v aplikaci, nebo [vlastní doménou](../fundamentals/add-custom-domain.md) ověřenou DNS, kterou jste přidali do tenanta služby Azure AD. 

-  Uživatel, který provádí ověřování, musí mít autorizaci k provádění změn v registraci aplikace v Azure AD a účtu MPN v partnerském centru. 

    -  V Azure AD musí být tento uživatel členem jedné z následujících [rolí](../users-groups-roles/directory-assign-admin-roles.md): správce aplikace, správce cloudové aplikace nebo globální správce. 

    -  V partnerském centru musí mít tento uživatel tyto [role](/partner-center/permissions-overview): správce MPN, účty správce nebo globální správce (Toto je sdílená role spravovaná ve službě Azure AD).
    
-  Uživatel, který provádí ověření, se musí přihlásit pomocí [Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md).

-  Vydavatel souhlasí s [podmínkami použití pro vývojáře na platformě Microsoft Identity](/legal/microsoft-identity-platform/terms-of-use).

Vývojáři, kteří už tyto požadavky splnili, se můžou ověřit během několika minut. Pokud se požadavky nesplní, nastavování je zdarma. 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy 
Níže najdete nejčastější dotazy týkající se ověřovacího programu vydavatele. Nejčastější dotazy týkající se požadavků a procesu najdete v článku [označení aplikace jako aplikace Publisher jako ověřený vydavatel](mark-app-as-publisher-verified.md).

- **Jaké informace neposkytuje ověřování __vydavatele__ ?**  Je-li aplikace označena jako vydavatel, neznamená to, zda aplikace nebo její Vydavatel splnila konkrétní certifikace, vyhovuje oborovým standardům, dodržuje osvědčené postupy atd. Tyto informace obsahují další programy společnosti Microsoft, včetně [Microsoft 365 certifikace aplikací](/microsoft-365-app-certification/overview).

- **Kolik to stojí? Vyžaduje se licence?** Společnost Microsoft neúčtuje vývojáře pro ověřování vydavatelů a nevyžaduje žádnou konkrétní licenci. 

- **Jak se to týká Microsoft 365 ověřování vydavatele? Co Microsoft 365 certifikace aplikací?** Jedná se o doplňkové programy, které můžou vývojáři použít k vytváření důvěryhodných aplikací, které můžou zákazníci bez obav přijmout. Ověření vydavatele je prvním krokem v tomto procesu a je třeba je dokončit všemi vývojáři, kteří vytvářejí aplikace, které splňují výše uvedená kritéria. 

  Vývojáři, kteří integrují taky Microsoft 365, můžou z těchto programů získat další výhody. Další informace najdete v tématu [Microsoft 365 ověřování vydavatele](/microsoft-365-app-certification/docs/attestation) a [Microsoft 365 certifikace aplikací](/microsoft-365-app-certification/docs/certification). 

- **Je to totéž jako Galerie aplikací Azure AD?** Ověřování bez vydavatele je doplňkový, ale oddělený program v [galerii aplikací Azure Active Directory](v2-howto-app-gallery-listing.md). Vývojáři, kteří vyhovují výše uvedeným kritériím, by měli dokončit proces ověření vydavatele nezávisle na účasti v tomto programu. 

## <a name="next-steps"></a>Další kroky
* Naučte se, jak [Označit aplikaci jako ověřenou vydavatelem](mark-app-as-publisher-verified.md).
* [Řešení potíží s](troubleshoot-publisher-verification.md) ověřováním vydavatele
