---
title: Azure Active Directory v2.0 obory, oprávnění a souhlasu | Microsoft Docs
description: Popis autorizace v koncového bodu v2.0 Azure AD, včetně obory, oprávnění a souhlasu.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: celested
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: f001751c9401b88d9bfaf35444882d3d5ccbfef3
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157310"
---
# <a name="scopes-permissions-and-consent-in-the-azure-active-directory-v20-endpoint"></a>Obory, oprávnění a souhlasu v koncového bodu v2.0 Azure Active Directory
Aplikace, které se integrují s Azure Active Directory (Azure AD), postupujte podle modelu autorizace, který nabízí uživatelům kontrolu nad přístupu svá data aplikace. Implementace v2.0 modelu autorizace byl aktualizován a změní způsob, jakým aplikace musí komunikovat s Azure AD. Tento článek se zabývá základními koncepty prostředí tato ověřování modelu, včetně obory, oprávnění a souhlasu.

> [!NOTE]
> Koncový bod v2.0 nepodporuje všechny scénáře Azure Active Directory a funkce. Pokud chcete zjistit, zda byste měli používat koncový bod v2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
>
>

## <a name="scopes-and-permissions"></a>Obory a oprávnění
Azure AD implementuje [OAuth 2.0](active-directory-v2-protocols.md) protokol autorizace. OAuth 2.0 je metoda, pomocí kterého aplikace třetích stran mají přístup k webové hostované prostředkům jménem uživatele. Všechny webové hostované prostředek, který se integruje se službou Azure AD má identifikátor prostředku nebo *identifikátor ID URI aplikace*. Například některé společnosti Microsoft hostuje webové prostředky zahrnují:

* Office 365 Unified e-mailu rozhraní API: `https://outlook.office.com`
* Rozhraní Azure AD Graph API: `https://graph.windows.net`
* Microsoft Graph: `https://graph.microsoft.com`

