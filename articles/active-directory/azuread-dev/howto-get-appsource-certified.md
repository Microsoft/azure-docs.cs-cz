---
title: Jak získat AppSource Certified for Azure Active Directory | Microsoft Docs
description: Podrobnosti o tom, jak získat AppSource certifikaci aplikace pro Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: how-to
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: d9a4da6fe65fda07609c7399518fa324017ea44c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649341"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Jak získat AppSource Certified pro Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[Microsoft AppSource](https://appsource.microsoft.com/) je cílem pro obchodní uživatele zjistit, vyzkoušet a spravovat obchodní aplikace SaaS (samostatné SaaS a doplňky do stávajících produktů Microsoftu SaaS).

K vypsání samostatné aplikace SaaS na AppSource musí aplikace přijmout jednotné přihlašování z pracovních účtů z jakékoli společnosti nebo organizace, která má Azure Active Directory (Azure AD). Proces přihlášení musí používat protokoly [OpenID Connect](v1-protocols-openid-connect-code.md) nebo [OAuth 2,0](v1-protocols-oauth-code.md) . Integrace SAML není pro certifikaci AppSource přijata.

## <a name="guides-and-code-samples"></a>Příručky a ukázky kódu

Pokud se chcete dozvědět, jak integrovat aplikaci se službou Azure AD pomocí Open ID Connect, postupujte podle pokynů pro vývojáře a ukázky kódu v [příručce pro vývojáře Azure Active Directory](v1-overview.md#get-started "Začínáme se službou Azure AD pro vývojáře").

## <a name="multi-tenant-applications"></a>Víceklientské aplikace

*Víceklientské aplikace* je aplikace, která přijímá přihlášení od uživatelů z jakékoli společnosti nebo organizace, která má Azure AD bez nutnosti samostatné instance, konfigurace nebo nasazení. AppSource doporučuje, aby aplikace implementovaly víceklientské prostředí pro zajištění bezplatné zkušební verze *jedním kliknutím* .

Pokud chcete v aplikaci povolit více tenantů, postupujte takto:
1. Nastavte `Multi-Tenanted` vlastnost na `Yes` informace o registraci vaší aplikace v [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Ve výchozím nastavení jsou aplikace vytvořené v Azure Portal nakonfigurované jako *[jeden tenant](#single-tenant-applications)*.
1. Aktualizujte kód, aby odesílal požadavky na `common` koncový bod. To provedete tak, že aktualizujete koncový bod z `https://login.microsoftonline.com/{yourtenant}` na `https://login.microsoftonline.com/common*` .
1. U některých platforem, jako je například ASP .NET, je nutné také aktualizovat kód, aby přijímal více vystavitelů.

Další informace o víceklientské architektuře najdete v tématu [jak se přihlásit k libovolnému uživateli Azure Active Directory (Azure AD) pomocí vzoru aplikace pro více tenantů](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="single-tenant-applications"></a>Aplikace s jedním klientem

*Jediná klientská aplikace* je aplikace, která přijímá jenom přihlášení od uživatelů definované instance Azure AD. Externí uživatelé (včetně pracovních nebo školních účtů z jiných organizací nebo osobních účtů) se můžou přihlašovat k jednomu tenantovi aplikace po přidání každého uživatele jako účtu hosta do instance Azure AD, kterou je aplikace zaregistrovaná. 

Do Azure AD můžete přidat uživatele jako účty hosta prostřednictvím [Spolupráce B2B Azure AD](../external-identities/what-is-b2b.md) a můžete to udělat [programově](../../active-directory-b2c/code-samples.md). Když uživatelé používají B2B, můžou vytvořit Samoobslužný portál, který nevyžaduje pozvání k přihlášení. Další informace najdete v tématu [Samoobslužný portál pro spolupráci služby Azure AD B2B – registrace](../external-identities/self-service-portal.md).

Aplikace s jedním tenantům můžou povolit možnosti *kontakt mi* , ale pokud chcete povolit zkušební prostředí s jedním kliknutím nebo bezplatnou verzí, které AppSource doporučuje, povolte pro svoji aplikaci více tenantů.

## <a name="appsource-trial-experiences"></a>AppSource zkušební prostředí

### <a name="free-trial-customer-led-trial-experience"></a>Bezplatná zkušební verze (zkušební prostředí pro zákazníky s indikátorem)

Zkušební verze na základě zákazníka je prostředí, které AppSource doporučuje, protože nabízí přístup k vaší aplikaci jediným kliknutím. Následující příklad ukazuje, jak toto chování vypadá takto:

<table >
<tr>    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt="Shows Free trial for customer-led trial experience."/><ul><li>Uživatel najde vaši aplikaci na webu AppSource.</li><li>Vybere možnost bezplatné zkušební verze.</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt="Shows how user is redirected to a URL in your web site."/><ul><li>AppSource přesměruje uživatele na adresu URL na webu.</li><li>Váš web automaticky spustí proces <i>jednotného přihlašování</i> (při zatížení stránky).</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt="Shows the Microsoft sign-in page."/><ul><li>Uživatel je přesměrován na přihlašovací stránku Microsoftu.</li><li>Uživatel poskytne přihlašovací údaje pro přihlášení.</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt="Example: Consent page for an application."/><ul><li>Uživatel udělí souhlas pro vaši aplikaci.</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt="Shows the experience the user sees when redirected back to your site."/><ul><li>Přihlášení se dokončí a uživatel se přesměruje zpátky na váš web.</li><li>Uživatel spustí bezplatnou zkušební verzi.</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Kontaktujte mě (zkušební prostředí, které vedlo k partnerům)

V případě, že se při zřizování uživatele/společnosti může provést ruční nebo dlouhodobá operace, můžete použít zkušební verzi partnerů. vaše aplikace potřebuje zřídit virtuální počítače, instance databáze nebo operace, jejichž dokončení trvá příliš dlouho. V takovém případě potom, co uživatel vybere tlačítko pro **vyžádání zkušební verze** a vyplní formulář, vám AppSource pošle kontaktní informace uživatele. Když tyto informace obdržíte, zřídíte prostředí a odešlete pokyny uživateli, jak získat přístup ke zkušební verzi:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt="Shows Contact me for partner-led trial experience"/><ul><li>Uživatel najde vaši aplikaci na webu AppSource.</li><li>Vybere možnost kontakt mi</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt="Shows an example form with contact info"/><ul><li>Vyplňování formuláře s kontaktními informacemi</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/usercontact.png" width="55%" alt="Shows placeholder for user information"/></td>
            <td>Dostanete informace o uživateli.</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/setupenv.png" width="55%" alt="Shows placeholder for setup environment info"/></td>
            <td>Nastavení prostředí</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/contactcustomer.png" width="55%" alt="Shows placeholder for trial info"/></td>
            <td>Kontaktujte uživatele s informacemi o zkušební verzi</td>
        </tr>
        </table><br/><br/>
        <ul><li>Získáte informace o uživateli a nastavování zkušební instance</li><li>Odešlete hypertextový odkaz pro přístup k aplikaci uživateli</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt="Shows the application sign-in screen"/><ul><li>Uživatel přistupuje k aplikaci a dokončuje proces jednotného přihlašování.</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt="Shows an example consent page for an application"/><ul><li>Uživatel udělí souhlas pro vaši aplikaci.</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt="Shows the experience the user sees when redirected back to your site"/><ul><li>Přihlášení se dokončí a uživatel se přesměruje zpátky na váš web.</li><li>Uživatel spustí bezplatnou zkušební verzi.</li></ul></td>  
</tr>
</table>

### <a name="more-information"></a>Další informace

Další informace o zkušebním prostředí AppSource najdete v [tomto videu](https://aka.ms/trialexperienceforwebapps). 

## <a name="next-steps"></a>Další kroky

- Další informace o vytváření aplikací podporujících přihlášení k Azure AD najdete v tématu [scénáře ověřování pro Azure AD](./v1-authentication-scenarios.md).
- Informace o tom, jak v AppSource zobrazit seznam aplikací SaaS, najdete v tématu [informace o partnerovi AppSource](https://appsource.microsoft.com/partners) .

## <a name="get-support"></a>Získání podpory

Pro integraci služby Azure AD používáme pro poskytování podpory [Microsoft Q&A](/answers/products/) společně s komunitou.

Důrazně doporučujeme, abyste si položili své dotazy na Microsoft Q&a Projděte si stávající problémy, abyste zjistili, jestli se někdo na vaši otázku předá dotaz. Ujistěte se, že jsou vaše dotazy nebo komentáře označené pomocí [`[azure-active-directory]`](/answers/topics/azure-active-directory.html) .

Následující komentáře vám poskytnou zpětnou vazbu a pomáhají nám zdokonalit a obsloužit náš obsah.

<!--Reference style links -->
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:v1-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->