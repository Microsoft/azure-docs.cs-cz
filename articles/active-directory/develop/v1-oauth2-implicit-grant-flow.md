---
title: Principy OAuth2 implicitní tok poskytování ve službě Azure AD | Dokumentace Microsoftu
description: Přečtěte si další informace o Azure Active Directory implementace protokolu oauth2 implicitní tok, poskytování a zda je pro vaši aplikaci nejvhodnější.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 90e42ff9-43b0-4b4f-a222-51df847b2a8d
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 22413684678cddc1a86f6acbe203b0041a4c6818
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581260"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Pochopení toku implicitní grant OAuth2 v Azure Active Directory (AD)
Implicitní grant OAuth2 je notorious za poskytnutí s nejdelší seznam zabezpečením ve specifikaci OAuth2. A ještě, je přístup pomocí knihovny ADAL JS a ten, který doporučujeme při psaní aplikace SPA implementovat. Co nabízí? Je všechny na vás kompromisy: a jak ukázalo se, implicitního udělení, je nejlepším přístupem může zneužít pro aplikace, které využívají webové rozhraní API prostřednictvím JavaScriptu v prohlížeči.

## <a name="what-is-the-oauth2-implicit-grant"></a>Co je implicitní grant OAuth2?
Quintessential [udělení autorizačního kódu OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) je udělení autorizace, která používá dva samostatné koncové body. Koncový bod autorizace se používá pro fázi interakce uživatelů, což vede k autorizační kód. Koncový bod tokenu se pak použije klient pro výměnu kód pro přístupový token a často token obnovení. Webové aplikace jsou potřeba prezentovat svoje vlastní přihlašovací údaje aplikace koncovému bodu tokenu, tak, aby autorizační server můžete ověřit klienta.

