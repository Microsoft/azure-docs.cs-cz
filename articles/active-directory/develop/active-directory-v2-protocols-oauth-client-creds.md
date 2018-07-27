---
title: Použít pro přístup k zabezpečeným prostředkům bez zásahu uživatele Azure AD v2.0 | Dokumentace Microsoftu
description: Vytvoření webové aplikace pomocí Azure AD implementaci ověřovacího protokolu OAuth 2.0.
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
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: f406c63ddd326b819219e72ad304d6052c65106d
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39264605"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-client-credentials-flow"></a>Azure Active Directory v2.0 a tok přihlašovacích údajů klienta OAuth 2.0
Můžete použít [udělení přihlašovacích údajů klienta OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.4) podle RFC 6749, říká se jim *OAuth s rameny dvě*, přístup k prostředkům hostované webové pomocí identity aplikace. Tento typ udělení běžně se používá pro interakce – servery, které musí běžet na pozadí bez okamžité interakce s uživatelem. Tyto typy aplikací často označují jako *procesy démon* nebo *účtů služeb*.

> [!NOTE]
> Koncový bod v2.0 nepodporuje všechny scénáře Azure Active Directory a funkce. Pokud chcete zjistit, zda by měl použít koncový bod verze 2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
>
>

Ve více standardních *OAuth s rameny tři*, klientské aplikace má přiděleno oprávnění k přístupu k prostředkům jménem konkrétního uživatele. Oprávnění se deleguje od uživatele k aplikaci, obvykle během [souhlas](active-directory-v2-scopes.md) procesu. Nicméně v tok přihlašovacích údajů klienta, jsou udělena oprávnění přímo do vlastní aplikace. Když aplikace představuje token pro prostředek, prostředek, které vynucuje samotné aplikace nemá autorizaci k provedení akce a ne, který uživatel nemá autorizaci.

## <a name="protocol-diagram"></a>Diagram protokolu
Tok přihlašovacích údajů klienta celý vypadá podobně jako následující diagram. Každý z kroků dále v tomto článku popisujeme.

