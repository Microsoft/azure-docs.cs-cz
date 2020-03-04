---
title: Definování technického profilu RESTful ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Definujte technický profil RESTful ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4638b5bfc3ff31d0d2149e7ee227c46d3360a306
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78254994"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu RESTful ve vlastní zásadě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro vlastní službu RESTful. Azure AD B2C odesílá data službě RESTful ve vstupní kolekci deklarací a přijímá data zpátky do výstupní kolekce deklarací. S integrací služby RESTful můžete:

- **Ověřit data vstupu uživatele** – zabrání ukládání poškozených dat do Azure AD B2C. Pokud hodnota od uživatele není platná, služba RESTful vrátí chybovou zprávu, která uživateli vydá pokyn k zadání položky. Můžete třeba ověřit, že e-mailová adresa zadaná uživatelem existuje v databázi vašeho zákazníka.
- **Přepsat vstupní deklarace identity** – umožňuje přeformátovat hodnoty ve vstupních deklaracích. Pokud například uživatel zadá křestní jméno do všech malých a velkých písmen, můžete název naformátovat pouze na první písmeno.
- **Rozšíření uživatelských dat** – umožňuje další integraci s podnikovými firemními aplikacemi. Například vaše služba RESTful může obdržet e-mailovou adresu uživatele, zadat dotaz na databázi zákazníka a získat věrnostní číslo uživatele Azure AD B2C. Vrácené deklarace identity je možné uložit, vyhodnotit v dalších krocích orchestrace nebo zahrnout do přístupového tokenu.
- **Spustit vlastní obchodní logiku** – umožňuje odesílat nabízená oznámení, aktualizovat podnikové databáze, spouštět proces migrace uživatelů, spravovat oprávnění, auditovat databáze a provádět další akce.

Vaše zásada může odesílat vstupní deklarace do vašeho REST API. REST API může také vracet výstupní deklarace identity, které můžete použít později v zásadách, nebo může vyvolat chybovou zprávu. Integraci s RESTful službami můžete navrhovat následujícími způsoby:

- **Technický profil ověření** – technický profil ověření volá službu RESTful. Technický profil ověření ověřuje uživatelem poskytnutá data před pokračováním cesty uživatele. S technickým profilem ověření se zobrazí chybová zpráva na stránce s vlastním kontrolním jménem a vrátí se do výstupních deklarací.
- **Výměna deklarací identity** – volání služby RESTful prostřednictvím kroku orchestrace se provede. V tomto scénáři není k dispozici uživatelské rozhraní pro vykreslení chybové zprávy. Pokud REST API vrátí chybu, bude uživatel přesměrován zpět do aplikace předávající strany s chybovou zprávou.

## <a name="protocol"></a>Protocol (Protokol)

Atribut **Name** elementu **Protocol** musí být nastaven na `Proprietary`. Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, které je používáno Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Následující příklad ukazuje technický profil RESTful:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Vstupní deklarace identity

Element **InputClaims** obsahuje seznam deklarací pro odeslání do REST API. Název vaší deklarace identity můžete také namapovat na název definovaný v REST API. Následující příklad ukazuje mapování mezi vaší zásadou a REST API. Tato **deklarace identity** se pošle REST API jako **FirstName**, zatímco **příjmení** se pošle jako **LastName**. Deklarace **e-mailu** je nastavená tak, jak je.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

Element **InputClaimsTransformations** může obsahovat kolekci prvků **InputClaimsTransformation** , které se používají k úpravě vstupních deklarací identity nebo k vygenerování nových před odesláním do REST API.

## <a name="send-a-json-payload"></a>Odeslat datovou část JSON

REST API Technical profil vám umožní poslat do koncového bodu složitou datovou část JSON.

Chcete-li odeslat složitou datovou část JSON:

1. Pomocí transformace deklarací [GenerateJson](json-transformations.md) Sestavte datovou část JSON.
1. V REST API Technical profil:
    1. Přidejte transformaci vstupních deklarací identity s odkazem na `GenerateJson` transformaci deklarací identity.
    1. Nastavte možnost metadata `SendClaimsIn` na `body`
    1. Nastavte možnost metadata `ClaimUsedForRequestPayload` na název deklarace identity obsahující datovou část JSON.
    1. Ve vstupní deklaraci identity přidejte odkaz na vstupní deklaraci, která obsahuje datovou část JSON.

Následující příklad `TechnicalProfile` odešle ověřovací e-mail pomocí e-mailové služby jiného výrobce (v tomto případě SendGrid).

