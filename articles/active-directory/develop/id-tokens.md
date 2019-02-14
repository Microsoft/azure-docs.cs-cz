---
title: Odkaz tokenu Azure Active Directory ID | Dokumentace Microsoftu
description: Další informace o použití id_tokens, protože ho vygeneroval koncové body v1.0 a v2.0 služby Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c92472d276b3f03e5a3855587de4ca8a045bfec2
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234420"
---
# <a name="id-tokens"></a>Tokeny ID

`id_tokens` se odesílají do klientské aplikace jako součást [OpenID Connect](v1-protocols-openid-connect-code.md) toku. Může být odeslána po straně nebo místo přístupový token a klient používá k ověření uživatele.

## <a name="using-the-idtoken"></a>Použití požadavku id_token

Tokeny typu ID by měla sloužit k ověření, že je uživatel který jejich tvrzení a další užitečné informace o nich – se nesmí používat pro povolení místo [přístupový token](access-tokens.md). Deklarace identity, které poskytuje lze použít pro uživatelské prostředí do své aplikace, vytvoření databáze a díky přístupu do klientské aplikace.

## <a name="claims-in-an-idtoken"></a>Deklarace identity ve za token ID.

`id_tokens` pro Microsoft identity jsou [tokeny Jwt](https://tools.ietf.org/html/rfc7519), což znamená sestávají z část záhlaví datové části a podpisu. Záhlaví a podpis slouží k ověření pravosti tokenu, zatímco datová část obsahuje informace o uživateli požadoval vašeho klienta. Pokud není uvedeno jinak všechny deklarace identity, které jsou tady uvedené zobrazovat v tokenech v1.0 a v2.0.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Zobrazit tento token ukázkové verze 1.0 v [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTE4ODA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjMzMzgwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0=.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Zobrazit tento token ukázkové verze 2.0 v [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTE4ODA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjMzMzgwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0=.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Deklarace identity záhlaví

|Deklarovat | Formát | Popis |
|-----|--------|-------------|
|`typ` | String – vždy tokenů JWT"" | Označuje, že token je token JWT.|
|`alg` | String | Určuje algoritmus, který se použil k podepsání token. Příklad: "RS256" |
|`kid` | String | Kryptografický otisk pro veřejný klíč použitý k podpisu tohoto tokenu. Zaznamenávány do v1.0 a v2.0 `id_tokens`. |
|`x5t` | String | Stejné (se používají a hodnota) jako `kid`. Je to ale starších deklarace identity zaznamenávány pouze do verze 1.0 `id_tokens` pro účely kompatibility. |

### <a name="payload-claims"></a>Datová část deklarace identity

|Deklarovat | Formát | Popis |
|-----|--------|-------------|
|`aud` |  Řetězec, identifikátor ID URI aplikace | Identifikuje zamýšlený příjemce tokenu. V `id_tokens`, cílová skupina je ID vaší aplikace, přiřazené vaší aplikaci na webu Azure Portal. Vaše aplikace by měl ověřit tuto hodnotu a odmítnout token, pokud hodnota se neshoduje. |
|`iss` |  Řetězec, identifikátor URI služby tokenů zabezpečení | Identifikuje službu tokenů zabezpečení (STS), který vytvoří a vrátí token a tenanta Azure AD, ve kterém byl uživatel ověřený. Pokud byl token vydán bodem v2.0, identifikátor URI, skončí za `/v2.0`.  Identifikátor GUID, který označuje, že uživatel je příjemce uživatele z účtu Microsoft je `9188040d-6c67-4c5b-b112-36a304b66dad`. Aplikace by měla část GUID deklarace slouží k omezení sadu klienty, kteří se můžou přihlásit k aplikaci, pokud je k dispozici. |
|`iat` |  int, UNIXOVÉ časové razítko | "Vystaveno v" označuje, kdy došlo k ověřování pro tento token.  |
|`idp`|Řetězec, obvykle o identifikátor URI služby tokenů zabezpečení | Zaznamenává zprostředkovatele identity, který ověřil subjekt tokenu. Tato hodnota se shoduje s hodnotu deklarace identity vystavitele, není-li uživatelský účet není ve stejném tenantovi jako vystavitel - hosté, třeba. Pokud deklarace identity není k dispozici, znamená to, že hodnota `iss` lze použít.  Pro osobní účty, které se používá v kontextu orgnizational (například pomocí osobního účtu pozvat do tenanta služby Azure AD) `idp` deklarace může být 'live.com' nebo identifikátor URI služby tokenů zabezpečení obsahující tenanta účtu Microsoft `9188040d-6c67-4c5b-b112-36a304b66dad`. |
|`nbf` |  int, UNIXOVÉ časové razítko | Deklarace identity "nbf" (ne dřív) označuje čas, před kterým musí nebylo přijato tokenů JWT pro zpracování.|
|`exp` |  int, UNIXOVÉ časové razítko | Deklarace identity "exp" (čas vypršení platnosti) označuje čas vypršení platnosti na nebo za které se token JWT nesmí přijaté ke zpracování.  Je důležité si uvědomit, že prostředek může před tímto časem také – Pokud například změny v ověřování se vyžaduje token zrušení byl zjištěn či odmítnout token. |
| `c_hash`| String |Hodnota hash kódu je součástí tokeny typu ID, pouze v případě, že je vydaný ID token s autorizačního kódu OAuth 2.0. Slouží k ověření pravosti autorizační kód. Podrobnosti o provedení tohoto ověření naleznete v tématu [OpenID Connect specifikace](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| String |Přístup, který je zahrnuta hodnota hash tokenu v ID tokeny, pouze pokud ID token vydaný s přístupovým tokenem OAuth 2.0. Slouží k ověření pravosti tokenu přístupu. Podrobnosti o provedení tohoto ověření naleznete v tématu [OpenID Connect specifikace](https://openid.net/specs/openid-connect-core-1_0.html). |
|`aio` | Neprůhledný řetězec | Vnitřní deklarace, která používá Azure AD při zaznamenávání dat pro opakované použití tokenu. Třeba ji ignorovat.|
|`preferred_username` | String | Primární uživatelské jméno, který reprezentuje uživatele. Může to být e-mailovou adresu, telefonní číslo nebo obecný uživatelské jméno bez zadaného formátu. Jeho hodnota je měnitelné a může v průběhu času měnit. Protože je proměnlivá, tato hodnota se nesmí používat pro autorizační rozhodnutí. `profile` Oboru se vyžaduje aby bylo možné dostávat tato deklarace identity.|
|`email` | String | `email` Deklarace identity je k dispozici ve výchozím nastavení pro účet hosta, které mají e-mailovou adresu.  Vaše aplikace můžou požádat o deklarace identity e-mailu pro spravované uživatele (ty ze stejného tenanta jako prostředek) pomocí `email` [volitelnou deklaraci](active-directory-optional-claims.md).  Na koncový bod v2.0, vaše aplikace také můžete vyžádat `email` obor OpenID Connect - není nutné požádat o volitelnou deklaraci a obor získat deklarace identity.  Deklarace identity e-mailu podporuje pouze adresovatelný e-mailu z informací v profilu uživatele. |
|`name` | String | `name` Deklarace identity poskytuje čitelné hodnotu, která identifikuje předmětem token. Hodnota nemusí být jedinečný, je proměnlivé a je navržena tak, aby se dá použít jenom pro účely zobrazení. `profile` Oboru se vyžaduje aby bylo možné dostávat tato deklarace identity. |
|`nonce`| String | Hodnota nonce odpovídá parametru součástí původní / authorize požadavek na zprostředkovatele identity. Pokud se neshoduje, vaše aplikace by měla odmítnout token. |
|`oid` | Řetězec identifikátoru GUID | Neměnné identifikátor objektu v Microsoft systém identit, v tomto případě uživatelský účet. Toto ID jednoznačně identifikuje uživatele v aplikacích – dva různé aplikace přihlášení stejného uživatele se zobrazí v stejnou hodnotu `oid` deklarací identity. Vrátí toto ID jako Microsoft Graph `id` vlastnost pro daný uživatelský účet. Protože `oid` umožňuje více aplikací ke koordinaci uživatelů, `profile` oboru se vyžaduje aby bylo možné dostávat tato deklarace identity. Všimněte si, že pokud jeden uživatel existuje v několika tenantech, uživatel bude obsahovat jiný objekt ID v jednotlivých tenantů – jsou považovány za různé účty, i když se uživatel přihlašuje každý účet pomocí stejných přihlašovacích údajů. |
|`rh` | Neprůhledný řetězec |Interní deklaraci identity Azure používá k odhlášením tokeny. Třeba ji ignorovat. |
|`sub` | Řetězec identifikátoru GUID | Objekt zabezpečení, o tom, které token vyhodnocuje informace, jako je například uživatel aplikace. Tato hodnota je neměnná a nelze přiřadit nebo znovu použít. Předmět je identifikátor pairwise – je jedinečné ID konkrétní aplikace. Proto pokud jeden uživatel zaregistruje do dvou různých aplikací s využitím dva identifikátory ID jiného klienta, na aplikace, které se zobrazí dvě různé hodnoty pro deklarace identity subjektu. To může nebo nemusí být požadovaných v závislosti na požadavcích vaší architektury a ochrana osobních údajů. |
|`tid` | Řetězec identifikátoru GUID | Identifikátor GUID, který představuje jejímž je uživatel z tenanta Azure AD. Pro pracovní a školní účty je identifikátor GUID ID neměnné tenanta organizace, které tento uživatel patří do. Pro osobní účty, je hodnota `9188040d-6c67-4c5b-b112-36a304b66dad`. `profile` Oboru se vyžaduje aby bylo možné dostávat tato deklarace identity. |
|`unique_name` | String | Poskytuje lidsky čitelnou hodnotu, která identifikuje subjekt tokenu. Tato hodnota nemusí být jedinečný v rámci tenanta a by měla sloužit pouze pro účely zobrazení. Pouze vystavených v v1.0 `id_tokens`. |
|`uti` | Neprůhledný řetězec | Interní deklaraci identity Azure používá k odhlášením tokeny. Třeba ji ignorovat. |
|`ver` | Řetězec, 1.0 nebo 2.0 | Určuje verzi modulu požadavku id_token. |

## <a name="validating-an-idtoken"></a>Ověřuje se za token ID.

Ověřování `id_token` je velmi podobná první krok [ověření přístupového tokenu](access-tokens.md#validating-tokens) – vašeho klienta by měl ověřit, zda správné vystavitele odeslal zpět token a že nikdo neoprávněně nemanipuloval s. Protože `id_tokens` jsou vždy token JWT, existuje mnoho knihoven pro ověření těchto tokenů – doporučujeme používat jednu z těchto spíše než to sami. 

Token ověří ručně, naleznete v postupu [ověření přístupového tokenu](access-tokens.md#validating-tokens). Po ověření podpisu v tokenu, následující deklarace by měla být ověřena v požadavku id_token (ty mohou také provádí knihovny ověřování tokenů):

* Časové razítko: `iat`, `nbf`, a `exp` časová razítka všechny patří před nebo po aktuálním čase podle potřeby. 
* Cílová skupina: `aud` deklarace musí odpovídat ID aplikace pro vaši aplikaci.
* Hodnota Nonce: `nonce` deklarace identity v datové části musí odpovídat parametr nonce předán koncovém bodu / authorize při počátečním požadavku.

## <a name="next-steps"></a>Další postup

* Další informace o [přístupové tokeny služby Azure AD](access-tokens.md)
