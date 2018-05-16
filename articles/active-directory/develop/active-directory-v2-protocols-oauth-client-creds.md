---
title: Používat pro přístup k zabezpečeným prostředkům bez zásahu uživatele Azure AD v2.0 | Microsoft Docs
description: Vytvoření webové aplikace pomocí Azure AD implementace ověřovacího protokolu OAuth 2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
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
ms.openlocfilehash: db466a3ae416c47f86bb66b3bb8ba4bcd7741f5f
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="azure-active-directory-v20-and-the-oauth-20-client-credentials-flow"></a>Azure Active Directory v2.0 a tok přihlašovacích údajů klienta OAuth 2.0
Můžete použít [udělení pověření klienta OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.4) zadaný v dokumentu RFC 6749, někdy označuje jako *s rameny dva OAuth*, přístup k prostředkům hostované webové pomocí identity aplikace. Tento typ udělení běžně se používá pro interakce serveru na server, které musí spustit na pozadí bez okamžitou interakce s uživatelem. Tyto typy aplikací, často se označují jako *démoni* nebo *účtům služby*.

> [!NOTE]
> Koncový bod v2.0 nepodporuje všechny scénáře Azure Active Directory a funkce. Pokud chcete zjistit, zda byste měli používat koncový bod v2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
>
>

Ve více standardních *s rameny tři OAuth*, klientská aplikace je uděleno oprávnění pro přístup k prostředkům jménem konkrétního uživatele. Oprávnění delegovaného od uživatele do aplikace, obvykle během [souhlas](active-directory-v2-scopes.md) procesu. Ale v tok přihlašovacích údajů klienta, jsou udělena oprávnění přímo do vlastní aplikace. Když uvede aplikace token pro prostředek, prostředek vynucuje, které aplikace má oprávnění k provedení akce a není, uživatel má autorizace.

## <a name="protocol-diagram"></a>Diagram protokolu
Tok přihlašovacích údajů celý klienta vypadá podobně jako další diagramu. Kterou jsou popsané všechny kroky později v tomto článku.

