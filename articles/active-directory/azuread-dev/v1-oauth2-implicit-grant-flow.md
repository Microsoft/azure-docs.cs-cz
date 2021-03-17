---
title: Princip toku implicitního grantu OAuth2 ve službě Azure AD | Microsoft Docs
description: Přečtěte si další informace o implementaci OAuth2 implicitního grantu a Azure Active Directory o tom, jestli je to pro vaši aplikaci správné.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80154504"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Princip toku implicitního udělení OAuth2 v Azure Active Directory (AD)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Implicitní grant OAuth2 je USTR pro udělení nejdelšího seznamu otázek zabezpečení ve specifikaci OAuth2. A ještě to je přístup implementovaný pomocí ADAL JS a ten, který doporučujeme při psaní aplikací SPA. Co nabízí? To je všechno kompromisy: a jak se to stane, implicitní udělení je nejlepší přístup, který můžete použít pro aplikace, které využívají webové rozhraní API prostřednictvím JavaScriptu z prohlížeče.

## <a name="what-is-the-oauth2-implicit-grant"></a>Co je implicitní grant OAuth2?

[Udělení autorizačního kódu OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) Quintessential je udělení autorizace, která používá dva samostatné koncové body. Koncový bod autorizace se používá pro fázi interakce uživatele, která má za následek autorizační kód. Koncový bod tokenu potom používá klient pro výměnu kódu přístupového tokenu a často také obnovovací token. Webové aplikace jsou nutné k tomu, aby v koncovém bodu tokenu předcházely vlastní přihlašovací údaje aplikace, aby mohl autorizační Server ověřit klienta.

