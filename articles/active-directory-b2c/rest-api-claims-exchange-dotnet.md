---
title: Integrace REST APIch výměn deklarací identity v cestě uživatele
titleSuffix: Azure AD B2C
description: Integrujte REST API výměn deklarací identity v cestě uživatele Azure AD B2C jako ověření vstupu uživatele.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 780d575bd7f035673510d5b1e62cff4dfd6ede16
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848756"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Integrace REST APIch výměn deklarací identity v cestě uživatele Azure AD B2C jako ověření vstupu uživatele

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

S architekturou prostředí identity, která je Azure Active Directory B2C (Azure AD B2C), můžete integrovat s rozhraním API RESTful v cestě uživatele. V tomto návodu se dozvíte, jak Azure AD B2C komunikuje s .NET Framework RESTful Services (webové rozhraní API).

## <a name="introduction"></a>Představení

Pomocí Azure AD B2C můžete přidat vlastní obchodní logiku k cestě uživatele voláním vlastní služby RESTful. Rozhraní identity Experience Framework odesílá data službě RESTful ve *vstupní kolekci deklarací* a přijímá data zpět z RESTful ve *výstupní kolekci deklarací* . S integrací služby RESTful můžete:

* **Ověřit data vstupu uživatele**: Tato akce zabrání v ukládání poškozených dat do služby Azure AD. Pokud hodnota od uživatele není platná, služba RESTful vrátí chybovou zprávu, která uživateli vydá pokyn k zadání položky. Můžete třeba ověřit, že e-mailová adresa zadaná uživatelem existuje v databázi vašeho zákazníka.
* **Přepsat vstupní deklarace**: Pokud například uživatel zadá jméno do všech malých písmen nebo všechna velká písmena, můžete název naformátovat pouze na první písmeno.
* **Rozšíření uživatelských dat o další integraci podnikových obchodních aplikací**: vaše služba RESTful může obdržet e-mailovou adresu uživatele, zadat dotaz na databázi zákazníka a vrátit věrnostní číslo uživatele do Azure AD B2C. Vrácené deklarace identity můžou být uložené v účtu Azure AD uživatele, vyhodnocené v dalších *krocích orchestrace*nebo zahrnuté do přístupového tokenu.
* **Spuštění vlastní obchodní logiky**: můžete odesílat nabízená oznámení, aktualizovat podnikové databáze, spouštět proces migrace uživatelů, spravovat oprávnění, auditovat databáze a provádět další akce.

Integraci s RESTful službami můžete navrhovat následujícími způsoby:

* **Technický profil ověření**: volání služby RESTful se stává v rámci ověřovacího technického profilu zadaného technického profilu. Technický profil ověření ověřuje uživatelem poskytnutá data před tím, než se přesune uživatel do cesty. S technickým profilem ověření můžete:
  * Odeslat vstupní deklarace identity.
  * Ověří vstupní deklarace identity a vyvolá vlastní chybové zprávy.
  * Odeslat zpět výstupní deklarace identity.

* **Výměna deklarací identity**: Tento návrh je podobný technickému profilu ověření, ale v kroku orchestrace se děje. Tato definice je omezená na:
  * Odeslat vstupní deklarace identity.
  * Odeslat zpět výstupní deklarace identity.

## <a name="restful-walkthrough"></a>Názorný postup RESTful

V tomto návodu vyvíjíte .NET Framework webové rozhraní API, které ověřuje vstup uživatele a poskytuje věrnostní číslo uživatele. Vaše aplikace může například udělit přístup k *výhodám Platinum* na základě věrnostního čísla.

Přehled:

* Vývoj služby RESTful (.NET Framework Web API)
* Použití služby RESTful v cestě uživatele
* Posílání vstupních deklarací identity a jejich čtení v kódu
* Ověřit křestní jméno uživatele
* Poslat číslo loajality zpět
* Přidat věrnostní číslo do JSON Web Token (JWT)

## <a name="prerequisites"></a>Požadavky

