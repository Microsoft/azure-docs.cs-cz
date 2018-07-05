---
title: Zabezpečení služby RESTful pomocí základního ověřování protokolu HTTP v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zabezpečené výměny deklarací identity vaše vlastní rozhraní REST API ve vaší Azure AD B2C s využitím základního ověřování protokolu HTTP.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 02dfec1ff572f846463b27a2ca0d91916fbee5ae
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444901"
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>Zabezpečení služby RESTful pomocí základního ověřování protokolu HTTP

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V [souvisejícím článku Azure AD B2C](active-directory-b2c-custom-rest-api-netfw.md), vytváření rozhraní RESTful služby (webové rozhraní API), která se integruje s Azure Active Directory B2C (Azure AD B2C) uživatel cesty bez ověřování. 

V tomto článku základního ověřování protokolu HTTP přidat do služby RESTful, které jsou jenom ověřené uživatele, včetně B2C, můžete přístup k rozhraní API. Základní ověřování protokolu HTTP nastavte ve vlastních zásadách přihlašovací údaje uživatele (ID aplikace a tajný klíč aplikace). 

Další informace najdete v tématu [základní ověřování v rozhraní ASP.NET web API](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication).

## <a name="prerequisites"></a>Požadavky
Proveďte kroky v [integrace rozhraní REST API deklarací výměny na vaší cestě uživatele Azure AD B2C](active-directory-b2c-custom-rest-api-netfw.md) článku.

## <a name="step-1-add-authentication-support"></a>Krok 1: Přidání podpory ověřování

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>Krok 1.1: Přidání nastavení aplikace do souboru web.config vašeho projektu
1. Otevřete projekt aplikace Visual Studio, který jste vytvořili dříve. 

2. Přidáním následujícího nastavení aplikace v souboru web.config v rámci `appSettings` element:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Vytvořit heslo a potom nastavte `WebApp:ClientSecret` hodnotu.

    Pokud chcete generovat složité heslo, spusťte následující kód Powershellu. Můžete použít libovolnou hodnotu.

    ```PowerShell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Krok 1.2: Instalace knihovny OWIN
Pokud chcete začít, přidáte do projektu balíčky NuGet middleware OWIN pomocí konzole Správce balíčků Visual Studio:

```
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>Krok 1.3: Přidejte třídu middleware ověřování
Přidat `ClientAuthMiddleware.cs` třídy v rámci *App_Start* složky. Postupujte následovně:

