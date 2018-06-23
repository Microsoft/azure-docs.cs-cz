---
title: Seznámení s OAuth2 implicitní tok poskytování ve službě Azure AD | Microsoft Docs
description: Další informace o službě Azure Active Directory provádění OAuth2 implicitní tok, poskytování a jestli je pro vaši aplikaci nejvhodnější.
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
ms.openlocfilehash: 47e4e0d342c0aad7fcc67a91ca0109eed6da9ef5
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317183"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Princip toku implicitní grant OAuth2 v Azure Active Directory (AD)
Implicitní grant OAuth2 je notorious pro probíhá grant s nejdelší seznam zabezpečení se specifikací OAuth2. A ještě, který je implementované ADAL JS a ten, který doporučujeme při psaní aplikací SPA přístup. Co nabízí? Je všechny řádu kompromisy: jak se jím implicitní grant se je nejlepším postupem můžete pokračovat pro aplikace, které využívají webové rozhraní API prostřednictvím jazyka JavaScript v prohlížeči.

## <a name="what-is-the-oauth2-implicit-grant"></a>Co je udělení implicitní OAuth2?
Quintessential [udělení autorizačního kódu OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) je udělení autorizace, který používá dva samostatné koncové body. Koncový bod autorizace se používá pro fázi interakce uživatele, což vede k autorizační kód. Koncový bod token se pak použije klient pro výměnu kód pro přístupový token a často token obnovení. Webové aplikace je potřeba k dispozici vlastní aplikace pověření koncovému bodu tokenu, tak, aby server ověřování můžete ověřit klienta.

