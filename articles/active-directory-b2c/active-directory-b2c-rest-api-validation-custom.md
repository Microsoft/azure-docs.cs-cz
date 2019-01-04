---
title: Rozhraní REST API služby výměny deklarací jako ověření v Azure Active Directory B2C | Dokumentace Microsoftu
description: Téma na vlastní zásady pro Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/24/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8af8e4b7844feb785600ef683891642ea89bccaf
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556896"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Průvodce: Integrace rozhraní REST API služby výměny deklarací identity na vaší cestě uživatele Azure AD B2C, jako na vstup uživatele

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Rozhraní prostředí pro Identity (IEF), které je základem Azure Active Directory B2C (Azure AD B2C) umožňuje identity pro vývojáře k integraci interakci se rozhraní RESTful API v cestě uživatele.  

Na konci tohoto návodu bude možné vytvořit cestu uživatele Azure AD B2C, který spolupracuje s služby typu REST.

IEF odesílá data jako deklarace identity a přijímá data zpět v deklaracích identity. Interakce s rozhraním API:

- Může sloužit jako výměna deklarací identit rozhraní REST API nebo jako profil ověření, který se v jednom z kroků Orchestrace.
- Obvykle ověřuje vstup uživatele. Pokud se hodnota od uživatele, uživatel zkusit znovu zadejte platnou hodnotu se moci vrátit chybovou zprávu.

Můžete také navrhnout zásahu jako krok Orchestrace. Další informace najdete v tématu [názorný postup: Integrace rozhraní REST API služby výměny deklarací identity na vaší cestě uživatele Azure AD B2C jako krok Orchestrace](active-directory-b2c-rest-api-step-custom.md).

Příklad profilu ověření budeme používat cesty uživatele upravit profil v souboru sady starter pack ProfileEdit.xml.

Abychom mohli ověřit, že název zadaný uživatelem v úpravě profilu není součástí seznamu vyloučení.

## <a name="prerequisites"></a>Požadavky

- Klient služby Azure AD B2C nakonfigurovaný tak, aby dokončit místní účet přihlášení-registrace/přihlášení, jak je popsáno v [Začínáme](active-directory-b2c-get-started-custom.md).
- Koncový bod rozhraní REST API pro interakci s. V tomto návodu, jsme zřídili ukázku lokality volá [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) pomocí rozhraní REST API služby.

## <a name="step-1-prepare-the-rest-api-function"></a>Krok 1: Příprava – funkce rozhraní REST API

> [!NOTE]
> Instalace funkce rozhraní REST API je mimo rámec tohoto článku. [Služba Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) poskytuje vynikající toolkit k vytvoření služby RESTful v cloudu.

Vytvořili jsme funkce Azure, která bude přijímat deklarace identity, který se očekává, že jako `playerTag`. Funkce ověří, jestli tato deklarace identity existuje. Můžete přistupovat ke kódu dokončení funkce Azure Functions v [Githubu](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

```csharp
if (requestContentAsJObject.playerTag == null)
{
  return request.CreateResponse(HttpStatusCode.BadRequest);
}

var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
{
  return request.CreateResponse<ResponseContent>(
    HttpStatusCode.Conflict,
    new ResponseContent
    {
      version = "1.0.0",
      status = (int) HttpStatusCode.Conflict,
      userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
    },
    new JsonMediaTypeFormatter(),
    "application/json");
}

return request.CreateResponse(HttpStatusCode.OK);
```

Očekává IEF `userMessage` deklarace identity, která vrací funkce Azure functions. Tato deklarace identity se zobrazí jako řetězec uživateli, pokud se ověření nezdaří, třeba když se vrátí stav 409 – konflikt v předchozím příkladu.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Krok 2: Výměna deklarací identit RESTful API nakonfigurovat jako technický profil v souboru TrustFrameworkExtensions.xml

Technický profil je úplná konfigurace exchange požadované službou RESTful. Otevřete soubor TrustFrameworkExtensions.xml a přidejte následující fragment kódu XML uvnitř `<ClaimsProviders>` elementu.

> [!NOTE]
> Následující kód XML, poskytovatele RESTful `Version=1.0.0.0` označen jako protokol. Vezměte v úvahu ji jako funkci, která bude komunikovat s externí služby. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
                <Item Key="AllowInsecureAuthInProduction">true</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
            <ValidationTechnicalProfiles>
                <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

`InputClaims` Element definuje deklarace, které se odešlou z IEF ke službě REST. V tomto příkladu obsah deklarace identity `givenName` se odešlou do služby REST jako `playerTag`. V tomto příkladu IEF neočekává zpět deklarací identity. Místo toho čeká na odpověď ze služby REST a funguje podle stavové kódy, které obdrží.

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>Krok 3: Zahrnout výměna deklarací identit RESTful služba s vlastním potvrzením technický profil, ve kterém chcete ověřit uživatelský vstup

Krok ověření používá nejčastěji používané v interakci s uživatelem. Všechny interakce, kde se očekává poskytování vstup uživatele jsou *držitelem s prohlašovanou technické profily*. V tomto příkladu přidáme samoobslužné Asserted ProfileUpdate technický profil ověření. Toto je technická profil, který souboru zásad předávající stranu `Profile Edit` používá.

Přidání výměna deklarací identit s vlastním potvrzením technický profil:

1. Otevřete soubor TrustFrameworkBase.xml a vyhledejte `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Zkontrolujte konfiguraci této technického profilu. Sledujte, jak je exchange s uživatelem definovaný jako deklarace identity, které vyzve uživatele (vstupními deklaracemi identity) a deklarace identity, které se dají očekávat zpět od s vlastním potvrzením zprostředkovatele (výstupní deklarace identit).
3. Vyhledejte `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`a Všimněte si, že tento profil je vyvolán jako krok Orchestrace 5 `<UserJourney Id="ProfileEdit">`.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>Krok 4: Nahrání a testovací soubor zásad RP úpravy profilu

1. Nahrajte novou verzi souboru TrustFrameworkExtensions.xml.
2. Použití **spustit nyní** otestovat soubor zásad RP úpravy profilu.
3. Otestovat ověření zadáním jednoho z existující názvy (například mcvinny) v **křestní jméno** pole. Pokud všechno je správně nastavené, byste měli obdržet zprávu, která upozorní uživatele, že se už používá značky přehrávače.

## <a name="next-steps"></a>Další postup

[Upravit profil registrace upravit a uživatelů sbírat dodatečné informace od uživatelů](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Návod: Integrace rozhraní REST API služby výměny deklarací identity na vaší cestě uživatele Azure AD B2C jako krok Orchestrace](active-directory-b2c-rest-api-step-custom.md)
