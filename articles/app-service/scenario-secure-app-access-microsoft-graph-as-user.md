---
title: Kurz – webová aplikace přistupuje Microsoft Graph jako uživatel | Azure
description: V tomto kurzu se dozvíte, jak získat přístup k datům v Microsoft Graph pro přihlášeného uživatele.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 01/28/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 3413c1a3f27b48c60ae730ad230c653928702faa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99063379"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-user"></a>Kurz: přístup k Microsoft Graph z zabezpečené aplikace jako uživatel

Naučte se, jak získat přístup k Microsoft Graph z webové aplikace běžící na Azure App Service.

:::image type="content" alt-text="Diagram, který zobrazuje přístup k Microsoft Graph." source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Chcete přidat přístup k Microsoft Graph z webové aplikace a provést nějakou akci jako přihlášený uživatel. V této části se dozvíte, jak udělit delegovaná oprávnění k webové aplikaci a získat informace o profilu přihlášeného uživatele z Azure Active Directory (Azure AD).

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Udělíte delegovaná oprávnění webové aplikaci.
> * Volání Microsoft Graph z webové aplikace pro přihlášeného uživatele.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Webová aplikace spuštěná v Azure App Service s [povoleným App Servicem ověřováním/autorizačním modulem](scenario-secure-app-authentication-app-service.md).

## <a name="grant-front-end-access-to-call-microsoft-graph"></a>Udělit front-end přístup k volání Microsoft Graph

Teď, když jste povolili ověřování a autorizaci ve webové aplikaci, je webová aplikace zaregistrovaná na platformě Microsoft identity a je zajištěna aplikací Azure AD. V tomto kroku udělíte webové aplikaci oprávnění k přístupu Microsoft Graph pro uživatele. (Technicky) aplikaci Azure AD pro webovou aplikaci udělíte oprávnění pro přístup k Microsoft Graph aplikaci Azure AD pro uživatele.)

