---
title: Rozhraní REST API deklarací výměny jako krok Orchestrace v Azure Active Directory B2C | Dokumentace Microsoftu
description: Téma v Azure Active Directory B2C vlastní zásady, které se integrují s rozhraním API.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/24/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: dddb42f53d4bb59113df937799bd4de10d31491c
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338775"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-an-orchestration-step"></a>Postupy: Integrace rozhraní REST API služby výměny deklarací identity na vaší cestě uživatele Azure AD B2C jako krok Orchestrace

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Rozhraní prostředí pro Identity (IEF), které je základem Azure Active Directory B2C (Azure AD B2C) umožňuje identity pro vývojáře k integraci interakci se rozhraní RESTful API v cestě uživatele.  

Na konci tohoto návodu bude možné vytvořit cestu uživatele Azure AD B2C, který spolupracuje s služby typu REST.

IEF odesílá data jako deklarace identity a přijímá data zpět v deklaracích identity. Výměna deklarací identit rozhraní REST API:

- Může sloužit jako krok Orchestrace.
- Externí akce můžete aktivovat. To například protokolovat událost v externí databázi.
- Je možné načíst hodnotu a uloží je v uživatelské databázi.

Přijaté deklarací identity můžete použít později změnit tok spouštění.

Můžete také navrhnout zásahu jako profil ověření. Další informace najdete v tématu [názorný postup: integrace rozhraní REST API deklarací výměny na vaší cestě uživatele Azure AD B2C, jako na vstup uživatele](active-directory-b2c-rest-api-validation-custom.md).

Tento scénář je, že když uživatel provede úpravy profilu, chceme:

1. Vyhledání uživatele v externím systému.
2. Získejte Město, ve kterém je tento uživatel zaregistrovaný.
3. Tento atribut vraťte se do aplikace jako deklarace identity.

## <a name="prerequisites"></a>Požadavky

- Klient služby Azure AD B2C nakonfigurovaný tak, aby dokončit místní účet přihlášení-registrace/přihlášení, jak je popsáno v [Začínáme](active-directory-b2c-get-started-custom.md).
- Koncový bod rozhraní REST API pro interakci s. Tento návod používá aplikace webhooku jednoduchou funkci Azure jako příklad.
- *Doporučené*: dokončení [rozhraní REST API deklarací návod exchange jako krok ověření](active-directory-b2c-rest-api-validation-custom.md).

## <a name="step-1-prepare-the-rest-api-function"></a>Krok 1: Příprava – funkce rozhraní REST API

> [!NOTE]
> Instalace funkce rozhraní REST API je mimo rámec tohoto článku. [Služba Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) poskytuje vynikající toolkit k vytvoření služby RESTful v cloudu.

