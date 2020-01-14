---
title: Zabezpečení služby RESTful pomocí ověřování HTTP Basic
titleSuffix: Azure AD B2C
description: Zabezpečte své vlastní REST APIé výměny deklarací identity v Azure AD B2C pomocí ověřování HTTP Basic.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 24aa0d3d3f12934c54ac9aaa5ab8ae5c0d710825
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930507"
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>Zabezpečte své služby RESTful pomocí ověřování HTTP Basic.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V [souvisejícím Azure AD B2C článku](active-directory-b2c-custom-rest-api-netfw.md)vytvoříte službu RESTful (webové rozhraní API), která se integruje s cestami uživatelů Azure Active Directory B2C (Azure AD B2C) bez ověřování.

V tomto článku přidáte ke službě RESTful základní ověřování HTTP, aby k rozhraní API měli přístup jenom ověření uživatelé, včetně B2C. Pomocí protokolu HTTP pro základní ověřování nastavíte přihlašovací údaje uživatele (ID aplikace a tajný klíč aplikace) ve vlastních zásadách.

Další informace najdete v tématu [základní ověřování ve webovém rozhraní API ASP.NET](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication).

## <a name="prerequisites"></a>Požadavky

Dokončete kroky v článku [integrace REST API deklarací identity v rámci vaší Azure AD B2C cesty uživatelů](active-directory-b2c-custom-rest-api-netfw.md) .

## <a name="step-1-add-authentication-support"></a>Krok 1: Přidání podpory ověřování

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>Krok 1,1: přidejte nastavení aplikace do souboru Web. config vašeho projektu.

1. Otevřete projekt sady Visual Studio, který jste vytvořili dříve.

2. Do souboru Web. config přidejte následující nastavení aplikace v prvku `appSettings`:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Vytvořte heslo a pak nastavte hodnotu `WebApp:ClientSecret`.

    Pokud chcete generovat složité heslo, spusťte následující kód PowerShellu. Můžete použít libovolnou libovolnou hodnotu.

    ```powershell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Krok 1,2: instalace knihoven OWIN

Pokud chcete začít, přidejte do projektu balíčky NuGet middleware OWIN pomocí konzoly Správce balíčků sady Visual Studio:

```powershell
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>Krok 1,3: přidejte třídu middleware pro ověřování

Do složky *app_start* přidejte třídu `ClientAuthMiddleware.cs`. Postupujte následovně:

