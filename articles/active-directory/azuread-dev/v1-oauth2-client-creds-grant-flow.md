---
title: Ověřování pomocí služby Azure AD s použitím OAuth 2.0 | Microsoft Docs
description: Tento článek popisuje, jak pomocí zpráv HTTP implementovat službu pro ověřování pomocí pověření pro udělení přihlašovacích údajů klienta OAuth 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: ryanwi
ms.reviewer: nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 977dfea28c5c0dc3f34ada0c138556d70c979e04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85551700"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Volání služeb prostřednictvím přihlašovacích údajů klienta (sdílený tajný klíč nebo certifikát)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Tok udělení přihlašovacích údajů klienta OAuth 2,0 umožňuje webové službě (*důvěrnému klientovi*) použít vlastní přihlašovací údaje místo zosobnění uživatele k ověření při volání jiné webové služby. V tomto scénáři je klient obvykle webová služba střední vrstvy, služba démona nebo Web. Pro zajištění vyšší úrovně jistoty Azure AD taky umožňuje volající službě používat certifikát (místo sdíleného tajného klíče) jako přihlašovací údaje.

## <a name="client-credentials-grant-flow-diagram"></a>Vývojový diagram udělení přihlašovacích údajů klienta
Následující diagram vysvětluje způsob, jakým pověření klienta udělují tok funguje v Azure Active Directory (Azure AD).