Nastavíme, která bude přijímat deklarace identity volá funkci Azure `email`a potom vrátí deklaraci identity `city` s přiřazenou hodnotou `Redmond`. Ukázka funkce Azure Functions je na [Githubu](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

`userMessage` Deklarace identity, která vrací funkce Azure Functions je volitelné v tomto kontextu, a IEF ho budou ignorovat. Potenciálně slouží jako zpráv do aplikace a budou zobrazovat uživateli později.

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

Aplikaci Azure function app umožňuje snadno získat adresu URL funkce, která obsahuje identifikátor konkrétní funkce. V takovém případě je adresa URL: https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==. Můžete ho použít pro testování.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>Krok 2: Konfigurace jako technický profil v souboru TrustFrameworExtensions.xml výměna deklarací identit RESTful API

Technický profil je úplná konfigurace exchange požadované službou RESTful. Otevřete soubor TrustFrameworkExtensions.xml a přidejte následující fragment kódu XML uvnitř `<ClaimsProvider>` elementu.

> [!NOTE]
> Následující kód XML, poskytovatele RESTful `Version=1.0.0.0` označen jako protokol. Vezměte v úvahu ji jako funkci, která bude komunikovat s externí služby. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

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

`<InputClaims>` Element definuje deklarace, které se odešlou z IEF ke službě REST. V tomto příkladu obsah deklarace identity `givenName` se odešlou do služby REST jako deklarace identity `email`.  

`<OutputClaims>` Element definuje deklarace, které IEF bude očekávat od služby REST. Bez ohledu na počet deklarace identity získané IEF bude používat jenom ty, které jsou identifikovány tady. V tomto příkladu se deklarace identity přijata jako `city` budou zmapována do IEF deklarace identity volá `city`.

## <a name="step-3-add-the-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Krok 3: Přidejte novou deklaraci `city` schématu souboru TrustFrameworkExtensions.xml

Deklarace identity `city` není ještě definovány kdekoli v našich schématu. Ano, přidejte definici uvnitř elementu `<BuildingBlocks>`. Můžete najít tento prvek na začátku souboru TrustFrameworkExtensions.xml.

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

## <a name="step-4-include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-trustframeworkextensionsxml"></a>Krok 4: Obsahovat výměna deklarací identit služby REST, jak je na vaší cestě uživatelské úpravy profilu v TrustFrameworkExtensions.xml krok Orchestrace

Přidejte krok k profilu úpravy cesty uživatele, poté, co uživatel byl ověřen (kroků Orchestrace 1 až 4 v následující kód XML) a uživatel zadal aktualizovaný profil informací (krok 5).

> [!NOTE]
> Existuje řada případů použití, kde volání rozhraní REST API je možné jako krok Orchestrace. Jako krok Orchestrace ho lze použít jako aktualizace externího systému poté, co uživatel úspěšně dokončí úlohu, jako první registraci nebo jako aktualizace profilu udržovat synchronizované informace. V takovém případě se používá k posílení údaje do aplikace po upravit profil.

Kopírování profil upravit uživatelský kód cestu XML ze souboru TrustFrameworkBase.xml do souboru TrustFrameworkExtensions.xml uvnitř `<UserJourneys>` elementu. Pak proveďte změny v kroku 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
    <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
    </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Pokud pořadí se neshoduje s vaší verzí, ujistěte se, že vloží kód jako krok před `ClaimsExchange` typ `SendClaims`.

Poslední XML pro cestu uživatele by měl vypadat nějak takto:

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

## <a name="step-5-add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Krok 5: Přidání deklarace identity `city` k předávající straně zásad souborů, takže deklarace identity se odešlou do vaší aplikace

Upravte soubor ProfileEdit.xml předávající stranu a upravovat `<TechnicalProfile Id="PolicyProfile">` prvek přidejte následující: `<OutputClaim ClaimTypeReferenceId="city" />`.

Po přidání nové deklarace technický profil vypadá takto:

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

## <a name="step-6-upload-your-changes-and-test"></a>Krok 6: Odešlete své změny a otestovat

Přepište existující verze zásad.

1.  (Volitelné:) Uložte stávající verzi (stažením) souboru rozšíření předtím, než budete pokračovat. Pokud chcete zachovat počáteční složitost nízké, doporučujeme nahrávat více verzí souboru rozšíření.
2.  (Volitelné:) Přejmenujte novou verzi ID zásad pro soubor zásad upravit tak, že změníte `PolicyId="B2C_1A_TrustFrameworkProfileEdit"`.
3.  Nahrání souboru rozšíření.
4.  Nahrajte soubor zásad upravit poskytovatele prostředků.
5.  Použití **Run Now** testování zásad. Zkontrolujte token, který IEF vrátí do aplikace.

Pokud všechno je správně nastavené, token, který bude obsahovat nové deklarace `city`, s hodnotou `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

[Použití rozhraní REST API jako krok ověření](active-directory-b2c-rest-api-validation-custom.md)

[Upravit profil upravit sbírat dodatečné informace od uživatelů](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
