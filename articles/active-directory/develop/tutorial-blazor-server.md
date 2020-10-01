---
title: Kurz – vytvoření aplikace Blazor serveru, která používá Microsoft Identity Platform pro ověřování | Azure
titleSuffix: Microsoft identity platform
description: V tomto kurzu nastavíte ověřování pomocí platformy Microsoft identity v aplikaci Blazor serveru.
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: 42aa51fdd3b0da5a0d438ba46b39bada159aeba6
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91611467"
---
# <a name="tutorial-create-a-blazor-server-app-that-uses-the-microsoft-identity-platform-for-authentication"></a>Kurz: Vytvoření aplikace serveru Blazor, která pro ověřování používá platformu Microsoft Identity Platform

Blazor Server poskytuje podporu pro hostování komponent Razor na serveru v aplikaci ASP.NET Core. V tomto kurzu se naučíte implementovat ověřování a načíst data z Microsoft Graph v serverové aplikaci Blazor s využitím platformy Microsoft identity.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření nové aplikace serveru Blazor, která je nakonfigurovaná tak, aby používala Azure Active Directory (Azure AD) pro ověřování
> * Ověřování a autorizace pomocí Microsoft. identity. Web
> * Načtěte data z chráněného webového rozhraní API Microsoft Graph

## <a name="prerequisites"></a>Předpoklady

