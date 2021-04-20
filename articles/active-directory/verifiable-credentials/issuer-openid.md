---
title: Příklady komunikace služby vystavitele (Preview) – Azure Active Directory ověřitelných přihlašovacích údajů
description: Podrobnosti o komunikaci mezi zprostředkovatelem identity a službou vystavitele
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 942b77f8338636f9dda5dcf6cd4262dad57b4b0a
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726263"
---
# <a name="issuer-service-communication-examples-preview"></a>Příklady komunikace služby vystavitele (Preview)

Služba ověřitelných přihlašovacích údajů Azure AD může vystavovat ověřitelné přihlašovací údaje načtením deklarací identity z tokenu ID generovaného poskytovatelem identity kompatibilního s OpenID vaší organizace. V tomto článku se naučíte, jak nastavit poskytovatele identity tak, aby ověřovatel mohl komunikovat s ním a načíst správný token ID, který se předává vydávající službě. 

> [!IMPORTANT]
> Azure Active Directory ověřitelných přihlašovacích údajů je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Pro vydání ověřitelných přihlašovacích údajů se ověřovatel vydá prostřednictvím stažení smlouvy za účelem shromáždění vstupu od uživatele a odeslání těchto informací do vydávající služby. Pokud potřebujete použít token ID, musíte nastavit poskytovatele identity, aby ověřovatel mohl přihlásit uživatele pomocí protokolu OpenID Connect. Deklarace identity ve výsledném tokenu ID se používají k naplnění obsahu ověřitelných přihlašovacích údajů. Ověřovatel ověřuje uživatele pomocí toku autorizačního kódu OpenID Connect. Váš poskytovatel OpenID musí podporovat tyto funkce OpenID Connect: 

| Funkce | Popis |
| ------- | ----------- |
| Typ udělení | Musí podporovat typ udělení autorizačního kódu. |
| Formát tokenu | Musí vydávat nešifrované kompaktní JWTs. |
| Algoritmus podpisu | Musí vydávat JWTs podepsané pomocí RSA 256. |
| Konfigurační dokument | Musí podporovat konfigurační dokument OpenID Connect a `jwks_uri` . | 
| Registrace klienta | Musí podporovat registraci veřejného klienta pomocí `redirect_uri` hodnoty `vclient://openid/` . | 
| PKCE | Doporučuje se z bezpečnostních důvodů, ale není to nutné. |

Níže jsou uvedeny příklady požadavků HTTP odeslaných poskytovateli identity. Váš poskytovatel identity musí přijmout a reagovat na tyto žádosti v souladu se standardem ověřování OpenID Connect.

## <a name="client-registration"></a>Registrace klienta

K získání ověřitelných přihlašovacích údajů se uživatelé musí přihlašovat k IDP z aplikace Microsoft Authenticator. 

Pokud chcete tento Exchange povolit, zaregistrujte aplikaci u svého poskytovatele identity. Pokud používáte Azure AD, najdete [tady](../develop/quickstart-register-app.md)pokyny. Při registraci použijte následující hodnoty.

| Nastavení | Hodnota |
| ------- | ----- |
| Název aplikace | `<Issuer Name> Verifiable Credential Service` |
| Identifikátor URI pro přesměrování | `vcclient://openid/ ` |


Po registraci aplikace u poskytovatele identity zaznamenejte jeho ID klienta. Budete ho používat v níže uvedené části. Také je nutné zapsat adresu URL do známého koncového bodu pro OIDC kompatibilního poskytovatele identity. Vydaná služba používá tento koncový bod ke stažení veřejných klíčů potřebných k ověření tokenu ID po jeho odeslání ověřovatelem.

Nakonfigurovaný identifikátor URI pro přesměrování používá ověřovatel, aby ví, kdy se přihlášení dokončilo, a může získat token ID. 

## <a name="authorization-request"></a>Žádost o autorizaci

Požadavek na autorizaci odeslaný Vašemu zprostředkovateli identity používá následující formát.

```HTTP
GET /authorize?client_id=<client-id>&redirect_uri=portableidentity%3A%2F%2Fverify&response_mode=query&response_type=code&scope=openid&state=12345&nonce=12345 HTTP/1.1
Host: www.contoso.com
Connection: Keep-Alive
```

