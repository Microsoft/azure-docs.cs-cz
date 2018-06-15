---
title: Jak získat AppSource certifikované pro Azure Active Directory | Microsoft Docs
description: Podrobnosti o tom, jak získat aplikace AppSource certifikované pro Azure Active Directory.
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
ms.date: 08/03/2017
ms.author: celested
ms.reviewer: andret
ms.custom: aaddev
ms.openlocfilehash: 844c19803f57987f4435d013cd12aa3d1ef8aeb8
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257527"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Jak získat AppSource certifikované pro Azure Active Directory
[Microsoft AppSource](https://appsource.microsoft.com/) je cíl pro firmy uživatelům zjišťovat, zkuste a spravovat-obchodní aplikace SaaS (samostatný SaaS a rozšíření na existující produktů Microsoft SaaS).

Pro zobrazení seznamu samostatnou aplikaci SaaS na AppSource, musíte aplikace přijmout jednotné přihlašování z pracovních účtů v jakémkoli společnosti nebo organizace, která má Azure Active Directory. Musíte použít proces přihlášení [OpenID Connect](./active-directory-protocols-openid-connect-code.md) nebo [OAuth 2.0](./active-directory-protocols-oauth-code.md) protokoly. Integrace SAML nebyla přijata AppSource certifikaci.

## <a name="guides-and-code-samples"></a>Vodítka a ukázky kódu
Pokud chcete další informace o tom, jak integrovat vaší aplikace pomocí Azure Active Directory pomocí Open ID připojení, postupujte podle naše příručky a ukázky v kódu [Příručka pro vývojáře Azure Active Directory](./active-directory-developers-guide.md#get-started "Začínáme s Azure AD pro vývojáře").

## <a name="multi-tenant-applications"></a>Víceklientským aplikacím

Aplikace, která přijímá přihlášení od uživatelů v jakémkoli společnosti nebo organizace, které mají Azure Active Directory bez nutnosti samostatné instanci, konfigurace nebo nasazení se označuje jako *víceklientské aplikace*. AppSource doporučuje, že aplikace implementovat víceklientské povolit *jedním kliknutím* volné zkušební verze.

Chcete-li povolit více klientů ve vaší aplikaci:
- Nastavit `Multi-Tenanted` vlastnost `Yes` na informace o registraci aplikace v [portálu Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) (ve výchozím nastavení jsou aplikace vytvořené na portálu Azure nakonfigurovaný jako *jednoho klienta*)
- Aktualizujte kód a odeslání žádosti o '`common`se koncový bod (aktualizaci koncového bodu z *https://login.microsoftonline.com/{yourtenant}* k *https://login.microsoftonline.com/common*)
- Pro některé platformy, jako je ASP.NET budete muset taky aktualizovat kód tak, aby přijímal více vystavitelů

Další informace o víceklientské najdete v tématu: [jak se přihlásit žádné uživatele Azure Active Directory (AD) pomocí vzoru víceklientské aplikace](./active-directory-devhowto-multi-tenant-overview.md).

### <a name="single-tenant-applications"></a>Single klientské aplikace
Aplikace, které přijímají pouze přihlášení z uživatelů definovaných instance služby Azure Active Directory se označují jako *jednoho klienta aplikace*. Externí uživatelé (včetně pracovní nebo školní účty z jiných organizací, nebo osobní účet) se můžete přihlásit k aplikaci jednoho klienta po přidání každého uživatele jako *účtu guest* k instanci služby Azure Active Directory, že aplikace je zaregistrován. Jako účet hosta. můžete přidat uživatele do služby Azure Active Directory pomocí [ *spolupráce Azure AD B2B* ](../b2b/what-is-b2b.md) - a je možné ji provést [programově](../b2b/code-samples.md). Když přidáte uživatele jako účet guest do Azure Active Directory, pozvánka e-mail je odeslán uživateli, který má k přijetí pozvánky kliknutím na odkaz v e-mailová pozvánka. Pozvánky, které se odesílají do dalšího uživatele v organizaci pozváním, která je také členem partnerské organizace nejsou muset přijmout pozvánku k přihlášení.

Můžete povolit jeden klienta aplikace *kontaktujte mi* prostředí, ale pokud chcete povolit jedním kliknutím / bezplatné zkušební prostředí, které doporučuje AppSource, povolte víceklientský ve vaší aplikaci místo.


## <a name="appsource-trial-experiences"></a>AppSource zkušební prostředí

### <a name="free-trial-customer-led-trial-experience"></a>Bezplatná zkušební verze (vedla zákazníka zkušební prostředí) 
*Vedla zákazníka zkušební verze* je prostředí, které doporučuje AppSource jako nabízí jedním kliknutím přístup k vaší aplikaci. Níže ilustraci toho, jak toto prostředí vypadá takto:<br/><br/>

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%"/><ul><li>Uživatel vyhledá aplikace ve AppSource webu</li><li>Vybere možnost "bezplatnou zkušební verzi.</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" /><ul><li>AppSource přesměruje uživatele na adresu URL vašeho webu</li><li>Váš webový server spustí <i>jednotného přihlašování</i> zpracování automaticky (podle načtení stránky)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%"/><ul><li>Uživatel je přesměrován na stránku přihlášení Microsoft</li><li>Uživatel zadá přihlašovací údaje pro přihlášení</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%"/><ul><li>Uživatel dává souhlasu pro vaši aplikaci</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Přihlášení dokončí a uživatel je přesměrován zpět na webové stránky</li><li>Spuštění uživatelem bezplatné zkušební verze</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Obraťte se na mě (vedla partnera zkušební prostředí)
*Partner zkušební verze* lze použít při ruční nebo dlouhodobé operaci, musí dojít ke zřízení uživatele / společnosti: například vaše aplikace potřebuje ke zřízení virtuálních počítačů, instancí databáze nebo operace, které trvat dlouho na dokončení. V takovém případě po uživatel vybere *'žádosti o zkušební verzi,* tlačítko a zadá formuláře, AppSource odešle kontaktní údaje uživatele. Po přijetí těchto informací, pak zřízení prostředí a odeslat pokyny pro uživatele o tom, jak získat přístup k zkušební prostředí:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%"/><ul><li>Vyhledá uživatele vaší aplikace AppSource webu</li><li>Vybere možnost, obraťte se na mě.</li></ul></td>
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
            <td>Kontaktujte uživatele s informacemi zkušební verze</td>
        </tr>
        </table><br/><br/>
        <ul><li>Zobrazí informace a instalace zkušební verze instance uživatele</li><li>Odeslat hypertextový odkaz na přístup k aplikaci pro uživatele</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%"/><ul><li>Uživatel přistupuje k aplikaci a dokončíte proces jednotného přihlašování</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%"/><ul><li>Uživatel dává souhlasu pro vaši aplikaci</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Přihlášení dokončí a uživatel je přesměrován zpět na webové stránky</li><li>Spuštění uživatelem bezplatné zkušební verze</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Další informace
Další informace o činnosti AppSource zkušební verze najdete v tématu [toto video](https://aka.ms/trialexperienceforwebapps). 
 
## <a name="next-steps"></a>Další kroky

- Další informace o vytváření aplikace, které podporují přihlášení Azure Active Directory, naleznete v části [scénáře ověřování pro Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios) 

- Informace o tom, jak uvedení aplikace SaaS v AppSource, najdete v tématu [informace o partnerovi AppSource](https://appsource.microsoft.com/partners)


## <a name="get-support"></a>Získat podporu
Integrace služby Azure Active Directory, používáme [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource) s komunitou kvůli zajištění podpory. 

Důrazně doporučujeme nejprve vaše dotazy posílejte na Stack Overflow a procházet stávající problémy, které chcete zobrazit, pokud někdo požádal váš dotaz před. Ujistěte se, že jsou označené svoje dotazy nebo připomínky [ `[azure-active-directory]` a `[appsource]` ](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#get-started


<!--Image references-->