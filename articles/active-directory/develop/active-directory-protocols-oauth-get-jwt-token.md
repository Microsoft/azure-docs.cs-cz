---
title: Ověření ve službě Azure AD a získá token JWT, a pomocí OAuth 2.0
description: Příklad kódu ukazuje, jak ověřování pomocí Azure Active Directory pomocí OAuth 2.0 pro přístup k zabezpečené webové aplikace a webová rozhraní API ve vaší organizaci.
services: active-directory
author: rloutlaw
manager: angerobe
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: routlaw
ms.custom: aaddev
ms.openlocfilehash: f4bcbb2064a8f7fcdfd2002d2e8d6e0c4f679ffc
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902905"
---
# <a name="how-to-request-an-access-token-using-oauth-20-to-access-web-apis-and-applications-secured-by-azure-ad"></a>Postupy: žádost o přístupový token pro přístup k webovým rozhraním API a aplikace zabezpečené pomocí Azure AD pomocí OAuth 2.0

Tento článek ukazuje, jak získat JSON Web Token (JWT) pro přístup k prostředkům zabezpečené pomocí Azure AD. Předpokládá, že máte [autorizační token](/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code) z uživateli uděleno oprávnění nebo prostřednictvím [instanční objekt služby](/azure/active-directory/develop/active-directory-application-objects).

## <a name="build-the-request"></a>Žádost o sestavení

Pomocí následujících `POST` požadavek HTTP se získat token JWT přístup konkrétní aplikaci nebo rozhraní API zabezpečené pomocí Azure AD.

```http
POST https://{tenant}/oauth2/v2.0/token?client_id={client-id}
&scope={scope}
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh   
```

### <a name="request-headers"></a>Hlavičky požadavku

Vyžadují se následující hlavičky:

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
| *Hostitel:* | https://login.microsoftonline.com |
| *Typ obsahu:*| Application/x--www-form-urlencoded |

### <a name="uri-parameters"></a>Parametry identifikátoru URI

| Parametr     |                       | Popis                                                                                                                                                                                                                                                                                                                                                                                                                                |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| tenant        | povinné              | `{tenant}` Hodnota v cestě požadavku je možné řídit, kdo se můžete přihlásit do aplikace. Povolené hodnoty jsou `common`, `organizations`, `consumers`a identifikátorů klienta. Další podrobnosti najdete v části [protokol Základy](active-directory-v2-protocols.md#endpoints).                                                                                                                                                   |
| client_id     | povinné              | ID aplikace, které na portál pro registraci ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) přiřazené vaší aplikaci.                                                                                                                                                                                                                             |
| Parametr grant_type    | povinné              | Musí být `authorization_code` pro tok autorizačního kódu.                                                                                                                                                                                                                                                                                                                                                                            |
| scope         | povinné              | Seznam oborů oddělených mezerami. Obory požadovaný v této fáze musí být ekvivalentní, nebo podmnožina oborů ve volání `/authorize`. Pokud span obory zadané v této žádosti více prostředků serveru, koncový bod verze 2.0 vrátí token pro prostředek určený v rámci první. Podrobnější vysvětlení oborů, najdete v tématu [oprávnění, vyjádření souhlasu a obory](v2-permissions-and-consent.md). |
| kód          | povinné              | Authorization_code, který jste získali při volání funkce `/authorize`                                                                                                                                                                                                                                                                                                                                                                |
| redirect_uri  | povinné              | Stejné redirect_uri hodnota, která byla použita k získání authorization_code.                                                                                                                                                                                                                                                                                                                                                             |
| Hodnota client_secret | vyžaduje se pro webové aplikace | Tajný klíč aplikace, kterou jste vytvořili v portálu pro registraci aplikace pro vaši aplikaci. Nepoužívejte v nativní aplikaci, protože client_secrets nemůže být spolehlivě uložená na zařízeních. Vyžaduje se pro webové aplikace a webová rozhraní API, které se budou moct bezpečně uložit hodnotu client_secret na straně serveru.  Tajné klíče klienta musí být kódovaná adresou URL před odesláním.                                                                                 |
| code_verifier | nepovinné              | Stejné code_verifier, který se používá k získání authorization_code. Povinné, pokud PKCE použila žádost o udělení autorizace kódu. Další informace najdete v tématu [PKCE RFC](https://tools.ietf.org/html/rfc7636)                                                                                                                                                                                                                                                                                             |

## <a name="handle-the-response"></a>Zpracování odpovědi

Úspěšné odpovědi tokenu bude obsahovat JWT token a bude vypadat:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parametr     | Popis                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| access_token  | Požadovaná [přístupový token](access-tokens.md). Aplikace můžete používat tento token k ověření k zabezpečeným prostředkům, jako je například webové rozhraní API.                                                                                                                                                                                                                                                                                                                                    |
| token_type    | Určuje hodnotu pro typ tokenu. Nosiče je jediným typem, který podporuje Azure AD                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | Jak dlouho je přístupový token platný (v sekundách).                                                                                                                                                                                                                                                                                                                                                                                                       |
| scope         | Obory, které je platný pro access_token.                                                                                                                                                                                                                                                                                                                                                                                                         |
| refresh_token | Aktualizace tokenu OAuth 2.0. Aplikace můžete používat tento token získat dodatečné přístupové tokeny, až vyprší platnost aktuálního tokenu přístupu. Refresh_tokens jsou dlouhodobé a slouží k přístupu k prostředkům uchovávat po dlouhou dobu. Další podrobnosti najdete [kódu v2.0 udělit odkaz](v2-oauth2-auth-code-flow.md#refresh-the-access-token). <br> **Poznámka:** pouze zadaný if `offline_access` byl vyžádán oboru.                                               |
| id_token      | Bez znaménka JSON Web Token (JWT). Aplikace může dekódovat segmenty tento token na žádost o informace o uživateli, který přihlášení. Aplikaci můžete ukládat do mezipaměti hodnoty a jejich zobrazení, ale na ně neměli spoléhat pro povolení nebo hranice zabezpečení. Další informace o id_tokens, najdete v článku [ `id_token reference` ](id-tokens.md). <br> **Poznámka:** pouze zadaný if `openid` byl vyžádán oboru. |