![Tok přihlašovacích údajů klienta](../../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Získat přímý autorizaci
Aplikace obvykle obdrží přímá oprávnění pro přístup k prostředku v jednom ze dvou způsobů: prostřednictvím seznam řízení přístupu (ACL) na prostředek, nebo přiřazení oprávnění aplikace v Azure Active Directory (Azure AD). Tyto dvě metody jsou nejobvyklejší ve službě Azure AD, a doporučujeme je pro klienty a prostředky, které provádějí klienta tok přihlašovacích údajů. Autorizovat svým klientům jinými způsoby, ale můžete zvolit prostředku. Každý server prostředků můžete zvolit metodu, která je nejvhodnější pro jeho aplikaci.

### <a name="access-control-lists"></a>Seznamy řízení přístupu
Zprostředkovatel prostředků může vynutit kontrolu autorizace na základě seznamu ID aplikace, který zná a uděluje přístup ke konkrétní úroveň. Pokud prostředek přijímá token z koncového bodu v2.0, může dekódovat token a extrahovat ID klienta aplikace z `appid` a `iss` deklarací identity. Pak porovná aplikace před ACL, který udržuje. Členitosti a metoda seznamu ACL může podstatně liší mezi prostředky.

Běžné případ použití je použije seznam ACL ke spuštění testů pro webovou aplikaci nebo webové rozhraní API. Webové rozhraní API může udělit jenom podmnožinu úplná oprávnění pro konkrétního klienta. Ke spuštění testů začátku do konce na volání rozhraní API, vytvoření testovacího klienta, který získá tokeny z koncového bodu v2.0 a poté je odešle do rozhraní API. Rozhraní API pro ID aplikace testovacího klienta pro úplný přístup k rozhraní API celý funkce zkontroluje seznam řízení přístupu. Pokud použijete tento druh seznamu ACL, nezapomeňte ověřit nejen volajícího `appid` hodnotu. Také ověřte, zda `iss` hodnota tokenu je důvěryhodný.

Tento typ ověřování je běžné démoni a účty služby, které potřebují přístup k datům ve vlastnictví uživatelé příjemce, kteří mají osobní účty Microsoft. Pro data vlastněná organizací doporučujeme, abyste měli nezbytné autorizace prostřednictvím oprávnění k aplikaci.

### <a name="application-permissions"></a>Oprávnění aplikací
Místo použití seznamy ACL, můžete použít rozhraní API ke zveřejnění sadu oprávnění aplikací. Aplikace oprávnění k aplikaci udělují libovolný správce organizace a lze použít pouze pro přístup k datům vlastníkem dané organizace a její zaměstnanci. Například Microsoft Graph zpřístupňuje několik oprávnění aplikací provést následující akce:

* Čtení pošty ve všech poštovních schránkách
* Čtení a zápis pošty ve všech poštovních schránkách
* Odesílání pošty jménem libovolného uživatele
* Čtení dat adresáře

Další informace o aplikaci oprávnění, přejděte na [Microsoft Graph](https://graph.microsoft.io).

Pomocí oprávnění aplikací ve vaší aplikaci, proveďte kroky, které se budeme zabývat v dalších částech.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Žádostí o oprávnění v portálu pro registraci aplikace
1. Přejděte do vaší aplikace v [portálu pro registraci aplikace](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), nebo [vytvořit aplikaci](active-directory-v2-app-registration.md), pokud jste tak ještě neučinili. Budete muset při vytváření aplikace použijte alespoň jeden tajný klíč aplikace.
2. Vyhledejte **Mcrosoft grafu oprávnění** části a poté přidejte **oprávnění aplikací** vyžadující vaší aplikace.
3. **Uložit** registrace aplikací.

#### <a name="recommended-sign-the-user-in-to-your-app"></a>Doporučená: Uživatele přihlaste do aplikace
Obvykle když vytvoříte aplikaci, která používá oprávnění aplikací, aplikace vyžaduje stránku nebo zobrazení, na kterém správce schválí oprávnění aplikace. Tato stránka může být součástí aplikace přihlášení tok, součást aplikace nastavení, nebo může být vyhrazený "připojit" toku. V mnoha případech má smysl pro aplikaci Ukážeme vám to "připojit" Zobrazit pouze po je uživatel přihlášený pomocí pracovního nebo školního účtu Microsoft.

Při přihlášení uživatele k aplikaci, můžete určit organizaci, do které uživatel patří před požádat uživatele ke schválení oprávnění aplikací. I když je to nezbytně nutné, ho můžete vytvořit více intuitivní prostředí pro uživatele. Při přihlášení uživatele ve, postupujte podle našich [v2.0 protokol kurzy](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Požádat správce directory oprávnění
Až budete připraveni s žádostí o oprávnění od správce organizace, můžete přesměruje uživatele na v2.0 *koncový bod admin souhlasu*.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| tenant |Požaduje se |Adresář klienta, který chcete, aby žádala o oprávnění z. To může být ve formátu popisný název nebo identifikátor GUID. Pokud nevíte, které uživatel patří do klienta a chcete, aby mohly přihlásit se pomocí jakékoli klienta, použijte `common`. |
| client_id |Požaduje se |ID aplikace, která [portálu pro registraci aplikace](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) přiřazené vaší aplikaci. |
| redirect_uri |Požaduje se |Identifikátor URI přesměrování místo odpověď k odeslání pro vaši aplikaci pro zpracování. Se musí přesně shodovat s jedním z přesměrování identifikátory URI, který je zaregistrovaný v portálu s tím rozdílem, že musí být kódovaná adresou URL, a může mít segmenty další cesty. |
| state |Doporučené |Hodnota, která je součástí požadavek, který je také vrácený v odpovědi tokenu. Může být řetězec o délce veškerý obsah, který chcete. Stav se používá ke kódování informace o stavu uživatele v aplikaci, než k žádosti o ověření, například stránky nebo zobrazení, které byly na. |

V tomto okamžiku Azure AD vynutí, že pouze správce klienta můžete přihlásit a dokončit žádost. Správce vyzve ke schválení všechna oprávnění přímé aplikace, které jste požadovali pro aplikaci v portálu pro registraci aplikace.

##### <a name="successful-response"></a>Úspěšná odpověď
Pokud správce schválí oprávnění pro vaši aplikaci, úspěšné odpovědi vypadá takto:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Popis |
| --- | --- | --- |
| tenant |Adresář klienta, který aplikace udělena oprávnění, která požadována ve formátu GUID. |
| state |Hodnota, která je součástí požadavek, který je také vrácený v odpovědi tokenu. Může být řetězec o délce veškerý obsah, který chcete. Stav se používá ke kódování informace o stavu uživatele v aplikaci, než k žádosti o ověření, například stránky nebo zobrazení, které byly na. |
| admin_consent |Nastavte na **true**. |

##### <a name="error-response"></a>Chybové odpovědi
Pokud správce nebude schvalovat oprávnění pro aplikace, se nezdařilo odpověď vypadá takto:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Popis |
| --- | --- | --- |
| error |Chybový kód řetězec, který lze použít ke klasifikaci typů chyb, a který můžete použít reagování na chyby. |
| error_description |Konkrétní chybová zpráva, která vám může pomoct určit hlavní příčinu chyby. |

Po přijetí úspěšná odpověď z koncového bodu pro zřizování aplikace je přímé aplikaci oprávnění, která je požadována z vaší aplikace. Nyní můžete požádat o token pro prostředek, který chcete.

## <a name="get-a-token"></a>Získání tokenu
Poté, co jste získali nezbytné autorizace pro aplikace, pokračujte získání přístupových tokenů pro rozhraní API. Chcete-li získat token pomocí klienta udělení pověření, odeslat požadavek POST do `/token` koncového bodu v2.0:

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Nejprve případ: žádosti o token přístupu s sdílený tajný klíč

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| tenant |Požaduje se | Adresář klienta aplikace plánuje fungovat před ve formátu název domény nebo identifikátor GUID. |
| client_id |Požaduje se |ID aplikace, která [portálu pro registraci aplikace](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) přiřazené vaší aplikaci. |
| scope |Požaduje se |Hodnota předaná `scope` parametr v této žádosti o by měl být identifikátor prostředku (aplikace, identifikátor ID URI) prostředku, který chcete, opatřen s `.default` příponu. Například Microsoft Graph hodnota je `https://graph.microsoft.com/.default`. Tato hodnota informuje koncový bod v2.0, že všechny přímé aplikaci oprávnění, které jste nakonfigurovali pro vaši aplikaci, ho měli vydání tokenu pro ty, které jsou přidružené k prostředku, který chcete použít. |
| tajný klíč client_secret |Požaduje se |Tajný klíč aplikace generovaný pro aplikaci v portálu pro registraci aplikace. |
| grant_type |Požaduje se |Musí být `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Druhé případ: tokenu žádosti o přístup pomocí certifikátu

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| tenant |Požaduje se | Adresář klienta aplikace plánuje fungovat před ve formátu název domény nebo identifikátor GUID. |
| client_id |Požaduje se |ID aplikace, která [portálu pro registraci aplikace](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) přiřazené vaší aplikaci. |
| scope |Požaduje se |Hodnota předaná `scope` parametr v této žádosti o by měl být identifikátor prostředku (aplikace, identifikátor ID URI) prostředku, který chcete, opatřen s `.default` příponu. Například Microsoft Graph hodnota je `https://graph.microsoft.com/.default`. Tato hodnota informuje koncový bod v2.0, že všechny přímé aplikaci oprávnění, které jste nakonfigurovali pro vaši aplikaci, ho měli vydání tokenu pro ty, které jsou přidružené k prostředku, který chcete použít. |
| client_assertion_type |povinné |Hodnota musí být `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |povinné | (Webového tokenu JSON) kontrolní výraz, který potřebujete k vytvoření a podepsání s certifikátem, můžete zaregistrovat jako přihlašovací údaje pro vaši aplikaci. Přečtěte si informace o [certifikát přihlašovacích údajů](active-directory-certificate-credentials.md) Další informace o registraci vašeho certifikátu a formát kontrolní výraz.|
| grant_type |Požaduje se |Musí být `client_credentials`. |

Všimněte si, že parametry jsou téměř stejné jako v případě požadavku pomocí sdílený tajný klíč, s tím rozdílem, že parametr tajný klíč client_secret je nahrazena dva parametry: client_assertion_type a client_assertion.

### <a name="successful-response"></a>Úspěšná odpověď
Úspěšná odpověď vypadá takto:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parametr | Popis |
| --- | --- |
| access_token |Požadovaný přístupový token. Aplikace můžete používat tento token k ověření k zabezpečeným prostředkům, například webového rozhraní API. |
| token_type |Určuje hodnotu typ tokenu. Pouze typ, který podporuje Azure AD je `bearer`. |
| expires_in |Jak dlouho přístupový token je platný (v sekundách). |

### <a name="error-response"></a>Chybové odpovědi
Chybnou odpověď vypadá takto:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, které můžete použít ke klasifikaci typů chyb, ke kterým došlo a reagování na chyby. |
| error_description |Konkrétní chybová zpráva, které vám můžou pomoct určit hlavní příčinu chyby ověřování. |
| error_codes |Seznam tokenů zabezpečení specifické chybové kódy, které vám můžou pomoct s diagnostiky. |
| časové razítko |Čas, kdy se stala chyba. |
| trace_id |Jedinečný identifikátor pro požadavek, který vám můžou pomoct s diagnostiky. |
| correlation_id |Jedinečný identifikátor pro požadavek, který může pomoct s diagnostikou mezi komponentami. |

## <a name="use-a-token"></a>Použít token
Teď, když jste získali token, použití tokenu provést žádosti o připojení k prostředku. Když vyprší platnost tokenu, opakovat požadavek `/token` koncový bod získat nový přístupový token.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>Ukázka kódu
Příklad aplikace, implementuje udělení pověření klienta pomocí Správce souhlas koncový bod, naleznete v našem [ukázka kódu démon v2.0](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
