---
title: Integrace rozhraní REST API deklarace identity výměn v vám dobře slouží uživatele Azure Active Directory B2C | Microsoft Docs
description: Integrace rozhraní REST API deklarace identity výměn v vám dobře slouží Azure AD B2C uživatele jako ověřování vstupu uživatele.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 09/30/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e17647016da0e877bd8f21357a4bd38121820f22
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "34709355"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Integrace rozhraní REST API deklarace identity výměn v vám dobře slouží Azure AD B2C uživatele jako ověřování vstupu uživatele

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

S použitím rozhraní Framework Identity, které základem Azure Active Directory B2C (Azure AD B2C), kterou můžete integrovat se rozhraní RESTful API cesty uživatele. V tomto návodu se dozvíte, jak Azure AD B2C komunikuje s rozhraní .NET Framework RESTful služeb (webové rozhraní API).

## <a name="introduction"></a>Úvod
Pomocí Azure AD B2C můžete přidat vlastní obchodní logiku cesty uživatele při volání služby RESTful. Rozhraní Framework prostředí Identity odešle data do služby RESTful *vstupní deklarace identity* kolekce a přijímá data zpět z dosáhl standardu RESTful v *výstup deklarace identity* kolekce. Díky integraci služba RESTful můžete:

* **Ověření uživatele vstupní data**: Tato akce zabrání poškozených datových uložením do služby Azure AD. Pokud hodnota od uživatele není platná, služby RESTful vrátí chybovou zprávu, která se dá pokyn, aby uživatel zadal položku. Ověřte například, že e-mailovou adresu poskytl uživatel existuje v databázi vašeho zákazníka.
* **Přepsat vstupních deklarací identity**: například, pokud uživatel zadá název první v celé malými písmeny a všechna písmena velká písmena, můžete ho naformátovat název s velkými písmeny pouze prvních písmen.
* **Rozšíření dat uživatele integrací další s podnikovým aplikacím obchodní**: vaše RESTful služby může přijímat e-mailovou adresu uživatele, dotazování databáze zákazníka a vrátí číslo věrného uživatele do Azure AD B2C. Návratový deklarace identity můžou být uložené v účtu uživatele Azure AD, vyhodnotí v dalším *kroků Orchestrace*, nebo zahrnuté v tokenu přístupu.
* **Spusťte vlastní obchodní logiku**: můžete odesílat nabízená oznámení, aktualizovat podnikové databáze, spusťte proces migrace uživatele, správě oprávnění, audit databáze a provádět další akce.

Integrace s služeb RESTful můžete navrhnout následujícími způsoby:

* **Profil ověření technické**: volání službu RESTful se stane v rámci profilu ověření technické zadaný technické profilu. Technické profil ověření ověří zadaný uživatelem data před cesty uživatel přesune dál. Technické profil ověření můžete:
   * Odešlete vstupní deklarace identity.
   * Ověření mezi vstupními deklaracemi identity a výjimku vlastní chybové zprávy.
   * Odešlete deklarace identity back výstup.

* **Deklarace identity exchange**: Tento návrh se podobá technické profil ověření, ale Odehrává se v rámci na krok orchestration. Tato definice je omezený na:
   * Odešlete vstupní deklarace identity.
   * Odešlete deklarace identity back výstup.

## <a name="restful-walkthrough"></a>RESTful návod
V tomto návodu vytvořte rozhraní .NET Framework webového rozhraní API, která ověří vstup uživatele a poskytuje věrného číslo uživatele. Aplikace může například udělit přístup k *platinové výhody* podle počtu věrného.

Přehled:
* Vytvořte službu RESTful (rozhraní .NET Framework webového rozhraní API).
* Použijte službu RESTful v cesty uživatele.
* Odesílat vstupní deklarace identity a přečtěte si je v kódu.
* Ověřte uživatelské jméno.
* Odešlete zpět věrného číslo. 
* Přidejte číslo věrného k JSON Web Token (JWT).

## <a name="prerequisites"></a>Požadavky
Proveďte kroky v [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md) článku.

## <a name="step-1-create-an-aspnet-web-api"></a>Krok 1: Vytvoření rozhraní ASP.NET web API

1. V sadě Visual Studio vytvořte projekt tak, že vyberete **soubor** > **nový** > **projektu**.

2. V **nový projekt** vyberte **Visual C#** > **webové** > **webové aplikace ASP.NET (rozhraní .NET Framework)**.

