---
title: Tokeny Microsoft Identity Platform ID | Azure
titleSuffix: Microsoft identity platform
description: Naučte se, jak používat id_tokens vydávané koncovými body Azure AD v 1.0 a Microsoft Identity Platform (v 2.0).
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: 6a1f4a02ebf42c0f181b595aae0a5fa0bcc9b41d
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755920"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Tokeny ID platformy Microsoft identity

`id_tokens` jsou odesílány do klientské aplikace jako součást toku [OpenID Connect](v2-protocols-oidc.md) (OIDC). Je možné je odeslat společně nebo místo přístupového tokenu a klient je používá k ověření uživatele.

## <a name="using-the-id_token"></a>Použití id_token

Tokeny ID by se měly použít k ověření, že uživatel je vyhodnocený a získá další užitečné informace – neměl by se používat k autorizaci místo [přístupového tokenu](access-tokens.md). Deklarace identity, které poskytuje, lze použít pro UX v rámci aplikace, jako [klíče v databázi](#using-claims-to-reliably-identify-a-user-subject-and-object-id)a poskytnutí přístupu k klientské aplikaci.  

## <a name="claims-in-an-id_token"></a>Deklarace identity v id_token

`id_tokens` jsou [JWTs](https://tools.ietf.org/html/rfc7519) (webové tokeny JSON), což znamená, že se skládají z hlavičky, datové části a části podpisu. Hlavičku a podpis můžete použít k ověření pravosti tokenu, zatímco datová část obsahuje informace o uživateli, který požaduje váš klient. S výjimkou případů, kdy je uvedeno jinak, jsou všechny deklarace JWT uvedené zde uvedeny v tokenech v 1.0 i v 2.0.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Podívejte se na vzorový token v 1.0 v [JWT.MS](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Zobrazit ukázkový token v 2.0 v [JWT.MS](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw)

### <a name="header-claims"></a>Deklarace hlaviček

|Deklarovat | Formát | Description |
|-----|--------|-------------|
|`typ` | Řetězec – vždycky "JWT" | Označuje, že token je token JWT.|
|`alg` | Řetězec | Určuje algoritmus, který se použil k podepsání tokenu. Příklad: "RS256" |
|`kid` | Řetězec | Kryptografický otisk veřejného klíče, který se používá k podepsání tohoto tokenu. Vygenerováno v 1.0 i v 2.0 `id_tokens` . |
|`x5t` | Řetězec | Stejné (používá se a hodnota) jako `kid` . Toto je však starší deklarace identity vygenerované pouze v 1.0 `id_tokens` pro účely kompatibility. |

### <a name="payload-claims"></a>Deklarace datové části

V tomto seznamu jsou uvedeny deklarace identity JWT, které jsou ve výchozím nastavení ve většině id_tokens (s výjimkou popsaných případů).  Vaše aplikace ale může použít [volitelné deklarace identity](active-directory-optional-claims.md) k vyžádání dalších deklarací JWT v id_token.  Ty můžou být v rozsahu od `groups` deklarace identity až po informace o jménu uživatele.

|Deklarovat | Formát | Description |
|-----|--------|-------------|
|`aud` |  Řetězec – identifikátor URI ID aplikace | Identifikuje zamýšleného příjemce tokenu. V aplikaci `id_tokens` je cílovou skupinou ID aplikace vaší aplikace, které je přiřazeno vaší aplikaci v Azure Portal. Vaše aplikace by měla tuto hodnotu ověřit a token zamítnout, pokud se hodnota neshoduje. |
|`iss` |  Řetězec, identifikátor URI služby STS | Identifikuje službu tokenů zabezpečení (STS), která vytvoří a vrátí token, a tenanta Azure AD, ve kterém byl uživatel ověřený. Pokud byl token vydán koncovým bodem v 2.0, identifikátor URI skončí `/v2.0` .  Identifikátor GUID, který označuje, že uživatel je uživatelem uživatele z účet Microsoft je `9188040d-6c67-4c5b-b112-36a304b66dad` . Vaše aplikace by měla použít část s identifikátorem GUID k omezení sady klientů, kteří se mohou k aplikaci přihlásit, pokud jsou k dispozici. |
|`iat` |  int, časové razítko systému UNIX | "Vydáno v" označuje, kdy došlo k ověření pro tento token.  |
|`idp`|Řetězec, obvykle identifikátor URI služby STS | Zaznamenává zprostředkovatele identity, který ověřil subjekt tokenu. Tato hodnota je shodná s hodnotou deklarace vystavitele, pokud uživatelský účet, který není ve stejném tenantovi jako host pro vystavitele, například. Pokud tato deklarace identity není k dispozici, znamená to, že se `iss` místo toho dá použít hodnota.  Pro osobní účty používané v organizačním kontextu (například osobní účet, který je pozván na tenanta Azure AD) `idp` může být deklarace identity Live.com nebo identifikátor URI STS obsahující klienta účet Microsoft `9188040d-6c67-4c5b-b112-36a304b66dad` . |
|`nbf` |  int, časové razítko systému UNIX | Deklarace "NBF" (ne dřív) určuje dobu, po jejímž uplynutí nesmí být požadavek JWT přijat ke zpracování.|
|`exp` |  int, časové razítko systému UNIX | Deklarace "EXP" (čas vypršení platnosti) identifikuje dobu vypršení platnosti nebo po jejímž uplynutí může být požadavek JWT přijat ke zpracování.  Je důležité si uvědomit, že prostředek může před touto dobou odmítat token, a to například v případě, že se vyžaduje změna v ověřování nebo že bylo zjištěno odvolání tokenu. |
| `c_hash`| Řetězec |Hodnota hash kódu je obsažena v tokenech ID pouze v případě, že je token ID vydán pomocí autorizačního kódu OAuth 2,0. Dá se použít k ověření pravosti autorizačního kódu. Podrobnosti o tom, jak provést toto ověření, najdete v tématu [specifikace OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| Řetězec |Hodnota hash přístupového tokenu je obsažena v tokenech ID pouze v případě, že je token ID vydán z `/authorize` koncového bodu pomocí přístupového tokenu OAuth 2,0. Dá se použít k ověření pravosti přístupového tokenu. Podrobnosti o tom, jak provést toto ověření, najdete v tématu [specifikace OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken). To se v tokenech ID z `/token` koncového bodu nevrací. |
|`aio` | Neprůhledný řetězec | Interní deklarace, kterou používá služba Azure AD k záznamu dat pro opakované použití tokenu By měla být ignorována.|
|`preferred_username` | Řetězec | Primární uživatelské jméno, které představuje uživatele. Může to být e-mailová adresa, telefonní číslo nebo obecné uživatelské jméno bez zadaného formátu. Jeho hodnota je proměnlivá a může se v průběhu času měnit. Protože je proměnlivá, nesmí se tato hodnota použít k rozhodování o autorizaci. `profile`Pro příjem této deklarace je vyžadován rozsah.|
|`email` | Řetězec | `email`Deklarace identity je ve výchozím nastavení k dispozici pro účty hosta, které mají e-mailovou adresu.  Vaše aplikace může požádat o e-mailovou deklaraci identity pro spravované uživatele (ze stejného tenanta jako prostředku), a to pomocí `email` [volitelné deklarace identity](active-directory-optional-claims.md).  V koncovém bodě verze 2.0 může aplikace požádat o `email` obor OpenID Connect – nemusíte požadovat žádnou volitelnou deklaraci identity a obor pro získání deklarace identity.  Deklarace e-mailu podporuje pouze adresovatelné pošty z informací o profilu uživatele. |
|`name` | Řetězec | `name`Deklarace identity poskytuje lidský čitelný údaj, který identifikuje předmět tokenu. Hodnota není zaručena, že je jedinečná, je proměnlivá a je navržena tak, aby se používala pouze pro účely zobrazení. `profile`Pro příjem této deklarace je vyžadován rozsah. |
|`nonce`| Řetězec | Hodnota nonce odpovídá parametru zahrnutému v původní žádosti/Authorize do IDP. Pokud se neshodují, vaše aplikace by měla token odmítnout. |
|`oid` | Řetězec, identifikátor GUID | Neproměnlivý identifikátor pro objekt v systému Microsoft identity, v tomto případě uživatelský účet. Toto ID jednoznačně identifikuje uživatele napříč aplikacemi – dvě různé aplikace přihlášené ke stejnému uživateli získají stejnou hodnotu v `oid` deklaraci identity. Microsoft Graph bude toto ID vracet jako `id` vlastnost pro daný uživatelský účet. Vzhledem k tomu, že `oid` umožňuje více aplikacím korelovat uživatele, `profile` je pro příjem této deklarace vyžadován rozsah. Všimněte si, že pokud jeden uživatel existuje ve více klientech, bude uživatel v každém tenantovi obsahovat jiné ID objektu – považují se za jiné účty, i když se uživatel do každého účtu přihlašuje pomocí stejných přihlašovacích údajů. `oid`Deklarace identity je GUID a nedá se znovu použít. |
|`roles`| Pole řetězců | Sada rolí, které byly přiřazeny uživateli, který se přihlašuje. |
|`rh` | Neprůhledný řetězec |Interní deklarace identity, kterou Azure používá k opětovnému ověření tokenů. By měla být ignorována. |
|`sub` | Řetězec | Objekt zabezpečení, o kterém token vyhodnotí informace, jako je například uživatel aplikace Tato hodnota je neměnná a nelze ji znovu přiřadit ani použít znovu. Subjekt je párový identifikátor, který je jedinečný pro konkrétní ID aplikace. Pokud se jeden uživatel přihlásí ke dvěma různým aplikacím pomocí dvou různých ID klientů, budou tyto aplikace pro deklaraci deklarace subjektu dostávat dvě odlišné hodnoty. To může nebo nemusí být žádoucí v závislosti na vaší architektuře a požadavcích na ochranu osobních údajů. |
|`tid` | Řetězec, identifikátor GUID | Identifikátor GUID, který představuje tenanta Azure AD, ze kterého je uživatel. V případě pracovních a školních účtů je identifikátor GUID neměnné ID klienta organizace, do které uživatel patří. U osobních účtů je tato hodnota `9188040d-6c67-4c5b-b112-36a304b66dad` . `profile`Pro příjem této deklarace je vyžadován rozsah. |
|`unique_name` | Řetězec | Poskytuje lidsky čitelnou hodnotu, která identifikuje subjekt tokenu. Tato hodnota je v jakémkoli daném bodu v čase jedinečná, ale když je možné znovu použít e-maily a jiné identifikátory, tato hodnota se může znovu zobrazit na jiných účtech a měla by se proto použít jenom pro účely zobrazení. Vydány jenom v 1.0 `id_tokens` . |
|`uti` | Neprůhledný řetězec | Interní deklarace identity, kterou Azure používá k opětovnému ověření tokenů. By měla být ignorována. |
|`ver` | Řetězec, buď 1,0 nebo 2,0 | Určuje verzi id_token. |
|`hasgroups`|Logická hodnota|Pokud je přítomen, vždy true, označuje uživatele alespoň v jedné skupině. Používá se místo deklarace identity skupin pro JWTs v implicitních tocích toků, pokud by deklarace identity celé skupiny rozšířila fragment identifikátoru URI za omezení délky adresy URL (aktuálně 6 nebo více skupin). Určuje, že klient musí použít rozhraní Microsoft Graph API k určení skupin uživatelů ( `https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects` ).|
|`groups:src1`|Objekt JSON | Pro žádosti o tokeny, které nejsou omezené na délku (viz `hasgroups` výše), ale u tokenu je ještě moc velká, se zobrazí odkaz na seznam úplných skupin pro uživatele. Pro JWTs jako distribuovanou deklaraci protokolu SAML jako nové deklarace místo `groups` deklarace identity. <br><br>**Ukázková hodnota JWT**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }`<br><br> Další informace najdete v tématu neoprávněná [deklarace skupin](#groups-overage-claim).|

> [!NOTE]
> Id_token v 1.0 a v 2.0 mají rozdíly v množství informací, které budou prováděny podle výše uvedených příkladů. Verze je založena na koncovém bodu, ze kterého byla vyžádána. I když existující aplikace nejspíš používají koncový bod Azure AD, měly by nové aplikace používat Microsoft Identity Platform.
>
> - v 1.0: koncové body Azure AD: `https://login.microsoftonline.com/common/oauth2/authorize`
> - v 2.0: koncové body platformy Microsoft identity: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

### <a name="using-claims-to-reliably-identify-a-user-subject-and-object-id"></a>Spolehlivá identifikace uživatele (subjektu a ID objektu) pomocí deklarací identity

Když identifikujete uživatele (řekněme, že je vyhledáte v databázi nebo rozhodujete, jaká oprávnění mají), je důležité používat informace, které zůstanou v čase konstantní a jedinečné. Starší verze aplikací někdy používají pole, jako je e-mailová adresa, telefonní číslo nebo hlavní název uživatele (UPN).  Všechny tyto změny se můžou v průběhu času měnit a dají se použít i v čase – Když zaměstnanec změní svůj název, nebo zaměstnanec má přiřazenou e-mailovou adresu, která odpovídá předchozímu, už není k dispozici zaměstnanec. Proto je **důležité** , aby vaše aplikace nepoužívala uživatelsky čitelné údaje k identifikaci uživatelsky čitelného čtení, obecně znamená, že někdo ho přečte a chcete ho změnit. Místo toho použijte deklarace identity poskytované standardem OIDC nebo rozšíření deklarací identity poskytovaných Microsoftem `sub` a `oid` deklaracemi identity.

Aby bylo možné správně ukládat informace pro jednotlivé uživatele, použití `sub` nebo `oid` samostatné (což jsou jedinečné identifikátory GUID), které se `tid` používají pro směrování nebo horizontálního dělení v případě potřeby.  Pokud potřebujete sdílet data napříč službami, `oid` + `tid` je nejlepší, protože všechny aplikace získají stejné `oid` a `tid` deklarace identity pro daného uživatele.  `sub`Deklarace identity na platformě Microsoft identity je "párové" – je jedinečná na základě kombinace příjemce, tenanta a uživatele tokenu.  Proto dvě aplikace, které vyžadují tokeny ID pro daného uživatele, obdrží různé `sub` deklarace identity, ale stejné `oid` deklarace identity pro tohoto uživatele.

>[!NOTE]
> Nepoužívejte `idp` deklaraci identity k ukládání informací o uživateli při pokusu o korelaci uživatelů napříč klienty.  Nebude fungovat, protože `oid` `sub` deklarace identity pro uživatele a změny v klientech navrhují, aby aplikace nemohly sledovat uživatele napříč klienty.  
>
> Hostované scénáře, kdy je uživatel doma v jednom tenantovi a ověřuje se v jiné, by měl uživatele zacházet, jako by se mu úplně zavedli.  Vaše dokumenty a oprávnění v tenantovi contoso by se neměly uplatňovat v tenantovi Fabrikam. To je důležité, aby se zabránilo náhodnému úniku dat napříč klienty.

### <a name="groups-overage-claim"></a>Nadlimitní deklarace skupin
Aby se zajistilo, že velikost tokenu nepřekračuje limity velikosti hlavičky HTTP, Azure AD omezí počet ID objektů, které v `groups` deklaraci identity zahrnuje. Pokud je uživatel členem více skupin, než je limit překročení (150 pro tokeny SAML, 200 pro tokeny JWT), služba Azure AD negeneruje v tokenu deklaraci identity skupin. Místo toho zahrnuje nadlimitní deklaraci identity v tokenu, která označuje aplikaci pro dotazování rozhraní Microsoft Graph API k načtení členství uživatele ve skupině.

```json
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
}
```

## <a name="validating-an-id_token"></a>Ověřování id_token

Ověřování `id_token` je podobné jako při [ověřování přístupového tokenu](access-tokens.md#validating-tokens) – váš klient může ověřit, jestli se token vrátil zpátky, a že se mu nepovedlo manipulováno. Vzhledem k tomu `id_tokens` , že jsou vždy token JWT, existuje mnoho knihoven, aby bylo možné tyto tokeny ověřit – doporučujeme místo toho použít jednu z nich.  Všimněte si, že tokeny ID by měly ověřovat jenom důvěrné klienty (s tajným klíčem).  Veřejné aplikace (kód spuštěný zcela na zařízení nebo v síti, které neovládáte – například v prohlížeči nebo v domácí síti) nevyužívají k ověřování tokenu ID, protože uživatel se zlými úmysly může zachytit a upravit klíče používané k ověření tokenu.

Postup ručního ověření tokenu najdete v podrobnostech v tématu [ověřování přístupového tokenu](access-tokens.md#validating-tokens). Po ověření podpisu tokenu by se měly v id_token ověřit následující deklarace identity JWT (můžou to udělat i vaše knihovna ověřování tokenů):

* Časová razítka `iat` : `nbf` `exp` Časová razítka, a musí všechny přecházet před aktuálním časem nebo po něm.
* Cílová skupina: `aud` deklarace identity by měla odpovídat ID aplikace pro vaši aplikaci.
* Hodnota nonce: `nonce` deklarace v datové části se musí shodovat s parametrem nonce předaným do koncového bodu/Authorize během počátečního požadavku.

## <a name="next-steps"></a>Další kroky

* Další informace o [přístupových tokenech](access-tokens.md)
* Přizpůsobte deklarace JWT ve vašem id_token pomocí [volitelných deklarací identity](active-directory-optional-claims.md).
