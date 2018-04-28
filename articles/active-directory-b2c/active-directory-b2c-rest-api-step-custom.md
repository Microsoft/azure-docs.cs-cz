---
title: 'Azure Active Directory B2C: REST API deklarací výměnu jako krok orchestration | Microsoft Docs'
description: Téma na Azure Active Directory B2C vlastní zásady, které se integrují s rozhraní API
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/24/2017
ms.author: davidmu
ms.openlocfilehash: 0a3f69f8262e67de624d4cd97e0cad49c6a96ef0
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-an-orchestration-step"></a>Návod: Integrace rozhraní REST API deklarace identity výměn v vám dobře slouží Azure AD B2C uživatele jako krok orchestration

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Framework prostředí Identity (IEF) podkladovou Azure Active Directory B2C (Azure AD B2C) umožňuje vývojáři identity integrovat interakci s rozhraní RESTful API cesty uživatele.  

Na konci tohoto průvodce bude možné vytvořit cestu uživatele Azure AD B2C, která komunikuje s služby RESTful.

IEF odesílá data v deklaracích identity a přijímá data zpět v deklaracích identity. Rozhraní REST API deklarací exchange:

- Můžete třeba navrhnout jako krok orchestration.
- Můžete aktivovat externí akce. Například se můžete přihlásit jako událost v externí databáze.
- Slouží k načtení hodnotu a uložte ho v uživatelské databázi.

Přijaté deklarace identity můžete použít později změnit tok provádění.

Můžete taky navrhnout interakce jako profil ověření. Další informace najdete v tématu [návod: integrace rozhraní API REST deklarací výměn v vám dobře slouží Azure AD B2C uživatele jako ověření na vstup uživatele](active-directory-b2c-rest-api-validation-custom.md).

Tento scénář je, že když uživatel provede úpravy profilu, chceme:

1. Vyhledání uživatele v externím systému.
2. Získáte Město, kde je tento uživatel zaregistrován.
3. Tento atribut vrátí do aplikace jako deklarace identity.

## <a name="prerequisites"></a>Požadavky

- Klient služby Azure AD B2C, nakonfigurované k dokončení registrace-množství nebo přihlášení, místní účet, jak je popsáno v [Začínáme](active-directory-b2c-get-started-custom.md).
- Koncový bod REST API pro interakci s. Tento návod používá jako příklad webhook, jehož jednoduché Azure funkce aplikace.
- *Doporučená*: dokončení [REST API deklarací exchange návod jako krok ověření](active-directory-b2c-rest-api-validation-custom.md).

## <a name="step-1-prepare-the-rest-api-function"></a>Krok 1: Příprava funkce rozhraní REST API

> [!NOTE]
> Instalace funkce rozhraní API REST je mimo rámec tohoto článku. [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) poskytuje vynikající sada nástrojů pro vytváření služeb RESTful v cloudu.