3. V **název** zadejte název pro aplikaci (například *Contoso.AADB2C.API*) a potom vyberte **OK**.

    ![Vytvoření nového projektu sady visual studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4. V **nové webové aplikace ASP.NET** vyberte **webového rozhraní API** nebo **aplikace Azure API** šablony.

    ![Vyberte šablonu, webové rozhraní API](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

5. Ujistěte se, že, ověření je nastavena na **bez ověřování**.

6. Vyberte **OK** a vytvořte tak projekt.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Krok 2: Příprava koncový bod REST API

### <a name="step-21-add-data-models"></a>Krok 2.1: Přidání datové modely
Modely představují mezi vstupními deklaracemi identity a deklarací výstupní data ve službě RESTful. Váš kód čte vstupní data pomocí deserializace model vstupních deklarací identity z řetězce formátu JSON na objekt C# (modelu). Rozhraní ASP.NET web API automaticky deserializuje výstupní deklarace identity model zpět do formátu JSON a pak zapíše serializovaná data do textu zprávy s odpovědí HTTP. 

Vytvoření modelu, který představuje vstupních deklarací identity následujícím způsobem:

1. Pokud Průzkumníku řešení ještě není otevřený, vyberte **zobrazení** > **Průzkumníku řešení**. 
2. V Průzkumníku řešení klikněte pravým tlačítkem na složku **Modely**, vyberte **Přidat** a pak vyberte **Třída**.

    ![Přidání modelu](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

3. Název třídy `InputClaimsModel`a poté přidejte následující vlastnosti pro `InputClaimsModel` třídy:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

4. Vytvořit nový model, `OutputClaimsModel`a poté přidejte následující vlastnosti pro `OutputClaimsModel` třídy:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

5. Vytvořte jeden další model `B2CResponseContent`, který použijete k throw ověření vstupu chybové zprávy. Přidejte následující vlastnosti pro `B2CResponseContent` třídy zadejte odkazy na chybějící a pak soubor uložte:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }    
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>Krok 2.2: Přidání kontroleru
Ve webové rozhraní API _řadič_ je objekt, který zpracovává požadavky HTTP. Řadičem vrátí výstup deklarací, nebo pokud křestní jméno není platné, vyvolá chybová zpráva o konfliktu HTTP.

1. V Průzkumníku řešení klikněte pravým tlačítkem na složku **Kontrolery**, vyberte **Přidat** a pak vyberte **Kontroler**.

    ![Přidat nový řadič](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2. V **přidat vygenerované uživatelské rozhraní** vyberte **kontroler Web API – prázdný**a potom vyberte **přidat**.

    ![Prázdný kontroler – vyberte webového rozhraní API 2](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3. V **přidat kontroler** období, název kontroleru **IdentityController**a potom vyberte **přidat**.

    ![Zadejte název řadiče](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    Generování uživatelského rozhraní vytvoří soubor s názvem *IdentityController.cs* v *řadiče* složky.

4. Pokud *IdentityController.cs* soubor již není otevřený, dvakrát na ni klikněte a pak nahraďte kód v souboru s následujícím kódem:

    ```csharp
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If no data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check the input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into an InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run an input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create an output claims object and set the loyalty number with a random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-the-project-to-azure"></a>Krok 3: Publikování projektu v Azure
1. V Průzkumníku řešení klikněte pravým tlačítkem myši **Contoso.AADB2C.API** projektu a potom vyberte **publikovat**.

    ![Publikování do služby Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2. V **publikovat** vyberte **Microsoft Azure App Service**a potom vyberte **publikovat**.

    ![Vytvořit nový Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    **Vytvořit službu App Service** otevře se okno. V něm vytvořit všechny potřebné prostředky Azure ke spouštění webové aplikace ASP.NET v Azure.

    > [!NOTE]
    >Další informace o tom, jak publikovat najdete v tématu [vytvoření webové aplikace ASP.NET v Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. V **název webové aplikace** zadejte jedinečným názvem aplikace (jsou platné znaky-z, 0 – 9 a pomlčky (-). Adresa URL webové aplikace je http://<app_name>.azurewebsites.NET, kde *app_name* je název vaší webové aplikace. Můžete přijmout automaticky vygenerovaný název, který je jedinečný.

    ![Zadejte vlastnosti služby App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4. Chcete-li začít vytvářet prostředky Azure, vyberte **vytvořit**.  
    Po vytvoření webové aplikace ASP.NET, Průvodce publikuje do Azure a pak spustí aplikaci ve výchozím prohlížeči.

6. Zkopírujte adresu URL webové aplikace.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Krok 4: Přidejte nové `loyaltyNumber` deklarace schématu TrustFrameworkExtensions.xml souboru
`loyaltyNumber` Deklarace identity ještě není definován v našem schématu. Přidejte definici v rámci `<BuildingBlocks>` element, který můžete najít na začátku *TrustFrameworkExtensions.xml* souboru.

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-add-a-claims-provider"></a>Krok 5: Přidejte zprostředkovatele deklarací identity 
Každý poskytovatel deklarací identity musí mít jeden nebo více technické profily, které určují koncových bodů a protokoly nutné pro komunikaci se zprostředkovateli deklarací. 

Poskytovatele deklarací identity může mít několik technické profilů z různých důvodů. Víc technických profilů například může definovat, protože zprostředkovatele deklarací identity podporuje více protokolů, koncových bodů může mít různou možnosti nebo verze můžete obsahují deklarace identity, které mají různé úrovně záruky. Může to být přijatelné k uvolnění citlivé deklarací cesty jednoho uživatele, ale ne v jiném. 

Následující fragment kódu XML obsahuje uzel zprostředkovatele deklarací se dva technické profily:

* **TechnicalProfile Id = "REST API SignUp"**: definuje služby RESTful. 
   * `Proprietary` je označen jako protokol pro zprostředkovatele na základě dosáhl standardu RESTful. 
   * `InputClaims` definuje deklarace identity, které budou odesílané z Azure AD B2C ke službě REST. 

   V tomto příkladu obsah deklarace `givenName` se odešle do služby REST jako `firstName`, obsah deklarace `surname` se odešle do služby REST jako `lastName`, a `email` odešle jako je. `OutputClaims` Element definuje deklarace identity, které jsou načteny z služba RESTful zpět do Azure AD B2C.

* **TechnicalProfile Id = "LocalAccountSignUpWithLogonEmail"**: technické profil ověření přidá do stávající profil technické (definovanou v základní zásady). Během registrace cesty vyvolá technické profil ověření předchozí technické profilu. Pokud službu RESTful vrátí chybu HTTP 409 (Chyba konflikt), zobrazí se chybová zpráva pro uživatele. 

Vyhledejte `<ClaimsProviders>` uzel a poté přidejte následující fragment kódu XML v části `<ClaimsProviders>` uzlu:

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
    
    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
        <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
        </InputClaims>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

<!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Krok 6: Přidejte `loyaltyNumber` deklarace identity do souboru zásad předávající strany, deklarace identity je odeslána do vaší aplikace
Upravit vaše *SignUpOrSignIn.xml* předávající stranu soubor a upravte TechnicalProfile Id = "PolicyProfile" elementu, který chcete přidat následující: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Po přidání nových deklarací identity, předávající strany kódu vypadá takto:

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Krok 7: Nahrajte zásady klienta

1. V [portál Azure](https://portal.azure.com), přepnout [kontextu klienta služby Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a pak otevřete **Azure AD B2C**.

2. Vyberte **Identity rozhraní Framework**.

3. Otevřete **všechny zásady**. 

4. Vyberte **nahrát zásady**.

5. Vyberte **přepsat zásady, pokud existuje** zaškrtávací políčko.

6. Nahrajte soubor TrustFrameworkExtensions.xml a ujistěte se, že předává ověření.

7. Opakujte předchozí krok souborem SignUpOrSignIn.xml.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Krok 8: Testování spustit pomocí vlastních zásad
1. Vyberte **nastavení Azure AD B2C**a pak přejděte na **Identity rozhraní Framework**.

    > [!NOTE]
    > **Spustit nyní** vyžaduje alespoň jedné aplikace do být preregistered u klienta. Další postup registrace aplikace najdete v tématu Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) článek nebo [registrace aplikace](active-directory-b2c-app-registration.md) článku.

2. Otevřete **B2C_1A_signup_signin**, předávající stranu vlastních zásad, které jste nahráli a potom vyberte **spustit nyní**.

    ![Okno B2C_1A_signup_signin](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. Otestujte proces zadáním **Test** v **křestní jméno** pole.  
    Azure AD B2C zobrazí chybovou zprávu v horní části okna.

    ![Vaše zásada testování](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4.  V **křestní jméno** zadejte název (jiné než "Test").  
    Azure AD B2C zaregistruje uživatele a pak odešle loyaltyNumber do vaší aplikace. Poznamenejte si číslo v této JWT.

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1507122303,
  "auth_time": 1507122303,
  "loyaltyNumber": "290",
  "given_name": "Emily",
  "emails": ["B2cdemo@outlook.com"]
}
```

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Volitelné) Stáhnout soubory dokončení zásad a kódu
* Po dokončení [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md) návod, doporučujeme vám vytvořit váš scénář pomocí vlastních zásad pro soubory. Pro vaši informaci uvádíme [ukázkové soubory zásad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw).
* Si můžete stáhnout kompletní kód z [řešení sady Visual Studio ukázkový pro referenci](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).
    
## <a name="next-steps"></a>Další postup
* [Zabezpečení rozhraní RESTful API pomocí základní ověřování (uživatelské jméno a heslo)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [Zabezpečení rozhraní RESTful API pomocí klientských certifikátů](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