```XML
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>Deklarace výstupů

Element **OutputClaims** obsahuje seznam deklarací vrácených REST API. Možná budete muset namapovat název deklarace identity definované v zásadě na název definovaný v REST API. Můžete také zahrnout deklarace identity, které nejsou vraceny zprostředkovatelem REST API identity, pokud nastavíte atribut `DefaultValue`.

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

Následující příklad ukazuje deklaraci identity vrácenou REST API:

- Deklarace identity **MembershipId** , která je namapovaná na název deklarace **loyaltyNumber**

Technický profil také vrací deklarace identity, které nejsou vraceny zprostředkovatelem identity:

- Deklarace identity **loyaltyNumberIsNew** , která má výchozí hodnotu nastavenou na `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ServiceUrl | Ano | Adresa URL koncového bodu REST API. |
| AuthenticationType | Ano | Typ ověřování prováděného zprostředkovatelem deklarací RESTful. Možné hodnoty: `None`, `Basic`, `Bearer`nebo `ClientCertificate`. Hodnota `None` označuje, že REST API není anonymní. Hodnota `Basic` označuje, že REST API je zabezpečeno pomocí základního ověřování HTTP. K rozhraní API můžou přistupovat jenom ověření uživatelé, včetně Azure AD B2C. Hodnota `ClientCertificate` (doporučeno) znamená, že REST API omezuje přístup pomocí ověřování klientského certifikátu. K vašemu rozhraní API můžou mít přístup jenom služby, které mají příslušné certifikáty, například Azure AD B2C. Hodnota `Bearer` označuje, že REST API omezuje přístup pomocí OAuth2 nosného tokenu klienta. |
| AllowInsecureAuthInProduction| Ne| Určuje, jestli `AuthenticationType` může být nastavená na `none` v produkčním prostředí (`DeploymentMode` [TrustFrameworkPolicy](trustframeworkpolicy.md) je nastavená na `Production`nebo není zadaná). Možné hodnoty: true nebo false (výchozí). |
| SendClaimsIn | Ne | Určuje, jakým způsobem se vstupní deklarace identity odesílají do zprostředkovatele deklarací RESTful. Možné hodnoty: `Body` (výchozí), `Form`, `Header`nebo `QueryString`. Hodnota `Body` je vstupní deklarace, která je odeslána v těle žádosti ve formátu JSON. Hodnota `Form` je vstupní deklarace, která se pošle v těle žádosti ve formátu hodnoty "& oddělovače klíče" typu ampersand. Hodnota `Header` je vstupní deklarace, která je odeslána v hlavičce požadavku. Hodnota `QueryString` je vstupní deklarace, která je odeslána v řetězci dotazu požadavku. Příkazy HTTP, které jsou vyvolány pomocí obou, jsou následující:<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: GET</li><li>`QueryString`: GET</li></ul> |
| ClaimsFormat | Ne | Aktuálně se nepoužívá, může být ignorováno. |
| ClaimUsedForRequestPayload| Ne | Název deklarace identity řetězce, která obsahuje datovou část, která se má odeslat do REST API. |
| DebugMode | Ne | Spustí technický profil v režimu ladění. Možné hodnoty: `true`nebo `false` (výchozí). V režimu ladění může REST API vrátit více informací. Přečtěte si část [vracení chybové zprávy](#returning-error-message) . |
| IncludeClaimResolvingInClaimsHandling  | Ne | Pro vstupní a výstupní deklarace identity určuje, jestli je [řešení deklarací identity](claim-resolver-overview.md) zahrnuté v technickém profilu. Možné hodnoty: `true`nebo `false` (výchozí). Pokud chcete použít překladač deklarací identity v technickém profilu, nastavte tuto hodnotu na `true`. |
| ResolveJsonPathsInJsonTokens  | Ne | Určuje, zda technický profil řeší cesty JSON. Možné hodnoty: `true`nebo `false` (výchozí). Tato metadata slouží ke čtení dat z vnořeného prvku JSON. V [OutputClaim](technicalprofiles.md#outputclaims)nastavte `PartnerClaimType` na element cesty JSON, který chcete výstup. Například: `firstName.localized`nebo `data.0.to.0.email`.|

## <a name="cryptographic-keys"></a>Kryptografické klíče

Pokud je typ ověřování nastaven na `None`, nepoužije se element **CryptographicKeys** .

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

Pokud je typ ověřování nastaven na `Basic`, element **CryptographicKeys** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Ano | Uživatelské jméno, které se používá k ověření. |
| BasicAuthenticationPassword | Ano | Heslo, které se používá k ověření. |

Následující příklad ukazuje technický profil se základním ověřováním:

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

Pokud je typ ověřování nastaven na `ClientCertificate`, element **CryptographicKeys** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ClientCertificate | Ano | Certifikát x509 (sada klíčů RSA), která se má použít k ověření. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

Pokud je typ ověřování nastaven na `Bearer`, element **CryptographicKeys** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | Ne | Nosný token OAuth 2,0. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-error-message"></a>Vracení chybové zprávy

Vaše REST API může potřebovat vrátit chybovou zprávu, například ' uživatel nebyl nalezen v systému CRM '. Pokud dojde k chybě, REST API by měla vracet chybovou zprávu HTTP 409 (kód stavu odpovědi na konflikt) s následujícími atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| version | Ano | 1.0.0 |
| status | Ano | 409 |
| code | Ne | Kód chyby od poskytovatele koncového bodu RESTful, který se zobrazí, když je povolená `DebugMode`. |
| Identifikátor | Ne | Identifikátor požadavku od poskytovatele koncového bodu RESTful, který se zobrazí, když je povolená `DebugMode`. |
| userMessage | Ano | Chybová zpráva, která se zobrazí uživateli. |
| developerMessage | Ne | Podrobný popis problému a jak ho opravit, který se zobrazí, když je povolený `DebugMode`. |
| moreInfo | Ne | Identifikátor URI, který odkazuje na Další informace, které se zobrazí, když je povolena `DebugMode`. |

Následující příklad ukazuje REST API, který vrací chybovou zprávu formátovanou ve formátu JSON:

```JSON
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

Následující příklad ukazuje C# třídu, která vrací chybovou zprávu:

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>Další kroky

Příklady použití technického profilu RESTful najdete v následujících článcích:

- [Integrace REST APIch výměn deklarací identity v cestě uživatele Azure AD B2C jako ověření vstupu uživatele](rest-api-claims-exchange-dotnet.md)
- [Zabezpečte své služby RESTful pomocí ověřování HTTP Basic.](secure-rest-api-dotnet-basic-auth.md)
- [Zabezpečte službu RESTful pomocí klientských certifikátů.](secure-rest-api-dotnet-certificate-auth.md)
- [Návod: integrace REST APIch výměn deklarací identity v cestě uživatele Azure AD B2C při ověřování vstupu uživatele](custom-policy-rest-api-claims-validation.md)
