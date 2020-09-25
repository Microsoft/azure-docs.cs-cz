---
title: Získat AppSource certifikaci pro Azure Active Directory | Microsoft Docs
description: Přečtěte si podrobnosti o tom, jak získat AppSource certifikaci aplikace pro Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 65d6151536eb92e2ab9b740a99984fb0b5977cab
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91256870"
---
# <a name="get-appsource-certified-for-azure-active-directory"></a>Získání AppSource certifikace pro Azure Active Directory

[Microsoft AppSource](https://appsource.microsoft.com/) je cílem pro obchodní uživatele zjistit, vyzkoušet a spravovat obchodní aplikace SaaS (samostatné SaaS a doplňky do stávajících produktů Microsoftu SaaS).

K vypsání samostatné aplikace SaaS na AppSource musí aplikace přijmout jednotné přihlašování z pracovních účtů z jakékoli společnosti nebo organizace, která má Azure Active Directory (Azure AD). Proces přihlášení musí používat protokoly [OpenID Connect](v2-protocols-oidc.md) nebo [OAuth 2,0](v2-oauth2-auth-code-flow.md) . Integrace SAML není pro certifikaci AppSource přijata.

## <a name="multi-tenant-applications"></a>Víceklientské aplikace

*Víceklientské aplikace* je aplikace, která přijímá přihlášení od uživatelů z jakékoli společnosti nebo organizace, která má Azure AD bez nutnosti samostatné instance, konfigurace nebo nasazení. AppSource doporučuje, aby aplikace implementovaly víceklientské prostředí pro zajištění bezplatné zkušební verze *jedním kliknutím* .

Pokud chcete v aplikaci povolit více tenantů, postupujte takto:
1. Nastavte `Multi-Tenanted` vlastnost na `Yes` informace o registraci vaší aplikace v [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Ve výchozím nastavení jsou aplikace vytvořené v Azure Portal nakonfigurované jako *[jeden tenant](#single-tenant-applications)*.
1. Aktualizujte kód, aby odesílal požadavky na `common` koncový bod. To provedete tak, že aktualizujete koncový bod z `https://login.microsoftonline.com/{yourtenant}` na `https://login.microsoftonline.com/common*` .
1. U některých platforem, jako je ASP.NET, potřebujete také aktualizovat kód, aby přijímal více vystavitelů.

Další informace o víceklientské architektuře najdete v tématu [jak se přihlásit k libovolnému uživateli Azure Active Directory (Azure AD) pomocí vzoru aplikace pro více tenantů](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Aplikace s jedním klientem

*Jediná klientská aplikace* je aplikace, která přijímá jenom přihlášení od uživatelů definované instance Azure AD. Externí uživatelé (včetně pracovních nebo školních účtů z jiných organizací nebo osobních účtů) se můžou přihlašovat k jednomu tenantovi aplikace po přidání každého uživatele jako účtu hosta do instance Azure AD, kterou je aplikace zaregistrovaná. 

Do Azure AD můžete přidat uživatele jako účty hosta prostřednictvím [Spolupráce B2B Azure AD](../external-identities/what-is-b2b.md) a můžete to udělat [programově](../../active-directory-b2c/code-samples.md). Když uživatelé používají B2B, můžou vytvořit Samoobslužný portál, který nevyžaduje pozvání k přihlášení. Další informace najdete v tématu [Samoobslužný portál pro spolupráci služby Azure AD B2B – registrace](../external-identities/self-service-portal.md).

Aplikace s jedním tenantům můžou povolit možnosti *kontakt mi* , ale pokud chcete povolit zkušební prostředí s jedním kliknutím nebo bezplatnou verzí, které AppSource doporučuje, povolte pro svoji aplikaci více tenantů.

## <a name="appsource-trial-experiences"></a>AppSource zkušební prostředí

### <a name="free-trial-customer-led-trial-experience"></a>Bezplatná zkušební verze (zkušební prostředí pro zákazníky s indikátorem)

Zkušební verze na základě zákazníka je prostředí, které AppSource doporučuje, protože nabízí přístup k vaší aplikaci jediným kliknutím. Následující příklad ukazuje, jak toto chování vypadá takto:

1.  Uživatel najde vaši aplikaci na webu AppSource a pak vybere možnost **bezplatné zkušební verze** .

    ![Zobrazuje bezplatnou zkušební verzi zkušebního prostředí pro zákazníky.](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png)

2.  AppSource přesměruje uživatele na adresu URL na webu. Váš web spustí proces *jednotného přihlašování* automaticky (při zatížení stránky).

    ![Ukazuje, jak se uživatel přesměruje na adresu URL na webu.](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png)

3.  Uživatel se přesměruje na přihlašovací stránku Microsoftu a uživatel poskytne přihlašovací údaje pro přihlášení.

    ![Zobrazí přihlašovací stránku Microsoftu.](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png)

4. Uživatel poskytne pro vaši aplikaci souhlas.

    ![Příklad: stránka souhlasu pro aplikaci](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png)

5.  Přihlašování se dokončí a uživatel se přesměruje zpátky na váš web.  Uživatel spustí bezplatnou zkušební verzi.

    ![Zobrazuje prostředí, které uživatel vidí při přesměrování zpátky na váš web.](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="contact-me-partner-led-trial-experience"></a>Kontaktujte mě (zkušební prostředí, které vedlo k partnerům)

V případě, že se při zřizování uživatele/společnosti může provést ruční nebo dlouhodobá operace, můžete použít zkušební verzi partnerů. vaše aplikace potřebuje zřídit virtuální počítače, instance databáze nebo operace, jejichž dokončení trvá příliš dlouho. V takovém případě potom, co uživatel vybere tlačítko pro **vyžádání zkušební verze** a vyplní formulář, vám AppSource pošle kontaktní informace uživatele. Když tyto informace obdržíte, zřídíte prostředí a odešlete pokyny uživateli, jak získat přístup ke zkušební verzi:<br/><br/>

1. Uživatel najde vaši aplikaci na webu AppSource a pak vybere **kontakt mi**.

    ![Zobrazí kontaktní informace pro zkušební prostředí řízené partnerem.](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png)

2. Uživatel vyplní formulář s kontaktními informacemi.

    ![Zobrazuje příklad formuláře s kontaktními informacemi.](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png)

3. Obdržíte informace o uživateli, nastavíte zkušební instanci a odešlete hypertextový odkaz pro přístup k aplikaci uživateli.

    ![Zobrazí zástupný text pro informace o uživateli.](./media/active-directory-devhowto-appsource-certified/usercontact.png)

4. Uživatel přistupuje k aplikaci a dokončuje proces jednotného přihlašování.

    ![Zobrazí obrazovku přihlášení aplikace.](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png)

5. Uživatel poskytne pro vaši aplikaci souhlas.

    ![Zobrazuje ukázkovou stránku pro vyjádření souhlasu pro aplikaci.](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png)

6. Přihlašování se dokončí a uživatel se přesměruje zpátky na váš web. Uživatel spustí bezplatnou zkušební verzi.

    ![Zobrazuje prostředí, které uživatel vidí při přesměrování zpátky na váš web.](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="more-information"></a>Další informace

Další informace o zkušebním prostředí AppSource najdete v [tomto videu](https://aka.ms/trialexperienceforwebapps). 

## <a name="get-support"></a>Získání podpory

Pro integraci služby Azure AD používáme pro poskytování podpory [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource) s komunitou.

Důrazně doporučujeme, abyste si nejdřív položili své dotazy na Stack Overflow a mohli si projít stávající problémy, abyste zjistili, jestli se někdo na svůj dotaz předá. Ujistěte se, že jsou vaše dotazy nebo komentáře označeny pomocí [ `[azure-active-directory]` a `[appsource]` ](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Následující komentáře vám poskytnou zpětnou vazbu a pomáhají nám zdokonalit a obsloužit náš obsah.

## <a name="next-steps"></a>Další kroky

- Další informace o vytváření aplikací podporujících přihlášení k Azure AD najdete v tématu [scénáře ověřování pro Azure AD](authentication-flows-app-scenarios.md).
- Informace o tom, jak v AppSource zobrazit seznam aplikací SaaS, najdete v tématu [informace o partnerovi AppSource](https://appsource.microsoft.com/partners) .