---
title: Odkaz na přístupové tokeny platformy identit y Microsoft | Azure
description: Další informace o přístupových tokenech vyzařovaných koncovými body platformy Azure AD v1.0 a platformy identit Microsoftu (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: 417829389a4b3a6bb55dcff9bfe59c2bc8693ca0
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383204"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Tokeny přístupu platformy identit Microsoftu

Přístupové tokeny umožňují klientům bezpečně volat api chráněná Azure. Přístupové tokeny platformy identit microsoftu jsou [JWTs](https://tools.ietf.org/html/rfc7519), Base64 kódované JSON objekty podepsané Azure. Klienti by měli považovat přístupové tokeny za neprůhledné řetězce, protože obsah tokenu je určen pouze pro prostředek. Pro účely ověření a ladění mohou vývojáři dekódovat jwts pomocí webu, jako [je jwt.ms](https://jwt.ms). Váš klient může získat přístupový token z koncového bodu v1.0 nebo koncového bodu v2.0 pomocí různých protokolů.

Když váš klient požádá o přístupový token, Azure AD také vrátí některá metadata o přístupový token pro spotřebu vaší aplikace. Tyto informace zahrnují čas vypršení platnosti přístupového tokenu a obory, pro které je platný. Tato data umožňují vaší aplikaci provést inteligentní ukládání přístupových tokenů do mezipaměti, aniž by bylo třeba analyzovat samotný přístupový token.

Pokud je vaše aplikace prostředek (webové rozhraní API), ke kterému mohou klienti požádat o přístup, poskytují přístupové tokeny užitečné informace pro použití při ověřování a autorizaci, jako je například uživatel, klient, vydavatel, oprávnění a další.

V následujících částech se dozvíte, jak prostředek může ověřit a použít deklarace identity uvnitř přístupového tokenu.

> [!IMPORTANT]
> Přístupové tokeny jsou vytvořeny na základě *cílové skupiny* tokenu, což znamená, že aplikace, která vlastní obory v tokenu.  To je, jak `accessTokenAcceptedVersion` nastavení prostředků `2` v [manifestu aplikace](reference-app-manifest.md#manifest-reference) umožňuje klientovi volání koncového bodu v1.0 přijímat přístupový token v2.0.  Podobně je to důvod, proč změna [volitelných deklarací](active-directory-optional-claims.md) přístupového tokenu pro vašeho `user.read`klienta nemění přístupový token přijatý při požadavku na token pro , který je vlastněn prostředkem.
> Ze stejného důvodu při testování klientské aplikace s osobním účtem (například hotmail.com nebo outlook.com) můžete zjistit, že přístupový token přijatý klientem je neprůhledný řetězec. Důvodem je, že prostředek, ke kterému se přistupuje, požádal o starší lístky MSA (účet Microsoft), které jsou šifrovány a klient je nemůže pochopit.

## <a name="sample-tokens"></a>Ukázkové tokeny

tokeny v1.0 a v2.0 vypadají podobně a obsahují mnoho stejných deklarací identity. Příklad každého z nich je uveden zde.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

Zobrazit tento token v1.0 v [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Zobrazit tento token v2.0 v [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>Deklarace identity v přístupových tokenech

JWT stříhá se na tři kusy:

* **Záhlaví** - Obsahuje informace o tom, jak [ověřit token,](#validating-tokens) včetně informací o typu tokenu a jak byl podepsán.
* **Datová část** – obsahuje všechna důležitá data o uživateli nebo aplikaci, která se pokouší volat vaši službu.
* **Podpis** - Je surovina použitá k ověření tokenu.

Každý kus je oddělen`.`tečkou ( ) a samostatně zakódován Base64.

Deklarace jsou k dispozici pouze v případě, že existuje hodnota k jeho vyplnění. Takže vaše aplikace by neměla mít závislost na deklaraci identity, která je přítomna. Příklady `pwd_exp` zahrnují (ne každý klient vyžaduje, `family_name` aby vypršela platnost hesel) nebo (pověření klienta ([v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v2.0)](v2-oauth2-client-creds-grant-flow.md)toky jsou jménem aplikací, které nemají názvy). Deklarace používané pro ověření přístupového tokenu budou vždy k dispozici.

> [!NOTE]
> Některé deklarace identity se používají k pomoci Azure AD zabezpečené tokeny v případě opakovaného použití. Ty jsou v popisu označeny jako neurčené pro veřejnou spotřebu. Tyto deklarace se mohou nebo nemusí zobrazovat v tokenu a nové mohou být přidány bez předchozího upozornění.

### <a name="header-claims"></a>Nároky záhlaví

|Deklarovat | Formát | Popis |
|--------|--------|-------------|
| `typ` | Řetězec - vždy "JWT" | Označuje, že token je JWT.|
| `nonce` | Řetězec | Jedinečný identifikátor používaný k ochraně před útoky na přehrání tokenů. Váš prostředek může zaznamenat tuto hodnotu k ochraně proti opakování. |
| `alg` | Řetězec | Označuje algoritmus, který byl použit k podepsání tokenu, například "RS256" |
| `kid` | Řetězec | Určuje kryptografický otisk pro veřejný klíč, který se používá k podepsání tohoto tokenu. Vyzařované v přístupových tokenech v1.0 i v2.0. |
| `x5t` | Řetězec | Funguje stejně (používá se `kid`a hodnota) jako . `x5t`je starší deklarace vyzařované pouze v přístupových tokenech verze 1.0 pro účely kompatibility. |

### <a name="payload-claims"></a>Nároky datové části

| Deklarovat | Formát | Popis |
|-----|--------|-------------|
| `aud` | Řetězec, identifikátor URI ID aplikace | Identifikuje zamýšleného příjemce tokenu. V tokenech ID je okruh uživatelů ID aplikace přiřazený k vaší aplikaci na webu Azure Portal. Vaše aplikace by měla ověřit tuto hodnotu a odmítnout token, pokud hodnota neodpovídá. |
| `iss` | Řetězec, identifikátor URI STS | Identifikuje službu tokenů zabezpečení (STS), která vytváří a vrací token, a klienta Azure AD, ve kterém byl uživatel ověřen. Pokud je vydaný token token v2.0 `ver` token (viz deklarace), uri skončí v `/v2.0`. Identifikátor GUID, který označuje, že uživatel je `9188040d-6c67-4c5b-b112-36a304b66dad`uživatel příjemce z účtu Microsoft je . Vaše aplikace by měla použít část deklarace GUID k omezení sady klientů, kteří se mohou k aplikaci přihlásit, pokud je to možné. |
|`idp`| Řetězec, obvykle identifikátor URI systému STS | Zaznamenává zprostředkovatele identity, který ověřil subjekt tokenu. Tato hodnota je shodná s hodnotou deklarace vystavittele, pokud uživatelský účet není ve stejném tenantovi jako vydavatel - hosté, například. Pokud deklarace není k dispozici, znamená `iss` to, že hodnota lze použít místo.  Pro osobní účty používané v kontextu organizace (například osobní účet pozvaný k `idp` tenantovi Azure AD) může být deklarace pohledávky "live.com" nebo IDENTIFIKÁTOR URI STS obsahující klienta `9188040d-6c67-4c5b-b112-36a304b66dad`účtu Microsoft . |
| `iat` | int, časové razítko systému UNIX | "Vydáno na" označuje, kdy došlo k ověření pro tento token. |
| `nbf` | int, časové razítko systému UNIX | Tvrzení "nbf" (nikoli dříve) označuje dobu, před kterou nesmí být JWT přijat ke zpracování. |
| `exp` | int, časové razítko systému UNIX | Deklarace "exp" (expirace) určuje dobu vypršení platnosti, po které nesmí být JWT přijatke zpracování. Je důležité si uvědomit, že prostředek může odmítnout token před touto dobou také, například když je požadována změna ověřování nebo bylo zjištěno odvolání tokenu. |
| `aio` | Neprůhledný řetězec | Interní deklarace, kterou používá Služba Azure AD k zaznamenání dat pro opětovné použití tokenu. Prostředky by neměly používat toto tvrzení. |
| `acr` | Řetězec, "0" nebo "1" | K dispozici pouze v tokenech v1.0. Deklarace "Třída kontextu ověřování". Hodnota "0" označuje, že ověřování koncovým uživatelem nesplňovalo požadavky protokolu ISO/IEC 29115. |
| `amr` | JSON pole řetězců | K dispozici pouze v tokenech v1.0. Určuje, jak byl předmět tokenu ověřen. Další podrobnosti naleznete [v části nároka na amr.](#the-amr-claim) |
| `appid` | Řetězec, identifikátor GUID | K dispozici pouze v tokenech v1.0. ID aplikace klienta pomocí tokenu. Aplikace může fungovat jako sama nebo jménem uživatele. ID aplikace obvykle představuje objekt aplikace, ale může také představovat objekt instančního objektu ve službě Azure AD. |
| `appidacr` | "0", "1" nebo "2" | K dispozici pouze v tokenech v1.0. Označuje, jak byl klient ověřen. Pro veřejného klienta je hodnota "0". Pokud se používá ID klienta a tajný klíč klienta, hodnota je "1". Pokud byl pro ověřování použit klientský certifikát, je hodnota "2". |
| `azp` | Řetězec, identifikátor GUID | K dispozici pouze v tokenech v2.0, náhrada za `appid`. ID aplikace klienta pomocí tokenu. Aplikace může fungovat jako sama nebo jménem uživatele. ID aplikace obvykle představuje objekt aplikace, ale může také představovat objekt instančního objektu ve službě Azure AD. |
| `azpacr` | "0", "1" nebo "2" | K dispozici pouze v tokenech v2.0, náhrada za `appidacr`. Označuje, jak byl klient ověřen. Pro veřejného klienta je hodnota "0". Pokud se používá ID klienta a tajný klíč klienta, hodnota je "1". Pokud byl pro ověřování použit klientský certifikát, je hodnota "2". |
| `preferred_username` | Řetězec | Primární uživatelské jméno, které představuje uživatele. Může to být e-mailová adresa, telefonní číslo nebo obecné uživatelské jméno bez zadaného formátu. Jeho hodnota je proměnlivá a může se v průběhu času měnit. Vzhledem k tomu, že je proměnlivá, tato hodnota nesmí být použita k rozhodování o autorizaci.  To může být použit pro uživatelské jméno rady ačkoli. K `profile` získání této deklarace je nutný rozsah. |
| `name` | Řetězec | Poskytuje hodnotu čitelnou pro člověka, která identifikuje předmět tokenu. Hodnota není zaručena jako jedinečná, je proměnlivá a je navržena pro použití pouze pro účely zobrazení. K `profile` získání této deklarace je nutný rozsah. |
| `scp` | Řetězec, mezera oddělený seznam oborů | Sada oborů vystavených vaší aplikací, pro které klientská aplikace požádala (a obdržela) souhlas. Vaše aplikace by měla ověřit, že tyto obory jsou platné obory vystavené vaší aplikací, a rozhodovat o autorizaci na základě hodnoty těchto oborů. Zahrnuto pouze pro [uživatelské tokeny](#user-and-application-tokens). |
| `roles` | Pole řetězců, seznam oprávnění | Sada oprávnění vystavených vaší aplikací, které žádající aplikace nebo uživatel dostal oprávnění k volání. Pro [tokeny aplikace](#user-and-application-tokens)se používá při toku pověření klienta ([v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)) místo uživatelských oborů.  Pro [uživatelské tokeny](#user-and-application-tokens) je to naplněno rolemi, které byl uživatel přiřazen v cílové aplikaci. |
| `wids` | Pole identifikátorů GUID [RoleTemplateID](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids) | Označuje role pro celý klient přiřazené tomuto uživateli z části rolí, které jsou na [stránce role správce](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids).  Tato deklarace je konfigurována pro aplikaci prostřednictvím vlastností `groupMembershipClaims` [manifestu aplikace](reference-app-manifest.md).  Je vyžadováno nastavení na "Vše" nebo "DirectoryRole".  Nemusí být k dispozici v tokenech získaných prostřednictvím implicitnítovky z důvodu konce tokenu obavy. |
| `groups` | JSON pole identifikátorů GUID | Obsahuje ID objektů, které představují členství subjektu ve skupinách. Tyto hodnoty jsou jedinečné (viz ID objektu) a lze je bezpečně použít pro správu přístupu, jako je například vynucení autorizace pro přístup k prostředku. Skupiny zahrnuté do deklarace skupin jsou konfigurovány `groupMembershipClaims` pro aplikaci prostřednictvím vlastností [manifestu aplikace](reference-app-manifest.md). Hodnota null nezahrnuje všechny skupiny, hodnota "SecurityGroup" bude obsahovat pouze členství skupiny zabezpečení služby Active Directory a hodnota "Vše" bude zahrnovat skupiny zabezpečení a distribuční seznamy office 365. <br><br>Podrobnosti `hasgroups` o použití pohledávky `groups` s implicitním grantem naleznete v níže uvedené deklaraci. <br>Pro ostatní toky, pokud počet skupin, ve kterých se uživatel nachází, překračuje limit (150 pro SAML, 200 pro JWT), bude do zdrojů deklarací prostředků, které odkazují na koncový bod aplikace Microsoft Graph obsahující seznam skupin pro uživatele, přidána deklarace nadlimitního množství. |
| `hasgroups` | Logická hodnota | Pokud je `true`přítomen, vždy , označující uživatel je alespoň v jedné skupině. Používá se místo `groups` deklarace pro JWTs v implicitní grantové toky, pokud úplné skupiny deklarace by rozšířit uri fragment za omezení délky adresy URL (v současné době 6 nebo více skupin). Označuje, že klient by měl používat rozhraní Microsoft Graph`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`API k určení skupin uživatele ( ). |
| `groups:src1` | Objekt JSON | Pro požadavky tokenů, které nejsou `hasgroups` omezeny na délku (viz výše), ale stále příliš velké pro token, bude zahrnut odkaz na úplný seznam skupin pro uživatele. Pro jwts jako distribuované pohledávky, pro SAML jako nový nárok namísto nároku. `groups` <br><br>**Příklad hodnoty JWT**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }` |
| `sub` | Řetězec, identifikátor GUID | Hlavní objekt, o kterém token uplatňuje informace, jako je například uživatel aplikace. Tato hodnota je neměnná a nelze ji znovu přiřadit ani znovu použít. Lze jej použít k bezpečnému provádění kontrol autorizace, například při použití tokenu pro přístup k prostředku a lze jej použít jako klíč v databázových tabulkách. Vzhledem k tomu, že předmět je vždy k dispozici v tokenech, které Azure AD problémy, doporučujeme použít tuto hodnotu v systému autorizace pro obecné účely. Předmět je však párový identifikátor - je jedinečný pro konkrétní ID aplikace. Proto pokud jeden uživatel přihlásí do dvou různých aplikací pomocí dvou různých ID klienta, tyto aplikace obdrží dvě různé hodnoty pro předmět deklarace. To může nebo nemusí být žádoucí v závislosti na vaší architektuře a požadavcích na ochranu osobních údajů. Viz také `oid` deklarace (která zůstává stejná v aplikacích v rámci tenanta). |
| `oid` | Řetězec, identifikátor GUID | Neměnný identifikátor objektu v platformě identit microsoftu, v tomto případě uživatelský účet. Lze jej také použít k bezpečnému provádění kontrol autorizace a jako klíč v databázových tabulkách. Toto ID jednoznačně identifikuje uživatele napříč aplikacemi – dvě různé aplikace podepisující se ve stejném uživateli obdrží stejnou hodnotu v deklaraci. `oid` Proto `oid` lze použít při dotazování na služby Microsoft online, jako je například Microsoft Graph. Microsoft Graph vrátí toto ID jako `id` vlastnost pro daný uživatelský [účet](/graph/api/resources/user). Vzhledem `oid` k tomu, že umožňuje `profile` více aplikací maže korelovat uživatele, obor je nutné pro příjem této deklarace. Všimněte si, že pokud jeden uživatel existuje ve více klientech, uživatel bude obsahovat jiné ID objektu v každém klientovi – jsou považovány za různé účty, i když se uživatel přihlásí ke každému účtu se stejnými pověřeními. |
| `tid` | Řetězec, identifikátor GUID | Představuje klienta Azure AD, ze kterého uživatel pochází. Pro pracovní a školní účty je identifikátor GUID neměnné ID klienta organizace, do které uživatel patří. Pro osobní účty je `9188040d-6c67-4c5b-b112-36a304b66dad`hodnota . K `profile` získání této deklarace je nutný rozsah. |
| `unique_name` | Řetězec | K dispozici pouze v tokenech v1.0. Poskytuje lidsky čitelnou hodnotu, která identifikuje subjekt tokenu. Tato hodnota není zaručeno, že je jedinečný v rámci klienta a by měl y používat pouze pro účely zobrazení. |
| `uti` | Neprůhledný řetězec | Interní deklarace, kterou Azure používá k opětovnému ověření tokenů. Prostředky by neměly používat toto tvrzení. |
| `rh` | Neprůhledný řetězec | Interní deklarace, kterou Azure používá k opětovnému ověření tokenů. Prostředky by neměly používat toto tvrzení. |
| `ver` | Řetězec, `1.0` buď nebo`2.0` | Označuje verzi přístupového tokenu. |

**Nároky na překročení skupin**

Chcete-li zajistit, že velikost tokenu nepřekročí omezení velikosti záhlaví HTTP, Azure AD omezuje počet ID objektů, které obsahuje ve skupinách deklarace. Pokud je uživatel členem více skupin, než je limit překročení (150 pro tokeny SAML, 200 pro tokeny JWT), pak Azure AD nevyzařuje deklarace skupiny v tokenu. Místo toho obsahuje nárok nadlimitní ho distore v tokenu, který označuje aplikaci dotaz rozhraní API aplikace Microsoft Graph načíst členství uživatele ve skupině.

```JSON
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

Můžete použít `BulkCreateGroups.ps1` uvedené ve složce [Skripty pro vytváření aplikací](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-2-Groups/AppCreationScripts) k testování scénářů nadbytku.

#### <a name="v10-basic-claims"></a>v1.0 základní pohledávky

Následující deklarace identity budou zahrnuty v tokenech v1.0, pokud je to možné, ale ve výchozím nastavení nejsou zahrnuty do tokenů v2.0. Pokud používáte v2.0 a potřebujete jeden z těchto deklarací, požádejte je o použití [volitelných deklarací .](active-directory-optional-claims.md)

| Deklarovat | Formát | Popis |
|-----|--------|-------------|
| `ipaddr`| Řetězec | Adresa IP, ze které uživatel ověřil. |
| `onprem_sid`| Řetězec ve [formátu SID](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | V případech, kdy má uživatel místní ověřování, tato deklarace poskytuje jejich SID. Můžete použít `onprem_sid` pro autorizaci ve starších aplikacích.|
| `pwd_exp`| int, časové razítko systému UNIX | Označuje, kdy vyprší platnost hesla uživatele. |
| `pwd_url`| Řetězec | Adresa URL, na které mohou být uživatelé odesláni k obnovení hesla. |
| `in_corp`| Boolean | Signalizuje, pokud se klient přihlašuje z podnikové sítě. Pokud tomu tak není, nárok není zahrnut. |
| `nickname`| Řetězec | Další jméno pro uživatele, oddělené od křestního jména nebo příjmení.|
| `family_name` | Řetězec | Poskytuje příjmení, příjmení nebo příjmení uživatele, jak je definováno na objektu uživatele. |
| `given_name` | Řetězec | Poskytuje křestní nebo křestní jméno uživatele, jak je nastaveno na objekt uživatele. |
| `upn` | Řetězec | Uživatelské jméno uživatele. Může se na lésmě pojet í telefonní číslo, e-mailová adresa nebo neformátovaný řetězec. By měl být použit pouze pro účely zobrazení a poskytování nápovědy uživatelské jméno ve scénářích opětovnéověřování. |

#### <a name="the-amr-claim"></a>Pohledávka `amr`

Identity společnosti Microsoft se mohou ověřovat různými způsoby, které mohou být relevantní pro vaši aplikaci. Deklarace `amr` zabezpečení je pole, které může `["mfa", "rsa", "pwd"]`obsahovat více položek, například pro ověřování, které používalo heslo i aplikaci Authenticator.

| Hodnota | Popis |
|-----|-------------|
| `pwd` | Ověřování heslem, buď heslo uživatele microsoftu nebo tajný klíč klienta aplikace. |
| `rsa` | Ověřování bylo založeno na důkazu klíče RSA, například v [aplikaci Microsoft Authenticator](https://aka.ms/AA2kvvu). To zahrnuje, pokud ověřování bylo provedeno vlastním podpisem JWT se službou vlastněnou certifikátem X509. |
| `otp` | Jednorázový přístupový kód pomocí e-mailu nebo textové zprávy. |
| `fed` | Byl použit federovaný kontrolní výraz ověřování (například JWT nebo SAML). |
| `wia` | Integrované ověřování systému Windows |
| `mfa` | Bylo použito vícefaktorové ověřování. Pokud je k dispozici ostatní metody ověřování budou také zahrnuty. |
| `ngcmfa` | Ekvivalentní `mfa`, používá pro zřizování některých typů rozšířených pověření. |
| `wiaormfa`| Uživatel k ověření použil systém Windows nebo pověření mfa. |
| `none` | Nebylo provedeno žádné ověření. |

## <a name="validating-tokens"></a>Ověřování žetonů

Chcete-li ověřit id_token nebo access_token, vaše aplikace by měla ověřit podpis tokenu i deklarace identity. Chcete-li ověřit přístupové tokeny, vaše aplikace by měla také ověřit vystavitela, okruh uživatelů a podpisové tokeny. Ty je třeba ověřit proti hodnotám v dokumentu zjišťování OpenID. Například verze dokumentu nezávislá na tenantovi [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration)je umístěna na adrese .

Middleware Azure AD má integrované funkce pro ověřování přístupových tokenů a můžete procházet naše [ukázky](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) a najít je v jazyce podle vašeho výběru.

Poskytujeme knihovny a ukázky kódu, které ukazují, jak zacházet s ověřením tokenu. Níže uvedené informace jsou uvedeny pro ty, kteří chtějí pochopit základní proces. Existuje také několik knihoven s otevřeným zdrojovým kódem třetích stran, které jsou k dispozici pro ověření JWT - existuje alespoň jedna možnost pro téměř každou platformu a jazyk. Další informace o knihovnách ověřování Azure AD a ukázkách kódu najdete [v tématu knihovny ověřování v1.0](../azuread-dev/active-directory-authentication-libraries.md) a [knihovny ověřování verze 2.0](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>Ověření podpisu

JWT obsahuje tři segmenty, které `.` jsou odděleny znakem. První segment se označuje jako **záhlaví**, druhý jako **tělo**a třetí jako **podpis**. Segment podpisu lze použít k ověření pravosti tokenu tak, aby mohl být důvěryhodný vaší aplikací.

Tokeny vydané službou Azure AD jsou podepsané pomocí oborových asymetrických šifrovacích algoritmů, jako je například RS256. Záhlaví JWT obsahuje informace o klíč a šifrování metoda použitá k podepsání tokenu:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

Deklarace `alg` označuje algoritmus, který byl použit `kid` k podepsání tokenu, zatímco deklarace označuje konkrétní veřejný klíč, který byl použit k ověření tokenu.

V daném okamžiku azure ad může podepsat id_token pomocí některého z určité sady párů veřejného a soukromého klíče. Azure AD otáčí možné sady klíčů v pravidelných intervalech, takže vaše aplikace by měla být zapsána pro zpracování těchto klíčových změn automaticky. Přiměřená frekvence pro kontrolu aktualizací veřejných klíčů používaných službou Azure AD je každých 24 hodin.

Data podpisového klíče potřebná k ověření podpisu můžete získat pomocí [dokumentu metadat OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) umístěného na adrese:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Zkuste tuto [adresu URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) v prohlížeči!

Tento dokument metadat:

* Je JSON objekt obsahující několik užitečných informací, jako je například umístění různých koncových bodů potřebných pro provádění ověřování OpenID Connect.
* Zahrnuje `jwks_uri`, který poskytuje umístění sadu veřejných klíčů používaných k podepisování tokenů. JSON Web Key (JWK) `jwks_uri` umístěný na obsahuje všechny informace veřejného klíče, které se používají v daném okamžiku v čase.  Formát JWK je popsán v [RFC 7517](https://tools.ietf.org/html/rfc7517).  Vaše aplikace můžete `kid` použít deklaraci v záhlaví JWT vybrat, který veřejný klíč v tomto dokumentu byl použit k podepsání konkrétního tokenu. Potom může provést ověření podpisu pomocí správného veřejného klíče a uvedeného algoritmu.

> [!NOTE]
> Koncový bod v1.0 vrátí `x5t` `kid` deklarace identity a zatímco koncový bod v2.0 odpovídá pouze deklarací. `kid` Do budoucna doporučujeme `kid` použít deklaraci k ověření tokenu.

Ověření podpisu je mimo rozsah tohoto dokumentu – v případě potřeby je k dispozici mnoho knihoven s otevřeným zdrojovým kódem, které vám s tím pomohou.  Platforma Microsoft Identity má však jedno rozšíření podpisu tokenu k standardům – vlastní podpisové klíče.

Pokud vaše aplikace obsahuje vlastní podpisové klíče v důsledku použití funkce `appid` [mapování deklarací identity,](active-directory-claims-mapping.md) musíte připojit parametr dotazu obsahující ID aplikace, abyste `jwks_uri` získali odkaz na informace o podpisovém klíči aplikace, které by se měly použít pro ověření. Například: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` obsahuje `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

### <a name="claims-based-authorization"></a>Autorizace založená na deklaracích

Obchodní logika vaší aplikace bude diktovat tento krok, některé běžné metody autorizace jsou uvedeny níže.

* Zkontrolujte `scp` `roles` nebo deklarace ověřte, zda všechny aktuální obory odpovídají těm, které jsou vystaveny rozhraní API, a umožněte klientovi provést požadovanou akci.
* Ujistěte se, že volající klient `appid` může volat vaše rozhraní API pomocí deklarace.
* Ověření stavu ověřování volajícího `appidacr` klienta pomocí – by neměla být 0, pokud veřejní klienti nemají povoleno volat vaše rozhraní API.
* Zkontrolujte seznam minulých `nonce` deklarací, které ověřují, že token se nepřehrává.
* Zkontrolujte, `tid` zda odpovídá klientovi, který je povoleno volat vaše rozhraní API.
* Pomocí `acr` deklarace slouží k ověření, že uživatel provedl vícefaktorové ověřování. To by mělo být vynuceno pomocí [podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).
* Pokud jste požádali `roles` `groups` o nebo deklarace identity v přístupový token, ověřte, že uživatel je ve skupině povoleno provést tuto akci.
  * U tokenů načtených pomocí implicitního toku budete pravděpodobně muset zadat dotaz na [microsoft graph](https://developer.microsoft.com/graph/) pro tato data, protože je často příliš velký a nevejde se do tokenu.

## <a name="user-and-application-tokens"></a>Tokeny uživatelů a aplikací

Vaše aplikace může přijímat tokeny jménem uživatele (obvyklý tok) nebo přímo z aplikace (prostřednictvím toku pověření klienta ([v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)). Tyto tokeny pouze pro aplikace označují, že toto volání pochází z aplikace a nemá uživatele, který jej podporuje. Tyto tokeny jsou zpracovány do značné míry stejné, s některými rozdíly:

* Tokeny pouze pro aplikace `scp` nebudou mít deklaraci `roles` pohledávky a místo toho mohou mít deklaraci. Toto je místo, kde oprávnění aplikace (na rozdíl od delegovaných oprávnění) budou zaznamenány. Další informace o delegovaných oprávněních a oprávněních aplikací naleznete v tématu oprávnění a souhlas ([v1.0](../azuread-dev/v1-permissions-consent.md), [v2.0](v2-permissions-and-consent.md)).
* Mnoho lidských specifických tvrzení bude `name` `upn`chybět, například nebo .
* A `sub` `oid` nároky budou stejné.

## <a name="token-revocation"></a>Odvolání tokenu

Obnovovací tokeny mohou být z různých důvodů kdykoli zneplatněny nebo odvolány. Ty spadají do dvou hlavních kategorií: časové lhůty a odvolání.

### <a name="token-timeouts"></a>Časové opoziny tokenu

Pomocí [konfigurace životnosti tokenu](active-directory-configurable-token-lifetimes.md)lze změnit životnost tokenů aktualizace.  Je normální a očekává se, že některé tokeny půjdou bez použití (např. uživatel neotevře aplikaci po dobu 3 měsíců), a proto vyprší.  Aplikace se setkají se scénáři, kdy přihlašovací server odmítne obnovovací token z důvodu jeho stáří. 

* MaxInactiveTime: Pokud obnovovací token nebyl použit v čase diktované MaxInactiveTime, obnovovací token již nebude platný.
* MaxSessionAge: Pokud MaxAgeSessionMultiFactor nebo MaxAgeSessionSingleFactor byly nastaveny na něco jiného než jejich výchozí (Until-revoked), pak opětovné ověření bude vyžadováno po uplynutí doby nastavené v MaxAgeSession * uplyne.
* Příklady:
  * Klient má MaxInactiveTime pět dní a uživatel šel na dovolenou na týden, a tak Azure AD nezaznamenala nový požadavek tokenu od uživatele za 7 dní. Při příštím požadavku uživatele na nový token zjistí, že jejich obnovovací token byl odvolán a musí znovu zadat svá pověření.
  * Citlivá aplikace má MaxAgeSessionSingleFactor jednoho dne. Pokud se uživatel přihlásí v pondělí a v úterý (po uplynutí 25 hodin), bude nutné jej znovu ověřit.

### <a name="revocation"></a>Odvolání

Obnovovací tokeny mohou být odvolány serverem z důvodu změny přihlašovacích údajů nebo z důvodu použití nebo akce správce.  Obnovovací tokeny spadají do dvou tříd – těch, které jsou vydávány důvěrným klientům (sloupec zcela vpravo) a tokeny vydané veřejným klientům (všechny ostatní sloupce).   

|   | Soubor cookie založený na heslech | Token založený na heslech | Soubor cookie nezaložený na heslech | Token nezaložený na heslech | Důvěrný token klienta |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| Platnost hesla vyprší | Zůstává naživu | Zůstává naživu | Zůstává naživu | Zůstává naživu | Zůstává naživu |
| Heslo změněno uživatelem | Odvoláno | Odvoláno | Zůstává naživu | Zůstává naživu | Zůstává naživu |
| Uživatel provádí sspr | Odvoláno | Odvoláno | Zůstává naživu | Zůstává naživu | Zůstává naživu |
| Správce resetuje heslo | Odvoláno | Odvoláno | Zůstává naživu | Zůstává naživu | Zůstává naživu |
| Uživatel odvolá své obnovovací tokeny [prostřednictvím Prostředí PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Odvoláno | Odvoláno | Odvoláno | Odvoláno | Odvoláno |
| Správce odvolá všechny obnovovací tokeny pro uživatele [prostřednictvím PowerShellu](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Odvoláno | Odvoláno |Odvoláno | Odvoláno | Odvoláno |
| Jednotné odhlášení ([v1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out), [v2.0](v2-protocols-oidc.md#single-sign-out) ) na webu | Odvoláno | Zůstává naživu | Odvoláno | Zůstává naživu | Zůstává naživu |

> [!NOTE]
> Přihlášení "Na základě hesla" je takové, ve kterém uživatel nezadal heslo, aby ho získal. Například pomocí obličeje s Windows Hello, klávesou FIDO2 nebo KÓDEM PIN.
>
> Primární obnovovací tokeny (PRT) ve Windows 10 jsou oddělené na základě pověření. Například Windows Hello a heslo mají své příslušné PRTs, izolované od sebe navzájem. Když se uživatel přihlásí pomocí přihlašovacího kódu Hello (PIN nebo biometrické údaje) a poté změní heslo, bude dříve získané prt na základě hesla odvoláno. Přihlašování zpět s heslem zruší platnost starého PRT a požádá o nový.
>
> Obnovovací tokeny nejsou neplatné nebo odvolány při použití k načtení nového přístupového tokenu a obnovovacího tokenu.  Vaše aplikace by však měla starou aplikaci zahodit, jakmile se použije, a nahradit ji novou, protože nový token má v sobě nový čas vypršení platnosti. 

## <a name="next-steps"></a>Další kroky

* Další informace ve [ `id_tokens` službě Azure AD](id-tokens.md).
* Další informace o oprávnění a souhlasu ( [v1.0](../azuread-dev/v1-permissions-consent.md), [v2.0](v2-permissions-and-consent.md)).
