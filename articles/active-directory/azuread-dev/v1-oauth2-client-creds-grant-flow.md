---
title: Azure AD Service auth pomocí OAuth2.0 | Dokumenty společnosti Microsoft
description: Tento článek popisuje použití zpráv HTTP k implementaci služby ověřování služeb pomocí toku udělení pověření klienta OAuth2.0.
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
ms.openlocfilehash: f2d1eaec80c8925eb7b38af848e29e944f1ebf69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154538"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Služba pro volání pomocí pověření klienta (sdílený tajný klíč nebo certifikát)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Grantový tok klientských pověření OAuth 2.0 umožňuje webové službě *(důvěrnému klientovi)* používat vlastní pověření namísto zosobnění uživatele k ověření při volání jiné webové služby. V tomto scénáři klient je obvykle webové služby střední vrstvy, daemon služby nebo webové stránky. Pro vyšší úroveň záruky Azure AD také umožňuje volající službě používat certifikát (namísto sdíleného tajného klíče) jako pověření.

## <a name="client-credentials-grant-flow-diagram"></a>Diagram toku pověření klienta
Následující diagram vysvětluje, jak funguje tok grantových pověření klienta ve službě Azure Active Directory (Azure AD).

![Tok grantu pověření klienta OAuth2.0](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Klientská aplikace se ověřuje do koncového bodu vystavování tokenu Azure AD a požaduje přístupový token.
2. Koncový bod vystavování tokenu Azure AD vydává přístupový token.
3. Přístupový token se používá k ověření zabezpečeného prostředku.
4. Data ze zabezpečeného prostředku jsou vrácena do klientské aplikace.

## <a name="register-the-services-in-azure-ad"></a>Registrace služeb ve službě Azure AD
Zaregistrujte volající i přijímající službu ve službě Azure Active Directory (Azure AD). Podrobné pokyny najdete [v tématu Integrace aplikací se službou Azure Active Directory](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="request-an-access-token"></a>Žádost o přístupový token
Chcete-li požádat o přístupový token, použijte HTTP POST na koncový bod Azure AD specifické pro klienta.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Požadavek na přístupový token služby
Existují dva případy v závislosti na tom, zda se klientská aplikace rozhodne pro zabezpečené sdíleným tajným kódem nebo certifikátem.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>První případ: Žádost o přístupový token se sdíleným tajným tajemstvím
Při použití sdíleného tajného klíče obsahuje požadavek přístupového tokenu služby ke službě následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| grant_type |Požadovaný |Určuje požadovaný typ grantu. V toku grantu pověření klienta musí být hodnota **client_credentials**. |
| client_id |Požadovaný |Určuje ID klienta Azure AD volající webové služby. Pokud chcete najít ID klienta volající aplikace, klikněte na [webu Azure Portal](https://portal.azure.com)na Azure Active **Directory**, klikněte na **Registrace aplikací**, klikněte na aplikaci. client_id je *ID aplikace* |
| client_secret |Požadovaný |Zadejte klíč registrovaný pro volající webovou službu nebo aplikaci daemon ve službě Azure AD. Pokud chcete vytvořit klíč, klikněte na webu Azure Portal na **Azure Active Directory**, klikněte na Registrace **aplikací**, klikněte na aplikaci, klikněte na **Nastavení**, klikněte na **Klíče**a přidejte klíč.  URL kódovat tento tajný klíč při jeho poskytování. |
| prostředek |Požadovaný |Zadejte identifikátor URI ID aplikace přijímající webové služby. Pokud chcete identifikátor URI Najít, klikněte na webu Azure Portal na **Azure Active Directory**, klikněte na Registrace **aplikací**, klikněte na aplikaci služby a potom klikněte na **Nastavení** a **vlastnosti**. |

#### <a name="example"></a>Příklad
Následující funkce HTTP POST požaduje [přístupový token](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) pro webovou službu. `https://service.contoso.com/` Identifikuje `client_id` webovou službu, která požaduje přístupový token.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Druhý případ: Žádost o přístupový token s certifikátem
Požadavek na přístupový token služby k službě s certifikátem obsahuje následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| grant_type |Požadovaný |Určuje požadovaný typ odpovědi. V toku grantu pověření klienta musí být hodnota **client_credentials**. |
| client_id |Požadovaný |Určuje ID klienta Azure AD volající webové služby. Pokud chcete najít ID klienta volající aplikace, klikněte na [webu Azure Portal](https://portal.azure.com)na Azure Active **Directory**, klikněte na **Registrace aplikací**, klikněte na aplikaci. client_id je *ID aplikace* |
| client_assertion_type |Požadovaný |Hodnota musí být`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |Požadovaný | Kontrolní výraz (webový token JSON), který potřebujete vytvořit a podepsat pomocí certifikátu, který jste zaregistrovali jako pověření pro vaši aplikaci. Přečtěte si o [pověření certifikátu,](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) kde se dozvíte, jak registrovat certifikát a formát kontrolního výrazu.|
| prostředek | Požadovaný |Zadejte identifikátor URI ID aplikace přijímající webové služby. Pokud chcete identifikátor URI Najít, klikněte na webu Azure Portal na **Azure Active Directory**, klikněte na Registrace **aplikací**, klikněte na aplikaci služby a potom klikněte na **Nastavení** a **vlastnosti**. |

Všimněte si, že parametry jsou téměř stejné jako v případě požadavku sdíleným tajným tajemstvím s tím rozdílem, že parametr client_secret je nahrazen dvěma parametry: client_assertion_type a client_assertion.

#### <a name="example"></a>Příklad
Následující funkce HTTP POST požaduje přístupový token pro webovou službu `https://service.contoso.com/` s certifikátem. Identifikuje `client_id` webovou službu, která požaduje přístupový token.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Odpověď tokenu přístupu od služby

Odpověď na úspěch obsahuje odpověď JSON OAuth 2.0 s následujícími parametry:

| Parametr | Popis |
| --- | --- |
| access_token |Požadovaný přístupový token. Volající webová služba může tento token použít k ověření přijímající webové služby. |
| token_type |Označuje hodnotu typu tokenu. Jediný typ, který podporuje Azure AD je **Nosič**. Další informace o žetonech na doručitele naleznete v [rámci autorizace OAuth 2.0: Použití žetonu nosiče (RFC 6750).](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Jak dlouho je přístupový token platný (v sekundách). |
| expires_on |Čas, kdy vyprší platnost přístupového tokenu. Datum je reprezentováno jako počet sekund od 1970-01-01T0:0:0Z UTC až do doby vypršení platnosti. Tato hodnota se používá k určení životnosti tokenů uložených v mezipaměti. |
| not_before |Čas, ze kterého se stane použitelný přístupový token. Datum je reprezentováno jako počet sekund od 1970-01-01T0:0:0Z UTC do doby platnosti tokenu.|
| prostředek |Identifikátor URI ID aplikace přijímající webové služby. |

#### <a name="example-of-response"></a>Příklad odpovědi
Následující příklad ukazuje odpověď na úspěšnou odpověď na požadavek na přístupový token k webové službě.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Viz také
* [OAuth 2.0 ve službě Azure AD](v1-protocols-oauth-code.md)
* [Ukázka volání služby c# se sdíleným tajným klíči](https://github.com/Azure-Samples/active-directory-dotnet-daemon) a [ukázkou volání služby služby s certifikátem v c.](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