Proveďte kroky popsané v článku [Začínáme s vlastními zásadami](custom-policy-get-started.md) .

## <a name="step-1-create-an-aspnet-web-api"></a>Krok 1: Vytvoření webového rozhraní API v ASP.NET

1. V aplikaci Visual Studio vytvořte projekt tak, že vyberete **soubor** > **Nový** > **projekt**.
1. V okně **Nový projekt** vyberte **Visual C#**  > **Web** **Application > ASP.NET webová aplikace (.NET Framework)** .
1. Do pole **název** zadejte název aplikace (například *contoso. AADB2C. API*) a pak vyberte **OK**.

    ![Vytvoření nového projektu sady Visual Studio v aplikaci Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-create-project.png)

1. V okně **Nová webová aplikace ASP.NET** vyberte **Web API** nebo šablonu **aplikace API Azure** .

    ![Výběr šablony webového rozhraní API v aplikaci Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-select-web-api.png)

1. Ujistěte se, že je ověřování nastaveno na **bez ověřování**.
1. Vyberte **OK** a vytvořte projekt.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Krok 2: Příprava koncového bodu REST API

### <a name="step-21-add-data-models"></a>Krok 2,1: Přidání datových modelů

Modely reprezentují vstupní deklarace identity a výstupní data deklarací identity ve službě RESTful. Váš kód přečte vstupní data tím, že deserializace vstupního modelu deklarací identity z řetězce JSON C# do objektu (modelu). Webové rozhraní API ASP.NET automaticky deserializace výstupní model deklarací identity zpět do formátu JSON a potom zapisuje Serializovaná data do těla zprávy s odpovědí HTTP.

Pomocí následujícího postupu vytvořte model reprezentující vstupní deklarace identity:

1. Pokud Průzkumník řešení ještě není otevřený, vyberte **zobrazit** > **Průzkumník řešení**.
1. V Průzkumníku řešení klikněte pravým tlačítkem na složku **Modely**, vyberte **Přidat** a pak vyberte **Třída**.

    ![Položka nabídky Přidat třídu vybraná v aplikaci Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-model.png)

1. Pojmenujte třídu `InputClaimsModel`a potom do `InputClaimsModel` třídy přidejte následující vlastnosti:

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

1. Vytvořte nový model `OutputClaimsModel`a přidejte do třídy `OutputClaimsModel` následující vlastnosti:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

1. Vytvořte jeden další model, `B2CResponseContent`, který použijete k vyvolání chybových zpráv ověřování vstupu. Do třídy `B2CResponseContent` přidejte následující vlastnosti, zadejte chybějící odkazy a pak soubor uložte:

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

### <a name="step-22-add-a-controller"></a>Krok 2,2: Přidání kontroleru

Ve webovém rozhraní API je _kontroler_ objekt, který zpracovává požadavky HTTP. Kontroler vrátí deklarace výstupů, nebo pokud jméno není platné, vyvolá chybovou zprávu protokolu HTTP v konfliktu.

1. V Průzkumníku řešení klikněte pravým tlačítkem na složku **Kontrolery**, vyberte **Přidat** a pak vyberte **Kontroler**.

    ![Přidání nového kontroleru do sady Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-controller-1.png)

1. V okně **Přidat generování uživatelského** rozhraní vyberte možnost **KONTROLER webového rozhraní API – prázdné**a pak vyberte **Přidat**.

    ![Výběr kontroleru webového rozhraní API 2 – prázdné v aplikaci Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-controller-2.png)