1. Klikněte pravým tlačítkem na složku *app_start* , vyberte **Přidat**a pak vyberte **Třída**.

   ![Přidat třídu ClientAuthMiddleware.cs ve složce App_Start](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. Do pole **název** zadejte **ClientAuthMiddleware.cs**.

   ![Vytvoření nové C# třídy v dialogovém okně Přidat novou položku v aplikaci Visual Studio](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. Otevřete soubor *app_start \clientauthmiddleware.cs* a nahraďte obsah souboru následujícím kódem:

    ```csharp

    using Microsoft.Owin;
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using System.Security.Principal;
    using System.Text;
    using System.Threading.Tasks;
    using System.Web;

    namespace Contoso.AADB2C.API
    {
        /// <summary>
        /// Class to create a custom owin middleware to check for client authentication
        /// </summary>
        public class ClientAuthMiddleware
        {
            private static readonly string ClientID = ConfigurationManager.AppSettings["WebApp:ClientId"];
            private static readonly string ClientSecret = ConfigurationManager.AppSettings["WebApp:ClientSecret"];

            /// <summary>
            /// Gets or sets the next owin middleware
            /// </summary>
            private Func<IDictionary<string, object>, Task> Next { get; set; }

            /// <summary>
            /// Initializes a new instance of the <see cref="ClientAuthMiddleware"/> class.
            /// </summary>
            /// <param name="next"></param>
            public ClientAuthMiddleware(Func<IDictionary<string, object>, Task> next)
            {
                this.Next = next;
            }

            /// <summary>
            /// Invoke client authentication middleware during each request.
            /// </summary>
            /// <param name="environment">Owin environment</param>
            /// <returns></returns>
            public Task Invoke(IDictionary<string, object> environment)
            {
                // Get wrapper class for the environment
                var context = new OwinContext(environment);

                // Check whether the authorization header is available. This contains the credentials.
                var authzValue = context.Request.Headers.Get("Authorization");
                if (string.IsNullOrEmpty(authzValue) || !authzValue.StartsWith("Basic ", StringComparison.OrdinalIgnoreCase))
                {
                    // Process next middleware
                    return Next(environment);
                }

                // Get credentials
                var creds = authzValue.Substring("Basic ".Length).Trim();
                string clientId;
                string clientSecret;

                if (RetrieveCreds(creds, out clientId, out clientSecret))
                {
                    // Set transaction authenticated as client
                    context.Request.User = new GenericPrincipal(new GenericIdentity(clientId, "client"), new string[] { "client" });
                }

                return Next(environment);
            }

            /// <summary>
            /// Retrieve credentials from header
            /// </summary>
            /// <param name="credentials">Authorization header</param>
            /// <param name="clientId">Client identifier</param>
            /// <param name="clientSecret">Client secret</param>
            /// <returns>True if valid credentials were presented</returns>
            private bool RetrieveCreds(string credentials, out string clientId, out string clientSecret)
            {
                string pair;
                clientId = clientSecret = string.Empty;

                try
                {
                    pair = Encoding.UTF8.GetString(Convert.FromBase64String(credentials));
                }
                catch (FormatException)
                {
                    return false;
                }
                catch (ArgumentException)
                {
                    return false;
                }

                var ix = pair.IndexOf(':');
                if (ix == -1)
                {
                    return false;
                }

                clientId = pair.Substring(0, ix);
                clientSecret = pair.Substring(ix + 1);

                // Return whether credentials are valid
                return (string.Compare(clientId, ClientAuthMiddleware.ClientID) == 0 &&
                    string.Compare(clientSecret, ClientAuthMiddleware.ClientSecret) == 0);
            }
        }
    }
    ```

### <a name="step-14-add-an-owin-startup-class"></a>Krok 1,4: přidejte třídu pro spuštění OWIN

Do rozhraní API přidejte třídu pro spuštění OWIN s názvem `Startup.cs`. Postupujte následovně:
1. Klikněte pravým tlačítkem na projekt, vyberte **přidat** > **novou položku**a pak vyhledejte **Owin**.

   ![Vytvoření třídy pro spuštění OWIN v dialogovém okně Přidat novou položku v aplikaci Visual Studio](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

2. Otevřete soubor *Startup.cs* a nahraďte obsah souboru následujícím kódem:

    ```csharp
    using Microsoft.Owin;
    using Owin;

    [assembly: OwinStartup(typeof(Contoso.AADB2C.API.Startup))]
    namespace Contoso.AADB2C.API
    {
        public class Startup
        {
            public void Configuration(IAppBuilder app)
            {
                app.Use<ClientAuthMiddleware>();
            }
        }
    }
    ```

### <a name="step-15-protect-the-identity-api-class"></a>Krok 1,5: ochrana třídy API identity

Otevřete Controllers\IdentityController.cs a přidejte značku `[Authorize]` do třídy Controller. Tato značka omezuje přístup k řadiči na uživatele, kteří splňují požadavek na autorizaci.

![Přidat do kontroleru autorizační značku](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Krok 2: publikování do Azure

Pokud chcete projekt publikovat, klikněte v Průzkumník řešení pravým tlačítkem myši na projekt **contoso. AADB2C. API** a pak vyberte **publikovat**.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>Krok 3: Přidání ID aplikace služby RESTful Services a tajného klíče aplikace do Azure AD B2C

Po ochraně služby RESTful pomocí ID klienta (uživatelské jméno) a tajného klíče musíte přihlašovací údaje uložit ve svém tenantovi Azure AD B2C. Vaše vlastní zásady poskytují přihlašovací údaje při volání služeb RESTful.

### <a name="step-31-add-a-restful-services-client-id"></a>Krok 3,1: Přidání ID klienta služby RESTful Services

1. Ve vašem tenantovi Azure AD B2C vyberte **nastavení B2C** > **Architektura prostředí identit**.


2. Vyberte **klíče zásad** pro zobrazení klíčů, které jsou k dispozici ve vašem tenantovi.

3. Vyberte **Přidat**.

4. V případě **možností**vyberte možnost **ručně**.

5. Jako **název**zadejte **B2cRestClientId**.
    *B2C_1A_* předpony je možné přidat automaticky.

6. Do pole **tajný kód** zadejte ID aplikace, které jste definovali dříve.

7. V případě **použití klíče**vyberte možnost **podpis**.

8. Vyberte **Vytvořit**.

9. Potvrďte, že jste vytvořili `B2C_1A_B2cRestClientId` klíč.

### <a name="step-32-add-a-restful-services-client-secret"></a>Krok 3,2: Přidání tajného kódu klienta služby RESTful Services

1. Ve vašem tenantovi Azure AD B2C vyberte **nastavení B2C** > **Architektura prostředí identit**.

2. Vyberte **klíče zásad** , abyste zobrazili klíče dostupné ve vašem tenantovi.

3. Vyberte **Přidat**.

4. V případě **možností**vyberte možnost **ručně**.

5. Jako **název**zadejte **B2cRestClientSecret**.
    *B2C_1A_* předpony je možné přidat automaticky.

6. Do pole **tajný kód** zadejte tajný klíč aplikace, který jste definovali dříve.

7. V případě **použití klíče**vyberte možnost **podpis**.

8. Vyberte **Vytvořit**.

9. Potvrďte, že jste vytvořili `B2C_1A_B2cRestClientSecret` klíč.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>Krok 4: Změna technického profilu tak, aby podporoval základní ověřování v zásadách rozšíření

1. V pracovním adresáři otevřete soubor zásad rozšíření (TrustFrameworkExtensions. XML).

2. Vyhledejte `<TechnicalProfile>` uzel, který obsahuje `Id="REST-API-SignUp"`.

3. Vyhledejte element `<Metadata>`.

4. Změňte *AuthenticationType* na *Basic*následujícím způsobem:

    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. Ihned po ukončení `<Metadata>` elementu přidejte následující fragment kódu XML:

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```

    Po přidání fragmentu by váš technický profil měl vypadat jako v následujícím kódu XML:

    ![Přidat elementy XML základního ověřování do TechnicalProfile](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Krok 5: nahrání zásady do tenanta

1. V [Azure Portal](https://portal.azure.com)na panelu nástrojů portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.

1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.

1. Vyberte **architekturu prostředí identity**.

1. Otevřete **všechny zásady**.

1. Vyberte **Odeslat zásadu**.

1. Zaškrtněte políčko **přepsat zásadu, pokud existuje** .

1. Nahrajte soubor *TrustFrameworkExtensions. XML* a ověřte, zda projde ověřením.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Krok 6: Vyzkoušejte si vlastní zásady pomocí rutiny spustit hned

1. Otevřete **Azure AD B2C nastavení**a pak vyberte **Architektura prostředí identity**.

    >[!NOTE]
    >Spustit teď vyžaduje, aby se v tenantovi předem zaregistrovala aspoň jedna aplikace. Informace o tom, jak zaregistrovat aplikace, najdete v článku Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) nebo v článku věnovaném [registraci aplikace](active-directory-b2c-app-registration.md) .

2. Otevřete **B2C_1A_signup_signin**, vlastní zásady předávající strany (RP), které jste nahráli, a pak vyberte **Spustit nyní**.

3. Otestujte proces zadáním příkazu **test** do **daného pole název** .
    Azure AD B2C zobrazí v horní části okna chybovou zprávu.

    ![Testování zadaného názvu ověření vstupu v rozhraní API identity](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. Do pole **daný název** zadejte název (jiný než "test").
    Azure AD B2C uživatele odhlásí a pak pošle věrnostní číslo do vaší aplikace. Všimněte si čísla v tomto příkladu:

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

## <a name="optional-download-the-complete-policy-files-and-code"></a>Volitelné Stažení úplných souborů a kódu zásad

* Po dokončení návodu [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md) doporučujeme sestavit svůj scénář pomocí vlastních souborů zásad. Pro váš odkaz jsme zadali [ukázkové soubory zásad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).
* Můžete si stáhnout kompletní kód z [ukázkového řešení sady Visual Studio pro referenci](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).

## <a name="next-steps"></a>Další kroky

* [Použití klientských certifikátů k zabezpečení rozhraní RESTful API](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