V nabídce [Azure Portal](https://portal.azure.com) vyberte **Azure Active Directory** nebo vyhledejte a vyberte **Azure Active Directory** na libovolné stránce.

Vyberte **Registrace aplikací**  >  **vlastněné aplikace**  >  **Zobrazit všechny aplikace v tomto adresáři**. Vyberte název vaší webové aplikace a pak vyberte **oprávnění rozhraní API**.

Vyberte **Přidat oprávnění** a pak vyberte rozhraní Microsoft api a Microsoft Graph.

Vyberte **delegovaná oprávnění** a pak v seznamu vyberte **User. Read** . Vyberte **Přidat oprávnění**.

## <a name="configure-app-service-to-return-a-usable-access-token"></a>Konfigurace služby App Service tak, aby vracela použitelný přístupový token

Tato webová aplikace má teď požadovaná oprávnění pro přístup k Microsoft Graph jako přihlášený uživatel. V tomto kroku nakonfigurujete App Service ověřování a autorizaci, abyste vám poskytli použitelný přístupový token pro přístup k Microsoft Graph. Pro účely tohoto kroku potřebujete ID klienta nebo aplikace služby pro příjem dat (Microsoft Graph). ID aplikace pro Microsoft Graph je *00000003-0000-0000-C000-000000000000*.

> [!IMPORTANT]
> Pokud nenastavíte App Service pro vrácení použitelného přístupového tokenu, zobrazí se ```CompactToken parsing failed with error code: 80049217``` Chyba při volání rozhraní api Microsoft Graph ve vašem kódu.

Přejděte na [Azure Resource Explorer](https://resources.azure.com/) a pomocí stromu prostředků Najděte svou webovou aplikaci. Adresa URL prostředku by měla být podobná `https://resources.azure.com/subscriptions/subscription-id/resourceGroups/SecureWebApp/providers/Microsoft.Web/sites/SecureWebApp20200915115914` .

Azure Resource Explorer se teď otevře s vaší webovou aplikací vybranou ve stromu prostředků. V horní části stránky vyberte **čtení/zápis** , aby se povolily úpravy vašich prostředků Azure.

V levém prohlížeči přejděte k části **Konfigurace**  >  **authsettings**.

V zobrazení **authsettings** vyberte **Upravit**. Nastavte ```additionalLoginParams``` na následující řetězec JSON pomocí ID klienta, které jste zkopírovali.

```json
"additionalLoginParams": ["response_type=code id_token","resource=00000003-0000-0000-c000-000000000000"],
```

Uložte nastavení výběrem možnosti **Put**. Toto nastavení může trvat několik minut, než se projeví. Vaše webová aplikace je teď nakonfigurovaná pro přístup k Microsoft Graph pomocí správného přístupového tokenu. Pokud to neuděláte, Microsoft Graph vrátí chybu oznamující, že formát kompaktního tokenu není správný.

## <a name="call-microsoft-graph-net"></a>Volat Microsoft Graph (.NET)

Vaše webová aplikace teď má požadovaná oprávnění a také přidá ID klienta Microsoft Graph do parametrů přihlášení. Pomocí [knihovny Microsoft. identity. Web](https://github.com/AzureAD/microsoft-identity-web/)App Web App získá přístupový token pro ověřování pomocí Microsoft Graph. Ve verzi 1.2.0 a novější se knihovna Microsoft. identity. Web integruje s a může běžet společně s App Service ověřování/autorizací. Microsoft. identity. Web zjistí, že je webová aplikace hostována v App Service a získá přístupový token z modulu ověřování a ověření v App Service. Přístupový token se pak předává spolu s ověřenými žádostmi pomocí rozhraní Microsoft Graph API.

Pokud chcete tento kód zobrazit jako součást ukázkové aplikace, podívejte se na [ukázku v GitHubu](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/2-WebApp-graphapi-on-behalf).

> [!NOTE]
> Knihovna Microsoft. identity. Web není ve vaší webové aplikaci vyžadována pro základní ověřování/autorizaci nebo pro ověřování požadavků pomocí Microsoft Graph. Je možné [bezpečně volat rozhraní API pro příjem dat](tutorial-auth-aad.md#call-api-securely-from-server-code) pouze s povoleným ověřováním App Service/autorizačním modulem.
> 
> Ověřování a autorizace App Service ale jsou navržené pro další scénáře základního ověřování. V případě složitějších scénářů (například zpracování vlastních deklarací identity) potřebujete knihovnu Microsoft. identity. Web nebo [knihovnu Microsoft Authentication Library](../active-directory/develop/msal-overview.md). Na začátku se nachází trochu další instalace a konfigurace, ale knihovna Microsoft. identity. Web Library může běžet společně s modulem App Service ověřování/autorizace. Později, pokud vaše webová aplikace potřebuje pracovat se složitějšími scénáři, můžete zakázat modul App Service Authentication/Authorization a Microsoft. identity. Web už je součástí vaší aplikace.

### <a name="install-client-library-packages"></a>Nainstalovat balíčky klientské knihovny

Pomocí rozhraní příkazového řádku .NET Core nebo konzoly Správce balíčků v aplikaci Visual Studio nainstalujte do svého projektu balíčky NuGet [Microsoft. identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) a [Microsoft. identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) .

# <a name="command-line"></a>[Příkazový řádek](#tab/command-line)

Otevřete příkazový řádek a přejděte do adresáře, který obsahuje soubor projektu.

Spusťte příkazy install.

```dotnetcli
dotnet add package Microsoft.Identity.Web.MicrosoftGraph

dotnet add package Microsoft.Identity.Web
```

# <a name="package-manager"></a>[Správce balíčků](#tab/package-manager)

Otevřete projekt nebo řešení v aplikaci Visual Studio a otevřete konzolu pomocí   >  příkazu **Správce balíčků NuGet správce** balíčků nástroje  >   .

Spusťte příkazy install.
```powershell
Install-Package Microsoft.Identity.Web.MicrosoftGraph

Install-Package Microsoft.Identity.Web
```

---

### <a name="startupcs"></a>Startup.cs

V souboru *Startup. cs* ```AddMicrosoftIdentityWebApp``` přidá metoda do vaší webové aplikace Microsoft. identity. Web. ```AddMicrosoftGraph```Metoda přidá podporu Microsoft Graph.

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Identity.Web;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

// Some code omitted for brevity.
public class Startup
{
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))
                    .EnableTokenAcquisitionToCallDownstreamApi()
                        .AddMicrosoftGraph(Configuration.GetSection("Graph"))
                        .AddInMemoryTokenCaches();

        services.AddRazorPages();
    }
}

```

### <a name="appsettingsjson"></a>appsettings.json

*AzureAd* určuje konfiguraci pro knihovnu Microsoft. identity. Web. V [Azure Portal](https://portal.azure.com)v nabídce portálu vyberte **Azure Active Directory** a pak vyberte **Registrace aplikací**. Vyberte registraci aplikace vytvořenou, když jste povolili App Service ověřovací/autorizační modul. (Registrace aplikace by měla mít stejný název jako vaše webová aplikace.) ID tenanta a ID klienta najdete na stránce Přehled registrace aplikace. Název domény najdete na stránce Přehled Azure AD pro vašeho tenanta.

*Graph* určuje Microsoft Graph koncový bod a počáteční obory, které aplikace potřebuje.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "fourthcoffeetest.onmicrosoft.com",
    "TenantId": "[tenant-id]",
    "ClientId": "[client-id]",
    // To call an API
    "ClientSecret": "[secret-from-portal]", // Not required by this scenario
    "CallbackPath": "/signin-oidc"
  },

  "Graph": {
    "BaseUrl": "https://graph.microsoft.com/v1.0",
    "Scopes": "user.read"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="indexcshtmlcs"></a>Index.cshtml.cs

Následující příklad ukazuje, jak volat Microsoft Graph jako přihlášeného uživatele a získat informace o uživateli. ```GraphServiceClient```Objekt je vložen do kontroleru a pro vás nakonfiguroval ověřování pomocí knihovny Microsoft. identity. Web.

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Graph;
using System.IO;
using Microsoft.Identity.Web;
using Microsoft.Extensions.Logging;

// Some code omitted for brevity.

[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public class IndexModel : PageModel
{
    private readonly ILogger<IndexModel> _logger;
    private readonly GraphServiceClient _graphServiceClient;

    public IndexModel(ILogger<IndexModel> logger, GraphServiceClient graphServiceClient)
    {
        _logger = logger;
        _graphServiceClient = graphServiceClient;
    }

    public async Task OnGetAsync()
    {
        try
        {
            var user = await _graphServiceClient.Me.Request().GetAsync();
            ViewData["Me"] = user;
            ViewData["name"] = user.DisplayName;

            using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
            {
                byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                ViewData["photo"] = Convert.ToBase64String(photoByte);
            }
        }
        catch (Exception ex)
        {
            ViewData["photo"] = null;
        }
    }
}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste s tímto kurzem hotovi a už nepotřebujete webovou aplikaci ani související prostředky, [vyčistěte prostředky, které jste vytvořili](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
>
> * Udělíte delegovaná oprávnění webové aplikaci.
> * Volání Microsoft Graph z webové aplikace pro přihlášeného uživatele.

> [!div class="nextstepaction"]
> [Přístup ke službě App Service Microsoft Graph jako aplikace](scenario-secure-app-access-microsoft-graph-as-app.md)