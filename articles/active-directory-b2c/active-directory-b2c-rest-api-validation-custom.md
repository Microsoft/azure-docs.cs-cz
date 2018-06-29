---
title: Rozhraní REST API deklarací výměnu jako ověřování v Azure Active Directory B2C | Microsoft Docs
description: Téma na Azure Active Directory B2C vlastní zásady.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/24/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2c8b676ffff0f95a0966bfe18ce171de888265b9
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "34709168"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Návod: Integrace rozhraní REST API deklarace identity výměn v vám dobře slouží Azure AD B2C uživatele jako ověření na vstup uživatele

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Framework prostředí Identity (IEF) podkladovou Azure Active Directory B2C (Azure AD B2C) umožňuje vývojáři identity integrovat interakci s rozhraní RESTful API cesty uživatele.  

Na konci tohoto průvodce bude možné vytvořit cestu uživatele Azure AD B2C, která komunikuje s služby RESTful.

IEF odesílá data v deklaracích identity a přijímá data zpět v deklaracích identity. Interakce s rozhraním API:

- Můžete třeba navrhnout jako deklarace identity systému exchange REST API nebo jako profil ověření, který se stane uvnitř na krok orchestration.
- Obvykle ověří vstup od uživatele. Pokud se odmítne hodnota od uživatele, uživatel můžete zkusit znovu zadejte platnou hodnotu s možnost vrátí chybovou zprávu.

Můžete taky navrhnout interakce jako krok orchestration. Další informace najdete v tématu [návod: integrace rozhraní API REST deklarací výměn v vám dobře slouží uživatele Azure AD B2C jako krok orchestration](active-directory-b2c-rest-api-step-custom.md).

Pro ověření profil příkladu použijeme cesty profil upravit uživatele v souboru pack starter ProfileEdit.xml.

Jsme můžete ověřit, že zadaný uživatelem v profilu upravit název není součástí seznam vyloučení.

## <a name="prerequisites"></a>Požadavky

- Klient služby Azure AD B2C, nakonfigurované k dokončení registrace-množství nebo přihlášení, místní účet, jak je popsáno v [Začínáme](active-directory-b2c-get-started-custom.md).
- Koncový bod REST API pro interakci s. V tomto návodu, jsme zřídili ukázkový web s názvem [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) službou REST API.

## <a name="step-1-prepare-the-rest-api-function"></a>Krok 1: Příprava funkce rozhraní REST API

> [!NOTE]
> Instalace funkce rozhraní API REST je mimo rámec tohoto článku. [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) poskytuje vynikající sada nástrojů pro vytváření služeb RESTful v cloudu.

Vytvořili jsme Azure funkce, která obdrží deklarace identity, která se očekává, že jako `playerTag`. Funkce ověřuje, zda existuje tuto deklaraci. Dostanete kód dokončení funkce Azure v [Githubu](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

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

Očekává IEF `userMessage` deklarace identity, který vrací funkci Azure. Tuto deklaraci bude zobrazovat jako řetězec uživatelům, pokud se ověření nezdaří, například když se vrátí stav 409 – konflikt v předchozím příkladu.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Krok 2: Konfigurace exchange deklarace identity rozhraní RESTful API jako technické profil v souboru TrustFrameworkExtensions.xml

Technické profil je úplná konfigurace exchange potřeby u služby RESTful. Otevřete soubor TrustFrameworkExtensions.xml a přidejte následující fragment kódu XML uvnitř `<ClaimsProviders>` elementu.

> [!NOTE]
> V následující soubor XML, RESTful zprostředkovatele `Version=1.0.0.0` označen jako protokol. Považuje za jako funkce, která bude komunikovat s externí služby. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

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

`InputClaims` Element definuje deklarace identity, které budou odesílané z IEF ke službě REST. V tomto příkladu obsah deklarace `givenName` zašle službě REST jako `playerTag`. V tomto příkladu IEF neočekává zpět deklarací identity. Místo toho čeká na odpověď od služby REST a jednání založené na stavové kódy, které obdrží.

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>Krok 3: Zahrnout exchange deklarace identity služba RESTful samoobslužné uplatňovaná technické profil, ve které chcete ověření vstupu uživatele

Krok ověření slouží nejčastěji v interakci s uživatelem. Všechny interakce, kde se očekává poskytování vstup uživatele jsou *samoobslužné prohlašovanou technické profily*. V tomto příkladu přidáme technické profil samoobslužných Asserted ProfileUpdate ověření. Toto je technická profilu, který soubor zásad předávající stranu `Profile Edit` používá.

Přidání deklarace identity exchange do samoobslužné uplatňovaná technické profil:

1. Otevřete soubor TrustFrameworkBase.xml a vyhledejte `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Zkontrolujte konfiguraci tohoto technické profilu. Sledujte, jak je exchange s uživatelem definovaný jako deklarace identity, které se dotaz uživatele (vstupních deklarací identity) a deklarací identity, které se budou zpět ze samoobslužných uplatňovaná zprostředkovatele (výstup deklarace identity).
3. Vyhledejte `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`a Všimněte si, že tento profil je vyvolána jako orchestration krok 4 `<UserJourney Id="ProfileEdit">`.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>Krok 4: Nahrání a testovací soubor zásad RP úpravy profilu

1. Nahrajte novou verzi souboru TrustFrameworkExtensions.xml.
2. Použití **spustit nyní** k testování soubor zásad RP úpravy profilu.
3. Test ověření některým z existující názvy (například mcvinny) v **křestní jméno** pole. Pokud všechno, co je správně nastavena, měli byste obdržet zprávu, která upozorní uživatele, že player značky se již používá.

## <a name="next-steps"></a>Další postup

[Upravit profil registrace upravit a uživatelů sbírat dodatečné informace od uživatelů](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Návod: Integrace rozhraní REST API deklarace identity výměn v vám dobře slouží Azure AD B2C uživatele jako krok orchestration](active-directory-b2c-rest-api-step-custom.md)