[Implicitní grant OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.2) je varianta jiných udělení autorizace. Umožňuje klientům získat přístupový token (a id_token, při použití [OpenId Connect](http://openid.net/specs/openid-connect-core-1_0.html)) přímo z koncového bodu autorizace, bez kontaktování koncovému bodu tokenu ani ověření klienta. Tato varianta je určená pro aplikace běžící ve webovém prohlížeči založené na jazyce JavaScript: v původní specifikace OAuth2 tokeny jsou vráceny ve fragmentu identifikátoru URI. Díky tomu se token bits k dispozici pro kód jazyka JavaScript v klientovi, ale zaručuje, že by se nezahrnuly do přesměrování směrem k serveru. Vrací tokeny přes prohlížeč přesměruje přímo z koncového bodu autorizace. Má také využívat odstranění případné požadavky pro různé zdroje volání, které jsou nutné v případě aplikací jazyka JavaScript je potřeba kontaktovat koncový bod tokenu.

Důležitou vlastnost implicitní grant OAuth2 je skutečnost, že tyto toky nikdy návratový obnovovací tokeny do klienta. V další části ukazuje, jak to není nezbytné a by ve skutečnosti potíže se zabezpečením.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Vhodné scénáře pro implicitní grant OAuth2
Specifikace OAuth2 deklaruje, že má implicitní grant navržen umožnit aplikacím uživatelský agent – to znamená aplikacím JavaScript provádění v prohlížeči. Charakteristickým takové aplikace je, že kód jazyka JavaScript se používá pro přístup k prostředkům serveru (obvykle webové rozhraní API) a odpovídajícím způsobem aktualizuje uživatelské prostředí aplikace. Představte si, že aplikace, jako je Outlook Web Access nebo Gmailu: při výběru zprávy z vaší doručené pošty, pouze na panelu zpráv vizualizace změní zobrazíte nový výběr, zatímco zbytek stránky zůstává beze změny. Tato vlastnost je rozdíl od tradičních na základě přesměrování webových aplikací, kde každá interakce uživatele výsledkem postbacku celé stránky a vykreslování celá stránka odpovědi na nový server.

Aplikace, které budou založené na jazyce JavaScript přístup k jeho extreme se nazývají jednostránkové aplikace nebo SPA: cílem je, že tyto aplikace pouze předložit úvodní stránku HTML a přidružené JavaScript, všechny následné interakce se využitím webového rozhraní API volání se provádí prostřednictvím JavaScriptu. Ale hybridním přístupům, kde aplikace je ve většině případů řízené zpětné volání, ale nahrazuje příležitostné volání JS, nejsou – diskuzi o používání implicitní tok je relevantní pro ty také.

Aplikace založené na přesměrování obvykle zabezpečit jejich požadavky pomocí souborů cookie, ale které přístup nefunguje i pro aplikace JavaScript. Soubory cookie fungovat jenom v doméně, které se byly vygenerovány pro, zatímco volání JavaScriptu může být zaměřený na jiných domén. Ve skutečnosti, které často bude v případě: Představte si, že volání rozhraní API Microsoft Graph API, rozhraní API Office, Azure – všechny umístěných mimo doménu, ze kterého je aplikace poskytovány aplikací. Rostoucí trend pro aplikace JavaScript je nechat jakéhokoli back-endu, předávající 100 % na třetí strany webová rozhraní API k implementaci jejich obchodní funkce.

V současné době je upřednostňovanou metodou ochrany volání webového rozhraní API pomocí OAuth2 nosný token přístupu, kde jsou všechna volání doplněny přístupového tokenu OAuth2. Webové rozhraní API zkontroluje příchozí přístupový token, a pokud najde v něm nezbytné obory, udělí přístup k požadované operace. Implicitní tok poskytuje vhodný mechanismus pro aplikace JavaScript získat přístupové tokeny pro webové rozhraní API, nabízí řadu výhod v souvislosti se soubory cookie:

* Tokeny můžete spolehlivě získat bez nutnosti pro různé zdroje volání – povinné registrace přesměrování, na které identifikátor URI, na který jsou tokeny návratový zaručuje, že nejsou vytiskne tokeny
* JavaScript aplikace můžete získat podle potřeby, pro libovolný počet rozhraní Web API cílovou – bez omezení na domény přístupové tokeny
* Funkce HTML5, jako jsou relace nebo místní úložiště poskytují plnou kontrolu nad ukládání tokenu do mezipaměti a správa životního cyklu, zatímco Správa souborů cookie je neprůhledný do aplikace
* Napadnutelné přes bezpečnostní vůči útokům padělání (CSRF) žádosti více webů nejsou přístupové tokeny

Implicitní grant toku bez vyvolání tokeny obnovení, většinou z bezpečnostních důvodů. Obnovovací token není v jako přístupové tokeny, poskytování mnohem větší výkon, proto inflicting mnohem více poškození v případě, že je neuniknou oboru podle přesně. Implicitní tok tokeny se dodávají v adrese URL, a proto riziko zachycení vyšší než v udělení autorizačního kódu.

Aplikace v jazyce JavaScript však má jiný mechanismus k dispozici pro obnovení přístupových tokenů bez opakovaného výzvy k zadání přihlašovacích údajů. Aplikace skrytý element iframe slouží k provádění žádostí o nový token na koncový bod autorizace Azure AD: jako prohlížeč stále obsahuje aktivní relaci (čtení: má soubor cookie relace) vůči doméně Azure AD, můžete žádost o ověření úspěšně dojít bez nutnosti zásahu uživatele.

Tento model umožňuje aplikaci JavaScript nezávisle obnovit přístupové tokeny a dokonce i získat nové značky pro nové rozhraní API (za předpokladu, že uživatel už odsouhlasený. pro ně. Tím se vyhnete přidání zatížení získává, údržbu a ochraně cenných artefaktů, například obnovovací token. Artefakt, který umožňuje bezobslužné obnovení souboru cookie relace Azure AD se spravuje mimo aplikaci. Další výhodou tohoto přístupu je, že uživatel může odhlásit se ze služby Azure AD, pomocí některé z aplikací, přihlášení k Azure AD běžící ve všech záložkách prohlížeče. Výsledkem je odstranění souboru cookie relace Azure AD a jazyka JavaScript aplikace automaticky nebudete už moct obnovit tokeny pro podepsané si uživatele.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Implicitní grant je vhodný pro moji aplikaci?
Implicitní grant představuje další rizik než ostatní udělí a jsou popsány oblasti je potřeba věnovat pozornost. Například [zneužití přístupový Token k vydávat se za vlastníka prostředku v implicitní tok] [ OAuth2-Spec-Implicit-Misuse] a [Model hrozeb OAuth 2.0 a důležité informace o zabezpečení] [ OAuth2-Threat-Model-And-Security-Implications]). Ale vyšší riziko profilu je do značné míry vzhledem k tomu, že je určená k umožňují aplikacím, které jsou spouštěny aktivním kódem, obsluhuje vzdálený prostředek do prohlížeče. Pokud plánujete SPA architekturu, jste žádné součásti back-end nebo úmyslu vyvolat webové rozhraní API prostřednictvím JavaScriptu, doporučujeme použít implicitní tok pro získání tokenu.

Pokud je vaše aplikace nativního klienta, implicitní tok se skvěle hodí. Neexistence souboru cookie relace Azure AD v rámci nativního klienta zbaví aplikace znamená, že zachování dlouhodobá relace. To znamená, že vaše aplikace opakovaně, vyzve uživatele při získávání přístupových tokenů pro nové prostředky.

Pokud vyvíjíte webové aplikace, která zahrnuje back-endu a využívání rozhraní API z jeho kód back-endu, implicitní tok je také vhodné. Ostatní udělí vám poskytují mnohem větší výkon. Například udělování přihlašovacích údajů klienta OAuth2 poskytuje možnost získat tokeny, které odpovídají oprávnění přiřazená k delegování uživatelů na rozdíl od vlastní, aplikace. To znamená, že klient má možnost spravovat i programový přístup k prostředkům, když uživatel není aktivně zapojený do relace a tak dále. Nejen to, ale takové uděluje poskytují vyšší záruky zabezpečení. Například přístupové tokeny nikdy přenosu přes prohlížeč uživatel, nemáte rizika, neuloží se do prohlížeče historie a tak dále. Klientská aplikace můžete také provést silné ověřování při vyžádání tokenu.

## <a name="next-steps"></a>Další postup
* Úplný seznam prostředků pro vývojáře, včetně referenční informace pro protokoly a autorizaci OAuth2 poskytnutí podpory toky službou Azure AD, najdete [příručce vývojáře v Azure AD][AAD-Developers-Guide]
* Zobrazit [jak integrovat aplikace s Azure AD] [ ACOM-How-To-Integrate] pro další podrobnosti o procesu integrace aplikace.

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]:azure-ad-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
