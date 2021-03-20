---
title: Microsoft Identity Platform – koncový bod informací | Azure
titleSuffix: Microsoft identity platform
description: Seznamte se s koncovým bodem UserInfo na platformě Microsoft identity.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 3bd0621d607b121f19bc47c717343b8e2e39b04f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98752733"
---
# <a name="microsoft-identity-platform-userinfo-endpoint"></a>Microsoft Identity Platform – UserInfo – koncový bod

Koncový bod UserInfo je součástí [standardu OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC), který je navržený tak, aby vracel deklarace identity pro ověřeného uživatele.  Pro platformu Microsoft identity je koncový bod UserInfo hostovaný na Microsoft Graph ( https://graph.microsoft.com/oidc/userinfo) . 

## <a name="find-the-well-known-configuration-endpoint"></a>Najít známý koncový bod konfigurace

Koncový bod UserInfo můžete programově zjistit pomocí dokumentu zjišťování OpenID Connect v `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` . Je uveden v `userinfo_endpoint` poli a tento model lze použít napříč cloudy, aby ukazoval na správný koncový bod.  Nedoporučujeme v aplikaci pevně zakódovat koncový bod UserInfo – použijte k vyhledání tohoto koncového bodu za běhu dokument zjišťování OIDC.

V rámci specifikace OpenID Connect se koncový bod UserInfo často automaticky volá pomocí [knihoven vyhovujících OIDC](https://openid.net/developers/certified/)  , aby se získaly informace o uživateli.  Bez hostování takového koncového bodu nedodržuje platforma Microsoft Identity Platform standardy a některé knihovny selžou.  V [seznamu deklarací identity identifikovaných ve standardu OIDC](https://openid.net/specs/openid-connect-core-1_0.html#StandardClaims) vytvoříme deklarace identity, deklarace identity subjektu a e-mail, pokud jsou dostupné a přijaté pro.  

## <a name="consider-use-an-id-token-instead"></a>Zvažte možnost použít místo toho token ID.

Informace, které jsou k dispozici v tokenu ID, který může vaše aplikace obdržet, jsou nadmnožinou informací, které může získat z koncového bodu UserInfo.  Vzhledem k tomu, že token ID můžete získat současně, pokud získáte token pro volání koncového bodu UserInfo, doporučujeme použít tento token ID k získání informací o uživateli namísto volání koncového bodu UserInfo.  Pomocí tokenu ID se odstraní jedna ze dvou síťových požadavků od spuštění vaší aplikace, čímž se sníží latence ve vaší aplikaci.

Pokud potřebujete další podrobnosti o uživateli, měli byste zavolat [ `/user` rozhraní API pro Microsoft Graph](/graph/api/user-get) , abyste získali informace, jako je číslo kanceláře nebo název úlohy.   Můžete také použít [volitelné deklarace identity](active-directory-optional-claims.md) , které budou obsahovat další informace o uživateli v ID a přístupové tokeny.

## <a name="calling-the-userinfo-endpoint"></a>Volání koncového bodu UserInfo

UserInfo je standardní rozhraní API nosných tokenů OAuth, které se volá jako jakékoli jiné Microsoft Graph rozhraní API, které používá přístupový token přijatý při získávání tokenu pro Microsoft Graph. Vrátí odpověď JSON obsahující deklarace identity uživatele.

### <a name="permissions"></a>Oprávnění

K volání rozhraní API pro UserInfo použijte následující [OIDC oprávnění](v2-permissions-and-consent.md#openid-connect-scopes) . `openid` je vyžadováno a `profile` `email` obory a zajišťují, že v odpovědi jsou k dispozici další informace.

|Typ oprávnění      | Oprávnění    |
|:--------------------|:---------------------------------------------------------|
|Delegovaný (pracovní nebo školní účet) | OpenID (povinné), profil, e-mail |
|Delegovaný (osobní účet Microsoft) | OpenID (povinné), profil, e-mail |
|Aplikace | Neuvedeno |

> [!TIP]
> Zkopírujte tuto adresu URL do prohlížeče, abyste získali token pro koncový bod UserInfo a také [token ID](id-tokens.md) a nahradili ID klienta a identifikátor URI přesměrování vlastními. Všimněte si, že žádá jenom o obory pro OpenID nebo pro obory grafů a nic jiného.  To je nutné, protože nemůžete požadovat oprávnění pro dva různé prostředky v rámci stejné žádosti o token.
>
> `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=<yourClientID>&response_type=token+id_token&redirect_uri=<YourRedirectUri>&scope=user.read+openid+profile+email&response_mode=fragment&state=12345&nonce=678910`
>
> Tento přístupový token můžete použít v další části.

Stejně jako u jakéhokoli jiného Microsoft Graph tokenu, který zde obdržíte, nemusí být tokenem JWT. Pokud jste se přihlásili pomocí účet Microsoftho uživatele, bude to formát šifrovaného tokenu. Důvodem je to, že Microsoft Graph má speciální vzor pro vystavení tokenu. To nemá vliv na možnost použití přístupového tokenu pro volání koncového bodu UserInfo.

### <a name="calling-the-api"></a>Volání rozhraní API

Rozhraní API pro UserInfo podporuje metodu GET i POST pro OIDC specifikaci.

```http
GET or POST /oidc/userinfo HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6Il…
```

### <a name="userinfo-response"></a>Odpověď na informace o uživateli

```jsonc
{
    "sub": "OLu859SGc2Sr9ZsqbkG-QbeLgJlb41KcdiPoLYNpSFA",
    "name": "Mikah Ollenburg", // names all require the “profile” scope.
    "family_name": " Ollenburg",
    "given_name": "Mikah",
    "email": "mikoll@contoso.com" //requires the “email” scope.
}
```

Deklarace, které jsou tady uvedené, jsou všechny deklarace identity, které může koncový bod UserInfo vracet.  Jedná se o stejné hodnoty, které by aplikace viděli v [tokenu ID](id-tokens.md) vydaném do aplikace.  

## <a name="notes-and-caveats-on-the-userinfo-endpoint"></a>Poznámky a upozornění na koncovém bodu UserInfo

* Pokud chcete tento koncový bod UserInfo volat, musíte použít koncový bod v 2.0.  Pokud používáte koncový bod v 1.0, získáte token pro koncový bod pro UserInfo v 1.0, který je hostovaný na login.microsoftonline.com.  Doporučujeme, aby všechny aplikace a knihovny kompatibilní s OIDC používaly koncový bod v 2.0 k zajištění kompatibility.
* Odpověď z koncového bodu UserInfo se nedá přizpůsobit.  Pokud chcete přizpůsobit deklarace identity, použijte prosím [mapování deklarací identity]( active-directory-claims-mapping.md) a upravte informace vrácené v tokenech.
* Odpověď z koncového bodu UserInfo se nedá přidat do.  Pokud chcete získat další deklarace identity uživatele, použijte prosím [volitelné deklarace identity]( active-directory-optional-claims.md) a přidejte do nich nové deklarace identity.

## <a name="next-steps"></a>Další kroky

* [Kontrola obsahu tokenů ID](id-tokens.md)
* [Přizpůsobení obsahu tokenu ID pomocí volitelných deklarací identity](active-directory-optional-claims.md)
* [Vyžádání přístupového tokenu a tokenu ID pomocí protokolu OAuth2](v2-protocols-oidc.md)