![Tok přihlašovacích údajů klienta](../../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Získat přímé autorizace
Aplikace obvykle obdrží přímé autorizaci pro přístup k prostředku v jednom ze dvou způsobů: prostřednictvím seznam řízení přístupu (ACL) na prostředek, nebo přiřazení oprávnění aplikace v Azure Active Directory (Azure AD). Tyto dvě metody jsou nejčastěji používaných ve službě Azure AD, a doporučujeme, abyste je pro klienty a prostředky, které provádějí klienta tok přihlašovacích údajů. K autorizaci klientů jinými způsoby, ale můžete zvolte prostředek. Každý prostředek serveru můžete zvolit metodu, která je nejvhodnější pro svou aplikaci.

### <a name="access-control-lists"></a>Seznamy řízení přístupu
Poskytovatel prostředků může vynutit kontrolu autorizace na základě seznamu identifikátorů aplikací, které ví a udělí konkrétní úroveň přístupu k. Pokud prostředek přijímá token z koncového bodu v2.0, může dekódovat token a extrahovat ID klienta aplikace z `appid` a `iss` deklarací identity. Pak porovná aplikace proti seznamu ACL portu, který udržuje. Členitost seznamu řízení přístupu a metoda mezi prostředky výrazně lišit.

Běžným případem použití je použití seznamu ACL portu ke spuštění testů pro webovou aplikaci nebo webové rozhraní API. Webové rozhraní API může udělit jenom určité podmnožiny úplná oprávnění pro konkrétního klienta. Ke spuštění testů začátku do konce pomocí rozhraní API, vytvoření testovací klient, který získá tokeny z koncového bodu v2.0 a odesílá je do rozhraní API. Rozhraní API pak zkontroluje seznam ACL pro ID aplikace testovacího klienta získáte plný přístup k celé funkce rozhraní API. Pokud použijete tento typ seznamu ACL, nezapomeňte ověřit nejen volajícího `appid` hodnotu. Také ověřit, `iss` hodnota tokenu je důvěryhodný.

Tento typ ověřování je běžné, že procesy démon a účty služeb, které potřebují přistup k datům ve vlastnictví uživatelů, kteří mají osobní účty Microsoft. Pro data ve vlastnictví organizace doporučujeme, že vám přinese potřebné autorizace prostřednictvím oprávnění k aplikaci.

### <a name="application-permissions"></a>Oprávnění aplikace
Namísto použití seznamů ACL, můžete použít rozhraní API k vystavení sady oprávnění aplikace. Aplikace oprávnění aplikaci udělují správce ve vaší organizaci a lze použít pouze pro přístup k datům ve vlastnictví organizace a její zaměstnanci. Například Microsoft Graph poskytuje několik oprávnění aplikace provést následující kroky:

* Čtení pošty ve všech poštovních schránkách
* Čtení a zápis pošty ve všech poštovních schránkách
* Odesílání pošty jménem libovolného uživatele
* Čtení dat adresáře

Další informace o oprávněních aplikace, přejděte na [Microsoft Graphu](https://graph.microsoft.io).

Pokud chcete použít oprávnění aplikací ve vaší aplikaci, proveďte kroky, které si popíšeme v další části.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Požádat o oprávnění v portálu pro registraci aplikace
1. Přejděte na svoji aplikaci [portál pro registraci aplikací](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), nebo [vytvořit aplikaci](active-directory-v2-app-registration.md), pokud jste tak již neučinili. Budete muset použít aspoň jeden tajný klíč aplikace při vytváření vaší aplikace.
2. Vyhledejte **oprávnění Microsoft Graphu** a pak přidejte **oprávnění aplikace** , která vaše aplikace vyžaduje.
3. **Uložit** registraci aplikace.

#### <a name="recommended-sign-the-user-in-to-your-app"></a>Doporučeno: Přihlášení uživatele do vaší aplikace
Obvykle když vytvoříte aplikaci, která používá oprávnění aplikace, aplikace vyžaduje stránku nebo zobrazení schválí její oprávnění správce. Na této stránce můžou být součástí aplikace přihlásit flow součástí nastavení aplikace, nebo může být vyhrazený tok "připojení". V mnoha případech je vhodné pro aplikace, aby to předvedli "připojení" Zobrazit pouze poté, co uživatel má přihlášení pomocí pracovního nebo školního účtu Microsoft.

Pokud přihlášení uživatele k aplikaci, můžete určit organizaci, do které uživatel patří před požádat uživatele ke schválení oprávnění aplikace. Ačkoli to není nezbytně nutné, pomůže vám vytvořit intuitivnější prostředí pro vaše uživatele. Přihlášení uživatele, postupujte podle našich [v2.0 protokol kurzy](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Požádat o oprávnění od správce adresáře
Jakmile budete připraveni k žádosti o oprávnění od správce organizace, můžete přesměruje uživatele v2.0 *koncový bod souhlas správce*.

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
| tenant |Požaduje se |Tenantu Active directory, kterou chcete požádat o oprávnění. To může být ve formátu popisný název nebo identifikátor GUID. Pokud neznáte kterého tenanta uživatel patří do a chcete, aby mohly přihlásit pomocí žádného tenanta, použijte `common`. |
| client_id |Požaduje se |ID aplikace, které [portál pro registraci aplikací](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) přiřazené vaší aplikaci. |
| redirect_uri |Požaduje se |Identifikátor URI pro přesměrování místo, kam chcete odpověď k odeslání pro vaši aplikaci ke zpracování. Musí přesně odpovídat jeden z identifikátorů URI, které jste zaregistrovali na portálu pro přesměrování s tím rozdílem, že ho musí mít kódování URL a může mít další segmenty. |
| state |Doporučené |Hodnota, která je zahrnutá v požadavku, který je vrácený v odpovědi tokenu. Může být řetězec s žádný obsah, který chcete. Stav se používá ke kódování informace o stavu uživatele v aplikaci předtím, než požadavek na ověření došlo k chybě, například stránky nebo zobrazení, které byly na. |

V tomto okamžiku vynucuje Azure AD, že pouze správce tenanta můžete přihlásit k dokončení požadavku. Správce bude požádán o schválení všechna oprávnění aplikace s přímým přístupem, které jste si vyžádali pro vaši aplikaci v portálu pro registraci aplikace.

##### <a name="successful-response"></a>Úspěšné odpovědi
Pokud správce schválí oprávnění pro vaši aplikaci, úspěšné odpovědi vypadá takto:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Popis |
| --- | --- | --- |
| tenant |Tenantu Active directory, která oprávnění aplikace, které požadovány ve formátu GUID. |
| state |Hodnota, která je zahrnutá v požadavku, který je vrácený v odpovědi tokenu. Může být řetězec s žádný obsah, který chcete. Stav se používá ke kódování informace o stavu uživatele v aplikaci předtím, než požadavek na ověření došlo k chybě, například stránky nebo zobrazení, které byly na. |
| admin_consent |Nastavte na **true**. |

##### <a name="error-response"></a>Odpověď na chybu
Pokud správce neschvaluje oprávnění pro vaši aplikaci, neúspěšnou odpověď vypadá takto:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Popis |
| --- | --- | --- |
| error |Řetězec kódu chyby, které lze použít ke klasifikaci typy chyb, a které vám umožní reagovat na chyby. |
| error_description |Určité chybové zprávě, které vám pomohou identifikovat hlavní příčinu chyby. |

Po přijetí úspěšné odpovědi z koncového bodu aplikace zřizování aplikace získalo přímo použít oprávnění, která je požadovaná. Teď můžete požádat o token pro prostředek, který chcete.

## <a name="get-a-token"></a>Získání tokenu
Poté, co pro vaši aplikaci, které jste získali nezbytné autorizace, pokračujte získání přístupových tokenů pro rozhraní API. Chcete-li získat token pomocí klienta udělování přihlašovacích údajů, odešlete požadavek POST do `/token` koncový bod verze 2.0:

### <a name="first-case-access-token-request-with-a-shared-secret"></a>První věc: žádost o přístupový token s sdílený tajný klíč

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
| tenant |Požaduje se | Tenantu Active directory aplikace v plánu fungovat proti ve formátu název domény nebo identifikátor GUID. |
| client_id |Požaduje se |ID aplikace, které [portál pro registraci aplikací](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) přiřazené vaší aplikaci. |
| scope |Požaduje se |Hodnota předaná `scope` parametr v této žádosti by měl být identifikátor prostředku (identifikátor URI ID aplikace) požadovaný prostředek, označeny `.default` příponu. Například Microsoft Graphu, je hodnota `https://graph.microsoft.com/.default`. Tato hodnota informuje koncového bodu v2.0, že všechny aplikace s přímým přístupem oprávnění, které jste nakonfigurovali pro vaše aplikace, ji by měl vystavit token pro ty, které jsou přidružené k prostředku, který chcete použít. |
| Hodnota client_secret |Požaduje se |Tajný klíč aplikace, kterou jste vygenerovali pro vaši aplikaci v portálu pro registraci aplikace. Tajný kód klienta musí být kódovaná adresou URL před odesláním.|
| Parametr grant_type |Požaduje se |Musí být `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Druhý případ: žádost o přístupový token s certifikátem

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
| tenant |Požaduje se | Tenantu Active directory aplikace v plánu fungovat proti ve formátu název domény nebo identifikátor GUID. |
| client_id |Požaduje se |ID aplikace, které [portál pro registraci aplikací](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) přiřazené vaší aplikaci. |
| scope |Požaduje se |Hodnota předaná `scope` parametr v této žádosti by měl být identifikátor prostředku (identifikátor URI ID aplikace) požadovaný prostředek, označeny `.default` příponu. Například Microsoft Graphu, je hodnota `https://graph.microsoft.com/.default`. Tato hodnota informuje koncového bodu v2.0, že všechny aplikace s přímým přístupem oprávnění, které jste nakonfigurovali pro vaše aplikace, ji by měl vystavit token pro ty, které jsou přidružené k prostředku, který chcete použít. |
| client_assertion_type |povinné |Hodnota musí být `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |povinné | (JSON Web Token) kontrolního výrazu, které potřebujete k vytvoření a podepsání certifikátem zaregistrujete jako přihlašovací údaje pro vaši aplikaci. Přečtěte si informace o [certifikát přihlašovacích údajů](active-directory-certificate-credentials.md) informace o registraci vašeho certifikátu a formát kontrolního výrazu.|
| Parametr grant_type |Požaduje se |Musí být `client_credentials`. |

Všimněte si, že parametry jsou téměř stejné jako v případě žádosti sdílený tajný klíč, s tím rozdílem, že parametr hodnota client_secret nahrazuje dva parametry: client_assertion_type a client_assertion.

### <a name="successful-response"></a>Úspěšné odpovědi
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
| access_token |Požadovaný přístupový token. Aplikace můžete používat tento token k ověření k zabezpečeným prostředkům, jako například webové rozhraní API. |
| token_type |Určuje hodnotu pro typ tokenu. Jediný typ, který podporuje Azure AD je `bearer`. |
| expires_in |Jak dlouho je přístupový token platný (v sekundách). |

### <a name="error-response"></a>Odpověď na chybu
Chybová odpověď vypadá takto:

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
| error |Řetězec kódu chyby, můžete použít ke klasifikaci typy chyb, ke kterým dochází a reagovat na chyby. |
| error_description |Určité chybové zprávě, která vám můžou pomoct identifikovat hlavní příčinu chyby ověřování. |
| error_codes |Seznam tokenů zabezpečení specifické chybové kódy, které mohly pomoct s diagnostikou. |
| časové razítko |Čas, kdy došlo k chybě. |
| trace_id |Jedinečný identifikátor pro žádosti, které můžou pomoct s diagnostikou. |
| correlation_id |Jedinečný identifikátor pro žádosti, které můžou pomoct s diagnostikou komponentami. |

## <a name="use-a-token"></a>Použít token
Teď, když jste získali token, pomocí tokenu prostředku provádět požadavky. Když vyprší platnost tokenu, opakovat žádost o `/token` koncový bod získat nový přístupový token.

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
Příklad aplikace, že implementuje udělení přihlašovacích údajů klienta s použitím správce souhlas koncového bodu, najdete v našich [ukázku kódu v2.0 démon](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