Nastavíme Azure funkce, která obdrží deklarace identity názvem `email`a potom se vrátí deklarace `city` s přiřazenou hodnotou `Redmond`. Ukázka funkce Azure je na [Githubu](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

`userMessage` Deklarace identity, který vrací funkci Azure je volitelná v tomto kontextu a IEF ho budou ignorovat. Potenciálně ho můžete použít jako zprávu do aplikace a zobrazovat uživatelům později.

```csharp
if (requestContentAsJObject.email == null)
{
    return request.CreateResponse(HttpStatusCode.BadRequest);
}

var email = ((string) requestContentAsJObject.email).ToLower();

return request.CreateResponse<ResponseContent>(
    HttpStatusCode.OK,
    new ResponseContent
    {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        userMessage = "User Found",
        city = "Redmond"
    },
    new JsonMediaTypeFormatter(),
    "application/json");
```

Aplikace Azure funkce usnadňuje získat adresu URL funkce, která obsahuje identifikátor konkrétní funkce. V takovém případě je adresa URL: https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==. Můžete ho použít pro testování.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>Krok 2: Konfigurace exchange deklarace identity rozhraní RESTful API jako technické profil v souboru TrustFrameworExtensions.xml

Technické profil je úplná konfigurace exchange potřeby u služby RESTful. Otevřete soubor TrustFrameworkExtensions.xml a přidejte následující fragment kódu XML uvnitř `<ClaimsProvider>` elementu.

> [!NOTE]
> V následující soubor XML, RESTful zprostředkovatele `Version=1.0.0.0` označen jako protokol. Považuje za jako funkce, která bude komunikovat s externí služby. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```XML
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-LookUpLoyaltyWebHook">
            <DisplayName>Check LookUpLoyalty Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
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

`<InputClaims>` Element definuje deklarace identity, které budou odesílané z IEF ke službě REST. V tomto příkladu obsah deklarace `givenName` zašle službě REST jako deklarace identity `email`.  

`<OutputClaims>` Element definuje deklarace identity, které budou IEF očekávat od služby REST. Bez ohledu na počet deklarace identity, které jsou přijaty IEF použije pouze ty, které jsou identifikovány sem. V tomto příkladu se deklarace identity jako přijata `city` budou mapována na IEF deklarace identity názvem `city`.

## <a name="step-3-add-the-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Krok 3: Přidejte novou deklaraci `city` schématu TrustFrameworkExtensions.xml souboru

Deklarace identity `city` není kdekoli definován zatím v našem schématu. Ano, přidat definici uvnitř elementu `<BuildingBlocks>`. Tento prvek na začátek souboru TrustFrameworkExtensions.xml můžete najít.

```XML
<BuildingBlocks>
    <!--The claimtype city must be added to the TrustFrameworkPolicy-->
    <!-- You can add new claims in the BASE file Section III, or in the extensions file-->
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

## <a name="step-4-include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-trustframeworkextensionsxml"></a>Krok 4: Zahrnují výměny deklarací identity služby REST, jak orchestration krok ve vašem profilu upravit uživatele cesty ve TrustFrameworkExtensions.xml

Přidejte krok profil upravit uživatele cesty, po uživatel ověřen (postup orchestration 1 – 4 v následující soubor XML) a uživatel zadal aktualizovaný profil informací (krok 5).

> [!NOTE]
> Existuje mnoho případy použití, kde volání rozhraní REST API služby lze použít jako krok orchestration. Krok orchestration může sloužit jako aktualizace do externího systému po uživatele byla úspěšně dokončena úloha jako první registrace, nebo jako aktualizace profilu pro zachování informací o synchronizovány. V takovém případě se používá k posílení informací uvedených aplikaci po upravit profil.

Kopírování profil upravit uživatelský kód cesty XML ze souboru TrustFrameworkBase.xml do souboru TrustFrameworkExtensions.xml uvnitř `<UserJourneys>` elementu. Proveďte změny v kroku 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
    <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
    </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Pokud pořadí neodpovídá vaší verzí, ujistěte se, že vloží kód jako krok před `ClaimsExchange` typu `SendClaims`.

Poslední XML pro cestu uživatel by měl vypadat takto:

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
                <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    </OrchestrationSteps>
    <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="step-5-add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Krok 5: Přidejte do deklarací `city` k předávající straně soubor zásad, deklarace identity je odeslána do vaší aplikace

Upravte svůj soubor ProfileEdit.xml předávající stranu a upravovat `<TechnicalProfile Id="PolicyProfile">` elementu, který chcete přidat následující: `<OutputClaim ClaimTypeReferenceId="city" />`.

Po přidání nových deklarací identity technické profil vypadá takto:

```XML
<DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="step-6-upload-your-changes-and-test"></a>Krok 6: Odešlete své změny a testování

Přepište existující verze zásad.

1.  (Volitelné:) Uložte stávající verzi (stažením) souboru rozšíření, než budete pokračovat. Chcete-li zachovat počáteční složitost nízkou, doporučujeme nenahrávejte více verzí souboru rozšíření.
2.  (Volitelné:) Přejmenujte novou verzi ID zásady pro soubor zásad, který upravit tak, že změníte `PolicyId="B2C_1A_TrustFrameworkProfileEdit"`.
3.  Nahrajte soubor rozšíření.
4.  Nahrajte soubor zásad, který RP upravit.
5.  Použití **spustit nyní** k testování zásad. Zkontrolujte token, který IEF vrátí do aplikace.

Pokud všechno, co je správně nastavena, je token bude obsahovat nová deklarace identity `city`, s hodnotou `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Další postup

[Pomocí rozhraní API REST jako krok ověření](active-directory-b2c-rest-api-validation-custom.md)

[Upravit profil úpravy sbírat dodatečné informace od uživatelů](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