1. Klikněte pravým tlačítkem myši *App_Start* složky, vyberte **přidat**a pak vyberte **třídy**.

   ![Přidejte třídu ClientAuthMiddleware.cs ve složce App_Start](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. V **název** zadejte **ClientAuthMiddleware.cs**.

   ![Vytvoření nové třídy C#](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. Otevřít *App_Start\ClientAuthMiddleware.cs* souboru a nahraďte souboru obsahu s následujícím kódem:

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

### <a name="step-14-add-an-owin-startup-class"></a>Krok 1.4: Přidání třídy pro spuštění OWIN
Přidání třídy pro spuštění OWIN s názvem `Startup.cs` rozhraní API. Postupujte následovně:
1. Klikněte pravým tlačítkem na projekt, vyberte **přidat** > **nová položka**a poté vyhledejte **OWIN**.

   ![Přidání třídy pro spuštění OWIN](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

2. Otevřít *Startup.cs* souboru a nahraďte souboru obsahu s následujícím kódem:

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

### <a name="step-15-protect-the-identity-api-class"></a>Krok 1.5: Ochrana Identity rozhraní API třídy
Otevřete Controllers\IdentityController.cs a přidejte `[Authorize]` značky třídy kontroleru. Tato značka omezuje přístup k řadiči pro uživatele, kteří splní požadavek na autorizaci.

![Přidat značku Authorize ke kontroleru](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Krok 2: Publikování aplikace do Azure
Publikování projektu v Průzkumníku řešení, klikněte pravým tlačítkem myši **Contoso.AADB2C.API** projektu a pak vyberte **publikovat**.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>Krok 3: Přidání služby typu REST ID aplikace a tajný kód aplikace do Azure AD B2C
Poté, co vaše služba RESTful ochrání ID klienta (uživatelské jméno) a tajný klíč, musíte uložit přihlašovací údaje ve vašem tenantovi Azure AD B2C. Vlastní zásady poskytuje přihlašovací údaje při vyvolá služby RESTful. 

### <a name="step-31-add-a-restful-services-client-id"></a>Krok 3.1: Přidejte ID klienta služby typu REST
1. Ve vašem tenantovi Azure AD B2C vyberte **nastavení B2C** > **architekturu rozhraní identit**.


2. Vyberte **klíče zásad** zobrazíte klíče, které jsou k dispozici ve vašem tenantovi.

3. Vyberte **Přidat**.

4. Pro **možnosti**vyberte **ruční**.

5. Pro **název**, typ **B2cRestClientId**.  
    Předpona, která *B2C_1A_* může být automaticky přidán.

6. V **tajný kód** zadejte ID aplikace, kterou jste definovali dříve.

7. Pro **použití klíče**vyberte **tajný klíč**.

8. Vyberte **Vytvořit**.

9. Potvrďte, že jste vytvořili `B2C_1A_B2cRestClientId` klíč.

### <a name="step-32-add-a-restful-services-client-secret"></a>Krok 3.2: Přidáte tajný klíč klienta služby typu REST
1. Ve vašem tenantovi Azure AD B2C vyberte **nastavení B2C** > **architekturu rozhraní identit**.

2. Vyberte **klíče zásad** zobrazíte klíče, které jsou k dispozici ve vašem tenantovi.

3. Vyberte **Přidat**.

4. Pro **možnosti**vyberte **ruční**.

5. Pro **název**, typ **B2cRestClientSecret**.  
    Předpona, která *B2C_1A_* může být automaticky přidán.

6. V **tajný kód** zadejte tajný kód aplikace, kterou jste definovali dříve.

7. Pro **použití klíče**vyberte **tajný klíč**.

8. Vyberte **Vytvořit**.

9. Potvrďte, že jste vytvořili `B2C_1A_B2cRestClientSecret` klíč.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>Krok 4: Změna technický profil pro podporu základní ověřování ve svojí zásadě rozšíření
1. Ve svém pracovním adresáři otevřete soubor rozšíření zásad (TrustFrameworkExtensions.xml).

2. Hledat `<TechnicalProfile>` uzel, který zahrnuje `Id="REST-API-SignUp"`.

3. Vyhledejte `<Metadata>` elementu.

4. Změnit *AuthenticationType* k *základní*, následujícím způsobem:
    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. Okamžitě po uplynutí `<Metadata>` prvku, přidejte následující fragment kódu XML: 

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```
    Po přidání fragmentu kódu, technický profil by měl vypadat jako v následujícím kódu XML:
    
    ![Přidání prvků XML základní ověřování](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Krok 5: Nahrajte zásady pro vašeho tenanta

1. V [webu Azure portal](https://portal.azure.com), přepněte [kontextu vašeho tenanta Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a pak otevřete **Azure AD B2C**.

2. Vyberte **architekturu rozhraní identit**.

3. Otevřít **všechny zásady**.

4. Vyberte **nahrát zásady**.

5. Vyberte **přepsat zásady, pokud existuje** zaškrtávací políčko.

6. Nahrát *TrustFrameworkExtensions.xml* souboru a poté se ujistěte, že projde úspěšně ověřovacím.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Krok 6: Testování spustit pomocí vlastních zásad
1. Otevřít **nastavení Azure AD B2C**a pak vyberte **architekturu rozhraní identit**.

    >[!NOTE]
    >Spustit nyní vyžaduje aspoň jednu aplikaci do být registrované u klienta. Informace o postupu registrace aplikací, najdete v tématu Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) článku nebo [registrace aplikace](active-directory-b2c-app-registration.md) článku.

2. Otevřít **B2C_1A_signup_signin**, předávající stranu vlastní zásady, které jste nahráli a pak vyberte **spustit nyní**.

3. Testování procesu tak, že zadáte **testovací** v **křestní jméno** pole.  
    Azure AD B2C zobrazí chybovou zprávu v horní části okna.

    ![Otestování rozhraní API vaší identity](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. V **křestní jméno** zadejte název (jiné než "Test").  
    Azure AD B2C zaregistruje uživatele a potom číslo věrnostní odesílá do vaší aplikace. Poznamenejte si číslo v tomto příkladu:

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

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Volitelné) Stažení kompletní zásady souborů a kódu
* Po dokončení [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md) návodu, doporučujeme vám vytvořit váš scénář s využitím vlastních zásad pro soubory. Pro srovnání si uvádíme [ukázkové soubory zásad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).
* Můžete stáhnout kompletní kód z [řešení sady Visual Studio ukázkový pro referenci](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).

## <a name="next-steps"></a>Další postup
* [Použití klientských certifikátů k zabezpečení rozhraní RESTful API](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