![Tok udělení přihlašovacích údajů klienta OAuth 2.0](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Klientská aplikace se ověří pro koncový bod vystavení tokenu Azure AD a požádá o přístupový token.
2. Koncový bod pro vystavení tokenu Azure AD vydá přístupový token.
3. Přístupový token se používá k ověření zabezpečeného prostředku.
4. Data z zabezpečeného prostředku se vrátí do klientské aplikace.

## <a name="register-the-services-in-azure-ad"></a>Registrace služeb ve službě Azure AD
Zaregistrujte volající službu i přijímající službu v Azure Active Directory (Azure AD). Podrobné pokyny najdete v tématu [Integrace aplikací s Azure Active Directory](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="request-an-access-token"></a>Vyžádání přístupového tokenu
K vyžádání přístupového tokenu použijte příspěvek HTTP na koncový bod Azure AD specifický pro tenanta.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Žádost o přístupový token služby na službu
Existují dva případy v závislosti na tom, jestli se klientská aplikace rozhodne zabezpečit sdíleným tajným klíčem nebo certifikátem.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>První případ: žádost přístupového tokenu se sdíleným tajným klíčem
Při použití sdíleného tajného klíče obsahuje požadavek na přístupový token služby na službu následující parametry:

| Parametr | Typ | Description |
| --- | --- | --- |
| grant_type |vyžadováno |Určuje požadovaný typ udělení. V toku udělení přihlašovacích údajů klienta musí být hodnota **client_credentials**. |
| client_id |vyžadováno |Určuje ID klienta služby Azure AD volající webové služby. Pokud chcete najít ID klienta volající aplikace, klikněte v [Azure Portal](https://portal.azure.com)na **Azure Active Directory**, klikněte na **Registrace aplikací** a pak na aplikaci. Client_id je *ID aplikace* |
| client_secret |vyžadováno |Zadejte klíč zaregistrovaný pro volání webové služby nebo démona aplikace v Azure AD. Pokud chcete vytvořit klíč, v Azure Portal klikněte na **Azure Active Directory**, klikněte na **Registrace aplikací**, klikněte na aplikaci, klikněte na **Nastavení**, klikněte na **klíče** a přidejte klíč.  Adresa URL – tento tajný klíč se zakóduje při jeho poskytování. |
| prostředek |vyžadováno |Zadejte identifikátor URI ID aplikace přijímající webové služby. Pokud chcete najít identifikátor URI ID aplikace, klikněte v Azure Portal na **Azure Active Directory**, klikněte na **Registrace aplikací**, klikněte na aplikaci služby a pak klikněte na **Nastavení** a **vlastnosti**. |

#### <a name="example"></a>Příklad
Následující příspěvek HTTP požaduje [přístupový token](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) pro `https://service.contoso.com/` webovou službu. `client_id`Určuje webovou službu, která žádá o přístupový token.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Druhý případ: žádost o přístupový token s certifikátem
Požadavek na přístupový token služby na službu s certifikátem obsahuje následující parametry:

| Parametr | Typ | Description |
| --- | --- | --- |
| grant_type |vyžadováno |Určuje požadovaný typ odpovědi. V toku udělení přihlašovacích údajů klienta musí být hodnota **client_credentials**. |
| client_id |vyžadováno |Určuje ID klienta služby Azure AD volající webové služby. Pokud chcete najít ID klienta volající aplikace, klikněte v [Azure Portal](https://portal.azure.com)na **Azure Active Directory**, klikněte na **Registrace aplikací** a pak na aplikaci. Client_id je *ID aplikace* |
| client_assertion_type |vyžadováno |Hodnota musí být `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |vyžadováno | Kontrolní výraz (JSON Web Token), který potřebujete k vytvoření a podepsání certifikátu, který jste zaregistrovali jako přihlašovací údaje pro vaši aplikaci. Přečtěte si informace o [přihlašovacích údajích k certifikátu](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) , kde se dozvíte, jak zaregistrovat certifikát a formát kontrolního výrazu.|
| prostředek | vyžadováno |Zadejte identifikátor URI ID aplikace přijímající webové služby. Pokud chcete najít identifikátor URI ID aplikace, klikněte v Azure Portal na **Azure Active Directory**, klikněte na **Registrace aplikací**, klikněte na aplikaci služby a pak klikněte na **Nastavení** a **vlastnosti**. |

Všimněte si, že parametry jsou skoro stejné jako v případě požadavku pomocí sdíleného tajného klíče s tím rozdílem, že parametr client_secret je nahrazen dvěma parametry: client_assertion_type a client_assertion.

#### <a name="example"></a>Příklad
Následující příspěvek HTTP požaduje přístupový token `https://service.contoso.com/` webové služby s certifikátem. `client_id`Určuje webovou službu, která žádá o přístupový token.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Odpověď na přístupový token služby na službu

Odpověď na úspěch obsahuje odpověď protokolu JSON OAuth 2,0 s následujícími parametry:

| Parametr | Popis |
| --- | --- |
| access_token |Požadovaný přístupový token Volající webová služba může tento token použít k ověření pro přijímající webovou službu. |
| token_type |Určuje hodnotu typu tokenu. Jediným typem, který podporuje Azure AD, je **nosič**. Další informace o nosných tokenech najdete v části [autorizační rozhraní OAuth 2,0: použití nosných tokenů (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Jak dlouho je přístupový token platný (v sekundách). |
| expires_on |Čas vypršení platnosti přístupového tokenu. Datum se reprezentuje jako počet sekund od roku 1970-01-01T0:0: 0Z UTC až do doby vypršení platnosti. Tato hodnota se používá k určení doby života tokenů uložených v mezipaměti. |
| not_before |Čas, ze kterého se přístupový token stane použitelný. Datum se reprezentuje jako počet sekund od roku 1970-01-01T0:0: 0Z UTC až do doby platnosti tokenu.|
| prostředek |Identifikátor URI ID aplikace přijímající webové služby. |

#### <a name="example-of-response"></a>Příklad odpovědi
Následující příklad ukazuje odpověď na úspěšnost žádosti o přístupový token webové službě.

```
{
"access_token":"eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```
## <a name="use-the-access-token-to-access-the-secured-resource"></a>Přístup k zabezpečenému prostředku pomocí přístupového tokenu

Služba může pomocí získaného přístupového tokenu provádět ověřené požadavky webového rozhraní API pro příjem dat, a to nastavením tokenu v `Authorization` záhlaví.

### <a name="example"></a>Příklad

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="see-also"></a>Viz také
* [OAuth 2,0 ve službě Azure AD](v1-protocols-oauth-code.md)
* [Ukázka v jazyce c# volání služby se sdíleným tajným klíčem](https://github.com/Azure-Samples/active-directory-dotnet-daemon) a [ukázkou v jazyce c# volání služby s certifikátem](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
