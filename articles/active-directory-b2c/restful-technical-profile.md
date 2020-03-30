---
title: Definování technického profilu RESTful ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Definujte technický profil RESTful ve vlastních zásadách ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 410f413fc8450c0ee33c3ca95e860a3e8de34107
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332613"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu RESTful ve vlastních zásadách služby Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro integraci vlastní služby RESTful. Azure AD B2C odesílá data do služby RESTful ve vstupní deklaraci kolekce a přijímá data zpět ve výstupní kolekci deklarací. Další informace najdete [v tématu Integrace renomovaných deklarací rozhraní REST API ve vlastních zásadách Azure AD B2C](custom-policy-rest-api-intro.md).  

## <a name="protocol"></a>Protocol (Protokol)

**Atribut Name** elementu **Protocol** musí být `Proprietary`nastaven na . Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, který používá Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Následující příklad ukazuje technický profil RESTful:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Vstupní deklarace

**InputClaims** Element obsahuje seznam deklarací identity k odeslání do rozhraní REST API. Název deklarace aplikace můžete také namapovat na název definovaný v rozhraní REST API. Následující příklad ukazuje mapování mezi zásadou a rozhraním REST API. Deklarace **givenName** je odeslána do rozhraní REST API jako **firstName**, zatímco **příjmení** je odesláno jako **příjmení lastName**. Deklarace **e-mailu** je nastavena tak, jak je.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

**InputClaimsTransformations** Element může obsahovat kolekci **InputClaimsTransformation** prvky, které se používají k úpravě vstupní deklarace identity nebo generovat nové před odesláním do rozhraní REST API.

## <a name="send-a-json-payload"></a>Odeslání datové části JSON

Technický profil rozhraní REST API umožňuje odeslat komplexní datovou část JSON do koncového bodu.

Odeslání komplexní datové části JSON:

1. Sestavte si svou datovou část JSON pomocí transformace [deklarací identity GenerateJson.](json-transformations.md)
1. V technickém profilu rozhraní REST API:
    1. Přidejte vstupní deklarace transformace `GenerateJson` s odkazem na transformace deklarací identity.
    1. Nastavte `SendClaimsIn` možnost metadat na`body`
    1. Nastavte `ClaimUsedForRequestPayload` možnost metadat na název deklarace, která obsahuje datovou část JSON.
    1. Do vstupní deklarace přidejte odkaz na vstupní deklaraci obsahující datovou část JSON.

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

## <a name="output-claims"></a>Výstupní pohledávky

**OutputClaims** Element obsahuje seznam deklarací vrácených rozhraním REST API. Možná budete muset namapovat název deklarace deklarace definované ve vaší zásadě na název definovaný v rozhraní REST API. Můžete také zahrnout deklarace identity, které nejsou vráceny zprostředkovatelem identity rozhraní REST API, pokud nastavíte `DefaultValue` atribut.

**OutputClaimsTransformations** Element může obsahovat kolekci **OutputClaimsTransformation** prvky, které se používají k úpravě výstupní deklarace nebo generovat nové.

Následující příklad ukazuje deklaraci vrácenou rozhraním REST API:

- Deklarace **MembershipID,** která je mapována na název deklarace argumentace **loyaltyNumber.**

Technický profil také vrátí deklarace identity, které nejsou vráceny zprostředkovatelem identity:

