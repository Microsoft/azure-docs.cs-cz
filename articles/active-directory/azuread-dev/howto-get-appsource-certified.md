---
title: Jak získat certifikaci AppSource pro Azure Active Directory| Dokumenty společnosti Microsoft
description: Podrobnosti o tom, jak získat aplikaci AppSource certifikovanou pro Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 3ad4efa3b8126a9b9c6557822f61e3bfff3fe120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154878"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Jak získat certifikaci AppSource pro Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[Microsoft AppSource](https://appsource.microsoft.com/) je cíl pro podnikové uživatele, aby zjišťovali, zkoušeli a spravovali obchodní aplikace SaaS (samostatný SaaS a doplněk k existujícím produktům Microsoft SaaS).

Chcete-li uvést samostatnou aplikaci SaaS na AppSource, vaše aplikace musí přijmout jednotné přihlašování z pracovních účtů z jakékoli společnosti nebo organizace, která má Azure Active Directory (Azure AD). Proces přihlášení musí používat protokoly [OpenID Connect](v1-protocols-openid-connect-code.md) nebo [OAuth 2.0.](v1-protocols-oauth-code.md) Integrace SAML není přijata pro certifikaci AppSource.

## <a name="guides-and-code-samples"></a>Vodítka a ukázky kódu

Pokud se chcete dozvědět o tom, jak integrovat vaši aplikaci s Azure AD pomocí Open ID connect, postupujte podle našich pokynů a ukázky kódu v [průvodci vývojářem Azure Active Directory](v1-overview.md#get-started "Začínáme s Azure AD pro vývojáře").

## <a name="multi-tenant-applications"></a>Víceklientské aplikace

*Víceklientská aplikace* je aplikace, která přijímá přihlášení od uživatelů z jakékoli společnosti nebo organizace, které mají Azure AD bez nutnosti samostatné instance, konfigurace nebo nasazení. AppSource doporučuje, aby aplikace implementovat více klienta povolit *jedno kliknutí* bezplatné zkušební prostředí.

Chcete-li povolit více klienta v aplikaci, postupujte takto:
1. Nastavte `Multi-Tenanted` vlastnost `Yes` na informace o registraci aplikace na [webu Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Ve výchozím nastavení jsou aplikace vytvořené na webu Azure Portal nakonfigurované jako *[jednoklientské](#single-tenant-applications)*.
1. Aktualizujte kód a odesílejte `common` požadavky do koncového bodu. Chcete-li to provést, `https://login.microsoftonline.com/{yourtenant}` aktualizujte koncový bod z na `https://login.microsoftonline.com/common*`.
1. Pro některé platformy, jako je technologie ASP .NET, je také nutné aktualizovat kód pro přijetí více vystavitelů.

Další informace o víceklientských nájmech najdete [v tématu Jak se přihlásit k libovolnému uživateli služby Azure Active Directory (Azure AD) pomocí vzoru víceklientské aplikace](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="single-tenant-applications"></a>Aplikace s jedním tenantem

*Aplikace s jedním tenantem* je aplikace, která přijímá pouze přihlášení od uživatelů definované instance Azure AD. Externí uživatelé (včetně pracovních nebo školních účtů z jiných organizací nebo osobních účtů) se můžou přihlásit k aplikaci s jedním tenantem po přidání každého uživatele jako účtu hosta do instance Azure AD, že je aplikace registrovaná. 

Můžete přidat uživatele jako účty hosta do Azure AD prostřednictvím [spolupráce Azure AD B2B](../b2b/what-is-b2b.md) a můžete to udělat [programově](../../active-directory-b2c/code-samples.md). Při použití B2B, uživatelé mohou vytvořit samoobslužný portál, který nevyžaduje pozvání k přihlášení. Další informace najdete v [tématu Samoobslužný portál pro registraci spolupráce Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal).

Aplikace s jedním tenantem můžete povolit *možnost i funkci Kontaktujte mě,* ale pokud chcete povolit jedno kliknutí/bezplatnou zkušební verzi, kterou doporučuje AppSource, povolte místo toho víceklientské prostředí ve vaší aplikaci.

## <a name="appsource-trial-experiences"></a>Zkušební prostředí AppSource

### <a name="free-trial-customer-led-trial-experience"></a>Bezplatná zkušební verze (zkušební verze vedená zákazníkem)

Zákazníkem vedená zkušební verze je prostředí, které AppSource doporučuje, protože nabízí přístup k vaší aplikaci jedním kliknutím. Následující příklad ukazuje, jak toto prostředí vypadá:

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt-text="Shows Free trial for customer-led trial experience"/><ul><li>Uživatel najde vaši aplikaci na webu AppSource.</li><li>Vybere možnost "Bezplatná zkušební verze".</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt-text="Shows how user is redirected to a URL in your web site"/><ul><li>AppSource přesměruje uživatele na adresu URL na vašem webu</li><li>Váš web spustí proces <i>jednotného přihlašování</i> automaticky (při načítání stránky)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt-text="Shows the Microsoft sign-in page"/><ul><li>Uživatel je přesměrován na přihlašovací stránku společnosti Microsoft.</li><li>Uživatel poskytuje přihlašovací údaje pro přihlášení</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt-text="Example: Consent page for an application"/><ul><li>Uživatel dává souhlas pro vaši žádost</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>Přihlášení dokončeno a uživatel je přesměrován zpět na váš web</li><li>Uživatel spustí bezplatnou zkušební verzi</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Kontaktujte mě (zkušenosti se zkušební mši vedenou partnerem)

Zkušební prostředí partnera můžete použít v případě, že ruční nebo dlouhodobé operace musí dojít k zřízení uživatele nebo společnosti – například vaše aplikace potřebuje zřídit virtuální počítače, instance databáze nebo operace, které trvat hodně času na dokončení. V tomto případě poté, co uživatel vybere tlačítko **Požádat o zkušební** verzi a vyplní formulář, AppSource vám odešle kontaktní informace uživatele. Když obdržíte tyto informace, potom zřídit prostředí a odeslat pokyny k uživateli o tom, jak získat přístup k zkušební prostředí:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt-text="Shows Contact me for partner-led trial experience"/><ul><li>Uživatel najde vaši aplikaci na webu AppSource</li><li>Vybere možnost "Kontaktujte mě".</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt-text="Shows an example form with contact info"/><ul><li>Vyplňuje formulář s kontaktními informacemi</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/usercontact.png" width="55%" alt-text="Shows placeholder for user information"/></td>
            <td>Obdržíte informace o uživateli</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/setupenv.png" width="55%" alt-text="Shows placeholder for setup environment info"/></td>
            <td>Nastavení prostředí</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/contactcustomer.png" width="55%" alt-text="Shows placeholder for trial info"/></td>
            <td>Kontaktování uživatele s informacemi o zkušební verzi</td>
        </tr>
        </table><br/><br/>
        <ul><li>Obdržíte informace o uživateli a zkušební instanci nastavení</li><li>Odešlete hypertextový odkaz pro přístup k aplikaci uživateli</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt-text="Shows the application sign-in screen"/><ul><li>Uživatel přistupuje k vaší aplikaci a dokončí proces jednotného přihlašování.</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt-text="Shows an example consent page for an application"/><ul><li>Uživatel dává souhlas pro vaši žádost</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>Přihlášení dokončeno a uživatel je přesměrován zpět na váš web</li><li>Uživatel spustí bezplatnou zkušební verzi</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Další informace

Další informace o zkušebním prostředí AppSource naleznete v [tomto videu](https://aka.ms/trialexperienceforwebapps). 

## <a name="next-steps"></a>Další kroky

- Další informace o vytváření aplikací, které podporují přihlášení azure ad, najdete v [tématu scénáře ověřování pro Azure AD](https://docs.microsoft.com/azure/active-directory/develop/v1-authentication-scenarios).
- Informace o tom, jak zobrazit seznam aplikace SaaS v AppSource, najdete v [tématu AppSource Partner Information](https://appsource.microsoft.com/partners)

## <a name="get-support"></a>Získat podporu

Pro integraci Azure AD používáme [přetečení zásobníku](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource) s komunitou k poskytování podpory.

Důrazně doporučujeme, abyste nejprve položili otázky týkající se přetečení zásobníku a procházet existující problémy, abyste zjistili, zda někdo vaši otázku předtím položil. Zkontrolujte, zda jsou vaše dotazy nebo komentáře označeny [ `[azure-active-directory]` písmenem a. `[appsource]` ](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource)

V následující části s komentáři můžete poskytnout zpětnou vazbu a pomoci nám vylepšit a utvářet náš obsah.

<!--Reference style links -->
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:v1-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->
