---
title: Kurz – přihlášení uživatelů a volání chráněného rozhraní API z aplikace Blazor WebAssembly
titleSuffix: Microsoft identity platform
description: V tomto kurzu se přihlaste uživatele a zavolejte chráněné rozhraní API s využitím platformy Microsoft Identity Platform v aplikaci Blazor WebAssembly (WASM).
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 5489feeeec64c7b3d4b5fc28eddfe8b780308796
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94979874"
---
# <a name="tutorial-sign-in-users-and-call-a-protected-api-from-a-blazor-webassembly-app"></a>Kurz: přihlášení uživatelů a volání chráněného rozhraní API z aplikace Blazor WebAssembly

V tomto kurzu vytvoříte Blazor aplikaci WebAssembly, která přihlašuje uživatele a získává data z Microsoft Graph pomocí platformy Microsoft identity a registraci vaší aplikace v Azure Active Directory (Azure AD).

K dispozici je také [kurz pro server Blazor](tutorial-blazor-server.md). 

V tomto kurzu:

> [!div class="checklist"]
>
> * Vytvoření nové aplikace WebAssembly v Blazor, která je nakonfigurovaná tak, aby používala Azure Active Directory (Azure AD) pro [ověřování a autorizaci](authentication-vs-authorization.md) pomocí platformy Microsoft Identity Platform
> * Načtěte data z chráněného webového rozhraní API, v tomto případě [Microsoft Graph](https://docs.microsoft.com/graph/overview)

## <a name="prerequisites"></a>Požadavky

* [Sada .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* Tenant služby Azure AD, kde můžete zaregistrovat aplikaci. Pokud nemáte přístup k tenantovi služby Azure AD, můžete si ho zaregistrovat pomocí [vývojářského programu Microsoft 365](https://developer.microsoft.com/microsoft-365/dev-program) nebo vytvořením [bezplatného účtu Azure](https://azure.microsoft.com/free).

## <a name="register-the-app-in-the-azure-portal"></a>Registrace aplikace v Azure Portal

Každá aplikace, která používá Azure Active Directory (Azure AD) pro ověřování, musí být zaregistrovaná ve službě Azure AD. Postupujte podle pokynů v části [Registrace aplikace](quickstart-register-app.md) s těmito specifikacemi:

- U **podporovaných typů účtů** vyberte **účty jenom v tomto organizačním adresáři**.
- Rozevírací seznam **identifikátor URI přesměrování** nastavte na **Web** a zadejte `https://localhost:5001/authentication/login-callback` . Výchozí port pro aplikaci běžící na Kestrel je 5001. Pokud je aplikace k dispozici na jiném portu, zadejte číslo portu místo `5001` .

Po registraci zaškrtněte v části **ověřování**  >  **implicitní udělení oprávnění** zaškrtnutí políček pro **přístupové tokeny** a **tokeny ID** a pak vyberte tlačítko **Uložit** .

## <a name="create-the-app-using-the-net-core-cli"></a>Vytvoření aplikace pomocí .NET Core CLI

K vytvoření aplikace potřebujete nejnovější šablony Blazor. Můžete je nainstalovat pro .NET Core CLI pomocí následujícího příkazu:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.1
```

Pak spusťte následující příkaz k vytvoření aplikace. Zástupné symboly v příkazu nahraďte správnými informacemi ze stránky přehled vaší aplikace a spusťte příkaz v příkazovém prostředí. Umístění výstupu zadané s `-o|--output` možností vytvoří složku projektu, pokud neexistuje a bude součástí názvu aplikace.

```dotnetcli
dotnet new blazorwasm2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| Zástupný symbol   | Název Azure Portal       | Příklad                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorWASMSample`                         |
| `{CLIENT ID}` | ID aplikace (klienta) | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | ID adresáře (tenanta)   | `e86c78e2-0000-0000-0000-918e0565a45e` |

## <a name="test-the-app"></a>Otestování aplikace

Nyní můžete sestavit a spustit aplikaci. Při spuštění této šablony aplikace je nutné zadat rozhraní, které se má spustit pomocí--frameworku. V tomto kurzu se používá .NET Standard 2,1, ale šablona podporuje i jiné architektury.

```dotnetcli
dotnet run --framework netstandard2.1
```

V prohlížeči přejděte na `https://localhost:5001` adresu a přihlaste se pomocí uživatelského účtu Azure AD, abyste viděli spuštěnou aplikaci a mohli uživatele přihlašovat pomocí platformy Microsoft identity.

Součásti této šablony, které povolují přihlášení pomocí služby Azure AD s využitím platformy Microsoft identity, jsou vysvětleny v [ASP.NET doc tohoto tématu](/aspnet/core/blazor/security/webassembly/standalone-with-azure-active-directory#authentication-package).

## <a name="retrieving-data-from-microsoft-graph"></a>Načítání dat z Microsoft Graph

[Microsoft Graph](/graph/overview) nabízí řadu rozhraní API, která poskytují přístup k datům Microsoft 365 uživatelům ve vašem tenantovi. Pomocí platformy Microsoft Identity jako poskytovatele identity vaší aplikace máte jednodušší přístup k těmto informacím, protože Microsoft Graph přímo podporuje tokeny vydané platformou Microsoft identity. V této části můžete přidat kód, který zobrazí e-maily přihlášeného uživatele na stránce načíst data aplikace.

Než začnete, odhlaste se od své aplikace, protože budete provádět změny požadovaných oprávnění a váš aktuální token nebude fungovat. Pokud jste to ještě neudělali, spusťte aplikaci znovu a před aktualizací kódu níže vyberte možnost **Odhlásit** se.

Nyní aktualizujete registraci a kód vaší aplikace, aby vyčetla e-maily uživatele a zobrazila zprávy v aplikaci.

Nejdřív přidejte `Mail.Read` k registraci aplikace oprávnění rozhraní API, aby se služba Azure AD dozvěděla, že aplikace bude vyžadovat přístup k e-mailu svých uživatelů.

1. V Azure Portal vyberte svou aplikaci v **Registrace aplikací**.
1. V části **Spravovat** vyberte **oprávnění rozhraní API**.
1. Vyberte **Přidat**  >  **Microsoft Graph** oprávnění.
1. Vyberte **delegovaná oprávnění**, vyhledejte a vyberte oprávnění **Pošta. číst** .
1. Vyberte **Přidat oprávnění**.

Dále do souboru *. csproj* projektu ve skupině **položek** netstandard 2.1 přidejte následující. To vám umožní vytvořit si vlastní HttpClient v dalším kroku.

```xml
<PackageReference Include="Microsoft.Extensions.Http" Version="3.1.7" />
```

Pak upravte kód tak, jak je uvedeno v následujících několika krocích. Tyto změny budou přidávat [přístupové tokeny](access-tokens.md) do odchozích požadavků odeslaných do rozhraní Microsoft Graph API. Tento model je podrobněji popsán v tématu [ASP.NET Core Blazor a další scénáře zabezpečení pro WebAssembly](/aspnet/core/blazor/security/webassembly/additional-scenarios).

Nejprve vytvořte nový soubor s názvem *GraphAuthorizationMessageHandler.cs* s následujícím kódem. Tato obslužná rutina bude uživateli přidat přístupový token pro `User.Read` `Mail.Read` obory a pro odchozí požadavky na rozhraní Microsoft Graph API.

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Mail.Read" });
    }
}
```

Potom nahraďte obsah `Main` metody v *program.cs* následujícím kódem. Tento kód využívá nové `GraphAPIAuthorizationMessageHandler` a přidané `User.Read` a `Mail.Read` jako výchozí obory, které bude aplikace požadovat při prvním přihlášení uživatele.

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
builder.RootComponents.Add<App>("app");

builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("User.Read");
    options.ProviderOptions.DefaultAccessTokenScopes.Add("Mail.Read");
});

await builder.Build().RunAsync();
```

Nakonec nahraďte obsah stránky *FetchData. Razor* následujícím kódem. Tento kód načte e-mailová data uživatelů z rozhraní Microsoft Graph API a zobrazí je jako seznam. V nástroji `OnInitializedAsync` `HttpClient` se vytvoří nový, který používá správný přístupový token, a použije se k vytvoření požadavku na rozhraní Microsoft Graph API.

```c#
@page "/fetchdata"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject IHttpClientFactory HttpClientFactory

<p>This component demonstrates fetching data from a service.</p>

@if (messages == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h1>Hello @userDisplayName !!!!</h1>
    <table class="table">
        <thead>
            <tr>
                <th>Subject</th>
                <th>Sender</th>
                <th>Received Time</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var mail in messages)
            {
                <tr>
                    <td>@mail.Subject</td>
                    <td>@mail.Sender</td>
                    <td>@mail.ReceivedTime</td>
                </tr>
            }
        </tbody>
    </table>
}

@code {

    private string userDisplayName;
    private List<MailMessage> messages = new List<MailMessage>();

    private HttpClient _httpClient;

    protected override async Task OnInitializedAsync()
    {
        _httpClient = HttpClientFactory.CreateClient("GraphAPI");
        try {
            var dataRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me");

            if (dataRequest.IsSuccessStatusCode)
            {
                var userData = System.Text.Json.JsonDocument.Parse(await dataRequest.Content.ReadAsStreamAsync());
                userDisplayName = userData.RootElement.GetProperty("displayName").GetString();
            }

            var mailRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me/messages?$select=subject,receivedDateTime,sender&$top=10");

            if (mailRequest.IsSuccessStatusCode)
            {
                var mailData = System.Text.Json.JsonDocument.Parse(await mailRequest.Content.ReadAsStreamAsync());
                var messagesArray = mailData.RootElement.GetProperty("value").EnumerateArray();

                foreach (var m in messagesArray)
                {
                    var message = new MailMessage();
                    message.Subject = m.GetProperty("subject").GetString();
                    message.Sender = m.GetProperty("sender").GetProperty("emailAddress").GetProperty("address").GetString();
                    message.ReceivedTime = m.GetProperty("receivedDateTime").GetDateTime();
                    messages.Add(message);
                }
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            // Tokens are not valid - redirect the user to log in again
            ex.Redirect();
        }
    }

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}
```

Nyní znovu spusťte aplikaci. Všimnete si, že budete vyzváni, abyste aplikaci přihlásili, aby mohla číst vaši poštu. To se očekává, když aplikace požaduje `Mail.Read` rozsah.

Po udělení souhlasu přejděte na stránku načíst data a přečtěte si pár e-mailů.

:::image type="content" source="./media/tutorial-blazor-webassembly/final-app.png" alt-text="Snímek obrazovky finální aplikace Má nadpis, který říká Hello Nicholas a zobrazuje seznam e-mailů, které patří k Nicholas.":::

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Osvědčené postupy a doporučení pro Microsoft Identity Platform](./identity-platform-integration-checklist.md)
