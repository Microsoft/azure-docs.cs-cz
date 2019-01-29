---
title: Definování rozhraní RESTful technický profil ve vlastních zásadách v Azure Active Directory B2C | Dokumentace Microsoftu
description: Definujte rozhraní RESTful technický profil ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9eb60f9581099813d96cecb9cb88155e64b7caa8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154316"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování rozhraní RESTful technický profil ve vlastních zásadách pro Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C poskytuje podporu pro služby RESTful. Azure AD B2C odešle data do služby RESTful ve vstupní kolekce deklarací a přijímá data zpět v kolekci výstupních deklarací identity. Integrace služby RESTful vám umožní:

- **Ověření vstupu dat uživatele** – brání poškozených datových uložením do Azure AD B2C. Pokud hodnota od uživatele není platná, vaše služba RESTful vrátí chybovou zprávu, která informuje uživatele k zadání položku. Ověřte například, že e-mailovou adresu, které zadal uživatel existuje v databázi vašeho zákazníka.
- **Přepsat vstupními deklaracemi identity** – umožňuje změnu formátu hodnot ve vstupních deklarací identity. Například pokud uživatel zadá křestní jméno v všechna písmena malá nebo všechna velká písmena, lze formátovat název pouze první písmeno velké.
- **Obohacení dat uživatele** – umožňuje další integraci s podnikovým – obchodní aplikace. Služby RESTful můžete například dostávat e-mailovou adresu uživatele, dotazy na databázi zákazníka a vrátí číslo věrnostní uživatele Azure AD B2C. Návratový deklarace identity lze uložit, vyhodnotí v dalších krocích Orchestrace nebo zahrnutý v přístupovém tokenu.
- **Spuštění vlastní obchodní logiky** – umožňuje odesílat nabízená oznámení, aktualizaci firemních databází, spusťte proces migrace uživatelů, oprávnění ke správě, audit databáze a provádět jiné akce.

Vaše zásada může odeslat vstupních deklarací identity k rozhraní REST API. Rozhraní REST API může také vrátit výstupní deklarace identit, které použijete později v zásadách nebo ji může vyvolat chybovou zprávu. Integrace služby RESTful můžete navrhnout následujícími způsoby:

- **Technický profil ověření** – technický profil ověření volá služba RESTful. Technický profil ověření ověří uživatelsky zadaných dat než cesty uživatele bude pokračovat. Chybová zpráva s technický profil ověření je zobrazení na stránku s vlastním potvrzením a vrácené v výstupní deklarace identit.
- **Deklarace identity exchange** – služba RESTful přes jeden krok Orchestrace je provedeno volání. V tomto scénáři neexistuje žádné uživatelské rozhraní k vykreslení chybové zprávě. Pokud vaše rozhraní REST API vrací chybu, bude uživatel přesměrován zpět do aplikace předávající strany s chybovou zprávou.

## <a name="protocol"></a>Protocol (Protokol)

