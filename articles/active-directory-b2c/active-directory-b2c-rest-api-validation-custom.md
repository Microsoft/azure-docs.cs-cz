---
title: REST API výměny deklarací identity jako ověřování
titleSuffix: Azure AD B2C
description: Návod pro vytvoření Azure AD B2C cesty uživatele, která komunikuje s RESTful službami.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8730870bfae9f704ee43594497f79942b70a6181
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949368"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Návod: integrace REST APIch výměn deklarací identity v cestě uživatele Azure AD B2C při ověřování vstupu uživatele

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Rozhraní identity Experience Framework (IEF), které je Azure Active Directory B2C (Azure AD B2C), umožňuje vývojářům identity integrovat interakci s rozhraním API RESTful v cestě uživatele.

Na konci tohoto návodu budete moct vytvořit Azure AD B2C cestu uživatele, která komunikuje s RESTful službami.

IEF odesílá data v deklaracích identity a přijímá data zpět v deklaracích identity. Interakce s rozhraním API:

- Dá se navrhovat jako REST API výměny deklarací identity nebo jako profil ověřování, který se nachází uvnitř kroku orchestrace.
- Obvykle ověřuje vstup od uživatele. Pokud se hodnota od uživatele odmítne, uživatel se může pokusit znovu zadat platnou hodnotu s příležitostí k vrácení chybové zprávy.

Můžete také navrhnout interakci jako krok orchestrace. Další informace najdete v tématu [Návod: integrace REST APIch výměn deklarací identity v cestě uživatele Azure AD B2C jako krok orchestrace](active-directory-b2c-rest-api-step-custom.md).

V příkladu profilu ověřování použijeme profil upravit cestu uživatele v souboru úvodní sady ProfileEdit. XML.

Můžeme ověřit, že název zadaný uživatelem v rámci úpravy profilu není součástí seznamu vyloučení.

## <a name="prerequisites"></a>Předpoklady

- Tenant Azure AD B2C nakonfigurovaný k dokončení registrace nebo přihlášení k místnímu účtu, jak je popsáno v tématu [Začínáme](active-directory-b2c-get-started-custom.md).
- REST API koncový bod, se kterým chcete pracovat. Pro tento návod jsme nastavili ukázkový web s názvem [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) s využitím služby REST API.

## <a name="step-1-prepare-the-rest-api-function"></a>Krok 1: Příprava funkce REST API

> [!NOTE]
> Instalace funkcí REST API je mimo rámec tohoto článku. [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) poskytuje vynikající sadu nástrojů pro vytváření služeb RESTful v cloudu.

Vytvořili jsme funkci Azure, která obdrží deklaraci identity, kterou očekáváme jako `playerTag`. Funkce ověří, zda tato deklarace existuje. Můžete získat přístup k kompletnímu kódu funkce Azure v [GitHubu](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

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

IEF očekává deklaraci identity `userMessage`, kterou funkce Azure vrátí. Tato deklarace identity se zobrazí jako řetězec pro uživatele, pokud se ověření nezdaří, například když se v předchozím příkladu vrátí stav konfliktu 409.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Krok 2: Konfigurace výměny deklarací identity rozhraní API RESTful jako technického profilu v souboru TrustFrameworkExtensions. XML

Technický profil je plná konfigurace serveru Exchange, který požaduje služba RESTful. Otevřete soubor TrustFrameworkExtensions. XML a přidejte následující fragment kódu XML do prvku `<ClaimsProviders>`.

> [!NOTE]
> V následujícím kódu XML `Version=1.0.0.0` poskytovatel RESTful je popsán jako protokol. Berte v úvahu jako funkci, která bude komunikovat s externí službou. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="SendClaimsIn">Body</Item>
                <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
                <Item Key="AuthenticationType">None</Item>
                <!-- REMOVE the following line in production environments -->
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

Element `InputClaims` definuje deklarace, které budou odeslány z IEF do služby REST. V tomto příkladu se obsah deklarace `givenName` do služby REST pošle jako `playerTag`. V tomto příkladu IEF neočekává deklarace identity zpátky. Místo toho čeká na odpověď ze služby REST a funguje na základě stavových kódů, které obdrží.

Komentáře uvedené výše `AuthenticationType` a `AllowInsecureAuthInProduction` určují změny, které byste měli dělat při přesunu do produkčního prostředí. Informace o tom, jak zabezpečit rozhraní API pro RESTful pro produkční prostředí, najdete v tématu [zabezpečení rozhraní API RESTful se základními ověřováním](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) a [zabezpečením RESTful API pomocí ověřování certifikátů](active-directory-b2c-custom-rest-api-netfw-secure-cert.md).

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>Krok 3: zahrňte výměnu deklarací identity služby RESTful do technického profilu s vlastním uplatněním, kde chcete ověřit vstup uživatele.

Nejběžnějším použitím tohoto kroku ověření je interakce s uživatelem. Všechny interakce, u kterých se očekává, že uživatel zadá vstup, jsou *technické profily s vlastním uplatněním*. V tomto příkladu přidáme ověření do technického profilu s vlastním kontrolním výrazem (ProfileUpdate). Toto je technický profil, který používá soubor zásad předávající strany (RP) `Profile Edit`.

Přidání výměny deklarací identity do technického profilu s vlastním uplatněním:

1. Otevřete soubor TrustFrameworkBase. XML a vyhledejte `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Zkontrolujte konfiguraci tohoto technického profilu. Sledujte, jak se Exchange s uživatelem definuje jako deklarace identity, které budou požádáni o uživatele (vstupní deklarace identity) a deklarace identity, které se vrátí zpět od zprostředkovatele s vlastním uplatněním (výstupní deklarace).
3. Vyhledejte `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`a Všimněte si, že tento profil je vyvolán jako krok Orchestration 5 `<UserJourney Id="ProfileEdit">`.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>Krok 4: nahrání a testování souboru zásad pro úpravu RP profilu

1. Nahrajte novou verzi souboru TrustFrameworkExtensions. XML.
2. Pomocí rutiny **Spustit nyní** otestujte soubor zásad pro úpravy v profilech.
3. Otestujte ověření zadáním jednoho z existujících názvů (například mcvinny) do **daného pole název** . Pokud je všechno nastavené správně, měli byste obdržet zprávu upozorňující uživatele, že se už používá značka přehrávače.

## <a name="next-steps"></a>Další kroky

[Umožňuje upravit úpravy profilu a registraci uživatelů, aby bylo možné získat další informace od uživatelů.](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Návod: integrace REST APIch výměn deklarací identity v Azure AD B2C cestě uživatele jako kroku orchestrace](active-directory-b2c-rest-api-step-custom.md)

[Referenční dokumentace: RESTful Technical Profile](restful-technical-profile.md)

Informace o tom, jak zabezpečit rozhraní API, najdete v následujících článcích:

* [Zabezpečení rozhraní API RESTful pomocí základního ověřování (uživatelské jméno a heslo)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [Zabezpečení rozhraní API RESTful pomocí klientských certifikátů](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