| Parametr | Hodnota |
| ------- | ----------- |
| `client_id` | ID klienta získané během procesu registrace aplikace. |
| `redirect_uri` | Musí používat `vcclient://openid/` . |
| `response_mode` | Musí podporovat `query` . |
| `response_type` | Musí podporovat `code` . |
| `scope` | Musí podporovat `openid` . |
| `state` | Musí se vrátit klientovi podle standardu OpenID Connect. |
| `nonce` | Musí se vrátit jako deklarace identity v tokenu ID podle standardu OpenID Connect. |

Když obdrží žádost o autorizaci, váš poskytovatel identity by měl ověřit uživatele a provést všechny kroky potřebné k dokončení přihlášení, jako je například ověřování Multi-Factor Authentication.

Proces přihlášení můžete přizpůsobit tak, aby vyhovoval vašim potřebám. Můžete požádat uživatele o poskytnutí dalších informací, přijmout podmínky služby, platit za své přihlašovací údaje a další. Po dokončení všech kroků odpovězte na žádost o autorizaci přesměrováním na identifikátor URI přesměrování, jak je znázorněno níže. 

```HTTP
vcclient://openid/?code=nbafhjbh1ub1yhbj1h4jr1&state=12345
```

| Parametr | Hodnota |
| ------- | ----------- |
| `code` |  Autorizační kód vrácený vaším poskytovatelem identity. |
| `state` | Musí se vrátit klientovi podle standardu OpenID Connect. |

## <a name="token-request"></a>Žádost o token

Požadavek na token odeslaný vašemu poskytovateli identity bude mít následující formu.

```HTTP
POST /token HTTP/1.1
Host: www.contoso.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 291

client_id=<client-id>&redirect_uri=vcclient%3A%2F%2Fopenid%2F&grant_type=authorization_code&code=nbafhjbh1ub1yhbj1h4jr1&scope=openid
```

| Parametr | Hodnota |
| ------- | ----------- |
| `client_id` | ID klienta získané během procesu registrace aplikace. |
| `redirect_uri` | Musí používat `vcclient://openid/` . |
| `scope` | Musí podporovat `openid` . |
| `grant_type` | Musí podporovat `authorization_code` . |
| `code` | Autorizační kód vrácený vaším poskytovatelem identity. |

Po přijetí žádosti o token by měl váš poskytovatel identity reagovat s tokenem ID.

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
"id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjFlOWdkazcifQ.ewogImlzc
    yI6ICJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ4Mjg5
    NzYxMDAxIiwKICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiAibi0wUzZ
    fV3pBMk1qIiwKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDEzMTEyODA5Nz
    AKfQ.ggW8hZ1EuVLuxNuuIJKX_V8a_OMXzR0EHR9R6jgdqrOOF4daGU96Sr_P6q
    Jp6IcmD3HP99Obi1PRs-cwh3LO-p146waJ8IhehcwL7F09JdijmBqkvPeB2T9CJ
    NqeGpe-gccMg4vfKjkM8FcGvnzZUN4_KSP0aAp1tOJ1zZwgjxqGByKHiOtX7Tpd
    QyHE5lcMiKPXfEIQILVq0pc_E2DzL7emopWoaoZTF_m0_N0YzFC6g6EJbOEoRoS
    K5hoDalrcvRYLSrQAZZKflyuVCyixEoV9GfNQC3_osjzw2PAithfubEEBLuVVk4
    XUVrWOLrLl0nx7RkKU8NXNHq-rvKMzqg"
}
```

Token ID musí používat formát serializace pro komprimaci JWT a nesmí být zašifrovaný. Token ID by měl obsahovat následující deklarace identity.

| Deklarovat | Hodnota |
| ------- | ----------- |
| `kid` | Identifikátor klíče, který se používá k podepsání tokenu ID odpovídající položce v OpenID poskytovatele `jwks_uri` . |
| `aud` | ID klienta získané během procesu registrace aplikace. |
| `iss` | Musí se jednat o `issuer` hodnotu v dokumentu konfigurace OpenID Connect. |
| `exp` | Musí obsahovat čas vypršení platnosti tokenu ID. |
| `iat` | Musí obsahovat čas vydání tokenu ID. |
| `nonce` | Hodnota zahrnutá v žádosti o autorizaci. |
| Další deklarace identity | Token ID by měl obsahovat jakékoli další deklarace, jejichž hodnoty budou zahrnuty do ověřitelných přihlašovacích údajů, které budou vydány. V této části je místo, kde byste měli zahrnout libovolné atributy uživatele, například jejich jméno. |

## <a name="next-steps"></a>Další kroky

- [Postup přizpůsobení Azure Active Directory ověřitelných přihlašovacích údajů](credential-design.md)
