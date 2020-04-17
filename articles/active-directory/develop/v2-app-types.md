---
title: Typy aplikací pro platformu microsoft identit | Azure
description: Typy aplikací a scénářů podporované koncovým bodem platformy Microsoft identit (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: bdbda8bed38819ca2b4d2fb1ef3d9bf591269890
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535906"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Typy aplikací pro platformu identit Microsoftu

Koncový bod platformy Microsoft identit (v2.0) podporuje ověřování pro celou řadu moderních architektur aplikací, všechny jsou založeny na standardních protokolech [OAuth 2.0 nebo OpenID Connect](active-directory-v2-protocols.md). Tento článek popisuje typy aplikací, které můžete vytvářet pomocí platformy identit Microsoftu, bez ohledu na preferovaný jazyk nebo platformu. Tyto informace jsou navrženy tak, aby vám pomohly porozumět scénářům na vysoké úrovni před [zahájením práce s kódem](v2-overview.md#getting-started).

> [!NOTE]
> Koncový bod platformy identit Microsoftu nepodporuje všechny scénáře a funkce Azure Active Directory (Azure AD). Chcete-li zjistit, zda byste měli používat koncový bod platformy identit společnosti Microsoft, přečtěte si o [omezení platformy identit společnosti Microsoft](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Základy

Každou aplikaci, která používá koncový bod platformy identit Microsoftu, musíte zaregistrovat na novém [portálu pro registraci aplikací](https://go.microsoft.com/fwlink/?linkid=2083908). Proces registrace aplikace shromažďuje a přiřazuje vám tyto hodnoty:

* **ID aplikace (klienta),** které jednoznačně identifikuje vaši aplikaci
* Identifikátor **URI přesměrování,** který můžete použít k přímému vrácení odpovědí do aplikace
* Několik dalších hodnot specifických pro scénář, jako jsou podporované typy účtů

Podrobnosti najdete v tom, jak [zaregistrovat aplikaci](quickstart-register-app.md).

Po registraci aplikace komunikuje s platformou identit microsoftu odesláním požadavků do koncového bodu. Poskytujeme open source architektury a knihovny, které zpracovávají podrobnosti o těchto požadavcích. Máte také možnost implementovat logiku ověřování sami vytvořením požadavků na tyto koncové body:

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Jednostránkové aplikace (JavaScript)

Mnoho moderních aplikací má jednostránkový front-end aplikace, který je primárně napsán v JavaScriptu. Často je napsán pomocí rozhraní jako Angular, React nebo Vue. Koncový bod platformy identit y Microsoft podporuje tyto aplikace pomocí [implicitního toku OAuth 2.0](v2-oauth2-implicit-grant-flow.md).

V tomto toku aplikace přijímá tokeny přímo z platformy identit microsoftu autorizovat koncový bod, bez jakékoli výměny mezi servery. Veškerá logika ověřování a zpracování relací probíhá výhradně v klientovi JavaScriptu bez dalších přesměrování stránek.

![Zobrazuje tok implicitního ověřování.](./media/v2-app-types/convergence-scenarios-implicit.svg)

Chcete-li zobrazit tento scénář v akci, zkuste jeden z ukázky kódu jednostránkové aplikace v [platformě identit Microsoft začínáme.](v2-overview.md#getting-started)

## <a name="web-apps"></a>Webové aplikace

Pro webové aplikace (.NET, PHP, Java, Ruby, Python, Node), ke kterým uživatel přistupuje prostřednictvím prohlížeče, můžete pro přihlášení uživatele použít [OpenID Connect.](active-directory-v2-protocols.md) V OpenID Connect získá webová aplikace token ID. Token ID je token zabezpečení, který ověřuje identitu uživatele a poskytuje informace o uživateli ve formě deklarací identity:

```JSON
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

Další podrobnosti o různých typech tokenů používaných v koncovém bodě platformy identity Microsoft jsou k dispozici v odkazu na [přístupový token](access-tokens.md) a [id_token odkaz](id-tokens.md)

V aplikacích webového serveru trvá tok ověřování přihlášení těmito kroky vysoké úrovně:

![Zobrazuje tok ověřování webové aplikace.](./media/v2-app-types/convergence-scenarios-webapp.svg)

Identitu uživatele můžete zajistit ověřením tokenu ID veřejným podpisovým klíčem, který je přijat z koncového bodu platformy identity Microsoftu. Je nastaven soubor cookie relace, který lze použít k identifikaci uživatele na následujících požadavcích na stránku.

Chcete-li zobrazit tento scénář v akci, zkuste jeden z ukázky kódu přihlášení webové aplikace v [části začínáme platformy identit Microsoftu.](v2-overview.md#getting-started)

Kromě jednoduchého přihlášení může být nutné, aby aplikace webového serveru měla přístup k jiné webové službě, například k rozhraní REST API. V tomto případě se aplikace webového serveru zabývá kombinovaným tokem OpenID Connect a OAuth 2.0 pomocí [toku autorizačního kódu OAuth 2.0](active-directory-v2-protocols.md). Další informace o tomto scénáři [načtete o tom, jak začít s webovými aplikacemi a webovými api](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>Webová rozhraní API

Koncový bod platformy identit Microsoftu můžete použít k zabezpečení webových služeb, jako je například webové rozhraní API RESTful vaší aplikace. Webová api lze implementovat v mnoha platformách a jazycích. Můžou být taky implementovat pomocí aktivačních událostí HTTP ve funkcích Azure. Místo tokenů ID a souborů cookie relace používá webové rozhraní API přístupový token OAuth 2.0 k zabezpečení dat a ověřování příchozích požadavků. Volající webového rozhraní API připojí přístupový token v hlavičce autorizace požadavku HTTP, například takto:

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Webové rozhraní API používá přístupový token k ověření identity volajícího rozhraní API a k extrahování informací o volajícím z deklarací, které jsou kódovány v přístupovém tokenu. Další podrobnosti o různých typech tokenů používaných v koncovém bodě platformy identity Microsoft jsou k dispozici v odkazu na [přístupový token](access-tokens.md) a [odkaz id_token.](id-tokens.md)

Webové rozhraní API může uživatelům poskytnout možnost vyjádřit se k odhlášení nebo odhlášení z konkrétních funkcí nebo dat vystavením oprávnění, označovaných také jako [obory](v2-permissions-and-consent.md). Aby volající aplikace získala oprávnění k oboru, musí uživatel souhlasit s oborem během toku. Koncový bod platformy identit y Microsoft požádá uživatele o oprávnění a pak zaznamená oprávnění ve všech přístupových tokenech, které webové rozhraní API obdrží. Webové rozhraní API ověří přístupové tokeny, které obdrží při každém volání, a provede kontroly autorizace.

Webové rozhraní API může přijímat přístupové tokeny ze všech typů aplikací, včetně aplikací webového serveru, aplikací pro stolní počítače a mobilních zařízení, jednostránkových aplikací, daemonů na straně serveru a dokonce i dalších webových rozhraní API. Tok vysoké úrovně pro webové rozhraní API vypadá takto:

![Zobrazuje tok ověřování webového rozhraní API.](./media/v2-app-types/convergence-scenarios-webapi.svg)

Chcete-li se dozvědět, jak zabezpečit webové rozhraní API pomocí přístupových tokenů OAuth2, podívejte se na ukázky kódu webového rozhraní API v části [Začínáme na platformě identit microsoftu.](v2-overview.md#getting-started)

V mnoha případech webová rozhraní API také potřebují provádět odchozí požadavky na další podřízená webová rozhraní API zabezpečená platformou identit microsoftu. Chcete-li tak učinit, webová rozhraní API můžete využít **toku on-behalf-of,** který umožňuje webové rozhraní API k výměně příchozí přístupový token pro jiný přístupový token, který má být použit v odchozích požadavků. Další informace naleznete [v tématu Microsoft identity platformy a OAuth 2.0 On-Behalf-Of toku](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Mobilní a nativní aplikace

Aplikace nainstalované zařízením, jako jsou mobilní a desktopové aplikace, často potřebují přístup ke službám back-end nebo webovým rozhraním API, která ukládají data a provádějí funkce jménem uživatele. Tyto aplikace můžete přidat přihlášení a autorizaci back-endové služby pomocí [toku autorizačního kódu OAuth 2.0](v2-oauth2-auth-code-flow.md).

V tomto toku aplikace obdrží autorizační kód z koncového bodu platformy identit y Microsoft, když se uživatel přihlásí. Autorizační kód představuje oprávnění aplikace k volání back-endových služeb jménem přihlášeného uživatele. Aplikace může vyměnit autorizační kód na pozadí za přístupový token OAuth 2.0 a obnovovací token. Aplikace můžete použít přístupový token k ověření webových api v požadavcích HTTP a použít obnovovací token získat nové přístupové tokeny, když vyprší platnost starších přístupových tokenů.

![Zobrazuje tok ověřování nativní aplikace.](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Daemons a aplikace na straně serveru

Aplikace, které mají dlouhotrvající procesy nebo které pracují bez interakce s uživatelem, také potřebují způsob, jak získat přístup k zabezpečeným prostředkům, jako jsou webová rozhraní API. Tyto aplikace můžete ověřit a získat tokeny pomocí identity aplikace, nikoli delegované identity uživatele, s tokpověření klienta OAuth 2.0. Identitu aplikace můžete prokázat pomocí tajného klíče klienta nebo certifikátu. Další informace naleznete v tématu [.NET Core daemon console aplikace pomocí platformy microsoft identity .](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)

V tomto toku aplikace interaguje přímo s koncovým `/token` bodem získat přístup:

![Zobrazuje tok ověřování aplikace daemonu.](./media/v2-app-types/convergence-scenarios-daemon.svg)

Chcete-li vytvořit aplikaci pro daemon, přečtěte si [dokumentaci k pověření klienta](v2-oauth2-client-creds-grant-flow.md)nebo vyzkoušejte [ukázkovou aplikaci .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
