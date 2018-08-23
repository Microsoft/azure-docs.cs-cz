---
title: Získání certifikace pro Azure Active Directory AppSource | Dokumentace Microsoftu
description: Podrobnosti o tom, jak získat aplikace pro Azure Active Directory certifikace AppSource.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/21/2018
ms.author: celested
ms.reviewer: andret
ms.custom: aaddev
ms.openlocfilehash: a2876ccdfe073a3c642304a1381faf77ae4a7d90
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "42054879"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Získání certifikace AppSource pro Azure Active Directory

[Microsoft AppSource](https://appsource.microsoft.com/) je cíl podnikovým uživatelům umožňuje zjišťovat, akci a spravovat – obchodní aplikace SaaS (nasazení samostatné služby SaaS a doplněk do existujícího produktů v rámci Microsoft SaaS).

Pro zobrazení seznamu samostatné aplikaci SaaS na AppSource, vaše aplikace musí přijmout jednotné přihlašování z pracovních účtů z jakéhokoli společnosti nebo organizace, která má Azure Active Directory (Azure AD). Proces přihlášení musí použít [OpenID Connect](v1-protocols-openid-connect-code.md) nebo [OAuth 2.0](v1-protocols-oauth-code.md) protokoly. Integrace SAML není přijat pro certifikace AppSource.

## <a name="guides-and-code-samples"></a>Průvodce a ukázky kódu

Pokud chcete další informace o tom, jak integrovat aplikace s Azure AD pomocí služby Open ID připojení, použijte naše průvodce a ukázky v kódu [Příručka pro vývojáře Azure Active Directory](azure-ad-developers-guide.md#get-started "Začínáme s Azure AD pro Vývojáři").

## <a name="multi-tenant-applications"></a>Aplikace s více tenanty

A *aplikace s více tenanty* je aplikace, která přijímá přihlášení od uživatele z jakékoli společnost nebo organizace, kteří mají služby Azure AD bez nutnosti samostatné instance, konfigurace nebo nasazení. AppSource se doporučuje, aby aplikace implementovat více tenantů, aby *jedním kliknutím* bezplatné zkušební verze.

Pokud chcete povolit více tenantů v aplikaci, postupujte takto:
1. Nastavte `Multi-Tenanted` vlastnost `Yes` na informace o registraci aplikace v [webu Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Ve výchozím nastavení, aplikace vytvořené na webu Azure Portal jsou nakonfigurované jako  *[jednoho tenanta](#single-tenant-applications)*.
1. Aktualizujte svůj kód k odeslání žádosti o `common` koncového bodu. Provedete to tak, aktualizujte koncový bod z `https://login.microsoftonline.com/{yourtenant}` k `https://login.microsoftonline.com/common*`.
1. Pro některé platformy, jako je ASP .NET budete muset taky aktualizovat váš kód tak, aby přijímal více vystavitelů.

Další informace o víceklientské architektury, najdete v části [postup přihlášení libovolného uživatele služby Azure Active Directory (Azure AD) pomocí vzoru aplikace s více tenanty](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Aplikace s jedním tenantem

A *jednoho tenanta aplikaci* je aplikace, která přijímá pouze přihlášení z uživatelé definované Azure AD instance. Externí uživatele (včetně pracovní nebo školní účty z jiných organizací nebo osobní účty) se můžete přihlásit do jednoho tenanta aplikaci po přidání každého uživatele do instance služby Azure AD, že aplikace bude zaregistrovaná jako účet guest. 

Můžete přidat uživatele jako hosta účty do služby Azure AD prostřednictvím [spolupráce B2B ve službě Azure AD](../b2b/what-is-b2b.md) a můžete to provést [programově](../../active-directory-b2c/code-samples.md). Při použití B2B, uživatelé mohou vytvářet samoobslužný portál, který nevyžaduje pozvánku k přihlášení. Další informace najdete v tématu [samoobslužný portál nástroje pro spolupráci B2B ve službě Azure AD, registraci](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal).

Aplikace pro jednoho tenanta můžete povolit *Kontaktujte mě* prostředí, ale pokud chcete povolit jedním kliknutím/bez zkušební prostředí, které doporučuje AppSource, povolení víceklientské architektury na svou aplikaci, místo toho.

## <a name="appsource-trial-experiences"></a>Zkušební prostředí AppSource

### <a name="free-trial-customer-led-trial-experience"></a>Bezplatná zkušební verze (vedené zákazníka zkušební prostředí) 

Zkušební verze vedené zákazníků je prostředí, který AppSource se doporučuje, protože nabízí přístup jedním kliknutím pro vaši aplikaci. Následující ilustraci toho, jak toto prostředí vypadá takto:<br/><br/>

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%"/><ul><li>Uživatel vyhledá aplikace na webu AppSource</li><li>Vybere možnost "bezplatnou zkušební verzi.</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" /><ul><li>AppSource přesměruje uživatele na adresu URL webu</li><li>Spuštění vaší webové stránky <i>single-sign-on</i> zpracovat automaticky (při načtení stránky)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%"/><ul><li>Uživatel je přesměrován na stránku přihlašování společnosti Microsoft</li><li>Uživatel zadá přihlašovací údaje pro přihlášení</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%"/><ul><li>Uživatel dává souhlas pro vaši aplikaci</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Přihlásit se dokončí a uživatel je přesměrován zpět na web</li><li>Spuštění bezplatné zkušební verze</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Kontaktujte mě (vedené partnerem zkušební prostředí)

Při ruční nebo dlouhodobé operaci musí dojít ke zřízení uživatele/společnosti – například vaše aplikace se musí zřídit virtuální počítače, databáze instance nebo operací, které trvat dlouho pro dokončení, můžete použít zkušební prostředí partnera. V takovém případě po uživatel vybere **požádat o zkušební verzi** tlačítko a vyplní formuláře AppSource odešle kontaktní údaje uživatele. Když se zobrazí tyto informace, pak zřízení prostředí a odeslat pokyny pro uživatele o tom, jak přistupovat k zkušební verzi:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%"/><ul><li>Vyhledá uživatele vaší aplikace na webu AppSource</li><li>Vybere možnost "Kontaktujte Me"</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%"/><ul><li>Vyplňování formuláře s kontaktní informace</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%"/></td>
            <td>Zobrazí informace o uživateli</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%"/></td>
            <td>Nastavení prostředí</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%"/></td>
            <td>Požádejte uživatele s informací o zkušební verzi</td>
        </tr>
        </table><br/><br/>
        <ul><li>Zobrazí informace a instalace zkušební verze instance uživatele</li><li>Odeslat hypertextový odkaz pro přístup k aplikaci pro uživatele</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%"/><ul><li>Uživatel má přístup k aplikaci a dokončit proces jednotného přihlašování</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%"/><ul><li>Uživatel dává souhlas pro vaši aplikaci</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Přihlásit se dokončí a uživatel je přesměrován zpět na web</li><li>Spuštění bezplatné zkušební verze</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Další informace

Další informace o AppSource zkušební prostředí, najdete v části [toto video](https://aka.ms/trialexperienceforwebapps). 
 
## <a name="next-steps"></a>Další kroky

- Další informace o vytváření aplikací, které podporují přihlášení Azure AD najdete v tématu [scénáře ověřování pro službu Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).
- Informace o tom, jak uvedení SaaS aplikace v AppSource, najdete v tématu [informace o partnerovi AppSource](https://appsource.microsoft.com/partners)


## <a name="get-support"></a>Získat podporu

Integrace služby Azure AD, používáme [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource) s komunitou k poskytování podpory. 

Důrazně doporučujeme nejprve pokládání otázek na Stack Overflow a Procházet zobrazíte, pokud někdo položil dotaz před stávající problémy. Ujistěte se, že jsou označené svoje dotazy nebo připomínky [ `[azure-active-directory]` a `[appsource]` ](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Pomocí následujícího oddílu pro komentáře na svůj názor a Pomozte nám vylepšit a obrazce náš obsah.

<!--Reference style links -->
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: azure-ad-developers-guide.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: azure-ad-developers-guide.md#get-started


<!--Image references-->
