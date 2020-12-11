---
title: Typy aplikací pro Microsoft Identity Platform | Azure
description: Typy aplikací a scénářů podporované koncovým bodem platformy Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev, fasttrack-edit, contperf-fy21q2
ms.openlocfilehash: fd1fc59fd1ade6036c57f15415afccfc693f7bff
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029749"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Typy aplikací pro platformu Microsoft Identity Platform

Koncový bod platformy Microsoft identity podporuje ověřování pro celou řadu moderních architektur aplikací, které jsou založené na standardních protokolech [OAuth 2,0 nebo OpenID Connect](active-directory-v2-protocols.md). Tento článek popisuje typy aplikací, které můžete sestavit pomocí platformy Microsoft Identity Platform, bez ohledu na preferovaný jazyk nebo platformu. Tyto informace jsou navržené tak, aby vám pomohly pochopit scénáře vysoké úrovně předtím, než začnete pracovat s kódem ve [scénářích aplikací](authentication-flows-app-scenarios.md#application-scenarios).

## <a name="the-basics"></a>Základy

Každou aplikaci, která používá koncový bod Microsoft Identity Platform, je nutné zaregistrovat v [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908)Azure Portal. Proces registrace aplikace shromažďuje a přiřazuje tyto hodnoty pro vaši aplikaci:

* **ID aplikace (klienta)** , které jedinečně identifikuje vaši aplikaci
* **Identifikátor URI pro přesměrování** , který můžete použít k přímému směrování odpovědí zpět do vaší aplikace
* Několik dalších hodnot specifických pro konkrétní scénář, jako jsou například podporované typy účtů

Další podrobnosti najdete v článku o [registraci aplikace](quickstart-register-app.md).

Po registraci aplikace komunikuje s platformou Microsoft identity pomocí odeslání požadavků do koncového bodu. Poskytujeme Open Source architektury a knihovny, které zpracovávají podrobnosti o těchto žádostech. Také máte možnost implementovat logiku ověřování sami, a to vytvořením požadavků na tyto koncové body:

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Jednostránkové aplikace (JavaScript)

Mnohé moderní aplikace mají front-end samoobslužnou aplikaci vytvořenou hlavně v JavaScriptu, často i v rámci architektury, jako je například úhlová, reakce nebo Vue. Služba Microsoft Identity Platform Endpoint podporuje tyto aplikace pomocí protokolu [OpenID Connect](v2-protocols-oidc.md) pro ověřování a buď [toku OAuth 2,0 implicitního udělení](v2-oauth2-implicit-grant-flow.md) identity, nebo novějšího [autorizačního kódu OAuth 2,0 + PKCE toku](v2-oauth2-auth-code-flow.md) pro autorizaci (viz níže).

Flowový diagram níže znázorňuje udělení autorizačního kódu OAuth 2,0 (s podrobnostmi o PKCE vynechán), kde aplikace přijímá kód z koncového bodu Microsoft Identity Platform `authorize` a uplatňuje ho pro tokeny a aktualizační tokeny pomocí webových požadavků pro různé weby. Obnovovací token vyprší každých 24 hodin a aplikace musí požádat o jiný kód. Kromě přístupového tokenu, `id_token` který představuje přihlášeného uživatele ke klientské aplikaci, se obvykle také požaduje prostřednictvím stejného toku nebo samostatné žádosti OpenID Connect (tady není zobrazená).

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="Diagram znázorňující tok autorizačního kódu OAuth 2 mezi jednostránkovou aplikací a koncovým bodem služby tokenu zabezpečení." border="false":::

Pokud se chcete podívat na tento scénář v akci, podívejte se na [kurz: přihlášení uživatelů a volání rozhraní API Microsoft Graph z JavaScript Spa pomocí toku kódu ověřování](tutorial-v2-javascript-auth-code.md).

### <a name="authorization-code-flow-vs-implicit-flow"></a>Tok autorizačního kódu vs. implicitní tok

Pro většinu historie OAuth 2,0 byl [implicitní tok](v2-oauth2-implicit-grant-flow.md) doporučeným způsobem pro vytváření jednostránkovéch aplikací. S odebráním [souborů cookie třetích stran](reference-third-party-cookies-spas.md) a [větší pozornost](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14) placeným proti problémům se zabezpečením v rámci implicitního toku jsme přešli na tok autorizačního kódu pro jednostránkové aplikace.

Aby se zajistila kompatibilita vaší aplikace v Safari a dalších prohlížečích s vědomím ochrany osobních údajů, už nedoporučujeme používat implicitní tok a místo toho doporučujeme tok autorizačního kódu.

## <a name="web-apps"></a>Webové aplikace

Pro webové aplikace (.NET, PHP, Java, Ruby, Python, Node), ke kterým uživatel přistupuje přes prohlížeč, můžete k přihlášení uživatele použít [OpenID Connect](active-directory-v2-protocols.md) . V OpenID Connect webová aplikace obdrží token ID. Token ID je token zabezpečení, který ověřuje identitu uživatele a poskytuje informace o uživateli v podobě deklarací identity:

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

Další podrobnosti o různých typech tokenů použitých v koncovém bodu platformy Microsoft Identity Platform jsou k dispozici v odkazu [přístupového tokenu](access-tokens.md) a v [id_token odkazech](id-tokens.md) .

V aplikacích webového serveru přebírá tok ověřování při přihlašování tyto kroky vysoké úrovně:

![Zobrazuje tok ověřování webové aplikace.](./media/v2-app-types/convergence-scenarios-webapp.svg)

Identitu uživatele můžete zajistit ověřením tokenu ID pomocí veřejného podpisového klíče, který se přijímá z koncového bodu Microsoft Identity Platform. Je nastavený soubor cookie relace, který se dá použít k identifikaci uživatele na dalších požadavcích na stránku.

Chcete-li zobrazit tento scénář v akci, vyzkoušejte ukázky kódu ve [webové aplikaci, která se přihlásí ke scénáři uživatelů](scenario-web-app-sign-user-overview.md).

Kromě jednoduchého přihlašování může aplikace webového serveru vyžadovat přístup k jiné webové službě, jako je například REST API. V takovém případě se aplikace webového serveru zavazuje do kombinovaného toku OpenID Connect a OAuth 2,0 pomocí [toku autorizačního kódu oauth 2,0](v2-oauth2-auth-code-flow.md). Další informace o tomto scénáři najdete v článku [Začínáme s webovými aplikacemi a webovými rozhraními API](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIDConnect-DotNet).

## <a name="web-apis"></a>Webová rozhraní API

Pomocí koncového bodu Microsoft Identity Platform můžete zabezpečit webové služby, například webové rozhraní API RESTful vaší aplikace. Webová rozhraní API je možné implementovat na různých platformách a jazycích. Můžete je taky implementovat pomocí triggerů HTTP v Azure Functions. Místo tokenů ID a souborů cookie relací používá webové rozhraní API přístupový token OAuth 2,0 k zabezpečení svých dat a ověřování příchozích požadavků. Volající webového rozhraní API připojí přístupový token v autorizační hlavičce požadavku HTTP, třeba takto:

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Webové rozhraní API používá přístupový token k ověření identity volajícího rozhraní API a extrakci informací o volajícím z deklarací identity, které jsou zakódované v přístupovém tokenu. Další podrobnosti o různých typech tokenů použitých v koncovém bodu platformy Microsoft Identity Platform jsou k dispozici v odkazu [přístupového tokenu](access-tokens.md) a v referenčních informacích o [id_token](id-tokens.md) .

Webové rozhraní API může uživatelům povolit, aby se mohli rozhodnout nebo odhlásit konkrétní funkce nebo data tím, že zveřejňuje oprávnění, označované taky jako [obory](v2-permissions-and-consent.md). Aby volající aplikace získala oprávnění k oboru, uživatel musí během toku souhlasit s rozsahem. Koncový bod platformy Microsoft Identity žádá uživatele o oprávnění a pak zaznamená oprávnění ve všech přístupových tokenech, které webové rozhraní API obdrží. Webové rozhraní API ověřuje přístupové tokeny, které přijímá při každém volání, a provádí kontroly autorizace.

Webové rozhraní API může přijímat přístupové tokeny ze všech typů aplikací, včetně aplikací webového serveru, desktopových a mobilních aplikací, aplikací s jednou stránkou, démonů na straně serveru a i dalších webových rozhraní API. Tok vysoké úrovně webového rozhraní API vypadá takto:

![Zobrazuje tok ověřování webového rozhraní API.](./media/v2-app-types/convergence-scenarios-webapi.svg)

Pokud chcete zjistit, jak zabezpečit webové rozhraní API pomocí přístupových tokenů OAuth2, podívejte se na ukázky kódu webového rozhraní API ve [scénáři chráněného webového rozhraní API](scenario-protected-web-api-overview.md).

V mnoha případech musí webová rozhraní API také vytvářet odchozí požadavky na jiná podřízená webová rozhraní API zabezpečená platformou Microsoft identity. K tomu může webová rozhraní API využívat **výhod toku,** který umožňuje webovému rozhraní API vyměňovat příchozí přístupový token pro jiný přístupový token, který se má použít v odchozích požadavcích. Další informace najdete v tématu [Flow Microsoft Identity Platform a OAuth 2,0](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Mobilní a nativní aplikace

Aplikace nainstalované v zařízení, například mobilní a desktopové aplikace, často potřebují přístup k back-endové službě nebo webovým rozhraním API, která ukládají data a vykonávají funkce jménem uživatele. Tyto aplikace můžou do back-endové služby přidat přihlášení a autorizaci pomocí [toku autorizačního kódu OAuth 2,0](v2-oauth2-auth-code-flow.md).

V tomto toku aplikace získá autorizační kód z koncového bodu Microsoft Identity Platform, když se uživatel přihlásí. Autorizační kód představuje oprávnění aplikace pro volání back-endové služby jménem uživatele, který je přihlášený. Aplikace může vyměňovat autorizační kód na pozadí pro přístupový token OAuth 2,0 a obnovovací token. Aplikace může pomocí přístupového tokenu ověřit u webových rozhraní API v požadavcích HTTP a pomocí aktualizačního tokenu získat nové přístupové tokeny, když vyprší platnost starších přístupových tokenů.

![Zobrazuje tok ověřování nativní aplikace.](./media/v2-app-types/convergence-scenarios-native.svg)

> [!NOTE]
> Pokud aplikace používá výchozí systémové WebView, Projděte si informace o možnosti "potvrdit moje přihlášení" a kód chyby AADSTS50199 ve [službě Azure AD ověřování a kódy chyb autorizace](reference-aadsts-error-codes.md).

## <a name="daemons-and-server-side-apps"></a>Démoni a aplikace na straně serveru

Aplikace, které mají dlouhotrvající procesy nebo které fungují bez interakce s uživatelem, potřebují také způsob, jak přistupovat k zabezpečeným prostředkům, například k webovým rozhraním API. Tyto aplikace můžou ověřovat a získávat tokeny pomocí identity aplikace namísto delegované identity uživatele, a to pomocí toku přihlašovacích údajů klienta OAuth 2,0. Identitu aplikace můžete prokázat pomocí tajného klíče klienta nebo certifikátu. Další informace najdete v tématu [aplikace konzoly .NET Core daemon pomocí platformy Microsoft Identity Platform](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2).

V tomto toku aplikace komunikuje přímo s `/token` koncovým bodem a získá přístup:

![Zobrazuje tok ověřování aplikace démona.](./media/v2-app-types/convergence-scenarios-daemon.svg)

Pokud chcete vytvořit aplikaci démona, přečtěte si [dokumentaci k přihlašovacím údajům klienta](v2-oauth2-client-creds-grant-flow.md)nebo vyzkoušejte [ukázkovou aplikaci .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).

## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámeni s typy aplikací, které podporuje platforma Microsoft identity, se dozvíte víc o protokolech [OAuth 2,0 a OpenID Connect](active-directory-v2-protocols.md) , abyste získali informace o součástech protokolu používaných různými scénáři.