- [Sada .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
- Tenant služby Azure AD, kde můžete zaregistrovat aplikaci. Pokud nemáte přístup k tenantovi služby Azure AD, můžete si ho zaregistrovat pomocí [vývojářského programu Microsoft 365](https://developer.microsoft.com/microsoft-365/dev-program) nebo vytvořením [bezplatného účtu Azure](https://azure.microsoft.com/free).

## <a name="register-the-app-in-the-azure-portal"></a>Registrace aplikace v Azure Portal

Každá aplikace, která používá Azure Active Directory (Azure AD) pro ověřování, musí být zaregistrovaná ve službě Azure AD. Postupujte podle pokynů v části [Registrace aplikace](quickstart-register-app.md) s těmito dodatky:

- U **podporovaných typů účtů**vyberte **účty jenom v tomto organizačním adresáři**.
- Rozevírací seznam **identifikátor URI přesměrování** nastavte na **Web** a zadejte `https://localhost:5001/signin-oidc` . Výchozí port pro aplikaci běžící na Kestrel je 5001. Pokud je aplikace k dispozici na jiném portu, zadejte číslo portu místo `5001` .

V části **ověřování**  >  **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**a pak vyberte tlačítko **Uložit** .

Vzhledem k tomu, že aplikace volá chráněné rozhraní API (v tomto případě Microsoft Graph), potřebuje klientský klíč klienta, aby ověřil jeho identitu, když požádá o přístupový token pro volání rozhraní API.

1. V rámci stejné registrace aplikace vyberte v části **Spravovat**možnost **certifikáty & tajných**kódů.
2. Vytvořte **nový tajný klíč klienta** , který nikdy nevyprší.
3. Poznamenejte si **hodnotu** tajného klíče, protože ji budete používat v dalším kroku. Po opuštění tohoto podokna k němu nemůžete získat přístup znovu. V případě potřeby ho ale můžete znovu vytvořit.

## <a name="create-the-app-using-the-net-cli"></a>Vytvoření aplikace pomocí rozhraní .NET CLI

Spuštěním následujícího příkazu Stáhněte šablony pro Microsoft. identity. Web, které využijeme v tomto kurzu.

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::0.4.0-preview
```

Pak spusťte následující příkaz k vytvoření aplikace. Zástupné symboly v příkazu nahraďte správnými informacemi ze stránky přehled vaší aplikace a spusťte příkaz v příkazovém prostředí. Umístění výstupu zadané s `-o|--output` možností vytvoří složku projektu, pokud neexistuje a bude součástí názvu aplikace.

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| Zástupný symbol   | Název Azure Portal       | Příklad                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | ID aplikace (klienta) | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | ID adresáře (tenanta)   | `e86c78e2-0000-0000-0000-918e0565a45e` |

Teď přejděte do svého editoru do nové aplikace Blazor a do souboru *appsettings.js* přidejte tajný klíč klienta. tím se nahradí text "tajný klíč-aplikace-registrace".

```json
"ClientSecret": "xkAlNiG70000000_UI~d.OS4Dl.-Cy-1m3",
```

## <a name="test-the-app"></a>Otestování aplikace

Nyní můžete sestavit a spustit aplikaci. Při spuštění této šablony aplikace je nutné zadat rozhraní, které se má spustit pomocí--frameworku. V tomto kurzu se používá sada .NET Core 3,1 SDK.

```dotnetcli
dotnet run --framework netcoreapp3.1
```

V prohlížeči přejděte na `https://localhost:5001` adresu a přihlaste se pomocí uživatelského účtu Azure AD a podívejte se na spuštěnou aplikaci.

## <a name="retrieving-data-from-microsoft-graph"></a>Načítání dat z Microsoft Graph

[Microsoft Graph](/graph/overview) nabízí řadu rozhraní API, která poskytují přístup k datům Microsoft 365 vašich uživatelů. Pomocí platformy Microsoft Identity jako poskytovatele identity vaší aplikace máte jednodušší přístup k těmto informacím, protože Microsoft Graph přímo podporuje tokeny vydané platformou Microsoft identity. V této části můžete přidat kód, který zobrazí e-maily přihlášeného uživatele na stránce načíst data aplikace.

Než začnete, odhlaste se od své aplikace, protože budete provádět změny požadovaných oprávnění a váš aktuální token nebude fungovat. Pokud jste to ještě neudělali, spusťte aplikaci znovu a před aktualizací kódu níže vyberte možnost **Odhlásit** se.

Teď aktualizujte registraci a kód vaší aplikace, abyste si vyžádají e-mail uživatele a zobrazili zprávy v aplikaci. Chcete-li to dosáhnout, nejprve zvětšete oprávnění registrace aplikace v Azure AD, aby bylo možné povolit přístup k e-mailovým datům. Potom do aplikace Blazor přidejte kód pro načtení a zobrazení těchto dat na jedné ze stránek.

1. V Azure Portal vyberte svou aplikaci v **Registrace aplikací**.
1. V části **Spravovat**vyberte **oprávnění rozhraní API**.
1. Vyberte **Přidat**  >  **Microsoft Graph**oprávnění.
1. Vyberte **delegovaná oprávnění**, vyhledejte a vyberte oprávnění **Pošta. číst** .
1. Vyberte **Přidat oprávnění**.

V *appsettings.jsv* souboru aktualizujte kód tak, aby načetl příslušný token se správnými oprávněními. Přidejte "mail. Read" za obor "User. Read" v části "DownstreamAPI". Tím určíte, které obory (nebo oprávnění) aplikace bude vyžadovat přístup.

```json
"Scopes": "user.read mail.read"
```

Dále aktualizujte kód v souboru *FetchData. Razor* tak, aby se místo výchozích (náhodných) údajů o počasí načetla e-mailová data. Nahraďte kód v tomto souboru následujícím kódem:

```csharp
@page "/fetchdata"

@inject IHttpClientFactory HttpClientFactory
@inject Microsoft.Identity.Web.ITokenAcquisition TokenAcquisitionService

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
        _httpClient = HttpClientFactory.CreateClient();


        // get a token
        var token = await TokenAcquisitionService.GetAccessTokenForUserAsync(new string[] { "User.Read", "Mail.Read" });

        // make API call
        _httpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}

```

Spustit aplikaci Všimnete si, že budete vyzváni k zadání nově přidaných oprávnění, což značí, že vše funguje podle očekávání. Nyní kromě základních dat profilu uživatele aplikace požaduje přístup k e-mailovým datům.

Po udělení souhlasu přejděte na stránku načíst data a přečtěte si pár e-mailů.

:::image type="content" source="./media/tutorial-blazor-server/final-app-2.png" alt-text="Snímek obrazovky finální aplikace Má nadpis, který říká Hello Nicholas a zobrazuje seznam e-mailů, které patří k Nicholas.":::

## <a name="next-steps"></a>Další kroky

Přečtěte si o volání vytváření webových aplikací, které přihlásí uživatele v naší řadě scénářů s více částmi:

> [!div class="nextstepaction"]
> [Scénář: webová aplikace, která se přihlásí uživatelům](scenario-web-app-sign-user-overview.md)