[OAuth2 implicitní grant](https://tools.ietf.org/html/rfc6749#section-1.3.2) je varianta jiných uděluje autorizace. Umožňuje klientům získat přístupový token (a požadavku id_token, při použití [OpenId Connect](http://openid.net/specs/openid-connect-core-1_0.html)) přímo z koncového bodu autorizace, bez kontaktování koncový bod token ani ověření klienta. Tento typ variant byl navržený pro JavaScript na základě aplikací, které běží ve webovém prohlížeči: ve specifikaci původní OAuth2 tokeny jsou vráceny ve fragmentu identifikátor URI. Který zpřístupní tokenu bits kód jazyka JavaScript v klientovi, ale zaručuje, že se nebudou zahrnuty do přesměrování směrem k serveru. Vrácení tokeny přes prohlížeč přesměruje přímo z koncového bodu autorizace. Je také výhod odstraňuje všechny požadavky pro křížové počátek volání, které jsou nezbytné, pokud je aplikace JavaScript je nutný k obraťte se na koncový bod tokenu.

Důležitou vlastností objektu implicitní grant OAuth2 je fakt, že například toků nikdy návratový obnovovacích tokenů klienta. V další části ukazuje, jak to není nezbytné a bude ve skutečnosti porušení zabezpečení.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Vhodný scénáře pro udělení implicitní OAuth2
Specifikace OAuth2 deklaruje, že má implicitní grant připravit umožnit aplikacím uživatelského agenta – to znamená aplikací JavaScript spouštění v prohlížeči. Definující typické pro takové aplikace je, že kód jazyka JavaScript se používá pro přístup k prostředkům serveru (obvykle Web API) a příslušným způsobem aktualizuje uživatelské prostředí aplikace. Představte si, že aplikace, jako je z Gmailu nebo Outlook Web Access: Když vyberete zprávy z vaší doručené poště, jenom na panelu zpráv vizualizace změny zobrazíte nové výběr, zatímco zbytek stránky zůstává beze změny. Tato vlastnost je rozdíl od tradičních na základě přesměrování webové aplikace, kde každý interakci s uživatelem výsledkem zpětné volání celou stránku a vykreslování celou stránku odpovědi na nový server.

Aplikace, které potřebují přístup založené na jazyce JavaScript na jeho extreme se nazývají jednostránkové aplikace nebo SPA: cílem je, že tyto aplikace jenom sloužit úvodní stránce HTML a přidružené JavaScript, s všechny následné interakce se řídí webového rozhraní API volání provést prostřednictvím jazyka JavaScript. Ale hybridní přístupy, kdy aplikace je ve většině případů řízené zpětné volání, ale provádí volání příležitostně JS, nejsou – diskuzi o využití implicitní tok je relevantní pro ty také.

Aplikace založené na přesměrování obvykle zabezpečit jejich požadavky pomocí souborů cookie, ale které přístup nefunguje také pro aplikace JavaScript. Soubory cookie fungovat pouze v doméně, které budou byly vygenerovány pro, při volání JavaScriptu může být zaměřený na jiných doménách. Ve skutečnosti, které často bude v případě: Představte si, že aplikace volání rozhraní API Microsoft Graph API, rozhraní API Office, Azure – všechny umístěný mimo doménu z kde zpracovat aplikace. Rostoucí trend pro aplikace JavaScript je, aby žádné back-end, předávající 100 % na třetí strany webovým rozhraním API implementovat funkce své firmy.

V současné době je preferovanou metodu ochrany volání webového rozhraní API pomocí OAuth2 nosiče tokenu přístupu, kde každé volání je přiložena přístupový token OAuth2. Rozhraní Web API prozkoumá příchozí přístupový token, a pokud najde v ní nezbytné obory, udělí přístup k požadované operace. Implicitní tok poskytuje vhodný mechanismus pro aplikace JavaScript získat přístupové tokeny webového rozhraní API, nabízí řadu výhod v souvislosti se soubory cookie:

* Tokeny spolehlivě získáte bez nutnosti křížové počátek volání – povinné registrace přesměrování, které se identifikátor URI, ke kterému jsou tokeny návratový zaručuje, že nejsou posunout tokeny
* JavaScript aplikací můžete získat podle potřeby, pro tolik webové rozhraní API se cíle – bez omezení v doménách přístupové tokeny
* Funkce jazyka HTML5, jako jsou relace nebo místní úložiště poskytují plnou kontrolu nad ukládání tokenu do mezipaměti a správu životního cyklu, zatímco Správa souborů cookie je neprůhledné do aplikace
* Přístupové tokeny nejsou náchylné k útokům padělání (proti útokům CSRF) požadavek webů

Implicitní grant toku nevydává tokeny obnovení, většinou z bezpečnostních důvodů. Token obnovení není v jako přístupových tokenů, udělení podstatně více power proto inflicting podstatně více poškození v případě, že je neuniknou jako úzce oboru. V implicitní tok tokeny se dodávají v adrese URL, proto je vyšší než v udělení autorizačního kódu riziko zachycení.

Aplikace JavaScript však má jiný mechanismus k dispozici pro obnovení přístupových tokenů bez opakovaného výzvy pro uživatele k zadání pověření. Aplikace můžete použít skrytá iframe provést nové žádosti o tokeny na koncový bod autorizace služby Azure AD: tak dlouho, dokud prohlížeče má stále aktivní relace (čtení: obsahuje soubor cookie relace) vůči doméně služby Azure AD, můžete žádost o ověření úspěšně dojít bez nutnosti zásahu uživatele.

Tento model umožňuje aplikace JavaScript nezávisle obnovit přístupové tokeny a i získat nové pro nové rozhraní API (za předpokladu, že uživatel dříve dá souhlas pro ně. Tím je zabráněno přidané zatížení získávání, Správa a ochrana artefaktů vysoké hodnoty například token obnovení. Artefaktu, který umožňuje bezobslužné obnovení souboru cookie relace Azure AD, se spravuje mimo aplikaci. Další výhodou tohoto přístupu je, že uživatel může odhlásit se ze služby Azure AD pomocí některé z aplikací přihlášení k Azure AD, spuštěná v jakémkoli záložkách prohlížeče. Výsledkem je odstranění souboru cookie relace Azure AD a aplikace JavaScript automaticky ztratíte možnost obnovit tokeny pro podepsané na uživatele.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Implicitní grant je vhodný pro mé aplikace?
Implicitní grant představuje další rizik než ostatní uděluje a oblastí, je potřeba věnovat pozornost jsou popsány. Například [zneužití přístup Token zosobnění vlastníka prostředku v implicitní tok] [ OAuth2-Spec-Implicit-Misuse] a [Model hrozeb OAuth 2.0 a aspekty zabezpečení] [ OAuth2-Threat-Model-And-Security-Implications]). Však vyšší riziko profilu je z velké části vzhledem k tomu, že smyslem je aplikace, které spustit kód s aktivní, obsloužených vzdálený prostředek do prohlížeče. Pokud plánujete SPA architekturu, jste žádné součásti back-end nebo volání webového rozhraní API prostřednictvím jazyka JavaScript v úmyslu, se doporučuje použít implicitní tok pro získání tokenu.

Pokud vaše aplikace je nativní klient, není implicitní tok skvělé přizpůsobit. Chybí soubor cookie relace Azure AD v kontextu nativního klienta zbaví aplikace znamená udržovat relaci dlouho povahy. To znamená, vaše aplikace vyzve opakovaně uživatele při získání přístupových tokenů pro nové prostředky.

Pokud vyvíjíte webovou aplikaci, která zahrnuje back-end a využívání rozhraní API z jeho back-end kód, implicitní tok je také vhodné. Další uděluje získáte mnohem více energie. Například udělení pověření klienta OAuth2 poskytuje schopnost získat tokeny, které odráží oprávnění přiřazená samotné, aplikace a delegování uživatele. To znamená, že klient má možnost spravovat i programový přístup k prostředkům, pokud uživatel není aktivně zapojený do relace a tak dále. Nejenom, ale tyto uděluje poskytují vyšší záruky zabezpečení. Například přístupové tokeny nikdy přenosu prostřednictvím prohlížeče uživatele, nemají riziko uloženy v historii prohlížeče a tak dále. Klientská aplikace můžete také provést silné ověřování, žádosti o token.

## <a name="next-steps"></a>Další postup
* Úplný seznam materiály pro vývojáře, včetně referenční informace pro protokoly a ověřování OAuth2 udělit toky podpory Azure AD, najdete v části [Příručka vývojáře pro Azure AD][AAD-Developers-Guide]
* V tématu [postup při integraci aplikace s Azure AD] [ ACOM-How-To-Integrate] pro další hloubku na proces integrace aplikací.

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]: active-directory-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