- **LoyaltyNumberIsNew** deklarace, která má `true`výchozí hodnotu nastavenou na .

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Adresa ServiceUrl | Ano | Adresa URL koncového bodu rozhraní REST API. |
| Authenticationtype | Ano | Typ ověřování, který provádí zprostředkovatel deklarací identity RESTful. Možné `None`hodnoty: `Basic` `Bearer`, `ClientCertificate`, , nebo . Hodnota `None` označuje, že rozhraní REST API není anonymní. Hodnota `Basic` označuje, že rozhraní REST API je zabezpečeno základním ověřováním HTTP. K vašemu rozhraní API mají přístup jenom ověření uživatelé, včetně Azure AD B2C. Hodnota `ClientCertificate` (doporučená) označuje, že rozhraní REST API omezuje přístup pomocí ověřování klientského certifikátu. K vašemu rozhraní API mají přístup pouze služby, které mají příslušné certifikáty, například Azure AD B2C. Hodnota `Bearer` označuje, že rozhraní REST API omezuje přístup pomocí klienta OAuth2 Bearer token. |
| AllowInsecureAuthInProduction| Ne| Označuje, `AuthenticationType` zda lze `none` nastavit v`DeploymentMode` produkčním prostředí [(TrustFrameworkPolicy](trustframeworkpolicy.md) je nastavena na `Production`, nebo není zadán). Možné hodnoty: true nebo false (výchozí). |
| SendClaimsIn | Ne | Určuje způsob odeslání vstupních deklarací identity zprostředkovateli deklarací RESTful. Možné `Body` hodnoty: `Form`(výchozí), , `Header`nebo `QueryString`. Hodnota `Body` je vstupní deklarace, která je odeslána v těle požadavku ve formátu JSON. Hodnota `Form` je vstupní deklarace, která je odeslána v těle požadavku ve formátu hodnoty klíče oddělených & ampersand "&". Hodnota `Header` je vstupní deklarace, která je odeslána v hlavičce požadavku. Hodnota `QueryString` je vstupní deklarace, která je odeslána v řetězci dotazu požadavku. Http slovesa vyvolána každý jsou následující:<br /><ul><li>`Body`: POŠTA</li><li>`Form`: POŠTA</li><li>`Header`: získejte</li><li>`QueryString`: získejte</li></ul> |
| Formát deklarací identity | Ne | Není aktuálně používán, lze je ignorovat. |
| ClaimusedForRequestPayload| Ne | Název deklarace řetězce, která obsahuje datovou část, která má být odeslána do rozhraní REST API. |
| Režim ladění | Ne | Spustí technický profil v režimu ladění. Možné hodnoty: `true` `false` , nebo (výchozí). V režimu ladění rozhraní REST API můžete vrátit další informace. Viz část [Vrácená chybová zpráva.](#returning-error-message) |
| IncludeClaimResolvingInClaimsHandling  | Ne | Pro vstupní a výstupní deklarace určuje, zda je [řešení deklarací](claim-resolver-overview.md) zahrnuto do technického profilu. Možné hodnoty: `true` `false`  , nebo (výchozí). Pokud chcete použít překladač deklarací identity v `true`technickém profilu, nastavte toto na . |
| VyřešitJsonPathsInJsonTokens  | Ne | Označuje, zda technický profil řeší cesty JSON. Možné hodnoty: `true` `false` , nebo (výchozí). Tato metadata slouží ke čtení dat z vnořeného prvku JSON. V [outputclaim](technicalprofiles.md#outputclaims)nastavte `PartnerClaimType` na prvek cesty JSON, který chcete výstup. Například: `firstName.localized`, `data.0.to.0.email`nebo .|
| UseClaimAsBearerToken| Ne| Název deklarace, která obsahuje token nosiče.|

## <a name="cryptographic-keys"></a>Kryptografické klíče

Pokud je typ ověřování `None`nastaven na , prvek **CryptographicKeys** se nepoužívá.

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

Pokud je typ ověřování `Basic`nastaven na , obsahuje element **CryptographicKeys** následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Základní ověřováníUživatelské jméno | Ano | Uživatelské jméno, které se používá k ověření. |
| Základní ověřováníPassword | Ano | Heslo, které se používá k ověření. |

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

Pokud je typ ověřování `ClientCertificate`nastaven na , obsahuje element **CryptographicKeys** následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Klientský certifikát | Ano | Certifikát X509 (sada klíčů RSA), který se má použít k ověření. |

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

Pokud je typ ověřování `Bearer`nastaven na , obsahuje element **CryptographicKeys** následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Token nosu AuthenticationToken | Ne | Žeton nosiče OAuth 2.0. |

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

## <a name="returning-error-message"></a>Vracející se chybová zpráva

Rozhraní REST API může být nutné vrátit chybovou zprávu, například "Uživatel nebyl nalezen v systému CRM". Pokud dojde k chybě, rozhraní REST API by měl vrátit chybovou zprávu HTTP 4xx, například 400 (chybný požadavek) nebo 409 (konflikt) kód stavu odpovědi. Tělo odpovědi obsahuje chybovou zprávu formátovanou v JSON:

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

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| version | Ano | Vaše verze rozhraní REST API. Například: 1.0.1 |
| status | Ano | Musí být 409 |
| kód | Ne | Kód chyby od zprostředkovatele koncového bodu RESTful, který se zobrazí, když `DebugMode` je povolena. |
| Requestid | Ne | Identifikátor požadavku od zprostředkovatele koncového bodu RESTful, který se zobrazí, když `DebugMode` je povolena. |
| userMessage | Ano | Chybová zpráva, která se zobrazí uživateli. |
| zpráva developerMessage | Ne | Podrobný popis problému a jak jej opravit, který se `DebugMode` zobrazí, když je povolen. |
| víceInformace | Ne | Identifikátor URI, který odkazuje na další `DebugMode` informace, které se zobrazí, když je povoleno. |


Následující příklad ukazuje třídu C#, která vrací chybovou zprávu:

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

Příklady použití technického profilu RESTful naleznete v následujících článcích:

- [Integrace renomovaných deklarací rozhraní REST ve vlastních zásadách Azure AD B2C](custom-policy-rest-api-intro.md)
- [Návod: Integrace serveru REST API pro deklarace identity v cestě uživatele Azure AD B2C jako ověření vstupu uživatele](custom-policy-rest-api-claims-validation.md)
- [Návod: Přidání serveru REST API pro deklarace identity do vlastních zásad ve službě Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Zabezpečení služeb rozhraní REST API](secure-rest-api.md)

