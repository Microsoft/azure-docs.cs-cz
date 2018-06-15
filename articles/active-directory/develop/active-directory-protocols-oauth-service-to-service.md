---
title: Azure AD do služby ověřování pomocí OAuth2.0 | Microsoft Docs
description: Tento článek popisuje, jak použít k implementaci služeb ověřování pomocí tok udělení přihlašovacích údajů klienta OAuth2.0 zpráv protokolu HTTP.
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
ms.openlocfilehash: 1a4da1af61be4b632b51f5b8f921f6f48e925559
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158189"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Služby volání služby pomocí pověření klienta (sdílený tajný klíč nebo certifikát)
OAuth 2.0 klienta pověření Grant toku umožňuje webové služby (*důvěrné klienta*) používat svoje vlastní přihlašovací údaje namísto zosobňování uživatele, k ověřování při volání metody jiné webové služby. V tomto scénáři klient je většinou střední vrstvy webové služby, služba démon nebo webu. Pro vyšší úroveň záruky Azure AD umožňuje také volání služby pro použití certifikátu (ne sdílený tajný klíč) jako pověření.

## <a name="client-credentials-grant-flow-diagram"></a>Vývojový diagram udělení pověření klienta
Následující diagram popisuje, jak udělit pověření klienta toku funguje v Azure Active Directory (Azure AD).

![OAuth2.0 tok udělení přihlašovacích údajů klienta](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Klientská aplikace přihlásí k vystavování tokenů koncového bodu Azure AD a požadavky přístupový token.
2. Koncový bod vystavování tokenů Azure AD vydá token přístupu.
3. Přístupový token se používá k ověření k zabezpečeným prostředkům.
4. Data z zabezpečeným prostředkům, je vrácen do webové aplikace.

## <a name="register-the-services-in-azure-ad"></a>Registrace služby ve službě Azure AD
Zaregistrujte službu volání a přijímající služby v Azure Active Directory (Azure AD). Podrobné pokyny najdete v tématu [integrace aplikací s Azure Active Directory](active-directory-integrating-applications.md).

## <a name="request-an-access-token"></a>Žádost o Token přístupu
Chcete-li požádat o token přístupu, použijte HTTP POST do konkrétního klienta koncového bodu Azure AD.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Žádosti o token Service-to-service přístup
Jsou dva případy, v závislosti na tom, zda klientská aplikace rozhodne zabezpečeny sdílený tajný klíč nebo certifikát.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Nejprve případ: žádosti o token přístupu s sdílený tajný klíč
Pokud používáte sdílený tajný klíč, žádosti o token přístupu service-to-service obsahuje následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| grant_type |povinné |Určuje typ požadovaný udělení. V toku udělení pověření klienta, musí být hodnota **client_credentials**. |
| client_id |povinné |Určuje id klienta Azure AD volání webové služby. K vyhledání ID klienta je volající aplikace, v [portál Azure](https://portal.azure.com), klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **registrace aplikace**, klikněte na aplikaci. Je client_id *ID aplikace* |
| tajný klíč client_secret |povinné |Zadejte klíč zaregistrovat pro volání webové služby nebo proces démon aplikace ve službě Azure AD. Chcete-li vytvořit klíč, na portálu Azure, klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **registrace aplikace**, klikněte na aplikaci, klikněte na **nastavení**, klikněte na tlačítko **klíče** , a přidejte klíč.|
| prostředek |povinné |Zadejte identifikátor ID URI aplikace přijímající webové služby. Chcete-li najít identifikátor ID URI aplikace na portálu Azure, klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **registrace aplikace**, klikněte na aplikaci služby a pak klikněte na tlačítko **nastavení** a  **Vlastnosti**. |

#### <a name="example"></a>Příklad:
Přístupový token pro požadavky následující HTTP POST https://service.contoso.com/ webové služby. `client_id` Identifikuje webové služby, která požaduje přístupový token.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Druhé případ: tokenu žádosti o přístup pomocí certifikátu
Žádosti o token service-to-service přístup pomocí certifikátu obsahuje následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| grant_type |povinné |Určuje typ požadované odpovědi. V toku udělení pověření klienta, musí být hodnota **client_credentials**. |
| client_id |povinné |Určuje id klienta Azure AD volání webové služby. K vyhledání ID klienta je volající aplikace, v [portál Azure](https://portal.azure.com), klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **registrace aplikace**, klikněte na aplikaci. Je client_id *ID aplikace* |
| client_assertion_type |povinné |Hodnota musí být `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |povinné | (Webového tokenu JSON) kontrolní výraz, který potřebujete k vytvoření a podepsání s certifikátem, můžete zaregistrovat jako přihlašovací údaje pro vaši aplikaci. Přečtěte si informace o [certifikát přihlašovacích údajů](active-directory-certificate-credentials.md) Další informace o registraci vašeho certifikátu a formát kontrolní výraz.|
| prostředek | povinné |Zadejte identifikátor ID URI aplikace přijímající webové služby. Chcete-li najít identifikátor ID URI aplikace na portálu Azure, klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **registrace aplikace**, klikněte na aplikaci služby a pak klikněte na tlačítko **nastavení** a  **Vlastnosti**. |

Všimněte si, že parametry jsou téměř stejné jako v případě požadavku pomocí sdílený tajný klíč, s tím rozdílem, že parametr tajný klíč client_secret je nahrazena dva parametry: client_assertion_type a client_assertion.

#### <a name="example"></a>Příklad:
Přístupový token pro požadavky následující HTTP POST https://service.contoso.com/ webové služby pomocí certifikátu. `client_id` Identifikuje webové služby, která požaduje přístupový token.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Odpovědi tokenu přístupu Service-to-Service

Úspěšná odpověď obsahuje odpověď JSON OAuth 2.0 s následujícími parametry:

| Parametr | Popis |
| --- | --- |
| access_token |Požadovaný přístupový token. Volání webové služby můžete použít tento token k ověření přijímající webové služby. |
| token_type |Určuje hodnotu typ tokenu. Pouze typ, který podporuje Azure AD je **nosiče**. Další informace o nosné tokeny, najdete v článku [Framework autorizace OAuth 2.0: použití tokenů nosiče (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Jak dlouho přístupový token je platný (v sekundách). |
| expires_on |Čas, kdy vyprší platnost přístupového tokenu. Datum je reprezentován jako počet sekund od pod hodnotou 1970-01-01T0:0:0Z UTC až do okamžiku vypršení platnosti. Tato hodnota se používá k určení doby platnosti tokenů v mezipaměti. |
| not_before |Čas, ze kterého se stane přístupový token použitelné. Datum je reprezentován jako počet sekund od pod hodnotou 1970-01-01T0:0:0Z UTC dokud čas platnosti tokenu.|
| prostředek |Identifikátor ID URI aplikace přijímající webové služby. |

#### <a name="example-of-response"></a>Příklad odpovědi
Následující příklad ukazuje úspěšná odpověď na žádost o token přístupu k webové službě.

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
* [OAuth 2.0 ve službě Azure AD](active-directory-protocols-oauth-code.md)
* [Ukázka v jazyce C# volání služeb s sdílený tajný klíč](https://github.com/Azure-Samples/active-directory-dotnet-daemon) a [ukázka v jazyce C# volání do služby pomocí certifikátu](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
