---
title: REST API výměn deklarací identity – Azure Active Directory B2C
description: Přidejte REST API výměny deklarací identity do vlastních zásad v Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 12ddbe9f43baf68f6c11c9b720a0f684316af46a
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065318"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Přidání výměn deklarací identity REST API do vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Do [vlastních zásad](active-directory-b2c-overview-custom.md) v Azure Active Directory B2C (Azure AD B2C) můžete přidat interakci s rozhraním API RESTful. V tomto článku se dozvíte, jak vytvořit Azure AD B2C cestu uživatele, která komunikuje s RESTful službami.

Interakce zahrnuje výměnu deklarací informací mezi REST API deklarací identity a Azure AD B2C. Výměny deklarací identity mají následující vlastnosti:

- Dá se navrhovat jako krok orchestrace.
- Může aktivovat externí akci. Například může protokolovat událost v externí databázi.
- Dá se použít k načtení hodnoty a jejímu uložení do uživatelské databáze.
- Může změnit tok provádění.

Scénář, který je reprezentován v tomto článku, zahrnuje následující akce:

1. Vyhledejte uživatele v externím systému.
2. Získejte město, kde je tento uživatel zaregistrován.
3. Vrátí tento atribut do aplikace jako deklaraci identity.

## <a name="prerequisites"></a>Požadavky

- Proveďte kroky v části Začínáme [s vlastními zásadami](active-directory-b2c-get-started-custom.md).
- REST API koncový bod, se kterým chcete pracovat. V tomto článku se jako příklad používá jednoduchá funkce Azure Functions. Pokud chcete vytvořit funkci Azure Functions, přečtěte si téma [Vytvoření první funkce v Azure Portal](../azure-functions/functions-create-first-azure-function.md).

## <a name="prepare-the-api"></a>Příprava rozhraní API

V této části připravíte funkci Azure Functions `email`, aby přijímala hodnotu, a potom vrátí `city` hodnotu, kterou může Azure AD B2C použít jako deklarace.

Změňte soubor run. csx pro funkci Azure, kterou jste vytvořili pro použití následujícího kódu:

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

## <a name="configure-the-claims-exchange"></a>Konfigurace výměny deklarací identity

Technický profil poskytuje konfiguraci pro výměnu deklarací identity.

Otevřete soubor *TrustFrameworkExtensions. XML* a přidejte do elementu **ClaimsProviders** následující element XML **ClaimsProvider** .

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
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

Element **InputClaims** definuje deklarace, které se odesílají do služby REST. V tomto příkladu je hodnota deklarace identity `givenName` odeslána službě REST jako deklarace identity. `email` Element **OutputClaims** definuje deklarace identity, které se očekávají od služby REST.

Výše uvedené `AuthenticationType` komentáře a `AllowInsecureAuthInProduction` určete změny, které byste měli dělat při přesunu do produkčního prostředí. Informace o tom, jak zabezpečit rozhraní API pro RESTful pro produkční prostředí, najdete v tématu [zabezpečení rozhraní API RESTful se základními ověřováním](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) a [zabezpečením RESTful API pomocí ověřování certifikátů](active-directory-b2c-custom-rest-api-netfw-secure-cert.md).

## <a name="add-the-claim-definition"></a>Přidat definici deklarace identity

Přidejte definici pro `city` uvnitř elementu **BuildingBlocks** . Tento element lze najít na začátku souboru TrustFrameworkExtensions. XML.

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

## <a name="add-an-orchestration-step"></a>Přidat krok orchestrace

Existuje mnoho případů použití, kde REST API volání lze použít jako krok orchestrace. Jako krok orchestrace se dá použít jako aktualizace externího systému poté, co uživatel úspěšně dokončil úkol, jako je například registrace v prvním čase, nebo jako aktualizace profilu, aby se informace synchronizovaly. V tomto případě se používá k rozšíření informací poskytnutých aplikaci po úpravě profilu.

Přidejte krok do profilu upravit cestu uživatele. Po ověření uživatele (kroky Orchestration 1-4 v následujícím kódu XML) a uživatel zadal aktualizované informace o profilu (krok 5). Zkopírujte kód XML cesty pro úpravy profilu uživatele ze souboru *TrustFrameworkBase. XML* do souboru *TrustFrameworkExtensions. XML* v rámci elementu **userjourney** . Pak proveďte úpravy jako krok 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Finální XML pro cestu uživatele by mělo vypadat jako v tomto příkladu:

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

## <a name="add-the-claim"></a>Přidat deklaraci identity

Upravte soubor *ProfileEdit. XML* a přidejte `<OutputClaim ClaimTypeReferenceId="city" />` jej do elementu **OutputClaims** .

Po přidání nové deklarace bude technický profil vypadat jako v tomto příkladu:

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

## <a name="upload-your-changes-and-test"></a>Nahrání změn a testování

1. Volitelné Než budete pokračovat, uložte existující verzi (stažením) souborů.
2. Nahrajte soubor *TrustFrameworkExtensions. XML* a *ProfileEdit. XML* a vyberte k přepsání stávajícího souboru.
3. Vyberte **B2C_1A_ProfileEdit**.
4. Pro **možnost vybrat aplikaci** na stránce Přehled v části vlastní zásady vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali. Ujistěte se, že **Adresa URL odpovědi** je `https://jwt.ms`.
4. Vyberte **Spustit nyní**. Přihlaste se pomocí přihlašovacích údajů k účtu a klikněte na **pokračovat**.

Pokud je všechno správně nastavené, token zahrnuje novou deklaraci identity `city`s hodnotou. `Redmond`

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

## <a name="next-steps"></a>Další kroky

Interakci můžete také navrhnout jako profil ověřování. Další informace najdete v tématu [Návod: Integrujte REST API výměn deklarací identity v cestě uživatele Azure AD B2C při ověřování vstupu](active-directory-b2c-rest-api-validation-custom.md)uživatele.

[Úpravou úpravy profilu Shromážděte Další informace od uživatelů.](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Odkaz RESTful Technical Profile](restful-technical-profile.md)

Informace o zabezpečení rozhraní API najdete v následujících článcích:

* [Zabezpečení rozhraní API RESTful pomocí základního ověřování (uživatelské jméno a heslo)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [Zabezpečení rozhraní API RESTful pomocí klientských certifikátů](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
