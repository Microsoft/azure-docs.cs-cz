---
title: Odkaz na token id platformy identity Společnosti Microsoft
description: Zjistěte, jak používat id_tokens vyzařované koncovými body platformy Azure AD v1.0 a platformy identit Microsoftu (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: 23ff71bf24c3acbce3d27276981739305e1d074a
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309537"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Tokeny ID platformy identit y Microsoft

`id_tokens`jsou odesílány do klientské aplikace jako součást toku [OpenID Connect.](v2-protocols-oidc.md) Mohou být odesílány spolu nebo místo přístupového tokenu a klient je používá k ověření uživatele.

## <a name="using-the-id_token"></a>Použití id_token

ID tokeny by měly být použity k ověření, že uživatel je, kdo tvrdí, že je a získat další užitečné informace o nich - by neměl být používán pro autorizaci namísto [přístupového tokenu](access-tokens.md). Deklarace, které poskytuje lze použít pro uživatelské uživatelské okno uvnitř aplikace, jako klíče v databázi a poskytuje přístup k klientské aplikaci.  Při vytváření klíčů pro `idp` databázi by neměly být používány, protože zmatky host scénáře.  Klíčování by mělo `sub` být provedeno samostatně (což je vždy jedinečné), s `tid` použitým pro směrování v případě potřeby.  Pokud potřebujete sdílet data mezi `oid` + `sub` + `tid` službami, bude fungovat, `oid`protože více služeb všechny získat stejné .

## <a name="claims-in-an-id_token"></a>Pohledávky v id_token

`id_tokens`pro identitu společnosti Microsoft jsou [JWTs](https://tools.ietf.org/html/rfc7519), což znamená, že se skládají z hlavičky, datové části a části podpisu. Záhlaví a podpis můžete použít k ověření pravosti tokenu, zatímco datová část obsahuje informace o uživateli požadované klientem. Pokud není uvedeno jinak, všechny deklarace zde uvedené se zobrazí v tokenech v1.0 i v2.0.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Zobrazit tento ukázkový token v1.0 v [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Zobrazit tento ukázkový token v2.0 v [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Nároky záhlaví

|Deklarovat | Formát | Popis |
|-----|--------|-------------|
|`typ` | Řetězec - vždy "JWT" | Označuje, že token je JWT.|
|`alg` | Řetězec | Označuje algoritmus, který byl použit k podepsání tokenu. Příklad: "RS256" |
|`kid` | Řetězec | Kryptografický otisk veřejného klíče, který se používá k podepsání tohoto tokenu. Vyzařované v v1.0 a v2.0 `id_tokens`. |
|`x5t` | Řetězec | Stejné (v použití a `kid`hodnota) jako . Toto je však starší deklarace vysílaná pouze v verzi 1.0 `id_tokens` pro účely kompatibility. |

### <a name="payload-claims"></a>Nároky datové části

Tento seznam zobrazuje deklarace identity, které jsou ve většině id_tokens ve výchozím nastavení (s výjimkou případů, kdy je uvedeno).  Vaše aplikace však může používat [volitelné deklarace identity](active-directory-optional-claims.md) k vyžádání dalších deklarací v id_token.  Ty mohou být `groups` v rozsahu od deklarace až po informace o jménu uživatele.

|Deklarovat | Formát | Popis |
|-----|--------|-------------|
|`aud` |  Řetězec, identifikátor URI ID aplikace | Identifikuje zamýšleného příjemce tokenu. V `id_tokens`aplikaci je okruh uživatelů ID aplikace přiřazený k vaší aplikaci na webu Azure Portal. Vaše aplikace by měla ověřit tuto hodnotu a odmítnout token, pokud hodnota neodpovídá. |
|`iss` |  Řetězec, identifikátor URI STS | Identifikuje službu tokenů zabezpečení (STS), která vytváří a vrací token, a klienta Azure AD, ve kterém byl uživatel ověřen. Pokud byl token vydán koncovým bodem v2.0, identifikátor `/v2.0`URI skončí v .  Identifikátor GUID, který označuje, že uživatel je `9188040d-6c67-4c5b-b112-36a304b66dad`uživatel příjemce z účtu Microsoft je . Vaše aplikace by měla použít část deklarace GUID k omezení sady klientů, kteří se mohou k aplikaci přihlásit, pokud je to možné. |
|`iat` |  int, časové razítko systému UNIX | "Vydáno na" označuje, kdy došlo k ověření pro tento token.  |
|`idp`|Řetězec, obvykle identifikátor URI systému STS | Zaznamenává zprostředkovatele identity, který ověřil subjekt tokenu. Tato hodnota je shodná s hodnotou deklarace vystavittele, pokud uživatelský účet není ve stejném tenantovi jako vydavatel - hosté, například. Pokud deklarace není k dispozici, znamená `iss` to, že hodnota lze použít místo.  Pro osobní účty používané v kontextu organizace (například osobní účet pozvaný k `idp` tenantovi Azure AD) může být deklarace pohledávky "live.com" nebo IDENTIFIKÁTOR URI STS obsahující klienta `9188040d-6c67-4c5b-b112-36a304b66dad`účtu Microsoft . |
|`nbf` |  int, časové razítko systému UNIX | Tvrzení "nbf" (ne dříve) označuje dobu, před kterou nesmí být JWT přijat ke zpracování.|
|`exp` |  int, časové razítko systému UNIX | Deklarace "exp" (expirace) identifikuje čas vypršení platnosti, na kterém nebo po kterém Nesmí být JWT přijat ke zpracování.  Je důležité si uvědomit, že prostředek může odmítnout token před touto dobou také - pokud je například požadována změna ověřování nebo bylo zjištěno odvolání tokenu. |
| `c_hash`| Řetězec |Hash kódu je součástí tokenů ID pouze v případě, že token ID je vydán s autorizačním kódem OAuth 2.0. Lze jej použít k ověření pravosti autorizačního kódu. Podrobnosti o provedení tohoto ověření naleznete ve [specifikaci OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| Řetězec |Hash přístupového tokenu je součástí tokenů ID pouze v případě, že je token ID vydán s přístupovým tokenem OAuth 2.0. Lze jej použít k ověření pravosti přístupového tokenu. Podrobnosti o provedení tohoto ověření naleznete ve [specifikaci OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`aio` | Neprůhledný řetězec | Interní deklarace, kterou používá Služba Azure AD k zaznamenání dat pro opětovné použití tokenu. By měly být ignorovány.|
|`preferred_username` | Řetězec | Primární uživatelské jméno, které představuje uživatele. Může to být e-mailová adresa, telefonní číslo nebo obecné uživatelské jméno bez zadaného formátu. Jeho hodnota je proměnlivá a může se v průběhu času měnit. Vzhledem k tomu, že je proměnlivá, tato hodnota nesmí být použita k rozhodování o autorizaci. K `profile` přijetí této deklarace je nutný rozsah.|
|`email` | Řetězec | Nárok `email` je k dispozici ve výchozím nastavení pro účty hosta, které mají e-mailovou adresu.  Vaše aplikace může požádat o e-mailovou deklaraci pro spravované `email` uživatele (ty ze stejného klienta jako prostředek) pomocí [volitelné deklarace .](active-directory-optional-claims.md)  V koncovém bodě v2.0 může vaše `email` aplikace také požádat o obor OpenID Connect – k získání deklarace nároku nemusíte požadovat jak volitelnou deklaraci, tak obor.  E-mailová deklarace podporuje pouze adresovatelnou poštu z informací profilu uživatele. |
|`name` | Řetězec | Deklarace `name` poskytuje hodnotu čitelnou pro člověka, která identifikuje předmět tokenu. Hodnota není zaručena jako jedinečná, je proměnlivá a je navržena pro použití pouze pro účely zobrazení. K `profile` přijetí této deklarace je nutný rozsah. |
|`nonce`| Řetězec | Nonce odpovídá parametru obsaženému v původním požadavku /authorize idp. Pokud se neshoduje, aplikace by měla odmítnout token. |
|`oid` | Řetězec, identifikátor GUID | Neměnný identifikátor objektu v systému identit společnosti Microsoft, v tomto případě uživatelský účet. Toto ID jednoznačně identifikuje uživatele napříč aplikacemi – dvě různé aplikace podepisující se ve stejném uživateli obdrží stejnou hodnotu v deklaraci. `oid` Microsoft Graph vrátí toto ID jako `id` vlastnost pro daný uživatelský účet. Vzhledem `oid` k tomu, že umožňuje `profile` více aplikací korelovat uživatele, obor je nutné přijímat tuto deklaraci. Všimněte si, že pokud jeden uživatel existuje ve více klientech, uživatel bude obsahovat jiné ID objektu v každém klientovi – jsou považovány za různé účty, i když se uživatel přihlásí ke každému účtu se stejnými pověřeními. Deklarace `oid` je identifikátor GUID a nelze jej znovu použít. |
|`roles`| Pole řetězců | Sada rolí, které byly přiřazeny uživateli, který se přihlašuje. |
|`rh` | Neprůhledný řetězec |Interní deklarace, kterou Azure používá k opětovnému ověření tokenů. By měly být ignorovány. |
|`sub` | Řetězec, identifikátor GUID | Hlavní objekt, o kterém token uplatňuje informace, jako je například uživatel aplikace. Tato hodnota je neměnná a nelze ji znovu přiřadit ani znovu použít. Předmět je párový identifikátor - je jedinečný pro konkrétní ID aplikace. Pokud se jeden uživatel přihlásí do dvou různých aplikací pomocí dvou různých ID klienta, obdrží tyto aplikace dvě různé hodnoty pro deklaraci předmětu. To může nebo nemusí být žádoucí v závislosti na vaší architektuře a požadavky na ochranu osobních údajů. |
|`tid` | Řetězec, identifikátor GUID | Identifikátor GUID, který představuje klienta Azure AD, ze kterého uživatel pochází. Pro pracovní a školní účty je identifikátor GUID neměnné ID klienta organizace, do které uživatel patří. Pro osobní účty je `9188040d-6c67-4c5b-b112-36a304b66dad`hodnota . K `profile` přijetí této deklarace je nutný rozsah. |
|`unique_name` | Řetězec | Poskytuje lidsky čitelnou hodnotu, která identifikuje subjekt tokenu. Tato hodnota je jedinečná v daném okamžiku, ale jako e-maily a další identifikátory lze znovu použít, tato hodnota může znovu objevit na jiných účtech, a proto by měly být použity pouze pro účely zobrazení. Vydáno pouze v v1.0 `id_tokens`. |
|`uti` | Neprůhledný řetězec | Interní deklarace, kterou Azure používá k opětovnému ověření tokenů. By měly být ignorovány. |
|`ver` | Řetězec, buď 1,0 nebo 2,0 | Označuje verzi id_token. |


> [!NOTE]
> V1 a v2 id_token mají rozdíly v množství informací, které budou mít, jak je patrné z výše uvedených příkladů. Verze v podstatě určuje koncový bod platformy Azure AD, odkud byla vydána. [Implementace Azure AD Oauth](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) se vyvíjela v průběhu let. V současné době máme dva různé koncové body oAuth pro aplikace AzureAD. Můžete použít některý z nových koncových bodů, které jsou rozděleny do kategorií jako v2 nebo starý, který je řekl, aby byl v1. Koncové body Oauth pro oba z nich se liší. Koncový bod V2 je novější, kde se snažíme migrovat všechny funkce koncového bodu v1 a doporučujeme novým vývojářům používat koncový bod v2. 
> - V1: Koncové body služby Azure Active Directory:`https://login.microsoftonline.com/common/oauth2/authorize`
> - V2: Koncové body platformy Microsoft Identity Platform:`https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

## <a name="validating-an-id_token"></a>Ověření id_token

Ověření ověření `id_token` je podobný prvnímu kroku [ověření přístupového tokenu](access-tokens.md#validating-tokens) – váš klient by měl ověřit, že správný vystavitel odeslal token zpět a že s ním nebylo manipulováno. Vzhledem k tomu, `id_tokens` že jsou vždy JWT, mnoho knihoven existují k ověření těchto tokenů - doporučujeme použít jeden z nich, spíše než dělat sami.

Chcete-li token ověřit ručně, přečtěte si podrobnosti o postupech [ověření přístupového tokenu](access-tokens.md#validating-tokens). Po ověření podpisu na tokenu by měly být ověřeny následující deklarace identity v id_token (ty mohou být také provedeny vaší knihovnou ověření tokenu):

* Časová `iat`razítka: `nbf`, `exp` , a časová razítka by měla všechny spadají před nebo po aktuálním čase, podle potřeby. 
* Cílová `aud` skupina: nárok by se měl shodovat s ID aplikace pro vaši aplikaci.
* Nonce: `nonce` deklarace v datové části se musí shodovat s parametrem nonce předaná koncovému bodu /authorize během počátečního požadavku.

## <a name="next-steps"></a>Další kroky

* Informace o [přístupových tokenech](access-tokens.md)
* Přizpůsobte deklarace identity ve id_token pomocí [volitelných deklarací identity](active-directory-optional-claims.md).
