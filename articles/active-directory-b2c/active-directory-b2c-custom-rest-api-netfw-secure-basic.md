---
title: 'Azure Active Directory B2C: Zabezpečte vaše RESTful služby pomocí základního ověřování protokolu HTTP'
description: Zabezpečit vaše vlastní výměnu REST API deklarace identity ve vašem Azure AD B2C pomocí základního ověřování protokolu HTTP
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 09/25/2017
ms.author: davidmu
ms.openlocfilehash: 749157d16c1c394b173545dddb8751d58fdcfd56
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>Zabezpečení vašich služeb RESTful pomocí základního ověřování protokolu HTTP

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V [souvisejícím článku Azure AD B2C](active-directory-b2c-custom-rest-api-netfw.md), vytvoření RESTful služby (webového rozhraní API), který se integruje se službou Azure Active Directory B2C cesty (Azure AD B2C) uživatele bez ověřování. 

V tomto článku základní ověřování protokolu HTTP přidat k RESTful službě tak, aby jen ověření uživatelé, včetně B2C, můžete přístup k rozhraní API. Základní ověřování protokolu HTTP nastavte přihlašovací údaje uživatele (ID aplikace a tajný klíč aplikace) ve vlastních zásadách. 

Další informace najdete v tématu [základní ověřování v rozhraní ASP.NET web API](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication).

## <a name="prerequisites"></a>Požadavky
Proveďte kroky v [integrovat REST API deklarací výměn v vám dobře slouží uživatele Azure AD B2C](active-directory-b2c-custom-rest-api-netfw.md) článku.

## <a name="step-1-add-authentication-support"></a>Krok 1: Přidání Podpora ověřování

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>Krok 1.1: Přidání nastavení aplikace do souboru web.config vašeho projektu
1. Otevřete projekt Visual Studio, který jste vytvořili dříve. 

2. Přidejte následující nastavení aplikace v souboru web.config v části `appSettings` element:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Vytvořte heslo a potom nastavte `WebApp:ClientSecret` hodnotu.

    Pokud chcete vygenerovat složité heslo, spusťte následující kód prostředí PowerShell. Můžete použít všechny libovolná hodnota.

    ```PowerShell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Krok 1.2: Instalace OWIN knihovny
Pokud chcete začít, přidejte do projektu balíčky NuGet middleware OWIN pomocí konzoly Správce balíčků Visual Studio:

```
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>Krok 1.3: Přidejte třídu middleware ověřování
Přidat `ClientAuthMiddleware.cs` třídy v části *App_Start* složky. Postupujte následovně:

