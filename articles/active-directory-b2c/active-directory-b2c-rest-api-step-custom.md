---
title: Rozhraní REST API deklarací výměny – Azure Active Directory B2C
description: Výměna deklarací rozhraní REST API přidejte do vlastních zásad v Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0bdef508e12a3b11143149b330da73838b53f860
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439014"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Přidání rozhraní REST API služby výměny deklarací identity do vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Můžete přidat interakci s rozhraní RESTful API do vaší [vlastní zásady](active-directory-b2c-overview-custom.md) v Azure Active Directory (Azure AD) B2C. Tento článek ukazuje, jak vytvořit cestu uživatele Azure AD B2C, který spolupracuje s služby typu REST.

Interakce zahrnuje výměna deklarací identit informací mezi deklarace rozhraní REST API a Azure AD B2C. Výměna deklarací identity mají následující vlastnosti:

- Může sloužit jako krok Orchestrace.
- Externí akce můžete aktivovat. To například protokolovat událost v externí databázi.
- Je možné načíst hodnotu a uloží je v uživatelské databázi.
- Můžete změnit tok spouštění.

Scénář, který je reprezentován v tomto článku obsahuje následující akce:

1. Vyhledání uživatele v externím systému.
2. Získejte Město, ve kterém je tento uživatel zaregistrovaný.
3. Tento atribut vraťte se do aplikace jako deklarace identity.

## <a name="prerequisites"></a>Požadavky

- Proveďte kroky v [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md).
- Koncový bod rozhraní REST API pro interakci s. Tento článek používá jednoduché Azure fungovat jako příklad. Vytvoření funkce Azure Functions najdete v tématu [vytvoření první funkce na webu Azure Portal](../azure-functions/functions-create-first-azure-function.md).

## <a name="prepare-the-api"></a>Příprava rozhraní API

V této části můžete připravit funkce Azure získat hodnotu pro `email`a pak se vraťte hodnotu `city` , který je možné pomocí Azure AD B2C, jako deklarace identity.

Změna souboru run.csx pro funkci Azure, kterou jste vytvořili pomocí následujícího kódu:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
  string email = req.Query["email"];
  string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
  dynamic data = JsonConvert.DeserializeObject(requestBody);
  email = email ?? data?.email;

  return email != null
    ? (ActionResult)new OkObjectResult(
      new ResponseContent
      {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        city = "Redmond"
      })
      : new BadRequestObjectResult("Please pass an email on the query string or in the request body");
}

public class ResponseContent
{
    public string version { get; set; }
    public int status { get; set; }
    public string city {get; set; }
}
```

## <a name="configure-the-claims-exchange"></a>Konfigurace výměna deklarací identit

Technický profil obsahuje konfiguraci exchange deklarace identity.

Otevřít *TrustFrameworkExtensions.xml* soubor a přidejte následující **ClaimsProvider** – XML element uvnitř **ClaimsProviders** elementu.

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

**InputClaims** element definuje deklarace identity, které se odesílají do služby REST. V tomto příkladu je hodnota deklarace identity `givenName` se odesílají službě REST jako deklarace identity `email`. **OutputClaims** element definuje deklarace, které se očekává, že ze služby REST.

## <a name="add-the-claim-definition"></a>Přidat definici deklarací identity

Přidejte definici pro `city` uvnitř **BuildingBlocks** elementu. Můžete najít tento prvek na začátku souboru TrustFrameworkExtensions.xml.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="city">
      <DisplayName>City</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your city</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

## <a name="add-an-orchestration-step"></a>Přidat na krok Orchestrace

Existuje řada případů použití, kde volání rozhraní REST API je možné jako krok Orchestrace. Jako krok Orchestrace ho lze použít jako aktualizace externího systému poté, co uživatel úspěšně dokončí úlohu, jako první registraci nebo jako aktualizace profilu udržovat synchronizované informace. V takovém případě se používá k posílení údaje do aplikace po upravit profil.

Přidání kroku do cesty uživatele úpravy profilu. Jakmile se uživatel ověřen (kroků Orchestrace 1 až 4 v následující kód XML), a uživatel zadal aktualizovaný profil informací (krok 5). Kopírování profil upravovat kód XML cesty uživatele z *TrustFrameworkBase.xml* soubor do vašeho *TrustFrameworkExtensions.xml* soubor uvnitř **Userjourney** element. Pak proveďte změny v kroku 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Poslední XML pro cestu uživatele by měl vypadat jako v tomto příkladu:

```XML
<UserJourney Id="ProfileEdit">
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
      <ClaimsProviderSelections>
        <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
        <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
      </ClaimsProviderSelections>
    </OrchestrationStep>
    <OrchestrationStep Order="2" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
        <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>localAccountAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="4" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>socialIdpAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="5" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <!-- Add a step 6 to the user journey before the JWT token is created-->
    <OrchestrationStep Order="6" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>Přidání deklarace identity

Upravit *ProfileEdit.xml* a přidejte `<OutputClaim ClaimTypeReferenceId="city" />` k **OutputClaims** elementu.

Po přidání nové deklarace technický profil bude vypadat jako v tomto příkladu:

```XML
<TechnicalProfile Id="PolicyProfile">
  <DisplayName>PolicyProfile</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
    <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    <OutputClaim ClaimTypeReferenceId="city" />
  </OutputClaims>
  <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>Odešlete své změny a otestovat

1. (Volitelné:) Uložte stávající verzi (stažením) souborů než budete pokračovat.
2. Nahrát *TrustFrameworkExtensions.xml* a *ProfileEdit.xml* a vybrat, zda chcete přepsat existující soubor.
3. Vyberte **B2C_1A_ProfileEdit**.
4. Pro **vyberte aplikaci** na stránce Přehled vlastních zásad, vyberte webovou aplikaci s názvem *webapp1* , které jste dříve zaregistrovali. Ujistěte se, že **adresy URL odpovědi** je `https://jwt.ms`.
4. Vyberte **spustit nyní**. Přihlaste se pomocí přihlašovacích údajů k účtu a klikněte na tlačítko **pokračovat**.

Pokud všechno je správně nastavené, token, který zahrnuje novou deklaraci `city`, s hodnotou `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_profileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Další postup

- Můžete také navrhnout zásahu jako profil ověření. Další informace najdete v tématu [názorný postup: Integrace rozhraní REST API služby výměny deklarací identity na vaší cestě uživatele Azure AD B2C, jako na vstup uživatele](active-directory-b2c-rest-api-validation-custom.md).
- [Upravit profil upravit sbírat dodatečné informace od uživatelů](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
