---
title: Definování technického profilu RESTful ve vlastních zásadách v Azure Active Directory B2C | Microsoft Docs
description: Definujte technický profil RESTful ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f535bc7d67198b3fe06326260bc1910b6afd36f2
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68346767"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu RESTful ve vlastní zásadě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C poskytuje podporu pro vlastní službu RESTful. Azure AD B2C odesílá data službě RESTful ve vstupní kolekci deklarací a přijímá data zpátky do výstupní kolekce deklarací. S integrací služby RESTful můžete:

- **Ověřit data vstupu uživatele** – zabrání ukládání poškozených dat do Azure AD B2C. Pokud hodnota od uživatele není platná, služba RESTful vrátí chybovou zprávu, která uživateli vydá pokyn k zadání položky. Můžete třeba ověřit, že e-mailová adresa zadaná uživatelem existuje v databázi vašeho zákazníka.
- **Přepsat vstupní deklarace identity** – umožňuje přeformátovat hodnoty ve vstupních deklaracích. Pokud například uživatel zadá křestní jméno do všech malých a velkých písmen, můžete název naformátovat pouze na první písmeno.
- **Rozšíření uživatelských dat** – umožňuje další integraci s podnikovými firemními aplikacemi. Například vaše služba RESTful může obdržet e-mailovou adresu uživatele, zadat dotaz na databázi zákazníka a získat věrnostní číslo uživatele Azure AD B2C. Vrácené deklarace identity je možné uložit, vyhodnotit v dalších krocích orchestrace nebo zahrnout do přístupového tokenu.
- **Spustit vlastní obchodní logiku** – umožňuje odesílat nabízená oznámení, aktualizovat podnikové databáze, spouštět proces migrace uživatelů, spravovat oprávnění, auditovat databáze a provádět další akce.

Vaše zásada může odesílat vstupní deklarace do vašeho REST API. REST API může také vracet výstupní deklarace identity, které můžete použít později v zásadách, nebo může vyvolat chybovou zprávu. Integraci s RESTful službami můžete navrhovat následujícími způsoby:

- **Technický profil ověření** – technický profil ověření volá službu RESTful. Technický profil ověření ověřuje uživatelem poskytnutá data před pokračováním cesty uživatele. S technickým profilem ověření se zobrazí chybová zpráva na stránce s vlastním kontrolním jménem a vrátí se do výstupních deklarací.
- **Výměna deklarací identity** – volání služby RESTful prostřednictvím kroku orchestrace se provede. V tomto scénáři není k dispozici uživatelské rozhraní pro vykreslení chybové zprávy. Pokud REST API vrátí chybu, bude uživatel přesměrován zpět do aplikace předávající strany s chybovou zprávou.

## <a name="protocol"></a>Protocol

Atribut **Name** elementu **Protocol** musí být nastaven na `Proprietary`hodnotu. Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, které je používáno Azure AD B2C `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`:.

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

## <a name="output-claims"></a>Deklarace výstupů

Element **OutputClaims** obsahuje seznam deklarací vrácených REST API. Možná budete muset namapovat název deklarace identity definované v zásadě na název definovaný v REST API. Můžete také zahrnout deklarace identity, které nejsou vraceny zprostředkovatelem REST API identity, pokud nastavíte `DefaultValue` atribut.

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

Následující příklad ukazuje deklaraci identity vrácenou REST API:

- Deklarace identity **MembershipId** , která je namapovaná na název deklarace **loyaltyNumber**

Technický profil také vrací deklarace identity, které nejsou vraceny zprostředkovatelem identity: 