Totéž platí pro všechny prostředky třetích stran, které mají integrované s Azure AD. Kterýkoli z těchto prostředků také můžete definovat sadu oprávnění, které lze použít k rozdělení na menší bloky dat funkci prostředku. Jako příklad [Microsoft Graph](https://graph.microsoft.io) definovaných oprávnění k provést následující úlohy, mimo jiné:

* Číst kalendář uživatele
* Zápis do kalendáře uživatele
* Odesílání pošty jménem uživatele

Definováním tyto typy oprávnění prostředek má jemně odstupňovanou kontrolu nad jeho data a jak je vystaven data. Aplikace jiných výrobců může požádat o oprávnění z aplikace uživatele. Uživatel aplikaci musí schválit oprávnění, než aplikace může fungovat jménem uživatele. Podle rozdělování funkce prostředku do menší sady oprávnění, se dají vytvářet aplikace jiných výrobců s žádostí o pouze konkrétní oprávnění, které potřebují k provedení jejich funkce. Uživatelé aplikaci můžete věděli, přesně jak aplikace bude používat svá data, a může se jednat o větší jistotu, že aplikace není chovají se zlými úmysly.

Ve službě Azure AD a OAuth, se nazývají tyto typy oprávnění *obory*. Taky někdy označují se jako *oAuth2Permissions*. Obor představuje ve službě Azure AD hodnotu řetězce. Pokračování příkladu Microsoft Graph s, je hodnota oboru pro každé oprávnění:

* Číst kalendář uživatele pomocí `Calendars.Read`
* Zápis do kalendáře uživatele pomocí `Calendars.ReadWrite`
* Odesílat poštu jménem uživatele pomocí podle `Mail.Send`

Aplikace může požádat o tato oprávnění zadáním obory v požadavcích na koncový bod v2.0.

## <a name="openid-connect-scopes"></a>OpenID Connect oborů
Implementace v2.0 OpenID Connect obsahuje několik dobře definovaný obory, které se nevztahují na konkrétní prostředek: `openid`, `email`, `profile`, a `offline_access`.

### <a name="openid"></a>openid
Pokud aplikace provede přihlášení pomocí [OpenID Connect](active-directory-v2-protocols.md), musíte požádat o `openid` oboru. `openid` Oboru se zobrazuje na stránce pracovní účet souhlasu oprávnění "Přihlásit" a na stránku souhlasu účtu Microsoft osobní oprávnění "Zobrazit váš profil a připojení k aplikacím a službám pomocí účtu Microsoft". Aplikace s tímto oprávněním může přijímat jedinečný identifikátor pro uživatele ve formě `sub` deklarací identity. Také nabízí přístup k aplikaci koncovému uživateli. `openid` Oboru lze použít na koncový bod tokenu v2.0 získat tokeny typu ID, které se dají použít k zabezpečení volání protokolu HTTP mezi různými součástmi aplikace.

### <a name="email"></a>e-mail
`email` Oboru lze použít s `openid` oboru a všechny další. Nabízí přístup k aplikaci pro uživatele primární e-mailovou adresu ve tvaru `email` deklarací identity. `email` Deklarace identity je součástí token pouze v případě, že uživatelský účet, který není vždy případě přidružen e-mailovou adresu. Pokud se používá `email` oboru, vaše aplikace by měla připravte se na zpracovat případy, ve kterém `email` deklarace identity neexistuje v tokenu.

### <a name="profile"></a>profil
`profile` Oboru lze použít s `openid` oboru a všechny další. Poskytuje přístup k aplikaci ke vyžadovat značné množství informací o uživateli. Obsahuje informace, které má přístup, ale není omezen na uživatele křestní jméno, příjmení, upřednostňované uživatelské jméno a ID objektu. Úplný seznam profilu deklarace, který je k dispozici v parametru id_tokens pro konkrétního uživatele, najdete v článku [v2.0 tokeny odkaz](active-directory-v2-tokens.md).

### <a name="offlineaccess"></a>offline_access
[ `offline_access` Oboru](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) poskytuje aplikaci přístup k prostředkům jménem uživatele po delší dobu. Na stránce pracovní účet souhlasu tento obor zobrazí jako "Přístup k datům kdykoli" oprávnění. Na osobní stránku účtu Microsoft souhlasu zobrazí se jako "Přístup k informacím kdykoli" oprávnění. Když uživatel schválí `offline_access` oboru, vaše aplikace může přijímat tokeny obnovení z koncového bodu v2.0 tokenu. Tokeny obnovení je dlouhodobé. Aplikaci můžete získat nové přístupové tokeny, protože platnost vyprší starší.

Pokud vaše aplikace neuvede v požadavku `offline_access` oboru, neobdrží obnovovacích tokenů. To znamená, že když uplatnit autorizační kód v [toku kódu autorizace OAuth 2.0](active-directory-v2-protocols.md), dostanete pouze přístupový token z `/token` koncový bod. Přístupový token je platný po krátkou dobu. Přístupový token se obvykle vyprší za jednu hodinu. AT, že bod, vaše aplikace musí přesměruje uživatele zpět na `/authorize` koncový bod pro získání nového kódu autorizace. Během této přesměrování, v závislosti na typu aplikace může uživatel muset znovu zadat své přihlašovací údaje nebo znovu souhlas oprávnění.

Další informace o tom, jak získat a použít tokeny obnovení najdete v tématu [referenci na protokol v2.0](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Vyžaduje souhlas jednotlivé uživatele
V [OpenID Connect nebo OAuth 2.0](active-directory-v2-protocols.md) požádat o autorizaci, aplikace můžete požádat o oprávnění, je nutné pomocí `scope` parametr dotazu. Když se uživatel přihlásí do aplikace, aplikace odešle požadavek jako v následujícím příkladu (pomocí konců řádků přidat pro čitelnost):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

`scope` Parametr je seznam obory, které aplikace požaduje oddělených mezerami. Každý obor je indikován hodnota oboru připojení k prostředku identifikátoru (ID URI aplikace). V příkladu žádosti o aplikace potřebuje oprávnění pro čtení kalendáře uživatele a odesílat poštu jménem uživatele.

Poté, co uživatel zadá své přihlašovací údaje, koncový bod v2.0 vyhledává odpovídající záznam *souhlas uživatele*. Pokud uživatel nebyl žádný z požadovaných oprávnění přijata v minulosti, koncový bod v2.0 požádá uživatele a udělit požadovaná oprávnění.

![Pracovní účet souhlasu](../../media/active-directory-v2-flows/work_account_consent.png)

Když uživatel schválí oprávnění, souhlasu se zaznamenává tak, aby uživatel nemá k souhlas znovu na následující účet přihlášení.

## <a name="requesting-consent-for-an-entire-tenant"></a>Vyžaduje souhlas pro celý klienta
Často organizace zakoupí licence nebo předplatné pro aplikaci, organizace chce plně zřídit aplikace pro své zaměstnance. V rámci tohoto procesu můžete udělit správce pro aplikaci zastupovat každý zaměstnanec souhlas. Pokud správce uděluje souhlas pro celý klienta, nebude organizace zaměstnanci zobrazit stránka s souhlasu pro aplikaci.

Pro vyžádání souhlasu pro všechny uživatele v klientovi, může vaše aplikace používá koncový bod admin souhlasu.

## <a name="admin-restricted-scopes"></a>Správce omezený oborů
Některé vysoká oprávnění v ekosystému Microsoft může být nastaven na *omezený správce*. Příklady těchto druhů obory jsou následující oprávnění:

* Čtení dat adresáře organizace pomocí `Directory.Read`
* Zapisovat data do adresáře organizace pomocí `Directory.ReadWrite`
* Číst pomocí skupin zabezpečení v adresáři organizace `Groups.Read.All`

I když uživatel příjemce může udělit přístup aplikace k tomuto typu dat, jsou omezené na organizační uživatele z udělení přístupu k stejnou sadu citlivá firemní data. Pokud vaše aplikace požaduje přístup k jednomu z těchto oprávnění od organizace uživatele, uživatel obdrží chybovou zprávu s upozorněním, že uživatel nemá oprávnění k souhlas oprávnění vaší aplikace.

Pokud vaše aplikace vyžaduje přístup k omezeným správce obory pro organizace, měli byste požádat o je přímo ze Správce společnosti, také pomocí koncového bodu souhlas správce, který je popsána dále.

Když správce uděluje oprávnění prostřednictvím koncový bod admin souhlasu, je pro všechny uživatele v klientovi udělen souhlas.

## <a name="using-the-admin-consent-endpoint"></a>Pomocí koncový bod admin souhlasu
Pokud budete postupovat podle těchto kroků, vaše aplikace může shromažďovat oprávnění pro všechny uživatele v klientovi, včetně správce omezený obory. Ukázka kódu, který implementuje kroky najdete v sekci [omezený správce obory ukázka](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Žádostí o oprávnění v portálu pro registraci aplikace
1. Přejděte do vaší aplikace v [portálu pro registraci aplikace](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), nebo [vytvořit aplikaci](active-directory-v2-app-registration.md) Pokud jste tak ještě neučinili.
2. Vyhledejte **Microsoft Graph oprávnění** části a poté přidejte oprávnění, která vaše aplikace vyžaduje.
3. Ověřte, že je **Uložit** registrace aplikací.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Doporučená: Uživatele přihlaste do aplikace
Obvykle když vytvoříte aplikaci, která používá koncový bod admin souhlasu, aplikace musí stránku nebo zobrazení, ve kterém můžete schválit správce oprávnění aplikace. Tato stránka může být součástí procesu registrace tok aplikace, součást aplikace nastavení, nebo může být vyhrazený "připojit" toku. V mnoha případech má smysl pro aplikaci Ukážeme vám to "připojit" Zobrazit pouze po je uživatel přihlášený pomocí pracovního nebo školního účtu Microsoft.

Při přihlášení uživatele k aplikaci, můžete určit organizaci, ke kterému patří správce před výzvou ke schválení potřebná oprávnění. I když je to nezbytně nutné, ho můžete vytvořit více intuitivní prostředí pro organizační uživatele. Při přihlášení uživatele ve, postupujte podle našich [v2.0 protokol kurzy](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Požádat správce directory oprávnění
Až budete připraveni s žádostí o oprávnění z správce vaší organizace, můžete přesměruje uživatele na v2.0 *koncový bod admin souhlasu*.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| tenant |Požaduje se |Adresář klienta, který chcete, aby žádala o oprávnění z. Můžete zadat ve formátu popisný název nebo identifikátor GUID nebo obecně odkazovaná adresou "běžné", jak je vidět v příkladu. |
| client_id |Požaduje se |ID aplikace, která [portálu pro registraci aplikace](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) přiřazené vaší aplikaci. |
| redirect_uri |Požaduje se |Identifikátor URI přesměrování místo odpověď k odeslání pro vaši aplikaci pro zpracování. Se musí přesně shodovat s jedním přesměrování identifikátory URI, který je zaregistrovaný v portálu pro registraci aplikace. |
| state |Doporučené |Hodnota, zahrnuté v požadavku, který bude vrácen také v odpovědi tokenu. Může být řetězec o délce požadovaný obsah. Použijte ke kódování informace o stavu uživatele v aplikaci, než k žádosti o ověření, například stránky nebo zobrazení, které byly na stav. |

V tomto okamžiku Azure AD vyžaduje správce klienta se přihlásit k dokončení požadavku. Správce se zobrazí výzva ke schválení všechna oprávnění, které jste požadovali pro aplikaci v portálu pro registraci aplikace.

#### <a name="successful-response"></a>Úspěšná odpověď
Pokud správce schválí oprávnění pro vaši aplikaci, úspěšné odpovědi vypadá takto:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Popis |
| --- | --- | --- |
| tenant |Adresář klienta, který udělena oprávnění, která byla požadována ve formátu GUID aplikace. |
| state |Hodnota, zahrnuté v požadavku, který bude také vrácen v odpovědi tokenu. Může být řetězec o délce požadovaný obsah. Stav se používá ke kódování informace o stavu uživatele v aplikaci, než k žádosti o ověření, například stránky nebo zobrazení, které byly na. |
| admin_consent |Bude nastavena pro **true**. |

#### <a name="error-response"></a>Chybové odpovědi
Pokud správce nebude schvalovat oprávnění pro aplikace, se nezdařilo odpověď vypadá takto:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Popis |
| --- | --- | --- |
| error |Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým došlo a slouží k reagovat na chyby. |
| error_description |Konkrétní chybová zpráva, která může pomoci vývojář určit hlavní příčinu chyby. |

Po přijetí úspěšná odpověď z koncového bodu souhlas správce má oprávnění, která byla požadována získávají aplikace. Dále může požádat o token pro prostředek, který chcete.

## <a name="using-permissions"></a>Použití oprávnění
Jakmile uživatel souhlasí oprávnění pro vaši aplikaci, vaše aplikace můžete získat přístupové tokeny, které představují vaší aplikace oprávnění pro přístup k prostředkům v některé kapacity. Přístupový token lze použít pouze pro jediný zdroj, ale kódovaný uvnitř přístupový token je každý oprávnění, kterému byla udělena vaší aplikace pro tento prostředek. Získat přístupový token aplikace se obrátit na token koncový bod v2.0, například takto:

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Výsledný token přístupu můžete použít v požadavcích HTTP k prostředku. Spolehlivě označuje k prostředku, že aplikace má správná oprávnění k provedení určitého úkolu. 

Další informace o protokolu OAuth 2.0 a jak získat přístupové tokeny, najdete v článku [referenci na protokol koncový bod v2.0](active-directory-v2-protocols.md).