[Implicitní grant OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.2) je variantou dalších autorizačních grantů. Umožňuje klientovi získat přístupový token (a id_token při použití [OpenId Connect](https://openid.net/specs/openid-connect-core-1_0.html)) přímo z koncového bodu autorizace bez kontaktování koncového bodu tokenu ani ověřování klienta. Tato varianta byla navržena pro aplikace založené na jazyce JavaScript běžící ve webovém prohlížeči: v původní specifikaci OAuth2 jsou tokeny vráceny v fragmentu identifikátoru URI. Díky tomu jsou bity tokenů dostupné pro kód jazyka JavaScript v klientovi, ale garantuje, že nebudou zahrnuty do přesměrování směrem k serveru. V OAuth2 implicitní udělení autorizace koncový bod autorizace vystavuje přístup k tokenům přímo klientovi pomocí identifikátoru URI přesměrování, který byl dříve dodán. Má také výhodu eliminace všech požadavků pro volání mezi zdroji, což je nezbytné v případě, že je aplikace jazyka JavaScript požadována pro kontaktování koncového bodu tokenu.

Důležitou vlastností OAuth2 implicitního udělení je skutečnost, že tyto toky nikdy nevrací aktualizační tokeny klientovi. V další části se dozvíte, jak to není nutné a že by ve skutečnosti došlo k potížím se zabezpečením.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Vhodné scénáře pro implicitní udělení OAuth2

Specifikace OAuth2 deklaruje, že implicitní Grant byl vytvořen za účelem povolení aplikací uživatelského agenta – to znamená, že aplikace JavaScriptu spouštěné v prohlížeči. Definováním charakteristických vlastností těchto aplikací je, že kód jazyka JavaScript se používá pro přístup k prostředkům serveru (typicky k webovému rozhraní API) a odpovídajícím způsobem pro aktualizaci uživatelského prostředí aplikace. Můžete si představit aplikace jako Gmail nebo Outlook Web Access: Když vyberete zprávu z doručené pošty, změní se jenom panel vizualizace zprávy, aby se zobrazil nový výběr, zatímco zbytek stránky zůstane beze změny. Tato vlastnost je v kontrastu s tradičními webovými aplikacemi založenými na přesměrování, kde každá interakce uživatele má za následek úplné vystavení stránky a úplné vykreslování nové serverové odpovědi.

Aplikace, které přijímají přístup na základě JavaScriptu na své extrémní, se nazývají jednostránkové aplikace nebo jednostránkové. Nápad je, že tyto aplikace poskytují pouze počáteční stránku HTML a přidružený JavaScript, přičemž všechny následné interakce jsou ovládány voláními webového rozhraní API provedenými prostřednictvím JavaScriptu. Nicméně hybridní přístupy, kde je aplikace většinou řízené postbackem, ale provádí občasné volání JS, nejsou neobvyklá – diskuze o použití implicitního toku je relevantní i pro tyto.

Aplikace založené na přesměrování obvykle zabezpečují své žádosti prostřednictvím souborů cookie, ale tento přístup nefunguje i pro aplikace JavaScriptu. Soubory cookie pracují pouze s doménou, pro kterou byly vygenerovány, zatímco volání JavaScriptu mohou být směrována k ostatním doménám. V takovém případě to často znamená, že aplikace vyvolají rozhraní Microsoft Graph API, rozhraní Office API, rozhraní API Azure – všechny nacházející se mimo doménu, ze kterých se aplikace obsluhuje. Rostoucí trend pro aplikace JavaScriptu nemá žádný back-end. spoléháme se 100% na webová rozhraní API třetích stran a implementujte svou obchodní funkci.

V současné době upřednostňovaná metoda ochrany volání webového rozhraní API je použít přístup k tokenu OAuth2 Bearer, kde každé volání je doprovázeno přístupovým tokenem OAuth2. Webové rozhraní API kontroluje token příchozího přístupu a pokud v něm najde potřebné obory, udělí přístup k požadované operaci. Implicitní tok poskytuje pohodlný mechanismus pro aplikace JavaScriptu k získání přístupových tokenů pro webové rozhraní API, který nabízí mnoho výhod v souvislosti s soubory cookie:

* Tokeny lze spolehlivě získat bez nutnosti pro volání mezi zdroji – povinnou registrací identifikátoru URI přesměrování, na které tokeny vrátí záruky, že tokeny nejsou posunuty.
* Aplikace JavaScriptu můžou získat tolik přístupových tokenů, kolik potřebují, a to u tolika webových rozhraní API, která cílí – bez omezení domén.
* Funkce HTML5, jako je například Session nebo místní úložiště, poskytují úplnou kontrolu nad ukládáním do mezipaměti a správou životnosti tokenů, zatímco Správa souborů cookie je neprůhledná
* Přístupové tokeny nejsou náchylné k útokům prostřednictvím CSRF (mezi lokalitami).

Tok implicitního udělení nevydá aktualizační tokeny, většinou z bezpečnostních důvodů. Obnovovací token není tak Zúžený rozsah jako přístupové tokeny. tím se ještě vyšší výkon, což způsobí mnohem větší škodu pro případ, že dojde k úniku. V implicitním toku jsou tokeny dodány v adrese URL, takže riziko zachycení je vyšší než v udělení autorizačního kódu.

Aplikace JavaScriptu ale má jiný mechanismus pro obnovení přístupových tokenů, aniž by bylo nutné opakovaně vyzvat uživatele k zadání přihlašovacích údajů. Aplikace může pomocí skrytého prvku IFRAME provádět nové žádosti o tokeny proti koncovému bodu autorizace služby Azure AD: Pokud má prohlížeč stále aktivní relaci (čtení: má soubor cookie relace) vůči doméně Azure AD, může k žádosti o ověření dojít úspěšně bez nutnosti zásahu uživatele.

Tento model uděluje aplikaci JavaScriptu možnost nezávisle obnovit přístupové tokeny a dokonce získat nové pro nové rozhraní API (za předpokladu, že ho uživatel dřív poslal). Tím se zabrání zvýšení zátěže, která získává, zachovává a chrání vysoce hodnotový artefakt, jako je aktualizační token. Artefakt, který způsobuje tiché obnovení, je soubor cookie relace služby Azure AD spravován mimo aplikaci. Další výhodou tohoto přístupu je, že se uživatel může odhlásit z Azure AD pomocí kterékoli z aplikací přihlášených do služby Azure AD, které běží na kterékoli z karet prohlížeče. Výsledkem je odstranění souboru cookie relace Azure AD a aplikace JavaScriptu bude automaticky ztratit schopnost obnovit tokeny pro odhlášeného uživatele.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Je implicitní grant vhodný pro moji aplikaci?

Implicitní grant představuje více rizik než jiné dotace a oblasti, které je potřeba věnovat pozornost, jsou dobře zdokumentováné (například [zneužití přístupového tokenu k zosobnění vlastníka prostředku v implicitním toku][OAuth2-Spec-Implicit-Misuse] a v [modelu hrozeb 2,0 OAuth a na zabezpečení][OAuth2-Threat-Model-And-Security-Implications]). Vyšší rizikový profil je však z velké části vzhledem k tomu, že je určeno k povolení aplikací, které spouštějí aktivní kód, obsluhované vzdáleným prostředkem do prohlížeče. Pokud plánujete architekturu SPA, nemáte žádné back-endové součásti ani nechtěli vyvolat webové rozhraní API prostřednictvím JavaScriptu, doporučuje se použít implicitní tok pro získání tokenu.

Pokud je vaše aplikace nativním klientem, implicitní tok se nevejde na skvělé. Neexistence souboru cookie relace Azure AD v kontextu nativního klienta odznamená vaši aplikaci z prostředků údržby dlouhotrvající relace. To znamená, že aplikace bude při získávání přístupových tokenů pro nové prostředky opakovaně vyzvat uživatele.

Pokud vyvíjíte webovou aplikaci, která zahrnuje back-end a spotřebováváte rozhraní API z back-endu kódu, implicitní tok také není vhodný. Další výkon vám poskytne mnohem víc. Například udělení přihlašovacích údajů klienta OAuth2 poskytuje možnost získat tokeny, které odrážejí oprávnění přiřazená samotné aplikaci, a to na rozdíl od delegování uživatelů. To znamená, že klient může spravovat programový přístup k prostředkům i v případě, že uživatel aktivně není v relaci, a tak dále. Nejen to, ale takové granty poskytují vyšší záruky zabezpečení. Přístupové tokeny se nikdy nemigrují přes prohlížeč uživatelů, ale neohrožují uložení v historii prohlížeče atd. Klientská aplikace může také při požadavku na token provádět silné ověřování.

## <a name="next-steps"></a>Další kroky

* Další informace o procesu integrace aplikací najdete v tématu [integrace aplikace se službou Azure AD][ACOM-How-To-Integrate] .

<!--Image references-->

<!--Reference style links in use-->
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: ../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
