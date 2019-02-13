---
title: Přístup k Azure Active Directory tokeny odkaz | Dokumentace Microsoftu
description: Další informace o přístupové tokeny, protože ho vygeneroval koncové body v1.0 a v2.0 služby Azure AD.
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
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45275a02467ab5e8592399e4eeae7f74a582c87a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56200373"
---
# <a name="azure-active-directory-access-tokens"></a>Azure Active Directory přístupové tokeny

Přístupové tokeny umožňují klientům bezpečně volat rozhraní API chráněné službou Azure. Azure Active Directory (Azure AD) přístupové tokeny jsou [tokeny Jwt](https://tools.ietf.org/html/rfc7519), objekty JSON podepsány Azure s kódováním Base64. Klienti by měly zpracovávat přístup tokeny jako neprůhledný řetězce, jako obsah tokenu jsou určené pro prostředek jenom. Pro ověření a účely ladění, vývojáři dekódoval tokeny Jwt pomocí web [jwt.ms](https://jwt.ms). Váš klient může získat přístupový token z obou koncového bodu (verze 1.0 nebo 2.0) pomocí různých protokolů.

Při žádosti o přístupový token Azure AD také vrátí hodnotu některá metadata o přístupový token pro využití vaší aplikace. Tyto informace zahrnují čas vypršení platnosti přístupového tokenu a oborů, pro které je platný. Tato data umožňuje aplikaci inteligentního ukládání přístupových tokenů bez nutnosti parsovat přístupový token, samotné do mezipaměti.

Pokud je aplikace, který klienti mohou požadovat přístup k prostředku (webové rozhraní API), zadejte přístupové tokeny pro účely ověřování a autorizace, jako je například uživatel, klient, Vystavitel, oprávnění a další užitečné informace. 

Naleznete v následujících částech se dozvíte, jak ověřit a používat deklarace identity uvnitř přístupového tokenu prostředku.

> [!NOTE]
> Při testování vaší klientské aplikace pomocí osobního účtu (například hotmail.com nebo outlook.com), můžete zjistit, že přístupový token přijatých vašeho klienta je neprůhledný řetězec. Je to proto, že prostředek, ke kterému přistupujete požádal o starší verze (účet Microsoft) lístky MSA, které jsou zašifrované a nemůže být srozumitelné pro klienta.

## <a name="sample-tokens"></a>Ukázka tokeny

tokeny V1.0 a v2.0 vypadat podobně jako a obsahují mnoho stejné deklarací identity. Příklad každého je k dispozici zde.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0=.D3H6pMUtQnoJAGq6AHd
```

Zobrazit tento token v1.0 v [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0=.D3H6pMUtQnoJAGq6AHd).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Zobrazit tento token v2.0 v [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>Deklarace identity do tokenů přístupu

Tokeny Jwt jsou rozděleny do tří částí: 

* **Hlavička** – poskytuje informace o tom, jak [token ověří](#validating-tokens) včetně informací o typu token a jak se znaménkem.
* **Datová část** – obsahuje všechna důležitá data o uživateli nebo aplikaci, která se pokouší volat vaši službu.
* **Podpis** – je suroviny použitý k ověření tokenu.

Každá část je oddělená tečkou (`.`) a samostatně kódování Base64.

Deklarace identity jsou k dispozici pouze v případě, že existuje hodnota do něj. Vaší aplikace tak, neměla by mít závislost na deklaraci identity byla vyžadována jeho přítomnost. Mezi příklady patří `pwd_exp` (ne každá klient vyžaduje hesla vyprší) nebo `family_name` ([pověření klienta](v1-oauth2-client-creds-grant-flow.md) toky se jménem aplikace, které nemají názvy). Deklarace identity použité k ověření tokenu přístupu budou vždy k dispozici.

> [!NOTE]
> Některé deklarace identity se používají k pomoci zabezpečit tokeny v případě opakované použití služby Azure AD. Jsou označeny jako nebude ke zveřejnění v popisu jako "Neprůhledné". Tyto deklarace může nebo nemusí zobrazit v tokenu, a mohou být přidány nové značky bez předchozího upozornění.

### <a name="header-claims"></a>Deklarace identity záhlaví

|Deklarovat | Formát | Popis |
|--------|--------|-------------|
| `typ` | String – vždy tokenů JWT"" | Označuje, že token je token JWT.|
| `nonce` | String | Jedinečný identifikátor sloužící k ochraně před útoky opětovného přehrání tokenu. Váš prostředek můžete zaznamenat tato hodnota k ochraně proti riziko. |
| `alg` | String | Určuje algoritmus, který se použil k podepsání token, například "RS256" |
| `kid` | String | Určuje kryptografický otisk pro veřejný klíč, který se používá k podepsání tento token. Zaznamenávány do v1.0 a v2.0 přístupové tokeny. |
| `x5t` | String | Funguje stejně (se používají a hodnota) jako `kid`. `x5t` je vygenerován starších deklarace identity jenom v v1.0 přístupové tokeny pro účely kompatibility. |

### <a name="payload-claims"></a>Datová část deklarace identity

| Deklarovat | Formát | Popis |
|-----|--------|-------------|
| `aud` | Řetězec, identifikátor ID URI aplikace | Identifikuje zamýšlený příjemce tokenu. V přístupových tokenech je cílová skupina ID vaší aplikace, přiřazené vaší aplikaci na webu Azure Portal. Vaše aplikace by měl ověřit tuto hodnotu a odmítnout token, pokud hodnota se neshoduje. |
| `iss` | Řetězec, identifikátor URI služby tokenů zabezpečení | Identifikuje službu tokenů zabezpečení (STS), který vytvoří a vrátí token a tenanta Azure AD, ve kterém byl uživatel ověřený. Pokud je token vydaný v2.0 token (najdete v článku `ver` deklarace identity), identifikátor URI, skončí za `/v2.0`. Identifikátor GUID, který označuje, že uživatel je příjemce uživatele z účtu Microsoft je `9188040d-6c67-4c5b-b112-36a304b66dad`. Aplikace by měla část GUID deklarace slouží k omezení sadu klienty, kteří se můžou přihlásit k aplikaci, pokud je k dispozici. |
|`idp`|Řetězec, obvykle o identifikátor URI služby tokenů zabezpečení | Zaznamenává zprostředkovatele identity, který ověřil subjekt tokenu. Tato hodnota se shoduje s hodnotu deklarace identity vystavitele, není-li uživatelský účet není ve stejném tenantovi jako vystavitel - hosté, třeba. Pokud deklarace identity není k dispozici, znamená to, že hodnota `iss` lze použít.  Pro osobní účty, které se používá v kontextu organizace (například pomocí osobního účtu pozvat do tenanta služby Azure AD) `idp` deklarace může být 'live.com' nebo identifikátor URI služby tokenů zabezpečení obsahující tenanta účtu Microsoft `9188040d-6c67-4c5b-b112-36a304b66dad`. |  
| `iat` | int, UNIXOVÉ časové razítko | "Vystaveno v" označuje, kdy došlo k ověřování pro tento token. |
| `nbf` | int, UNIXOVÉ časové razítko | Deklarace identity "nbf" (ne dřív) označuje čas, před kterým nesmí být přijaty tokenů JWT pro zpracování. |
| `exp` | int, UNIXOVÉ časové razítko | Deklarace identity "exp" (čas vypršení platnosti) označuje čas vypršení platnosti, po který tokenů JWT nesmí být přijata ke zpracování dni. Je důležité si uvědomit, že prostředek může odmítnout token před tímto časem stejně, jako je například, pokud je požadované změny v ověřování nebo byl zjištěn token zrušení. |
| `acr` | Řetězec "0" a "1" | Deklarace identity "Authentication context – třída". Hodnota "0" značí, že ověřování koncového uživatele nesplňuje požadavky ISO/IEC 29115. |
| `aio` | Neprůhledný řetězec | Vnitřní deklarace, která používá Azure AD při zaznamenávání dat pro opakované použití tokenu. Prostředky, neměli byste používat tato deklarace identity. |
| `amr` | Pole JSON řetězců | Pouze k dispozici v tokenech v1.0. Určuje, jak byl předmět token ověření. V tématu [amr deklarace identity části](#the-amr-claim) další podrobnosti. |
| `appid` | Řetězec identifikátoru GUID | Pouze k dispozici v tokenech v1.0. ID aplikace pomocí tokenu klienta. Aplikace může fungovat jako samotné nebo jménem uživatele. ID aplikace obvykle představuje objekt aplikace, ale může také představovat instanční objekt ve službě Azure AD. |
| `appidacr` | "0", "1" nebo "2" | Pouze k dispozici v tokenech v1.0. Určuje, jak došlo k ověření klienta. Pro veřejné klienta hodnota je "0". Pokud používáte ID klienta a tajný kód klienta, hodnota je "1". Pokud certifikát klienta byla použita pro ověřování, hodnota je "2". |
| `azp` | Řetězec identifikátoru GUID | Pouze k dispozici v tokenech verze 2.0. ID aplikace pomocí tokenu klienta. Aplikace může fungovat jako samotné nebo jménem uživatele. ID aplikace obvykle představuje objekt aplikace, ale může také představovat instanční objekt ve službě Azure AD. |
| `azpacr` | "0", "1" nebo "2" | Pouze k dispozici v tokenech verze 2.0. Určuje, jak došlo k ověření klienta. Pro veřejné klienta hodnota je "0". Pokud používáte ID klienta a tajný kód klienta, hodnota je "1". Pokud certifikát klienta byla použita pro ověřování, hodnota je "2". |
| `groups` | Pole JSON identifikátorů GUID | Poskytuje ID objektů, které představují členství ve skupinách daného subjektu. Tyto hodnoty jsou jedinečné (viz ID objektu) a lze jej bezpečně používat při správě přístupu, jako je například vynucování autorizaci pro přístup k prostředku. Skupiny součástí skupiny deklarace identity jsou nakonfigurované na základě jednotlivých aplikací prostřednictvím `groupMembershipClaims` vlastnost [manifest aplikace](reference-app-manifest.md). Hodnotu null, vyloučí všechny skupiny, hodnota "Skupiny SecurityGroup" bude obsahovat pouze členství ve skupinách zabezpečení Active Directory a hodnota "Vše" bude obsahovat skupiny zabezpečení a Office 365 distribuční seznamy. <br><br>Najdete v článku `hasgroups` deklarace identity níže podrobnosti o použití `groups` deklarace identity s implicitního udělení. <br>Pro další toky Pokud počet skupin, které je uživatel v překročí omezení (pro SAML 150, 200 pro token JWT), pak při překročení limitu deklarace identity se přidají do zdroje deklarací identity směřující na koncový bod grafu obsahující seznam skupin pro daného uživatele. |
| `hasgroups` | Logická hodnota | Pokud jsou k dispozici, vždy `true`, které označuje, že uživatel je v aspoň jedné skupině. Použité místo `groups` deklaraci identity pro tokeny Jwt implicitní grant toků, pokud celé skupiny deklarací identity i fragment URI nad rámec omezení délky adresu URL (aktuálně 6 nebo více skupin). Označuje, že klient musí použít graf k určení skupin uživatele (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` | JSON – objekt | Pro žádosti o tokeny, které nejsou omezena délka (naleznete v tématu `hasgroups` výše), ale stále příliš velký pro daný token, odkaz na seznam úplné skupiny pro uživatele, budou zahrnuty. Pro tokeny Jwt jako deklaraci distribuovaný pro SAML jako novou deklaraci místo `groups` deklarací identity. <br><br>**Příklad hodnoty tokenů JWT**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }`|
| `preferred_username` | String | Primární uživatelské jméno, který reprezentuje uživatele. Může to být e-mailovou adresu, telefonní číslo nebo obecný uživatelské jméno bez zadaného formátu. Jeho hodnota je měnitelné a může v průběhu času měnit. Protože je proměnlivá, tato hodnota se nesmí používat pro autorizační rozhodnutí.  Může sloužit pro uživatelské jméno Rady ale. `profile` Oboru se vyžaduje aby bylo možné dostávat tato deklarace identity. |
| `name` | String | Poskytuje čitelné hodnotu, která identifikuje předmětem token. Hodnota nemusí být jedinečný, je proměnlivé a je navržena tak, aby se dá použít jenom pro účely zobrazení. `profile` Oboru se vyžaduje aby bylo možné dostávat tato deklarace identity. |
| `oid` | Řetězec identifikátoru GUID | Neměnné identifikátor objektu v platformě Microsoft identity, v tomto případě uživatelský účet. To lze použít také k provedení kontroly autorizace bezpečně a jako klíč v tabulkách databáze. Toto ID jednoznačně identifikuje uživatele v aplikacích – dva různé aplikace přihlášení stejného uživatele se zobrazí v stejnou hodnotu `oid` deklarací identity. Proto `oid` se dá použít při vytváření dotazů k online službám Microsoftu, jako je například Microsoft Graphu. Vrátí toto ID jako Microsoft Graph `id` vlastnost pro daný uživatelský účet. Protože `oid` umožňuje více aplikací ke koordinaci uživatelů, `profile` oboru se vyžaduje aby bylo možné dostávat tato deklarace identity. Všimněte si, že pokud jeden uživatel existuje v několika tenantech, uživatel bude obsahovat jiný objekt ID v jednotlivých tenantů – jsou považovány za různé účty, i když se uživatel přihlašuje každý účet pomocí stejných přihlašovacích údajů. |
| `rh` | Neprůhledný řetězec | Interní deklaraci identity Azure používá k odhlášením tokeny. Prostředky, neměli byste používat tato deklarace identity. |
| `scp` | Řetězec, mezerou oddělený seznam oborů | Sada oborů vystaven vaší aplikací, pro kterou má klientská aplikace požadované (a přijaté) vyjádřit souhlas. Vaše aplikace by měl ověřit, že tyto obory platný ty, které jsou vystavené vaší aplikace a rozhodnutí o autorizaci na základě hodnoty příslušných oborech. Zahrnuto pouze z důvodu [tokeny uživatele](#user-and-application-tokens). |
| `roles`| Řetězec, mezerou oddělený seznam oprávnění | Sada oprávnění vystavené aplikaci žádající aplikací má přiděleno oprávnění k volání. Používá se během [přihlašovacích údajů klienta](v1-oauth2-client-creds-grant-flow.md) tok namísto uživatele obory a platí jenom v [tokenů aplikace](#user-and-application-tokens). |
| `sub` | Řetězec identifikátoru GUID | Objekt zabezpečení, o tom, které token vyhodnocuje informace, jako je například uživatel aplikace. Tato hodnota je neměnná a nelze přiřadit nebo znovu použít. Můžete použít k provedení kontroly autorizace bezpečně, třeba když se používá token pro přístup k prostředku a můžete použít jako klíč v tabulkách databáze. Protože předmět je vždy k dispozici v tokenech, problémy s Azure AD, doporučujeme použít tuto hodnotu v systému pro obecné účely autorizace. Předmět je však identifikátor pairwise – je jedinečné ID konkrétní aplikace. Proto pokud jeden uživatel zaregistruje do dvou různých aplikací s využitím dva identifikátory ID jiného klienta, na aplikace, které se zobrazí dvě různé hodnoty pro deklarace identity subjektu. To může nebo nemusí být požadovaných v závislosti na požadavcích vaší architektury a ochrana osobních údajů. |
| `tid` | Řetězec identifikátoru GUID | Představuje jejímž je uživatel z tenanta Azure AD. Pro pracovní a školní účty je identifikátor GUID ID neměnné tenanta organizace, které tento uživatel patří do. Pro osobní účty, je hodnota `9188040d-6c67-4c5b-b112-36a304b66dad`. `profile` Oboru se vyžaduje aby bylo možné dostávat tato deklarace identity. |
| `unique_name` | String | Pouze k dispozici v tokenech v1.0. Poskytuje lidsky čitelnou hodnotu, která identifikuje subjekt tokenu. Tato hodnota nemusí být jedinečný v rámci tenanta a by měla sloužit pouze pro účely zobrazení. |
| `uti` | Neprůhledný řetězec | Interní deklaraci identity Azure používá k odhlášením tokeny. Prostředky, neměli byste používat tato deklarace identity. |
| `ver` | Řetězec, 1.0 nebo 2.0 | Určuje verzi modulu přístupový token. |

#### <a name="v10-basic-claims"></a>deklarace základní verze 1.0

Následující deklarace identity se zahrnou v1.0 tokeny, pokud je k dispozici, ale nejsou součástí v2.0 tokeny ve výchozím nastavení. Pokud používáte verze 2.0 a potřebu jeden tyto deklarace, o ně požádat pomocí [nepovinných deklarací identity](active-directory-optional-claims.md).

| Deklarovat | Formát | Popis |
|-----|--------|-------------|
| `ipaddr`| String | Uživatel se ověřil z IP adresy |
| `onprem_sid`| Řetězec v [formát čísel SID](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | V případech, kde má uživatel místní ověřování poskytuje tato deklarace identifikátoru SID. Můžete použít `onprem_sid` pro ověření ve starší verzi aplikace. |
| `pwd_exp`| int, UNIXOVÉ časové razítko | Určuje, kdy vyprší platnost hesla uživatele. |
| `pwd_url`| String | Adresa URL odešle uživatelům resetovat své heslo. |
| `in_corp`|Boolean | Signály, pokud je klient přihlašování z podnikové sítě. Pokud nejsou, není součástí deklarace identity. |
| `nickname`| String | Další jméno pro uživatele, nezávisle na první nebo poslední název.|
| `family_name` | String | Poskytuje poslední jméno, příjmení nebo příjmení uživatele, jak je definováno v objektu user. |
| `given_name` | String | Nabízí první nebo křestní jméno uživatele, jak v objektu user. |
| `upn` | String | Uživatelské jméno uživatele. Může být telefonního čísla, e-mailovou adresu nebo neformátovaném tvaru řetězec. By měla sloužit pouze pro účely zobrazení a zadání pomocných parametrů uživatelské jméno ve scénářích opětovné ověření. |

#### <a name="the-amr-claim"></a>`amr` Deklarace identity

Microsoft identity můžete ověřit v celou řadu způsobů, který může být relevantní pro vaši aplikaci. `amr` Deklarace identity je pole, které mohou obsahovat více položek, jako například `["mfa", "rsa", "pwd"]`, pro ověření, který používá heslo a ověřovací aplikaci. 

| Hodnota | Popis |
|-----|-------------|
| `pwd` | Ověřování pomocí hesla uživatele Microsoft heslo nebo tajný klíč klienta aplikace. |
| `rsa` | Ověřování bylo založeno na důkaz klíč RSA, například s [aplikaci Microsoft Authenticator](https://aka.ms/AA2kvvu). Patří sem, pokud bylo ověřování službou vlastněných X509 prováděné token JWT podepsaný svým držitelem certifikátu. |
| `otp` | Jednorázové heslo pomocí e-mailem nebo textovou zprávu. |
| `fed` | Kontrolní výraz federovaného ověřování (například token JWT nebo SAML) byla použita. |
| `wia` | Integrované ověřování systému Windows |
| `mfa` | Ověřování službou Multi-Factor Authentication byl použit. Když existuje jiné metody ověřování budou také zahrnuty. |
| `ngcmfa` | Ekvivalentní `mfa`, která slouží k zřizování určité typy pokročilé přihlašovacích údajů. |
| `wiaormfa`| Uživatel používá Windows nebo pověření vícefaktorové ověřování k ověření. |
| `none` | Byla provedena bez ověřování. |

## <a name="validating-tokens"></a>Ověřování tokenů

Ověření tokentu id_token nebo access_token, by měla vaše aplikace ověřit podpis tokenu a deklarace identity. Chcete-li ověřit přístupové tokeny, by měla vaše aplikace také ověření vystavitele, cílovou skupinu a podepisování tokenů. Tyto názvy musí být ověřena hodnoty v dokumentu zjišťování OpenID. Například se nachází v tenantovi nezávislé verzi dokumentu [ https://login.microsoftonline.com/common/.well-known/openid-configuration ](https://login.microsoftonline.com/common/.well-known/openid-configuration). 

Azure AD middleware obsahuje integrované funkce pro ověřování tokenů přístupu, a můžete procházet pomocí našich [ukázky](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) najít v jazyce podle vašeho výběru. Další informace o tom, jak explicitně ověřit JWT token, najdete v článku [ruční Ukázka ověřování tokenů JWT](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation). 

Poskytuje knihovny a ukázky kódu, které ukazují, jak snadno zpracovávat ověřování tokenů. Níže uvedené informace, které se poskytuje pro ty, kteří chtějí porozumět základního procesu. Dostupné jsou také několik jiných výrobců open source knihoven ověřování tokenů JWT: k dispozici aspoň jedna možnost pro téměř každou platformu a jazyk tam. Další informace o knihoven ověřování služby Azure AD a ukázky kódu najdete v tématu [knihovny ověřování v1.0](active-directory-authentication-libraries.md) a [knihovny pro ověřování v2.0](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>Ověření podpisu

Token JWT obsahuje tři segmenty, které jsou odděleny `.` znak. První segment se označuje jako **záhlaví**, druhý jako **tělo**a třetí jako **podpis**. Segment podpis slouží k ověření pravosti tokenu tak, aby může být důvěřuje vaší aplikace.

Tokeny vystavené službou Azure AD jsou podepsány pomocí algoritmů standardní asymetrického šifrování odvětví, jako je například RSA 256. Záhlaví tokenů JWT obsahuje informace o metodě a šifrovací klíče použitý k podpisu tokenu:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

`alg` Deklarace identity označuje algoritmus, který se použil k podepsání tokenů, přestože `kid` deklarace identity označuje konkrétní veřejný klíč, který se použil k podepsání token.

V libovolném časovém okamžiku v čase může vyžadovat přihlášení Azure AD tokentu id_token pomocí některé z sadu párů veřejného a privátního klíče. Azure AD otočí možné sadu klíčů v pravidelných intervalech, takže vaše aplikace by měla napsané tak, aby tyto klíče změny automaticky. Přiměřené intervalech a kontrolují dostupnost aktualizací pro veřejné klíče používané službou Azure AD je každých 24 hodin.

Můžete získat podpisové klíče data potřebná k ověření podpisu pomocí dokument metadat OpenID Connect, který se nachází v:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [!TIP]
> Vyzkoušejte to [URL](https://login.microsoftonline.com/common/.well-known/openid-configuration) v prohlížeči.

Tento dokument metadat:

* Je objekt JSON obsahující několik užitečné údaje, jako je například umístění různých koncových bodů požadovaných pro ověřování OpenID Connect. 
* Zahrnuje `jwks_uri`, což dává umístění sady veřejného klíče používané k podepisování tokenů. Umístění dokumentu JSON `jwks_uri` obsahuje všechny údaje o veřejném klíči použité v této konkrétní okamžik v čase. Vaše aplikace může používat `kid` deklarací identity v hlavičce JWT k výběru, který veřejný klíč v tomto dokumentu se použil k podepsání konkrétní token. Pak můžete provádět ověření podpisu pomocí správný veřejný klíč a označený algoritmus.

> [!NOTE]
> Koncový bod verze 1.0 vrací i `x5t` a `kid` deklarací identity, zatímco koncový bod v2.0 odpoví pouze `kid` deklarací identity. Od této chvíle, vám doporučujeme používat `kid` deklarace identity k ověření tokenu.

Provádí se ověření podpisu je mimo rámec tohoto dokumentu – k dispozici řada open source knihoven pomáhá vám tak v případě potřeby.

### <a name="claims-based-authorization"></a>Autorizace deklarovaných identit

Obchodní logiky vaší aplikace bude určovat tento krok, některé běžné metody ověřování jsou podrobně popsány níže.

* Zkontrolujte, `scp` nebo `roles` deklarace identity k ověření, že všechny obory k dispozici shodovat s hodnotami vystavené vašeho rozhraní API, a povolit klienta k provedení požadované akce.
* Zajistila volajícího klienta může volat rozhraní API pomocí `appid` deklarací identity.
* Ověřit stav ověření volajícího klienta pomocí `appidacr` -by neměl být 0, pokud veřejní klienti nejsou povoleny pro volání rozhraní API.
* Zkontrolujte seznam z minulosti `nonce` deklarací identity k ověření tokenu není právě znovu přehrát.
* Zkontrolujte, že `tid` odpovídá tenanta, která může volat rozhraní API.
* Použití `acr` deklarace identity, chcete-li ověřit, uživatel provedl vícefaktorové ověřování. To by se měly vynucovat použití [podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).
* Pokud jste požadovali `roles` nebo `groups` deklarací identity v tokenu přístupu, ověřte, že uživatel je ve skupině oprávnění k provedení této akce.
  * Pro tokeny pomocí implicitního toku načíst, bude pravděpodobně nutné k dotazování [Microsoft Graphu](https://developer.microsoft.com/graph/) pro tato data, protože je často token příliš velká. 

## <a name="user-and-application-tokens"></a>Tokeny uživatelů a aplikací

Vaše aplikace může přijímat tokeny jménem uživatele (obvykle tok) nebo přímo z aplikace (prostřednictvím [údajů klienta, které tok](v1-oauth2-client-creds-grant-flow.md)). Tyto tokeny jen pro aplikace znamenat, že toto volání pochází z aplikace a nemá žádné uživatele jejich zálohování. Tyto tokeny jsou zpracovány z velké části stejný, s určitými rozdíly:

* Tokeny jen pro aplikace nebudou mít `scp` deklarace identity a místo toho budou mít `roles` deklarací identity. Je to, kde budou zaznamenány oprávnění k aplikaci (na rozdíl od delegovaná oprávnění). Další informace o oprávnění pro delegovanou a aplikace, najdete v části oprávnění a souhlas v [v1.0](v1-permissions-and-consent.md) a [v2.0](v2-permissions-and-consent.md).
* Mnoho deklarace identity specifické pro lidské budou chybět, jako například `name`.

## <a name="token-revocation"></a>Token zrušení

Aktualizovat tokeny můžete platnost nebo kdykoli pro celou řadu důvodů odvolat. Spadají do dvou hlavních kategorií: vypršení časového limitu a zrušení.

### <a name="token-timeouts"></a>Vypršení časového limitu pro token

* MaxInactiveTime: Pokud obnovovací token nebyl použit v čase, závisí MaxInactiveTime, aktualizovat Token nebude platný. 
* MaxSessionAge: Pokud MaxAgeSessionMultiFactor nebo MaxAgeSessionSingleFactor byla nastavena na jinou hodnotu než výchozí (až do zrušeno), pak opětovné ověření se bude vyžadovat po dobu nastavenou v MaxAgeSession * uplyne. 
* Příklady:
  * Klient má MaxInactiveTime 5 dní a uživatel přešel na dovolenou týden a proto AAD není vidět nové žádosti o token uživatele za 7 dní. Při příštím uživatel požaduje nový token, najdou jejich aktualizovat Token se odvolal, a musí znovu zadat své přihlašovací údaje.
  * Citlivé aplikace má MaxAgeSessionSingleFactor 1 den. Pokud se uživatel přihlásí pondělí a Tuesday (po uplynutí 25 hodin), bude vyžadovat donutit k.

### <a name="revocation"></a>Odvolání

|   | Soubor cookie založené na heslech | Token založené na heslech | Soubory cookie bez hesla podle | Heslo nezaložené token | Token důvěrnému klientovi| 
|---|-----------------------|----------------------|---------------------------|--------------------------|--------------------------|
| Vypršení platnosti hesla | Zůstane aktivní| Zůstane aktivní | Zůstane aktivní | Zůstane aktivní | Zůstane aktivní |
| Heslo změněno uživatelem | Odvoláno | Odvoláno | Zůstane aktivní | Zůstane aktivní | Zůstane aktivní |
| Uživatel provede samoobslužné resetování HESLA | Odvoláno | Odvoláno | Zůstane aktivní | Zůstane aktivní | Zůstane aktivní |
| Resetování hesla správce | Odvoláno | Odvoláno | Zůstane aktivní | Zůstane aktivní | Zůstane aktivní |
| Uživatel Odvolá jejich obnovovací tokeny [přes PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Odvoláno | Odvoláno |Odvoláno | Odvoláno |Odvoláno | Odvoláno |
| Správce pro tenanta odvolá všechny obnovovací tokeny [přes PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Odvoláno | Odvoláno |Odvoláno | Odvoláno |Odvoláno | Odvoláno |
| [Odhlášení jednoho](v1-protocols-openid-connect-code.md#single-sign-out) na webu | Odvoláno | Zůstane aktivní | Odvoláno | Zůstane aktivní | Zůstane aktivní |

> [!NOTE]
> Kde uživatele nebylo zadáno heslo se dá stáhnout je "bez hesla na základě" přihlášení. Například použití vaší pro rozpoznávání tváře s Windows Hello, FIDO klíč nebo PIN kód. 
>
> Známý problém s Windows primární aktualizovat Token existuje. Pokud PRT je získali prostřednictvím heslo a pak uživatel přihlásí pomocí Hello, nezmění se původ PRT a bude možné odvolat, pokud uživatel změní heslo.
>
> Tokeny obnovení nejsou zneplatněny nebo odebrán, když se používá k načtení nového přístupového tokenu a obnovovací token.  

## <a name="next-steps"></a>Další postup

* Další informace o [ `id_tokens` ve službě Azure AD](id-tokens.md).
* Další informace o oprávnění a souhlas v [v1.0](v1-permissions-and-consent.md) a [v2.0](v2-permissions-and-consent.md).
