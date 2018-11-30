---
title: Ověřování služba-služba Azure AD pomocí OAuth 2.0 | Dokumentace Microsoftu
description: Tento článek popisuje, jak používat zprávy HTTP k implementaci ověřování služba-služba pomocí tok udělování přihlašovacích údajů klienta OAuth 2.0.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8f32d8f05d5ba5a7a813157adbf07ff7590153bb
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52425374"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Služby volání mezi službami pomocí přihlašovacích údajů klienta (sdílený tajný klíč nebo certifikát)

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

OAuth 2.0 klienta tok udělování přihlašovacích údajů umožňuje webové službě (*důvěrnému klientovi*) používat svoje vlastní přihlašovací údaje místo zosobňování uživatele, k ověření při volání jiné webové služby. V tomto scénáři klient je obvykle střední vrstvy webové služby, služba démona nebo webu. Pro vyšší úroveň záruky umožňuje Azure AD také volání služby k používání certifikátu (ne sdílený tajný klíč) jako pověření.

## <a name="client-credentials-grant-flow-diagram"></a>Vývojový diagram udělení přihlašovacích údajů klienta
Následující diagram popisuje, jak udělit pověření klienta tok funguje ve službě Azure Active Directory (Azure AD).

![Tok udělování přihlašovacích údajů klienta OAuth 2.0](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Klientská aplikace přihlásí k vystavování tokenů koncový bod Azure AD a požádá přístupový token.
2. Koncový bod vystavení tokenu Azure AD vydá token přístupu.
3. Přístupový token se používá k ověření k zabezpečeným prostředkům.
4. Data z zabezpečené prostředků se vrátí do klientské aplikace.

## <a name="register-the-services-in-azure-ad"></a>Registrace služeb v Azure AD
Volání služby a přijímání služby zaregistrujte ve službě Azure Active Directory (Azure AD). Podrobné pokyny najdete v tématu [integrace aplikací s Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="request-an-access-token"></a>Žádost o přístupový Token
Chcete-li požádat o přístupový token, použijte HTTP POST do konkrétního klienta koncového bodu Azure AD.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Žádost o Service to service přístupový token
Existují dva možné případy v závislosti na tom, jestli klientská aplikace rozhodne bylo zabezpečené protokolem sdílený tajný klíč, nebo certifikát.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>První věc: žádost o přístupový token s sdílený tajný klíč
Pokud používáte sdílený tajný klíč, žádosti o token přístupu service to service obsahuje následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| Parametr grant_type |povinné |Určuje typ udělení požadovaná oprávnění. V toku udělení přihlašovacích údajů klienta, musí být hodnota **client_credentials**. |
| client_id |povinné |Určuje id klienta Azure AD volání webové služby. K vyhledání ID klienta volající aplikace, v [webu Azure portal](https://portal.azure.com), klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **registrace aplikací**, klikněte na aplikaci. Je client_id *ID aplikace* |
| Hodnota client_secret |povinné |Zadejte klíč zaregistrovaný pro volání webové služby nebo démona aplikace ve službě Azure AD. Chcete-li vytvořit klíč, na webu Azure Portal, klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **registrace aplikací**, klikněte na aplikaci, klikněte na tlačítko **nastavení**, klikněte na tlačítko **klíče** , a přidejte klíč.  Adresa URL – kódování tomto tajných kódů při jeho zadávání. |
| prostředek |povinné |Zadejte identifikátor URI ID aplikace přijímající webové služby. Chcete-li najít identifikátor ID URI aplikace na webu Azure Portal, klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **registrace aplikací**, klikněte na aplikaci služby a potom klikněte na tlačítko **nastavení** a  **Vlastnosti**. |

#### <a name="example"></a>Příklad:
Následující požadavky HTTP POST [přístupový token](access-tokens.md) pro https://service.contoso.com/ webové služby. `client_id` Identifikuje webová služba, která požaduje přístupový token.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Druhý případ: žádost o přístupový token s certifikátem
Žádosti o token service to service přístup pomocí certifikátu obsahuje následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| Parametr grant_type |povinné |Určuje požadovaný typ odpovědi. V toku udělení přihlašovacích údajů klienta, musí být hodnota **client_credentials**. |
| client_id |povinné |Určuje id klienta Azure AD volání webové služby. K vyhledání ID klienta volající aplikace, v [webu Azure portal](https://portal.azure.com), klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **registrace aplikací**, klikněte na aplikaci. Je client_id *ID aplikace* |
| client_assertion_type |povinné |Hodnota musí být `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |povinné | (JSON Web Token) kontrolního výrazu, které potřebujete k vytvoření a podepsání certifikátem zaregistrujete jako přihlašovací údaje pro vaši aplikaci. Přečtěte si informace o [certifikát přihlašovacích údajů](active-directory-certificate-credentials.md) informace o registraci vašeho certifikátu a formát kontrolního výrazu.|
| prostředek | povinné |Zadejte identifikátor URI ID aplikace přijímající webové služby. Chcete-li najít identifikátor ID URI aplikace na webu Azure Portal, klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **registrace aplikací**, klikněte na aplikaci služby a potom klikněte na tlačítko **nastavení** a  **Vlastnosti**. |

Všimněte si, že parametry jsou téměř stejné jako v případě žádosti sdílený tajný klíč, s tím rozdílem, že parametr hodnota client_secret nahrazuje dva parametry: client_assertion_type a client_assertion.

#### <a name="example"></a>Příklad:
Vyžádá přístupový token pro následující HTTP POST https://service.contoso.com/ webové služby pomocí certifikátu. `client_id` Identifikuje webová služba, která požaduje přístupový token.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Služba služba přístupu k odpovědi tokenu

Úspěšná odpověď obsahuje odpověď JSON OAuth 2.0 s následujícími parametry:

| Parametr | Popis |
| --- | --- |
| access_token |Požadovaný přístupový token. Volání webové služby můžete použít tento token k ověření přijímající webové služby. |
| token_type |Určuje hodnotu pro typ tokenu. Jediný typ, který podporuje Azure AD je **nosiče**. Další informace o nosné tokeny, najdete v článku [Framework autorizace OAuth 2.0: použití nosného tokenu (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Jak dlouho je přístupový token platný (v sekundách). |
| expires_on |Čas, kdy vyprší platnost přístupového tokenu. Datum je vyjádřena jako počet sekund od 1970-01-01T0:0:0Z UTC až do okamžiku vypršení platnosti. Tato hodnota se používá k určení doby života tokenů v mezipaměti. |
| not_before |Čas, ze kterého se stane použitelný přístupový token. Datum je vyjádřena jako počet sekund od 1970-01-01T0:0:0Z UTC do doby platnosti tokenu.|
| prostředek |Identifikátor URI ID aplikace přijímající webové služby. |

#### <a name="example-of-response"></a>Příklad odpovědi
Následující příklad ukazuje úspěšná odpověď na žádost o přístupový token k webové službě.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Další informace najdete v tématech
* [OAuth 2.0 ve službě Azure AD](v1-protocols-oauth-code.md)
* [Ukázka v jazyce C# volání služba-služba s sdílený tajný klíč](https://github.com/Azure-Samples/active-directory-dotnet-daemon) a [ukázka v jazyce C# volání služba-služba s certifikátem](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
