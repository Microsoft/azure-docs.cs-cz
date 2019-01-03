---
title: Integrace rozhraní REST API služby výměny deklarací identity na vaší cestě uživatele Azure Active Directory B2C | Dokumentace Microsoftu
description: Integrace rozhraní REST API služby výměny deklarací identity na vaší cestě uživatele Azure AD B2C jako ověření vstupu uživatele.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/30/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b8718e02bc0306db1ac8cd4f5b133ebdb17a4ec3
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557277"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Integrace rozhraní REST API služby výměny deklarací identity na vaší cestě uživatele Azure AD B2C jako ověření vstupu uživatele

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

S architekturu rozhraní identit, která je základem Azure Active Directory B2C (Azure AD B2C), můžete integrovat se službou rozhraní RESTful API v cestě uživatele. V tomto návodu se dozvíte, jak Azure AD B2C komunikuje pomocí rozhraní .NET Framework RESTful služby (webové rozhraní API).

## <a name="introduction"></a>Úvod
S využitím Azure AD B2C, můžete přidat vlastní obchodní logiku do cesty uživatele voláním služby RESTful. Architekturu rozhraní identit odesílá data do služby RESTful v *vstupní deklarace identity* kolekce a přijímá data z RESTful v *výstupních deklarací identity* kolekce. Integrace služby RESTful vám umožní:

* **Ověření vstupu dat uživatele**: Tato akce zabraňuje poškozených datových uložením do služby Azure AD. Pokud hodnota od uživatele není platná, vaše služba RESTful vrátí chybovou zprávu, která informuje uživatele k zadání položku. Ověřte například, že e-mailovou adresu, které zadal uživatel existuje v databázi vašeho zákazníka.
* **Přepsat vstupními deklaracemi identity**: Například pokud uživatel zadá křestní jméno v všechna písmena malá nebo všechna velká písmena, lze formátovat název pouze první písmeno velké.
* **Obohaťte uživatelská data další integraci s podnikovým aplikacím z podnikové**: Vaše služba RESTful může přijímat e-mailovou adresu uživatele, dotazy na databázi zákazníka a vrátí číslo věrnostní uživatele Azure AD B2C. Vrácení deklarací identity, můžou být uložené v účtu uživatele Azure AD, vyhodnocen v příštích *kroků Orchestrace*, nebo zahrnutý v přístupovém tokenu.
* **Spuštění vlastní obchodní logiky**: Můžete odesílat nabízená oznámení, aktualizovat firemní databáze, spusťte proces migrace uživatelů, spravovat oprávnění, audit databáze a provádět jiné akce.

Integrace služby RESTful můžete navrhnout následujícími způsoby:

* **Technický profil ověření**: Volání služby RESTful se stane v rámci ověření technický profil zadaný technického profilu. Technický profil ověření ověřuje uživatelsky zadaných dat před cesty uživatele pokračuje. Technický profil ověření můžete:
   * Odeslání vstupních deklarací identity.
   * Ověření mezi vstupními deklaracemi identity a vyvolat vlastní chybové zprávy.
   * Odešlete deklarace identity back výstup.

* **Deklarace identity exchange**: Tento návrh se podobá technický profil ověření, ale to se stane, že v jednom z kroků Orchestrace. Tato definice je omezená na:
   * Odeslání vstupních deklarací identity.
   * Odešlete deklarace identity back výstup.

## <a name="restful-walkthrough"></a>RESTful návodu
V tomto podrobném návodu vývoj rozhraní .NET Framework webového rozhraní API, která ověřuje vstup uživatele a poskytuje věrnostní číslo uživatele. Vaše aplikace může například udělit přístup k *platinum výhody* závisí na počtu věrnostních programů.

Přehled:
* Vývoj služby RESTful (rozhraní .NET Framework webového rozhraní API).
* Použijte službu RESTful v cestě uživatele.
* Odeslání vstupních deklarací identity a přečtěte si je ve vašem kódu.
* Ověřte uživatelské jméno.
* Odešlete zpět celou věrnostních programů. 
* Přidáte číslo loajalitu na JSON Web Token (JWT).

## <a name="prerequisites"></a>Požadavky
Proveďte kroky v [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md) článku.

## <a name="step-1-create-an-aspnet-web-api"></a>Krok 1: Vytvoření webového rozhraní API ASP.NET

1. V sadě Visual Studio vytvořte projekt tak, že vyberete **souboru** > **nový** > **projektu**.

2. V **nový projekt** okně **Visual C#** > **webové** > **webová aplikace ASP.NET (.NET Framework)**.