1. V okně **Přidat kontrolér** pojmenujte kontrolér **IdentityController**a pak vyberte **Přidat**.

    ![Zadání názvu kontroleru v aplikaci Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    Generování uživatelského rozhraní vytvoří ve složce *Controllers* soubor s názvem *IdentityController.cs* .

1. Pokud soubor *IdentityController.cs* již není otevřen, poklikejte na něj a potom nahraďte kód v souboru následujícím kódem:

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

## <a name="step-3-publish-the-project-to-azure"></a>Krok 3: publikování projektu do Azure

1. V Průzkumník řešení klikněte pravým tlačítkem myši na projekt **contoso. AADB2C. API** a pak vyberte **publikovat**.

    ![Publikování Microsoft Azure App Service pomocí sady Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

1. V okně **publikovat** vyberte možnost **Microsoft Azure App Service**a pak vyberte **publikovat**.

    ![Vytvoření nové App Service Microsoft Azure pomocí sady Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    Otevře se okno **vytvořit App Service** . V tomto případě vytvoříte všechny potřebné prostředky Azure ke spuštění webové aplikace v ASP.NET v Azure.

    > [!TIP]
    > Další informace o tom, jak publikovat, najdete v tématu [Vytvoření webové aplikace v ASP.NET v Azure](../app-service/app-service-web-get-started-dotnet-framework.md).

1. Do pole **název webové aplikace** zadejte jedinečný název aplikace (platné znaky jsou a-z, 0-9 a spojovníky (-). Adresa URL webové aplikace je http://< app_name >. azurewebsites. NET, kde *APP_NAME* je název vaší webové aplikace. Můžete přijmout automaticky vygenerovaný název, který je jedinečný.

    ![Konfigurace vlastností App Service](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

1. Pokud chcete začít vytvářet prostředky Azure, vyberte **vytvořit**.
    Po vytvoření webové aplikace v ASP.NET ji průvodce publikuje do Azure a pak aplikaci spustí ve výchozím prohlížeči.

1. Zkopírujte adresu URL webové aplikace.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Krok 4: přidejte novou `loyaltyNumber`ovou deklaraci do schématu souboru TrustFrameworkExtensions. XML

Deklarace `loyaltyNumber` ještě není definovaná v našem schématu. Přidejte definici v rámci prvku `<BuildingBlocks>`, který můžete najít na začátku souboru *TrustFrameworkExtensions. XML* .

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

## <a name="step-5-add-a-claims-provider"></a>Krok 5: Přidání zprostředkovatele deklarací identity

Každý zprostředkovatel deklarací identity musí mít jeden nebo více technických profilů, které určují koncové body a protokoly potřebné ke komunikaci se zprostředkovatelem deklarací.

Zprostředkovatel deklarací může mít více technických profilů z různých důvodů. Například může být definováno více technických profilů, protože zprostředkovatel deklarací podporuje více protokolů, koncové body mohou mít různé možnosti, nebo verze mohou obsahovat deklarace identity, které mají různé úrovně záruky. Může být přijatelné vydávat citlivé deklarace identity na jednu cestu uživatele, ale ne v jiném.

Následující fragment kódu XML obsahuje uzel zprostředkovatele deklarací se dvěma technickými profily:

* **TechnicalProfile ID = "REST-API-SignUp"** : definuje vaši službu RESTful.
  * `Proprietary` je popsána jako protokol pro poskytovatele založeného na RESTful.
  * `InputClaims` definuje deklarace identity, které se budou odesílat z Azure AD B2C do služby REST.

    V tomto příkladu obsah deklarace identity `givenName` odesílá službě REST jako `firstName`, obsah deklarace identity `surname` posílá službě REST jako `lastName`a `email` posílá tak, jak je. `OutputClaims` prvek definuje deklarace identity, které jsou načteny ze služby RESTful zpět do Azure AD B2C.

* **TechnicalProfile ID = "LocalAccountSignUpWithLogonEmail"** : přidá technický profil ověření pro stávající technický profil (definovaný v základních zásadách). Během cesty pro registraci vyvolal technický profil ověření předchozí technický profil. Pokud služba RESTful vrátí chybu protokolu HTTP 409 (chyba konfliktu), zobrazí se uživateli chybová zpráva.

Vyhledejte uzel `<ClaimsProviders>` a přidejte následující fragment kódu XML do uzlu `<ClaimsProviders>`:

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
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
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

Komentáře uvedené výše `AuthenticationType` a `AllowInsecureAuthInProduction` určují změny, které byste měli dělat při přesunu do produkčního prostředí. Informace o tom, jak zabezpečit rozhraní API pro RESTful pro produkční prostředí, najdete v tématu [zabezpečení rozhraní API RESTful se základními ověřováním](secure-rest-api-dotnet-basic-auth.md) a [zabezpečením RESTful API pomocí ověřování certifikátů](secure-rest-api-dotnet-certificate-auth.md).

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Krok 6: přidejte do souboru zásad předávající strany deklaraci `loyaltyNumber`, aby se deklarace identity poslala do vaší aplikace.

Upravte soubor *SignUpOrSignIn. XML* předávající strany (RP) a upravte element TechnicalProfile ID = "PolicyProfile" tak, aby přidal následující: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Po přidání nové deklarace bude kód předávající strany vypadat takto:

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

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Krok 7: nahrání zásady do tenanta

1. V [Azure Portal](https://portal.azure.com)na panelu nástrojů portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.

1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.

1. Vyberte **architekturu prostředí identity**.

1. Otevřete **všechny zásady**.

1. Vyberte **Odeslat zásadu**.

1. Zaškrtněte políčko **přepsat zásadu, pokud existuje** .

1. Nahrajte soubor TrustFrameworkExtensions. XML a ujistěte se, že projde ověřením.

1. Opakujte předchozí krok se souborem SignUpOrSignIn. XML.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Krok 8: Vyzkoušejte si vlastní zásady pomocí rutiny spustit hned

1. Vyberte **nastavení Azure AD B2C**a pak navštivte **rozhraní identity Experience Framework**.

    > [!NOTE]
    > **Spustit teď** vyžaduje, aby se v tenantovi předem zaregistrovala aspoň jedna aplikace. Informace o tom, jak zaregistrovat aplikace, najdete v článku Azure AD B2C [Začínáme](tutorial-create-tenant.md) nebo v článku věnovaném [registraci aplikace](tutorial-register-applications.md) .

2. Otevřete **B2C_1A_signup_signin**, vlastní zásady předávající strany (RP), které jste nahráli, a pak vyberte **Spustit nyní**.

    ![Stránka B2C_1A_signup_signin vlastní zásady v Azure Portal](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-run.png)

3. Otestujte proces zadáním příkazu **test** do **daného pole název** .
    Azure AD B2C zobrazí v horní části okna chybovou zprávu.

    ![Testování zadaného názvu ověření vstupu na přihlašovací stránce pro přihlášení](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-test.png)

4. Do pole **daný název** zadejte název (jiný než "test").
    Azure AD B2C uživatele odhlásí a pak pošle loyaltyNumber vaší aplikaci. Poznamenejte si číslo v této tokenu JWT.

```JSON
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

## <a name="optional-download-the-complete-policy-files-and-code"></a>Volitelné Stažení úplných souborů a kódu zásad

* Po dokončení návodu [Začínáme s vlastními zásadami](custom-policy-get-started.md) doporučujeme sestavit svůj scénář pomocí vlastních souborů zásad. Pro váš odkaz jsme zadali [ukázkové soubory zásad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw).

* Můžete si stáhnout kompletní kód z [ukázkového řešení sady Visual Studio pro referenci](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).

## <a name="next-steps"></a>Další kroky

Vaším dalším úkolem je zabezpečit rozhraní API RESTful pomocí základního nebo klientského ověřování certifikátu. Informace o tom, jak zabezpečit rozhraní API, najdete v následujících článcích:

* [Zabezpečení rozhraní API RESTful pomocí základního ověřování (uživatelské jméno a heslo)](secure-rest-api-dotnet-basic-auth.md)
* [Zabezpečení rozhraní API RESTful pomocí klientských certifikátů](secure-rest-api-dotnet-certificate-auth.md)

Informace o všech prvcích dostupných v RESTful Technical profilech najdete v článku [referenční informace: RESTful Technical Profile](restful-technical-profile.md).
