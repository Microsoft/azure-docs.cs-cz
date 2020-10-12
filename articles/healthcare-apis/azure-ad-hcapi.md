---
title: Azure Active Directory konfigurace identity pro Azure API pro FHIR
description: Seznamte se s principy identity, ověřování a autorizace pro servery Azure FHIR.
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: cdb73670996341e9219230bb277e087009266f32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87846016"
---
# <a name="azure-active-directory-identity-configuration-for-azure-api-for-fhir"></a>Azure Active Directory konfigurace identity pro Azure API pro FHIR

Důležitou součástí při práci s zdravotními daty je zajistit, aby data byla zabezpečená a nepřístupná pro neoprávněné uživatele a aplikace. Servery FHIR používají [OAuth 2,0](https://oauth.net/2/) k zajištění zabezpečení těchto dat. [Rozhraní Azure API pro FHIR](https://azure.microsoft.com/services/azure-api-for-fhir/) je zabezpečené pomocí [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/), což je příklad zprostředkovatele identity OAuth 2,0. Tento článek poskytuje přehled autorizace serveru FHIR a postup potřebný k získání tokenu pro přístup k serveru FHIR. I když budou tyto kroky platit pro všechny FHIR servery a poskytovatele identity, provedeme vás prostřednictvím rozhraní Azure API pro FHIR jako se serverem FHIR a Azure AD jako náš poskytovatel identity v tomto článku.

## <a name="access-control-overview"></a>Přehled řízení přístupu

Aby klientská aplikace mohla získat přístup k rozhraní API Azure pro FHIR, musí předložit přístupový token. Přístupový token je podepsaná sada vlastností (deklarací identity) kódovaná v [kódování Base64](https://en.wikipedia.org/wiki/Base64) , která předává informace o identitě klienta a rolích a oprávněních udělených klientovi.

Existuje několik způsobů, jak získat token, ale rozhraní API Azure pro FHIR nezáleží na tom, jak se token získává, pokud se jedná o vhodně podepsaný token se správnými deklaracemi. 

Použití [toku autorizačního kódu](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code) jako příklad s přístupem k serveru FHIR projde čtyři kroky níže:

![Autorizace FHIR](media/azure-ad-hcapi/fhir-authorization.png)

1. Klient pošle požadavek na `/authorize` koncový bod Azure AD. Služba Azure AD přesměruje klienta na přihlašovací stránku, kde se uživatel ověří pomocí příslušných přihlašovacích údajů (například uživatelské jméno a heslo nebo dvojúrovňové ověřování). Podívejte se na podrobnosti o [získání autorizačního kódu](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code#request-an-authorization-code). Po úspěšném ověření se klientovi vrátí *autorizační kód* . Azure AD povolí vrácení tohoto autorizačního kódu jenom do registrované adresy URL odpovědi nakonfigurované v registraci klientské aplikace (viz níže).
1. Klientská aplikace vyměňuje autorizační kód pro *přístupový token* na `/token` koncovém bodu služby Azure AD. Klientská aplikace může při požadavku na token zadat tajný klíč klienta (heslo aplikace). Podívejte se na podrobnosti o [získání přístupového tokenu](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code#use-the-authorization-code-to-request-an-access-token).
1. Klient vytvoří požadavek na rozhraní API Azure pro FHIR, například `GET /Patient` pro hledání všech pacientů. Při vytváření žádosti zahrnuje přístupový token v hlavičce požadavku HTTP, například `Authorization: Bearer eyJ0e...` , kde `eyJ0e...` představuje přístupový token kódovaný v kódování Base64.
1. Rozhraní API Azure pro FHIR ověřuje, že token obsahuje odpovídající deklarace identity (vlastnosti v tokenu). Pokud vše ověří, dokončí požadavek a vrátí FHIR sadu s výsledky klientovi.

Je důležité si uvědomit, že rozhraní Azure API pro FHIR není zapojené do ověřování přihlašovacích údajů uživatele a nevydá token. Ověřování a vytváření tokenu provádí služba Azure AD. Rozhraní Azure API pro FHIR jednoduše ověří, jestli je token správně podepsaný (je jeho platnost) a jestli má příslušné deklarace identity.

## <a name="structure-of-an-access-token"></a>Struktura přístupového tokenu

Vývoj aplikací FHIR často zahrnuje problémy s přístupem k ladění. Pokud je klientovi odepřen přístup k rozhraní API Azure pro FHIR, je vhodné pochopit strukturu přístupového tokenu a způsob, jakým se dá dekódovat, abyste zkontrolovali obsah (deklarace identity) tokenu. 

Servery FHIR typicky očekávají [JSON web token](https://en.wikipedia.org/wiki/JSON_Web_Token) (JWT, někdy se vysloví jako "poznačte"). Skládá se ze tří částí:

1. Záhlaví, které může vypadat takto:
    ```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```
1. Datová část (deklarace identity), například:
    ```json
    {
     "oid": "123",
     "iss": "https://issuerurl",
     "iat": 1422779638,
     "roles": [
        "admin"
      ]
    }
    ```
1. Podpis, který je vypočítán zřetězením obsahu kódovaného Base64 v hlavičce a datové části a výpočtem kryptografické hodnoty hash na základě algoritmu ( `alg` ) zadaného v hlavičce. Server bude moci získat veřejné klíče od poskytovatele identity a ověřit, zda byl tento token vydán konkrétním poskytovatelem identity a nebyl zfalšován.

Úplný token se skládá z verzí těchto tří segmentů kódovaných ve formátu Base64 (skutečná adresa URL s kódováním base64). Tři segmenty jsou zřetězené a oddělené znakem `.` (tečka).

Níže je uveden příklad tokenu:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

Token se dá dekódovat a prověřit pomocí nástrojů, jako je [https://jwt.ms](https://jwt.ms) . Výsledek dekódování tokenu je:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "oid": "123",
  "iss": "https://issuerurl",
  "iat": 1422779638,
  "roles": [
    "admin"
  ]
}.[Signature]
```

## <a name="obtaining-an-access-token"></a>Získání přístupového tokenu

Jak je uvedeno výše, existuje několik způsobů, jak získat token ze služby Azure AD. Jsou podrobně popsané v [dokumentaci pro vývojáře Azure AD](https://docs.microsoft.com/azure/active-directory/develop/).

Azure AD má dvě různé verze koncových bodů OAuth 2,0, které jsou označovány jako `v1.0` a `v2.0` . Obě tyto verze jsou koncové body OAuth 2,0 a `v1.0` návrhy a se `v2.0` týkají rozdílů v tom, jak Azure AD implementuje tento standard. 

Při použití serveru FHIR můžete použít `v1.0` `v2.0` koncovou položku nebo. Volba může záviset na knihovnách ověřování, které používáte v klientské aplikaci.

V dokumentaci k Azure AD jsou relevantní tyto části:

* `v1.0` Služba
    * [Tok autorizačního kódu](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code).
    * [Tok přihlašovacích údajů klienta](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow).
* `v2.0` Služba
    * [Tok autorizačního kódu](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow).
    * [Tok přihlašovacích údajů klienta](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow).

Pro získání tokenu jsou k dispozici jiné varianty (například za jménem toku). Podrobnosti najdete v dokumentaci k Azure AD. Při použití rozhraní API Azure pro FHIR jsou k dispozici také některé zkratky pro získání přístupového tokenu (pro účely ladění) [pomocí rozhraní příkazového řádku Azure CLI](get-healthcare-apis-access-token-cli.md).

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se seznámili s některými základními koncepcemi, které se týkají zabezpečení přístupu k Azure API pro FHIR pomocí Azure AD. Pokud se chcete dozvědět, jak nasadit instanci rozhraní API Azure pro FHIR, přejděte k rychlému startu nasazení.

>[!div class="nextstepaction"]
>[Nasazení Azure API for FHIR](fhir-paas-portal-quickstart.md)