3. V **název** zadejte název aplikace (například *Contoso.AADB2C.API*) a pak vyberte **OK**.

    ![Vytvořit nový projekt sady visual studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4. V **nová webová aplikace ASP.NET** okně **webového rozhraní API** nebo **aplikace Azure API** šablony.

    ![Výběr šablony webové rozhraní API](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

5. Ujistěte, že ověřování je nastavena na **bez ověřování**.

6. Vyberte **OK** a vytvořte projekt.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Krok 2: Příprava koncový bod rozhraní REST API

### <a name="step-21-add-data-models"></a>Krok 2.1: Přidat datové modely
Modely představují mezi vstupními deklaracemi identity a deklarací výstupní data ve službě RESTful. Váš kód načítá vstupní data pomocí deserializace modelu vstupních deklarací identity z řetězce JSON na objekt jazyka C# (modelu). ASP.NET web API automaticky deserializuje model deklarací výstupu zpět do formátu JSON a pak zapíše serializovaná data do textu zprávy s odpovědí HTTP. 

Vytvoření modelu, který představuje vstupních deklarací identity s následujícím způsobem:

1. Pokud Průzkumník řešení již není otevřen, vyberte **zobrazení** > **Průzkumníka řešení**. 
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

4. Vytvořit nový model `OutputClaimsModel`a poté přidejte následující vlastnosti pro `OutputClaimsModel` třídy:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

5. Vytvořte jeden další model `B2CResponseContent`, který použijete k vyvolání ověření vstupu chybové zprávy. Přidejte následující vlastnosti pro `B2CResponseContent` třídy, zadejte chybějící odkazy a pak soubor uložte:

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
Ve webovém rozhraní API _řadič_ je objekt, který zpracovává požadavky HTTP. Kontroler vrací výstup deklarací, nebo pokud křestní jméno není platné, vyvolá chybovou zprávu HTTP ke konfliktu.

1. V Průzkumníku řešení klikněte pravým tlačítkem na složku **Kontrolery**, vyberte **Přidat** a pak vyberte **Kontroler**.

    ![Přidat nový kontroler](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2. V **přidat vygenerované uživatelské rozhraní** okně **Kontroleru webového rozhraní API – prázdný**a pak vyberte **přidat**.

    ![Prázdný kontroler – vyberte webového rozhraní API 2](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3. V **přidat kontroler** okna, názvu kontroleru **IdentityController**a pak vyberte **přidat**.

    ![Zadejte název řadiče](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    Základní kostry aplikace vytvoří soubor s názvem *IdentityController.cs* v *řadiče* složky.

4. Pokud *IdentityController.cs* soubor již není otevřen, poklepejte na něj a pak nahraďte kód v souboru následujícím kódem:

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

## <a name="step-3-publish-the-project-to-azure"></a>Krok 3: Publikování projektu do Azure
1. V Průzkumníku řešení klikněte pravým tlačítkem myši **Contoso.AADB2C.API** projektu a pak vyberte **publikovat**.

    ![Publikování do Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2. V **publikovat** okně **Microsoft Azure App Service**a pak vyberte **publikovat**.

    ![Vytvořit novou službu Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    **Vytvořit službu App Service** otevře se okno. V něm můžete vytvořit všechny prostředky Azure potřebné ke spuštění webové aplikace ASP.NET v Azure.

    > [!NOTE]
    >Další informace o tom, jak publikovat, naleznete v tématu [vytvoření webové aplikace ASP.NET v Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. V **název webové aplikace** zadejte jedinečný název aplikace (platné znaky jsou a – z, 0-9 a spojovníky (-). Adresa URL webové aplikace je http://<app_name>.azurewebsites.NET, kde *app_name* je název vaší webové aplikace. Můžete přijmout automaticky vygenerovaný název, který je jedinečný.

    ![Zadat vlastnosti služby App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4. Chcete-li začít vytvářet prostředky Azure, vyberte **vytvořit**.  
    Po vytvoření webové aplikace ASP.NET, Průvodce publikuje ji do Azure a pak aplikaci spustí ve výchozím prohlížeči.

6. Zkopírujte adresu URL webové aplikace.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Krok 4: Přidejte nové `loyaltyNumber` deklaraci identity pro schéma souboru TrustFrameworkExtensions.xml
`loyaltyNumber` Deklarace identity ještě není definovaná v našich schématu. Přidat definici v rámci `<BuildingBlocks>` elementu, které můžete vyhledat na začátku *TrustFrameworkExtensions.xml* souboru.

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

## <a name="step-5-add-a-claims-provider"></a>Krok 5: Přidat zprostředkovatele deklarací identity 
Každý zprostředkovatele deklarací identity, musí mít nejmíň jeden technické profily, které určíte, koncové body a protokoly, které jsou potřeba ke komunikaci s zprostředkovatele deklarací identity. 

Zprostředkovatel deklarací může mít více technické profily z různých důvodů. Například více technické profily mohou být definovány, protože více protokolů podporuje zprostředkovatel deklarací, koncové body mohou mít různé možnosti nebo vydané verze může obsahovat deklarace identity, které mají různé úrovně záruky. Může být přijatelný uvolnit citlivých deklarací identity v cestě jednoho uživatele, ale ne v jiném. 

Následující fragment kódu XML obsahuje uzel poskytovatele deklarací identity dva technické profily:

* **Technický profil Id = "REST API SignUp"**: Definuje vaši službu RESTful. 
   * `Proprietary` je popsána jako protokol pro zprostředkovatele na základě RESTful. 
   * `InputClaims` definuje deklarace, které se odešlou do služby REST z Azure AD B2C. 

   V tomto příkladu obsah deklarace identity `givenName` odešle službě REST jako `firstName`, obsah se deklarace `surname` odešle službě REST jako `lastName`, a `email` odešle je. `OutputClaims` Element definuje deklarace identity, které jsou načteny z služba RESTful zpět do Azure AD B2C.

* **Technický profil Id = "LocalAccountSignUpWithLogonEmail"**: Technický profil ověření přidá do stávající technický profil (definováno v základních zásadách). Technický profil ověření během registrace cesty, vyvolá předchozí technický profil. Pokud služba RESTful vrátí chybu HTTP 409 (konflikt chyba), zobrazí se chybová zpráva pro uživatele. 

Vyhledejte `<ClaimsProviders>` uzel a potom přidejte následující fragment kódu XML v rámci `<ClaimsProviders>` uzlu:

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
        <Item Key="AllowInsecureAuthInProduction">true</Item>
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

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Krok 6: Přidat `loyaltyNumber` deklaraci identity pro předávající strany soubor zásad, takže deklarace identity se odešlou do vaší aplikace
Upravit vaše *SignUpOrSignIn.xml* předávající stranu souboru a změnit TechnicalProfile Id = "PolicyProfile" prvek a přidat následující: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Po přidání nových deklarací identity, předávající strana kódu vypadá takto:

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

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Krok 7: Odeslání zásady do vašeho tenanta

1. V [webu Azure portal](https://portal.azure.com), přepněte [kontextu vašeho tenanta Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a pak otevřete **Azure AD B2C**.

2. Vyberte **architekturu rozhraní identit**.

3. Otevřít **všechny zásady**. 

4. Vyberte **nahrát zásady**.

5. Vyberte **přepsat zásady, pokud existuje** zaškrtávací políčko.

6. Nahrajte soubor TrustFrameworkExtensions.xml a ujistěte se, že projde úspěšně ověřovacím.

7. Opakujte předchozí krok souborem SignUpOrSignIn.xml.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Krok 8: Testování vlastní zásady pomocí možnosti spustit hned
1. Vyberte **nastavení Azure AD B2C**a pak přejděte na **architekturu rozhraní identit**.

    > [!NOTE]
    > **Spustit nyní** vyžaduje aspoň jednu aplikaci do být registrované u klienta. Informace o postupu registrace aplikací, najdete v tématu Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) článku nebo [registrace aplikace](active-directory-b2c-app-registration.md) článku.

2. Otevřít **B2C_1A_signup_signin**, předávající stranu vlastní zásady, které jste nahráli a pak vyberte **spustit nyní**.

    ![V okně B2C_1A_signup_signin](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. Testování procesu tak, že zadáte **testovací** v **křestní jméno** pole.  
    Azure AD B2C zobrazí chybovou zprávu v horní části okna.

    ![Vaše zásada testování](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4.  V **křestní jméno** zadejte název (jiné než "Test").  
    Azure AD B2C přihlásí uživatele a pak loyaltyNumber odesílá do vaší aplikace. Všimněte si čísla ve tento token JWT.

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Volitelné) Stažení kompletní zásady souborů a kódu
* Po dokončení [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md) návodu, doporučujeme vám vytvořit váš scénář s využitím vlastních zásad pro soubory. Pro srovnání si uvádíme [ukázkové soubory zásad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw).
* Můžete stáhnout kompletní kód z [řešení sady Visual Studio ukázkový pro referenci](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).
    
## <a name="next-steps"></a>Další postup
* [Zabezpečení rozhraní RESTful API pomocí základního ověřování (uživatelské jméno a heslo)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [Zabezpečení rozhraní RESTful API pomocí klientských certifikátů](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