- Deklarace identity **loyaltyNumberIsNew** , která má výchozí hodnotu nastavenou `true`na.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ServiceUrl | Ano | Adresa URL koncového bodu REST API. | 
| AuthenticationType | Ano | Typ ověřování prováděného zprostředkovatelem deklarací RESTful. Možné hodnoty: `None`, `Basic`, nebo `ClientCertificate`. `None` Hodnota označuje, že REST API není anonymní. `Basic` Hodnota označuje, že REST API je zabezpečeno pomocí ověřování HTTP Basic. K rozhraní API můžou přistupovat jenom ověření uživatelé, včetně Azure AD B2C. Hodnota `ClientCertificate` (doporučeno) znamená, že REST API omezuje přístup pomocí ověřování klientského certifikátu. K vaší službě mají přístup jenom služby, které mají příslušné certifikáty, například Azure AD B2C. | 
| SendClaimsIn | Ne | Určuje, jakým způsobem se vstupní deklarace identity odesílají do zprostředkovatele deklarací RESTful. Možné hodnoty: `Body` (výchozí), `Form`, `Header`nebo `QueryString`. `Body` Hodnota je vstupní deklarace, která je odeslána v těle žádosti ve formátu JSON. `Form` Hodnota je vstupní deklarace, která se pošle v těle žádosti ve formátu hodnoty oddělovače & znaku. `Header` Hodnota je vstupní deklarace, která je odeslána v hlavičce požadavku. `QueryString` Hodnota je vstupní deklarace, která je odeslána v řetězci dotazu požadavku. | 
| ClaimsFormat | Ne | Určuje formát pro výstupní deklarace identity. Možné hodnoty: `Body` (výchozí), `Form`, `Header`nebo `QueryString`. `Body` Hodnota je výstupní deklarace, která je odeslána v těle žádosti ve formátu JSON. `Form` Hodnota je výstupní deklarace, která se pošle v těle žádosti ve formátu hodnoty "oddělovače" & znaku. `Header` Hodnota je výstupní deklarace, která je odeslána v hlavičce požadavku. `QueryString` Hodnota je výstupní deklarace, která je odeslána v řetězci dotazu požadavku. | 
| DebugMode | Ne | Spustí technický profil v režimu ladění. V režimu ladění může REST API vrátit více informací. Přečtěte si část vracení chybové zprávy. | 

## <a name="cryptographic-keys"></a>Kryptografické klíče

Pokud je typ ověřování nastaven na `None`, není použit element **CryptographicKeys** .

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

| Atribut | Požadováno | Popis |
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

| Atribut | Požadováno | Popis |
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

## <a name="returning-error-message"></a>Vracení chybové zprávy

Vaše REST API může potřebovat vrátit chybovou zprávu, například ' uživatel nebyl nalezen v systému CRM '. V případě chyby by REST API měla vrátit chybovou zprávu HTTP 409 (kód stavu odpovědi na konflikt) s následujícími atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| version | Ano | 1.0.0 | 
| status | Ano | 409 | 
| code | Ne | Kód chyby od poskytovatele koncového bodu RESTful, který se zobrazí, `DebugMode` když je povolený. | 
| Identifikátor | Ne | Identifikátor požadavku od poskytovatele koncového bodu RESTful, který se zobrazí, `DebugMode` když je povolený. | 
| userMessage | Ano | Chybová zpráva, která se zobrazí uživateli. | 
| developerMessage | Ne | Podrobný popis problému a jak ho opravit, který se zobrazí, když `DebugMode` je povolený. | 
| moreInfo | Ne | Identifikátor URI, který odkazuje na Další informace, které se zobrazí `DebugMode` , když je povolený. | 

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

## <a name="examples"></a>Příklady:
- [Integrace REST APIch výměn deklarací identity v cestě uživatele Azure AD B2C jako ověření vstupu uživatele](active-directory-b2c-custom-rest-api-netfw.md) 
- [Zabezpečte své služby RESTful pomocí ověřování HTTP Basic.](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
- [Zabezpečte službu RESTful pomocí klientských certifikátů.](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
- [Podrobné Integrace REST APIch výměn deklarací identity v cestě uživatele Azure AD B2C při ověřování vstupu uživatele](active-directory-b2c-rest-api-validation-custom.md)

 