**Název** atribut **protokol** elementu musí být nastavena na `Proprietary`. **Obslužná rutina** atribut musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, který používá Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Následující příklad ukazuje RESTful technický profil:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...    
```

## <a name="input-claims"></a>Vstupní deklarace identity

**InputClaims** prvek obsahuje seznam deklarací identity k odesílání do rozhraní REST API. Můžete také namapovat na název vašeho nároku na název definovaný v rozhraní REST API. Následující příklad ukazuje mapování mezi vaší zásady a rozhraní REST API. **GivenName** deklarace identity se odešle jako rozhraní REST API **firstName**, zatímco **příjmení** se odešle jako **lastName**. **E-mailu** sady deklarací, jako je.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

**InputClaimsTransformations** element může obsahovat kolekci **InputClaimsTransformation** prvků, které slouží k úpravě mezi vstupními deklaracemi identity nebo generování nových před odesláním do rozhraní REST API.

## <a name="output-claims"></a>Výstupní deklarace identit.

**OutputClaims** prvek obsahuje seznam deklarací identity vrácená rozhraním REST API. Budete muset namapovat název deklarace identity, definovaný ve svojí zásadě název definovaný v rozhraní REST API. Můžete také zahrnout deklarace identity, které nejsou vrácené zprostředkovatelem identity rozhraní REST API, tak dlouho, dokud je nastavit `DefaultValue` atribut.

**OutputClaimsTransformations** element může obsahovat kolekci **OutputClaimsTransformation** prvky, které se používají ke změně výstupní deklarace identit nebo generovat nové značky.

Následující příklad ukazuje deklaraci vrácená rozhraním REST API:

- **MembershipId** deklarace identity, který je namapovaný na **loyaltyNumber** název deklarace.

Technický profil také vrátí hodnotu deklarace identity, které nejsou vrácené zprostředkovatelem identity: 

- **LoyaltyNumberIsNew** deklaraci identity, která má výchozí hodnotu nastavte na `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ServiceUrl | Ano | Adresa URL koncového bodu rozhraní REST API. | 
| AuthenticationType | Ano | Typ ověřování prováděné poskytovatelem deklarací identit RESTful. Možné hodnoty: `None`, `Basic`, nebo `ClientCertificate`. `None` Hodnota značí, že není anonymní rozhraní REST API. `Basic` Hodnota značí, že je rozhraní REST API zabezpečené pomocí základního ověřování protokolu HTTP. Jenom ověřit uživatele, včetně Azure AD B2C, můžete přístup k rozhraní API. `ClientCertificate` (Doporučeno) hodnota značí, že rozhraní REST API omezuje přístup pomocí ověření klientského certifikátu. Pouze služby, které mají příslušné certifikáty, jako je například Azure AD B2C můžete přístup ke službě. | 
| SendClaimsIn | Ne | Určuje, jak se mezi vstupními deklaracemi identity odesílají do zprostředkovatele deklarací identit RESTful. Možné hodnoty: `Body` (výchozí), `Form`, `Header`, nebo `QueryString`. `Body` Hodnota je vstupní deklaraci identity, která je odeslána v textu požadavku ve formátu JSON. `Form` Hodnota je vstupní deklaraci identity, který posílá v těle požadavku ampersand ' a ' oddělené klíče ve formátu. `Header` Hodnota je vstupní deklaraci identity odeslaný v hlavičce požadavku. `QueryString` Hodnota je vstupní deklaraci identity, která je odeslána v řetězci dotazu požadavku. | 
| ClaimsFormat | Ne | Určuje formát pro výstupní deklarace identit. Možné hodnoty: `Body` (výchozí), `Form`, `Header`, nebo `QueryString`. `Body` Hodnota je výstupní deklarací, které je odesláno v textu požadavku ve formátu JSON. `Form` Hodnotu, která se odešle v textu požadavku v ampersand ' a ' oddělené klíče ve formátu deklarace identity výstup. `Header` Hodnota je výstupní deklarací odeslaný v hlavičce požadavku. `QueryString` Hodnota je výstupní deklarací, které je odesláno v řetězci dotazu požadavku. | 
| Režim DebugMode | Ne | Technický profil se spustí v režimu ladění. V režimu ladění rozhraní REST API můžete vrátit další informace. Naleznete v části vracející chybová zpráva. | 

## <a name="cryptographic-keys"></a>Kryptografické klíče

Pokud typ ověřování nastavený na `None`, **CryptographicKeys** není použit element.

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

Pokud typ ověřování nastavený na `Basic`, **CryptographicKeys** prvek obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Ano | Uživatelské jméno, který se používá k ověření. | 
| BasicAuthenticationPassword | Ano | Heslo, které se používá k ověření. |

Následující příklad ukazuje technického profilu se základním ověřováním:

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

Pokud typ ověřování nastavený na `ClientCertificate`, **CryptographicKeys** prvek obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ClientCertificate | Ano | X509 certifikát (sadu klíčů RSA) se má použít k ověření. | 

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

## <a name="returning-error-message"></a>Vrací chybovou zprávu

Rozhraní REST API může být zapotřebí vracet chybová zpráva, například "uživatel nebyl nalezen v systému CRM". K chybě dojde, rozhraní REST API by měla vrátit chybovou zprávu protokolu HTTP 409 (konflikt stavový kód odpovědi) s následujícími atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| version | Ano | 1.0.0 | 
| status | Ano | 409 | 
| kód | Ne | Kód chyby z poskytovatele RESTful koncový bod, který se zobrazí, když `DebugMode` je povolená. | 
| requestId | Ne | Identifikátor žádosti od poskytovatele koncový bod RESTful, které se zobrazí, když `DebugMode` je povolená. | 
| userMessage | Ano | Chybová zpráva, která se zobrazí uživateli. | 
| developerMessage | Ne | Podrobný popis problému a vyřešit, který se zobrazí, když `DebugMode` je povolená. | 
| moreInfo | Ne | Identifikátor URI, který odkazuje na další informace, které se zobrazí, když `DebugMode` je povolená. | 

Následující příklad ukazuje rozhraní REST API, která vrátí chybovou zprávu ve formátu JSON:

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

Následující příklad ukazuje třídu C#, která vrátí chybovou zprávu:

```C#
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
- [Integrace rozhraní REST API služby výměny deklarací identity na vaší cestě uživatele Azure AD B2C jako ověření vstupu uživatele](active-directory-b2c-custom-rest-api-netfw.md) 
- [Zabezpečení služby RESTful pomocí základního ověřování protokolu HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
- [Zabezpečení služby RESTful pomocí klientských certifikátů](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
- [Návod: Integrace rozhraní REST API služby výměny deklarací identity na vaší cestě uživatele Azure AD B2C, jako na vstup uživatele](active-directory-b2c-rest-api-validation-custom.md)

 