1. Klikněte pravým tlačítkem myši *App_Start* složky, vyberte **přidat**a potom vyberte **třída**.

   ![Přidání třídy ClientAuthMiddleware.cs ve složce App_Start](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. V **název** zadejte **ClientAuthMiddleware.cs**.

   ![Vytvořte novou třídu C#](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. Otevřete *App_Start\ClientAuthMiddleware.cs* souboru a nahradit soubor obsahu s následujícím kódem:

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

2. Otevřete *Startup.cs* souboru a nahradit soubor obsahu s následujícím kódem:

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

### <a name="step-15-protect-the-identity-api-class"></a>Krok 1.5: Ochrana rozhraní API Identity – třída
Otevřete Controllers\IdentityController.cs a přidejte `[Authorize]` značka do třídy kontroleru. Tato značka omezuje přístup k řadiči pro uživatele, kteří splní požadavek na autorizaci.

![Přidání značky autorizovat do kontroleru](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Krok 2: Publikování aplikace do Azure
K publikování projektu v Průzkumníku řešení, klikněte pravým tlačítkem myši **Contoso.AADB2C.API** projektu a potom vyberte **publikovat**.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>Krok 3: Přidání služeb RESTful ID a aplikace tajný klíč aplikace do Azure AD B2C
Jakmile služby RESTful je chráněný pomocí ID klienta (username) a tajný klíč, je třeba uložit přihlašovací údaje v klientovi služby Azure AD B2C. Vaše vlastní zásada poskytuje pověření, pokud vyvolá vaší služby RESTful. 

### <a name="step-31-add-a-restful-services-client-id"></a>Krok 3.1: ID klienta služby RESTful přidáte
1. Ve vašem klientu Azure AD B2C, vyberte **nastavení B2C** > **Identity rozhraní Framework**.


2. Vyberte **zásad klíče** zobrazíte klíčů, které jsou k dispozici ve vašem klientovi.

3. Vyberte **Přidat**.

4. Pro **možnosti**, vyberte **ruční**.

5. Pro **název**, typ **B2cRestClientId**.  
    Předpona *B2C_1A_* může automaticky přidat.

6. V **tajný klíč** zadejte ID aplikace, kterou jste definovali dříve.

7. Pro **použití klíče**, vyberte **tajný klíč**.

8. Vyberte **Vytvořit**.

9. Potvrďte, že jste vytvořili `B2C_1A_B2cRestClientId` klíč.

### <a name="step-32-add-a-restful-services-client-secret"></a>Krok 3.2: Přidejte sdílený tajný klíč klienta služby RESTful
1. Ve vašem klientu Azure AD B2C, vyberte **nastavení B2C** > **Identity rozhraní Framework**.

2. Vyberte **zásad klíče** zobrazíte klíče, které jsou k dispozici ve vašem klientovi.

3. Vyberte **Přidat**.

4. Pro **možnosti**, vyberte **ruční**.

5. Pro **název**, typ **B2cRestClientSecret**.  
    Předpona *B2C_1A_* může automaticky přidat.

6. V **tajný klíč** zadejte tajný klíč aplikace, kterou jste definovali dříve.

7. Pro **použití klíče**, vyberte **tajný klíč**.

8. Vyberte **Vytvořit**.

9. Potvrďte, že jste vytvořili `B2C_1A_B2cRestClientSecret` klíč.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>Krok 4: Změňte profilem technické podpory základní ověřování v zásadě rozšíření
1. V pracovním adresáři otevřete soubor rozšíření zásad (TrustFrameworkExtensions.xml).

2. Vyhledejte `<TechnicalProfile>` uzlu, který zahrnuje `Id="REST-API-SignUp"`.

3. Vyhledejte `<Metadata>` elementu.

4. Změna *AuthenticationType* k *základní*, a to takto:
    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. Ihned po zavření `<Metadata>` elementu, přidejte následující fragment kódu XML: 

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```
    Po přidání fragmentu váš technické profil by měl vypadat podobně jako následující kód XML:
    
    ![Přidat elementů XML základní ověřování](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Krok 5: Nahrajte zásady klienta

1. V [portál Azure](https://portal.azure.com), přepnout [kontextu klienta služby Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a pak otevřete **Azure AD B2C**.

2. Vyberte **Identity rozhraní Framework**.

3. Otevřete **všechny zásady**.

4. Vyberte **nahrát zásady**.

5. Vyberte **přepsat zásady, pokud existuje** zaškrtávací políčko.

6. Nahrát *TrustFrameworkExtensions.xml* souboru a pak se ujistěte, že předává ověření.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Krok 6: Testování spustit pomocí vlastních zásad
1. Otevřete **nastavení Azure AD B2C**a potom vyberte **Identity rozhraní Framework**.

    >[!NOTE]
    >Spustit nyní vyžaduje alespoň jedné aplikace do být preregistered u klienta. Další postup registrace aplikace najdete v tématu Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) článek nebo [registrace aplikace](active-directory-b2c-app-registration.md) článku.

2. Otevřete **B2C_1A_signup_signin**, předávající stranu vlastních zásad, které jste nahráli a potom vyberte **spustit nyní**.

3. Otestujte proces zadáním **Test** v **křestní jméno** pole.  
    Azure AD B2C zobrazí chybovou zprávu v horní části okna.

    ![Testování vaší identity rozhraní API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. V **křestní jméno** zadejte název (jiné než "Test").  
    Azure AD B2C zaregistruje uživatele a pak odešle číslo věrného do vaší aplikace. Poznamenejte si číslo v tomto příkladu:

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
* Po dokončení [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md) návod, doporučujeme vám vytvořit váš scénář pomocí vlastních zásad pro soubory. Pro vaši informaci uvádíme [ukázkové soubory zásad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).
* Si můžete stáhnout kompletní kód z [řešení sady Visual Studio ukázkový pro referenci](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).

## <a name="next-steps"></a>Další postup
* [Používat klientské certifikáty k zabezpečení rozhraní RESTful API.](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

