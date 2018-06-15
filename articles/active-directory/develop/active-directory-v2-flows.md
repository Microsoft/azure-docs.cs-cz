---
title: Typy aplikací pro koncový bod v2.0 Azure Active Directory | Microsoft Docs
description: Typy aplikací a scénáře podporované koncovým bodem v2.0 Azure Active Directory.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 134199c879f6793cb4ed0a88cf0593786341f6d8
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157662"
---
# <a name="app-types-for-the-azure-active-directory-v20-endpoint"></a>Typy aplikací pro koncový bod v2.0 Azure Active Directory
Koncový bod v2.0 Azure Active Directory (Azure AD) podporuje ověřování pro celou řadu architektur moderní aplikace, všechny z nich založené na standardních protokolech [OAuth 2.0 nebo OpenID Connect](active-directory-v2-protocols.md). Tento článek popisuje typy aplikací, které můžete vytvořit pomocí Azure AD v2.0, bez ohledu na to upřednostňovaný jazyk nebo platformu. Informace v tomto článku je navržená tak, které vám pomohou pochopit scénáře vysoké úrovně před [zahájení práce s kódem](active-directory-appmodel-v2-overview.md#getting-started).

> [!NOTE]
> Koncový bod v2.0 nepodporuje všechny scénáře Azure Active Directory a funkce. Pokud chcete zjistit, zda byste měli používat koncový bod v2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
> 
> 

## <a name="the-basics"></a>Základy
Je nutné zaregistrovat každou aplikaci, která používá koncový bod v2.0 v [portálu pro registraci aplikace Microsoft](https://apps.dev.microsoft.com). Proces registrace aplikace shromáždí a přiřadí tyto hodnoty pro svou aplikaci:

* **ID aplikace** jednoznačně identifikuje vaši aplikaci
* A **identifikátor URI pro přesměrování** , můžete použít k cílení odpovědí zpět do aplikace
* Pár dalších hodnot konkrétní scénáře

Další podrobnosti, jak [zaregistrovat aplikaci](active-directory-v2-app-registration.md).

Po registraci aplikace aplikace komunikuje se službou Azure AD pomocí zasílání požadavků do koncového bodu v2.0 Azure AD. Poskytujeme open-source architektury a knihovny, které zpracovávají údaje o tyto požadavky. Máte také možnost implementovat logiku ověřování vytvořením požadavky s těmito koncovými body:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## <a name="web-apps"></a>Webové aplikace
Pro webové aplikace (.NET, PHP, Java, Ruby, Python, uzel) kterým uživatel přistupuje prostřednictvím prohlížeče, můžete použít [OpenID Connect](active-directory-v2-protocols.md) pro přihlášení uživatele. Webové aplikace v OpenID Connect, obdrží ID token. ID token je token zabezpečení, která ověřuje identitu uživatele a poskytuje informace o uživateli ve formě deklarací identity:

```
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Dozvíte se o všech typech tokenů a deklaracích identity, které jsou k dispozici pro aplikaci [v2.0 tokeny odkaz](active-directory-v2-tokens.md).

V serveru webové aplikace trvá tok ověření přihlášení těchto kroků:

![Tok ověřování webové aplikace](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

Zajistíte tím, že ověří ID token pomocí veřejného podpisového klíče přijatého z koncového bodu v2.0 identitu uživatele. Soubor cookie relace je nastavit, které lze použít k identifikaci uživatele požadavky na dalších stránkách.

Pokud chcete zobrazit tento scénář v akci, vyzkoušejte některý z ukázky přihlášení kódu webové aplikace v našem v2.0 [Začínáme](active-directory-appmodel-v2-overview.md#getting-started) části.

Kromě jednoduchého přihlášení může server webové aplikace potřebujete přístup k jiné webové služby, jako je například rozhraní REST API. V takovém případě zapojí aplikace webového serveru v kombinovaném OpenID Connect a OAuth 2.0 toku, pomocí [toku kódu autorizace OAuth 2.0](active-directory-v2-protocols.md). Další informace o tomto scénáři, přečtěte si informace o [Začínáme s webovými aplikacemi a webovým rozhraním API](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>Webová rozhraní API
Koncový bod v2.0 můžete použít k zabezpečení webových služeb, jako je vaše aplikace RESTful webového rozhraní API. Místo ID tokeny a soubory cookie relace webového rozhraní API používá přístupový token OAuth 2.0 pro zabezpečené svoje data a ověřují příchozí požadavky. Volající webového rozhraní API připojí token přístupu v hlavičce autorizace požadavku HTTP, například takto:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Webového rozhraní API používá přístupový token ověřit identitu volajícího a extrahovat informace o volajícím z deklarací identity zakódovaných v tokenu přístupu. Další informace o všech typech tokenů a deklaracích identity, které jsou k dispozici pro aplikace, najdete v článku [v2.0 tokeny odkaz](active-directory-v2-tokens.md).

Webové rozhraní API můžete uživatelům udělit napájení vyjádřit výslovný souhlas nebo vyjádření výslovného nesouhlasu s konkrétní funkce nebo data zveřejněním oprávnění, také známé jako [obory](active-directory-v2-scopes.md). Pro volání aplikaci získat oprávnění k oboru musí uživatel souhlasit do oboru během k toku. Koncový bod v2.0 požádá uživatele o oprávnění a potom zaznamenává oprávnění ve všech přístupové tokeny, které obdrží webového rozhraní API. Rozhraní Web API ověří přístupových tokenů přijetí při každém volání a provádí kontroly autorizace.

Webové rozhraní API může přijímat tokeny přístupu ze všech typů aplikací, včetně webových aplikací serveru, desktop a mobilní aplikace, jednostránkové aplikace, démonů na straně serveru a i další webovým rozhraním API. Podrobný postup pro webového rozhraní API vypadá takto:

![Tok ověření webového rozhraní API](../../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

Chcete-li se dozvědět, jak zabezpečit webové rozhraní API pomocí přístupových tokenů OAuth2, podívejte se na ukázky kódu webového rozhraní API v našich [Začínáme](active-directory-appmodel-v2-overview.md#getting-started) části.

V mnoha případech webové rozhraní API také třeba, aby odchozí požadavky na jiné podřízené webové rozhraní API pro zabezpečené službou Azure Active Directory. Uděláte to tak, webová rozhraní API můžete využít výhod Azure AD **na jménem z** tok, který umožňuje webové rozhraní API pro výměnu příchozí přístupový token pro dalšího přístupového tokenu, který se má použít v odchozí požadavky. V2.0 pro koncový bod jménem postup je popsán v [podrobností zde](active-directory-v2-protocols-oauth-on-behalf-of.md).

## <a name="mobile-and-native-apps"></a>Mobilní a nativní aplikace
Zařízení nainstalované aplikace, jako je například mobilních a desktopových aplikací, často potřebují přístup k back endovým službám nebo webovým rozhraním API, která ukládají data a provádět operace jménem uživatele. Tyto aplikace můžete přidat přihlašování a autorizaci k back endové služby pomocí [toku kódu autorizace OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

V tomto toku aplikace obdrží autorizační kód z koncového bodu v2.0 při přihlášení uživatele. Autorizační kód představuje oprávnění aplikace volat back endové služby jménem uživatele, který je přihlášený. Aplikace může provést výměnu autorizační kód na pozadí pro přístupový token OAuth 2.0 a obnovovací token. Aplikace můžete používat k ověřování k webovým rozhraním API v požadavcích HTTP přístupový token a použít obnovovací token získat nové přístupové tokeny, když vyprší platnost starší přístupové tokeny.

![Tok ověřování nativní aplikace](../../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="single-page-apps-javascript"></a>Jednostránkové aplikace (JavaScript)
Řada moderních aplikací využívá jednostránkový front-end, který je primárně napsané v jazyce JavaScript. Často je zapsán pomocí rozhraní jako AngularJS, Ember.js nebo Durandal.js. Koncový bod v2.0 Azure AD podporuje tyto aplikace pomocí [implicitního toku OAuth 2.0](active-directory-v2-protocols-implicit.md).

V tomto toku aplikace obdrží tokeny přímo z v2.0 zajistí autorizaci koncového bodu, bez jakékoli výměnu serveru na server. Všechny logiku ověřování a relace trvá zpracování umístit zcela v JavaScript klienta, bez přesměrování další stránky.

![Tok implicitní ověřování](../../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

Pokud chcete zobrazit tento scénář v akci, vyzkoušejte některý z ukázky kódu jednostránkové aplikace v našem [Začínáme](active-directory-appmodel-v2-overview.md#getting-started) části.

## <a name="daemons-and-server-side-apps"></a>Démoni a serverové aplikace
Aplikace, které mají dlouho běžící procesy nebo které pracují bez interakce s uživatelem také potřebují způsob, jak přistupovat k zabezpečeným prostředkům, například webovým rozhraním API. Tyto aplikace se můžou ověřovat a získat tokeny pomocí identity aplikace, a nikoli uživatele delegované identity s tok přihlašovacích údajů klienta OAuth 2.0.

V tomto toku aplikace komunikuje přímo s `/token` koncový bod k získání koncové body:

![Démon tok ověřování aplikace](../../media/active-directory-v2-flows/convergence_scenarios_daemon.png)

Vytvořit aplikaci démon, najdete v článku [klienta pověření dokumentaci](active-directory-v2-protocols-oauth-client-creds.md), nebo to zkuste [ukázkové aplikace .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
