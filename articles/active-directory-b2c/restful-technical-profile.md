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
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eb6d82019cccd1da327461cb0a0635aea4f3647f
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174967"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu RESTful ve vlastní zásadě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro integraci vlastní služby RESTful. Azure AD B2C odesílá data službě RESTful ve vstupní kolekci deklarací a přijímá data zpátky do výstupní kolekce deklarací. Další informace najdete v tématu [integrace REST APIch výměn deklarací identity ve vlastních zásadách Azure AD B2C](custom-policy-rest-api-intro.md).  

## <a name="protocol"></a>Protokol

Atribut **Name** elementu **Protocol** musí být nastaven na hodnotu `Proprietary` . Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, které je používáno Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` .

Následující příklad ukazuje technický profil RESTful:

```xml
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Vstupní deklarace identity

Element **InputClaims** obsahuje seznam deklarací pro odeslání do REST API. Název vaší deklarace identity můžete také namapovat na název definovaný v REST API. Následující příklad ukazuje mapování mezi vaší zásadou a REST API. Tato **deklarace identity** se pošle REST API jako **FirstName**, zatímco **příjmení** se pošle jako **LastName**. Deklarace **e-mailu** je nastavená tak, jak je.

```xml
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
    1. Přidejte transformaci vstupních deklarací identity s odkazem na `GenerateJson` transformaci deklarací.
    1. Nastavte `SendClaimsIn` možnost metadata na `body`
    1. Nastavte `ClaimUsedForRequestPayload` možnost metadata na název deklarace identity obsahující datovou část JSON.
    1. Ve vstupní deklaraci identity přidejte odkaz na vstupní deklaraci, která obsahuje datovou část JSON.

Následující příklad `TechnicalProfile` odešle ověřovací e-mail s použitím e-mailové služby jiného výrobce (v tomto případě SendGrid).

```xml
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
    <Item Key="DefaultUserMessageIfRequestFailed">Cannot process your request right now, please try again later.</Item>
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

Element **OutputClaims** obsahuje seznam deklarací vrácených REST API. Možná budete muset namapovat název deklarace identity definované v zásadě na název definovaný v REST API. Můžete také zahrnout deklarace identity, které nejsou vraceny zprostředkovatelem REST API identity, pokud nastavíte `DefaultValue` atribut.

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

Následující příklad ukazuje deklaraci identity vrácenou REST API:

- Deklarace identity **MembershipId** , která je namapovaná na název deklarace **loyaltyNumber**

Technický profil také vrací deklarace identity, které nejsou vraceny zprostředkovatelem identity:

