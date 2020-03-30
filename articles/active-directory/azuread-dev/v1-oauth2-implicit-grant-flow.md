---
title: Principy OAuth2 implicitní grantový tok ve službě Azure AD | Dokumenty společnosti Microsoft
description: Přečtěte si další informace o implementaci implicitního grantového toku OAuth2 službou Azure Active Directory a o tom, jestli je to vhodné pro vaši aplikaci.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 08/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: eaa3844bfbbef8cb71dbe8691cab894c921ce00a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154504"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Principy implicitního grantového toku OAuth2 ve službě Azure Active Directory (AD)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Implicitní grant OAuth2 je známý tím, že je grantem s nejdelším seznamem bezpečnostních problémů ve specifikaci OAuth2. A přesto, že je přístup implementovaný ADAL JS a ten doporučujeme při psaní SPA aplikací. Co dává? Je to všechno otázka kompromisů: a jak se ukázalo, implicitní grant je nejlepší přístup, který můžete sledovat pro aplikace, které spotřebovávají webové api prostřednictvím JavaScriptu z prohlížeče.

## <a name="what-is-the-oauth2-implicit-grant"></a>Co je implicitní grant OAuth2?

Typický [maještní kód OAuth2 udělení autorizačníkód](https://tools.ietf.org/html/rfc6749#section-1.3.1) je udělení autorizace, který používá dva samostatné koncové body. Koncový bod autorizace se používá pro fázi interakce uživatele, což má za následek autorizační kód. Koncový bod tokenu se pak používá klientem pro výměnu kódu pro přístupový token a často také obnovovací token. Webové aplikace jsou povinny prezentovat vlastní pověření aplikace do koncového bodu tokenu, aby autorizační server mohl ověřit klienta.

[Implicitní grant OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.2) je variantou jiných povolení. Umožňuje klientovi získat přístupový token (a id_token, při použití [OpenId Connect](https://openid.net/specs/openid-connect-core-1_0.html)) přímo z koncového bodu autorizace, bez kontaktování koncového bodu tokenu ani ověření klienta. Tato varianta byla navržena pro aplikace založené na JavaScriptu spuštěné ve webovém prohlížeči: v původní specifikaci OAuth2 jsou tokeny vráceny v fragmentu URI. To zpřístupní bity tokenu kódu JavaScript u klienta, ale zaručí, že nebudou zahrnuty do přesměrování směrem k serveru. V implicitním udělení OAuth2 koncový bod autorizace vydává přístupové tokeny přímo klientovi pomocí identifikátoru URI přesměrování, který byl dříve dodán. Má také tu výhodu, že eliminuje všechny požadavky na volání mezi původem, které jsou nezbytné, pokud je aplikace JavaScript vyžadována ke kontaktování koncového bodu tokenu.

Důležitou charakteristikou implicitního grantu OAuth2 je skutečnost, že tyto toky nikdy nevrátí tokeny aktualizace klientovi. Další část ukazuje, jak to není nutné a ve skutečnosti by se bezpečnostní problém.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Vhodné scénáře pro implicitní grant OAuth2

Specifikace OAuth2 prohlašuje, že implicitní grant byl navržen tak, aby umožňoval aplikace user-agent - to znamená, javascriptové aplikace spouštěné v prohlížeči. Definující charakteristikou těchto aplikací je, že kód JavaScript se používá pro přístup k prostředkům serveru (obvykle webové rozhraní API) a pro odpovídajícím způsobem aktualizaci uživatelského prostředí aplikace. Přemýšlejte o aplikacích, jako je Gmail nebo Outlook Web Access: když vyberete zprávu z doručené pošty, změní se pouze panel vizualizace zpráv, aby se zobrazil nový výběr, zatímco zbytek stránky zůstane nezměněn. Tato charakteristika je v kontrastu s tradičními webovými aplikacemi založenými na přesměrování, kde každá interakce uživatele vede k zpětnému odeslání celé stránky a vykreslování nové odpovědi na server na celé stránce.

Aplikace, které se javascript založený přístup k jeho extrému se nazývají jednostránkové aplikace, nebo SPA. Myšlenka spočine na to, že tyto aplikace slouží pouze počáteční stránce HTML a přidruženému JavaScriptu, přičemž všechny následné interakce jsou řízeny voláním webového rozhraní API prováděnými prostřednictvím JavaScriptu. Hybridní přístupy, kde je aplikace většinou řízena postbackem, ale provádí příležitostné hovory JS, však nejsou neobvyklé – diskuse o implicitním využití toku je relevantní i pro ty.

Aplikace založené na přesměrování obvykle zabezpečují své požadavky prostřednictvím souborů cookie, tento přístup však nefunguje stejně dobře pro aplikace JavaScript. Soubory cookie fungují pouze proti doméně, pro kterou byly vygenerovány, zatímco volání javascriptu mohou směřovat do jiných domén. Ve skutečnosti to bude často případ: představte si aplikace s vyvoláním rozhraní Microsoft Graph API, rozhraní API Office, rozhraní Azure API – všechny s bydlištěm mimo doménu, ze které se aplikace obsluhuje. Rostoucí trend pro javascriptové aplikace je mít žádný back-end vůbec, spoléhat se 100% na webová api třetích stran k implementaci jejich obchodní funkce.

V současné době upřednostňovanou metodou ochrany volání webového rozhraní API je použití přístupu tokenu nosiče OAuth2, kde je každé volání doprovázeno přístupovým tokenem OAuth2. Webové rozhraní API zkontroluje příchozí přístupový token a pokud v něm najde potřebné obory, udělí přístup k požadované operaci. Implicitní tok poskytuje pohodlný mechanismus pro javascriptové aplikace pro získání přístupových tokenů pro webové rozhraní API, které nabízejí řadu výhod, pokud jde o soubory cookie:

* Tokeny lze spolehlivě získat bez nutnosti volání křížového původu – povinná registrace identifikátoru URI přesměrování, ke kterému jsou tokeny vráceny, zaručuje, že tokeny nejsou posunuty
* Aplikace JavaScriptu mohou získat tolik přístupových tokenů, kolik potřebují, pro tolik webových api, na které cílí – bez omezení domén
* Funkce HTML5, jako je relace nebo místní úložiště, poskytují plnou kontrolu nad ukládáním tokenů do mezipaměti a správou životnosti, zatímco správa souborů cookie je pro aplikaci neprůhledná
* Přístupové tokeny nejsou náchylné k útokům padělání požadavků na příčové stránky (CSRF)

Implicitní grantový tok nevydává tokeny aktualizace, většinou z bezpečnostních důvodů. Obnovovací token není tak úzce vymezenjako přístupové tokeny, což poskytuje mnohem větší moc, a tím způsobuje mnohem větší škody v případě, že je unikly. V implicitním toku jsou tokeny doručovány v adrese URL, proto je riziko zachycení vyšší než v udělení autorizačního kódu.

Aplikace JavaScript má však jiný mechanismus k dispozici pro obnovení přístupových tokenů bez opakovaného dotazování uživatele na pověření. Aplikace můžete použít skrytý iframe k provádění nových požadavků na tokeny proti koncovému bodu autorizace Azure AD: pokud prohlížeč má stále aktivní relaci (čti: má soubor cookie relace) proti doméně Azure AD, požadavek na ověření může úspěšně dojít bez nutnosti interakce s uživatelem.

Tento model uděluje aplikaci JavaScript možnost nezávisle obnovovat přístupové tokeny a dokonce získat nové pro nové rozhraní API (za předpokladu, že uživatel s nimi dříve souhlasil). Tím se zabrání další zátěž získání, údržbu a ochranu artefakt vysoké hodnoty, jako je například token aktualizace. Artefakt, který umožňuje tiché obnovení, soubor cookie relace Azure AD, se spravuje mimo aplikaci. Další výhodou tohoto přístupu je, že se uživatel může odhlásit z Azure AD pomocí libovolné aplikace přihlašované do Služby Azure AD, které běží na kterékoli záložce prohlížeče. To má za následek odstranění souboru cookie relace Azure AD a aplikace JavaScript automaticky ztratí možnost obnovit tokeny pro odpojiného uživatele.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Je implicitní grant vhodný pro mou aplikaci?

Implicitní grant představuje více rizik než jiné granty a oblasti, které je třeba věnovat pozornost, jsou dobře zdokumentovány (například [zneužití přístupového tokenu k zosobnění vlastníka prostředků v implicitním toku][OAuth2-Spec-Implicit-Misuse] a [OAuth 2.0 Threat Model a bezpečnostní aspekty][OAuth2-Threat-Model-And-Security-Implications]). Vyšší rizikový profil je však z velké části způsobeno skutečností, že je určen k povolení aplikací, které spouštějí aktivní kód, obsluhovaný vzdáleným prostředkem do prohlížeče. Pokud plánujete architekturu SPA, nemáte žádné back-endové komponenty nebo chcete vyvolat webové rozhraní API prostřednictvím JavaScriptu, doporučujeme použít implicitní tok pro získání tokenu.

Pokud je vaše aplikace nativní klient, implicitní tok není skvělé přizpůsobení. Absence souboru cookie relace Azure AD v kontextu nativního klienta zbavuje vaši aplikaci prostředků pro údržbu relace s dlouhou životností. To znamená, že vaše aplikace bude opakovaně vyzvat uživatele při získávání přístupových tokenů pro nové prostředky.

Pokud vyvíjíte webovou aplikaci, která obsahuje back-end a spotřebovává rozhraní API z jeho back-endového kódu, implicitní tok také není vhodné. Jiné granty vám dávají mnohem větší moc. Například udělení pověření klienta OAuth2 poskytuje možnost získat tokeny, které odrážejí oprávnění přiřazená samotné aplikaci, na rozdíl od delegování uživatelů. To znamená, že klient má možnost udržovat programový přístup k prostředkům i v případě, že uživatel není aktivně zapojen do relace a tak dále. Nejen to, ale takové granty poskytují vyšší bezpečnostní záruky. Například přístupové tokeny nikdy neprocházejí uživatelským prohlížečem, neriskují, že budou uloženy v historii prohlížeče a tak dále. Klientská aplikace může také provádět silné ověřování při požadování tokenu.

## <a name="next-steps"></a>Další kroky

* Další hloubku procesu integrace aplikací najdete [v tématu Jak integrovat aplikaci s Azure AD.][ACOM-How-To-Integrate]

<!--Image references-->

<!--Reference style links in use-->
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: ../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