- Deklarace identity **loyaltyNumberIsNew** , která má výchozí hodnotu nastavenou na `true` .

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ServiceUrl | Ano | Adresa URL koncového bodu REST API. |
| AuthenticationType | Ano | Typ ověřování prováděného zprostředkovatelem deklarací RESTful. Možné hodnoty: `None` , `Basic` , `Bearer` ,  `ClientCertificate` , nebo `ApiKeyHeader` . <br /><ul><li>`None`Hodnota označuje, že REST API je anonymní. </li><li>`Basic`Hodnota označuje, že REST API je zabezpečeno pomocí ověřování HTTP Basic. K rozhraní API můžou přistupovat jenom ověření uživatelé, včetně Azure AD B2C. </li><li>`ClientCertificate`Hodnota (doporučeno) znamená, že REST API omezuje přístup pomocí ověřování klientského certifikátu. K vašemu rozhraní API můžou mít přístup jenom služby, které mají příslušné certifikáty, například Azure AD B2C. </li><li>`Bearer`Hodnota označuje, že REST API omezí přístup pomocí tokenu nosiče klienta OAuth2. </li><li>`ApiKeyHeader`Hodnota označuje, že REST API je zabezpečená pomocí HLAVIČKY http klíče rozhraní API, jako je například *x-Functions-Key*. </li></ul> |
| AllowInsecureAuthInProduction| Ne| Určuje, zda `AuthenticationType` lze nastavit na hodnotu `none` v produkčním prostředí ( `DeploymentMode` [TrustFrameworkPolicy](trustframeworkpolicy.md) je nastaven na `Production` nebo není určen). Možné hodnoty: true nebo false (výchozí). |
| SendClaimsIn | Ne | Určuje, jakým způsobem se vstupní deklarace identity odesílají do zprostředkovatele deklarací RESTful. Možné hodnoty: `Body` (výchozí), `Form` , `Header` `Url` nebo `QueryString` . `Body`Hodnota je vstupní deklarace, která je odeslána v těle žádosti ve formátu JSON. `Form`Hodnota je vstupní deklarace, která se pošle v těle žádosti ve formátu hodnoty oddělovače & znaku. `Header`Hodnota je vstupní deklarace, která je odeslána v hlavičce požadavku. `Url`Hodnota je vstupní deklarace, která se pošle v adrese URL, třeba https://{claim1}. example. com/{claim2}/{claim3}? { claim4} = {claim5}. `QueryString`Hodnota je vstupní deklarace, která je odeslána v řetězci dotazu požadavku. Příkazy HTTP, které jsou vyvolány pomocí obou, jsou následující:<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: GET</li><li>`Url`: GET</li><li>`QueryString`: GET</li></ul> |
| ClaimsFormat | Ne | Aktuálně se nepoužívá, může být ignorováno. |
| ClaimUsedForRequestPayload| Ne | Název deklarace identity řetězce, která obsahuje datovou část, která se má odeslat do REST API. |
| DebugMode | Ne | Spustí technický profil v režimu ladění. Možné hodnoty: `true` , nebo `false` (výchozí). V režimu ladění může REST API vrátit více informací. Přečtěte si část [vracení chybové zprávy](#returning-validation-error-message) . |
| IncludeClaimResolvingInClaimsHandling  | Ne | Pro vstupní a výstupní deklarace identity určuje, jestli je [řešení deklarací identity](claim-resolver-overview.md) zahrnuté v technickém profilu. Možné hodnoty: `true` , nebo `false` (výchozí). Pokud chcete použít překladač deklarací identity v technickém profilu, nastavte tuto hodnotu na `true` . |
| ResolveJsonPathsInJsonTokens  | Ne | Určuje, zda technický profil řeší cesty JSON. Možné hodnoty: `true` , nebo `false` (výchozí). Tato metadata slouží ke čtení dat z vnořeného prvku JSON. V [OutputClaim](technicalprofiles.md#output-claims)nastavte na `PartnerClaimType` element cesty JSON, který chcete výstup. Například: `firstName.localized` nebo `data.0.to.0.email` .|
| UseClaimAsBearerToken| Ne| Název deklarace identity, která obsahuje nosný token.|

## <a name="error-handling"></a>Zpracování chyb

Následující metadata lze použít ke konfiguraci chybových zpráv zobrazených při REST API chybě. Chybové zprávy lze [lokalizovat](localization-string-ids.md#restful-service-error-messages).

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| DefaultUserMessageIfRequestFailed | Ne | Výchozí přizpůsobená chybová zpráva pro všechny výjimky REST API.|
| UserMessageIfCircuitOpen | Ne | Chybová zpráva, když REST API není dostupná. Pokud není zadaný, DefaultUserMessageIfRequestFailed se vrátí. |
| UserMessageIfDnsResolutionFailed | Ne | Chybová zpráva pro výjimku překladu DNS Pokud není zadaný, DefaultUserMessageIfRequestFailed se vrátí. | 
| UserMessageIfRequestTimeout | Ne | Chybová zpráva, když vypršel časový limit připojení Pokud není zadaný, DefaultUserMessageIfRequestFailed se vrátí. | 

## <a name="cryptographic-keys"></a>Kryptografické klíče

Pokud je typ ověřování nastaven na, není `None` použit element **CryptographicKeys** .

```xml
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

Pokud je typ ověřování nastaven na `Basic` , element **CryptographicKeys** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Ano | Uživatelské jméno, které se používá k ověření. |
| BasicAuthenticationPassword | Ano | Heslo, které se používá k ověření. |

Následující příklad ukazuje technický profil se základním ověřováním:

```xml
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

Pokud je typ ověřování nastaven na `ClientCertificate` , element **CryptographicKeys** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ClientCertificate | Ano | Certifikát x509 (sada klíčů RSA), která se má použít k ověření. |

```xml
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

Pokud je typ ověřování nastaven na `Bearer` , element **CryptographicKeys** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | Ne | Nosný token OAuth 2,0. |

```xml
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

Pokud je typ ověřování nastaven na `ApiKeyHeader` , element **CryptographicKeys** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Název hlavičky protokolu HTTP, například `x-functions-key` nebo `x-api-key` . | Ano | Klíč, který se používá k ověření. |

> [!NOTE]
> V tuto chvíli Azure AD B2C pro ověřování podporuje jenom jednu hlavičku HTTP. Pokud vaše volání RESTful vyžaduje více hlaviček, jako je ID klienta a tajný kód klienta, budete muset požadavek proxy nějakým způsobem vyzvat.

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ApiKeyHeader</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-validation-error-message"></a>Vracení chybové zprávy ověřování

Vaše REST API může potřebovat vrátit chybovou zprávu, například ' uživatel nebyl nalezen v systému CRM '. Pokud dojde k chybě, REST API by měla vracet chybovou zprávu HTTP 4xx, jako je například 400 (chybný požadavek) nebo 409 (konflikt) kód stavu odpovědi. Tělo odpovědi obsahuje chybovou zprávu formátovanou ve formátu JSON:

```json
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

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| verze | Ano | Vaše verze REST API. Příklad: 1.0.1 |
| status | Ano | Musí být 409 |
| kód | Ne | Kód chyby od poskytovatele koncového bodu RESTful, který se zobrazí, když `DebugMode` je povolený. |
| Identifikátor | Ne | Identifikátor požadavku od poskytovatele koncového bodu RESTful, který se zobrazí, když `DebugMode` je povolený. |
| userMessage | Ano | Chybová zpráva, která se zobrazí uživateli. |
| developerMessage | Ne | Podrobný popis problému a jak ho opravit, který se zobrazí, když `DebugMode` je povolený. |
| moreInfo | Ne | Identifikátor URI, který odkazuje na Další informace, které se zobrazí, když `DebugMode` je povolený. |


Následující příklad ukazuje třídu jazyka C#, která vrací chybovou zprávu:

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

- [Integrace REST APIch výměn deklarací identity do vlastních zásad Azure AD B2C](custom-policy-rest-api-intro.md)
- [Návod: integrace REST APIch výměn deklarací identity v Azure AD B2C cestě uživatele jako ověření vstupu uživatele](custom-policy-rest-api-claims-validation.md)
- [Návod: Přidání výměn deklarací identity REST API do vlastních zásad v Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Zabezpečení služby REST API Services](secure-rest-api.md)
