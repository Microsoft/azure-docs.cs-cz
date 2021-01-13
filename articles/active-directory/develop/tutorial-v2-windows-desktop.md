---
title: 'Kurz: Vytvoření aplikace Windows Presentation Foundation (WPF), která používá Microsoft Identity Platform pro ověřování | Azure'
titleSuffix: Microsoft identity platform
description: V tomto kurzu vytvoříte aplikaci WPF, která pomocí platformy Microsoft Identity přihlašuje uživatele a získá přístupový token pro volání rozhraní API Microsoft Graph jménem.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0d759b3af097067ba0c9215b65b212d50474d571
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178343"
---
# <a name="tutorial-call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Kurz: volání rozhraní Microsoft Graph API z desktopové aplikace pro Windows

V tomto kurzu vytvoříte nativní aplikaci pro Windows Desktop .NET (XAML), která se přihlásí uživatelům a získá přístupový token pro volání rozhraní API Microsoft Graph. 

Po dokončení průvodce bude vaše aplikace moci volat chráněné rozhraní API, které používá osobní účty (včetně outlook.com, live.com a dalších). Aplikace bude také používat pracovní a školní účty z jakékoli společnosti nebo organizace, která používá Azure Active Directory.

V tomto kurzu:

> [!div class="checklist"]
> * Vytvoření projektu *Windows Presentation Foundation (WPF)* v aplikaci Visual Studio
> * Instalace knihovny Microsoft Authentication Library (MSAL) pro .NET
> * Registrace aplikace v Azure Portal
> * Přidat kód pro podporu přihlášení a odhlášení uživatele
> * Přidat kód pro volání rozhraní API Microsoft Graph
> * Otestování aplikace

## <a name="prerequisites"></a>Požadavky

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak ukázková aplikace vygenerovaná touto příručkou funguje

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

Ukázková aplikace, kterou vytvoříte pomocí této příručky, umožňuje desktopovou aplikaci pro Windows, která se dotazuje na rozhraní Microsoft Graph API nebo webové rozhraní API, které přijímá tokeny z koncového bodu Microsoft Identity-Platform. V tomto scénáři přidáte token do požadavků HTTP prostřednictvím autorizační hlavičky. Knihovna Microsoft Authentication Library (MSAL) zpracovává získání a obnovení tokenu.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Zpracování získání tokenu pro přístup k chráněným webovým rozhraním API

Po ověření uživatele obdrží ukázková aplikace token, který můžete použít k dotazování Microsoft Graph rozhraní API nebo webového rozhraní API, které je zabezpečené platformou Microsoft identity pro vývojáře.

Rozhraní API, například Microsoft Graph, vyžadují token, který umožňuje přístup ke konkrétním prostředkům. Například token je vyžadován ke čtení profilu uživatele, přístupu k kalendáři uživatele nebo k odeslání e-mailu. Vaše aplikace může požádat o přístupový token pomocí MSAL pro přístup k těmto prostředkům zadáním oborů rozhraní API. Tento přístupový token se pak přidá do hlavičky Authorization protokolu HTTP pro každé volání provedené proti chráněnému prostředku.

MSAL spravuje ukládání do mezipaměti a aktualizace přístupových tokenů za vás, takže vaše aplikace nemusí.

## <a name="nuget-packages"></a>Balíčky NuGet

Tato příručka používá následující balíčky NuGet:

|Knihovna|Description|
|---|---|
|[Microsoft. identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Knihovna Microsoft Authentication Library (MSAL.NET)|

## <a name="set-up-your-project"></a>Nastavení projektu

V této části vytvoříte nový projekt, který předvede integraci aplikace Windows Desktop .NET (XAML) s *přihlášením do společnosti Microsoft* , aby se aplikace mohla dotazovat webová rozhraní API, která vyžadují token.

Aplikace, kterou vytvoříte pomocí této příručky, zobrazuje tlačítko, které se používá k volání grafu, oblasti pro zobrazení výsledků na obrazovce a tlačítka pro odhlášení.

> [!NOTE]
> Chcete raději stáhnout tuto ukázkovou aplikaci Visual Studio? [Stáhněte si projekt](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)a přejděte k [konfiguračnímu kroku](#register-your-application) , abyste před spuštěním nakonfigurovali ukázku kódu.
>

Chcete-li vytvořit aplikaci, postupujte následovně:

1. V aplikaci Visual Studio vyberte **soubor**  >  **Nový**  >  **projekt**.
2. V části **šablony** vyberte **Visual C#**.
3. Vyberte možnost **aplikace WPF (.NET Framework)** v závislosti na verzi sady Visual Studio, kterou používáte.

## <a name="add-msal-to-your-project"></a>Přidání MSAL do projektu

1. V aplikaci Visual Studio vyberte **nástroje**  >  **Správce balíčků NuGet** >  **Konzola správce balíčků**.
2. V okně konzoly Správce balíčků vložte následující příkaz Azure PowerShell:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE]
    > Tento příkaz nainstaluje knihovnu Microsoft Authentication Library. MSAL zpracovává získání, ukládání do mezipaměti a aktualizace uživatelských tokenů, které se používají pro přístup k rozhraním API chráněným Azure Active Directory v 2.0
    >

## <a name="register-your-application"></a>Registrace aplikace

Aplikaci můžete zaregistrovat oběma způsoby.

### <a name="option-1-express-mode"></a>Možnost 1: Expresní režim

Aplikaci můžete rychle zaregistrovat pomocí následujícího postupu:
1. Přejít k prostředí rychlý Start pro <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs" target="_blank">Azure Portal registrace aplikací <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.

### <a name="option-2-advanced-mode"></a>Možnost 2: rozšířený režim

Postup při registraci aplikace a přidání informací o registraci aplikace k řešení:
1. Přihlaste se <a href="https://portal.azure.com/" target="_blank">k <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Portal</a>.
1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
1. Zadejte **název** vaší aplikace, například `Win-App-calling-MsGraph` . Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
1. V části **podporované typy účtů** vyberte **účty v jakémkoli adresáři organizace (libovolný adresář Azure AD – víceklientské rozhraní) a osobní účty Microsoft (např. Skype, Xbox)**.
1. Vyberte **Zaregistrovat**.
1. V části **Spravovat** vyberte **ověřování**  >  **Přidat platformu**.
1. Vyberte **mobilní a desktopové aplikace**.
1. V části **identifikátory URI pro přesměrování** vyberte **https://login.microsoftonline.com/common/oauth2/nativeclient** .
1. Vyberte **Konfigurovat**.
1. Do sady Visual Studio otevřete soubor *App.XAML.cs* a potom `Enter_the_Application_Id_here` v následujícím fragmentu kódu nahraďte ID aplikace, které jste právě zaregistrovali a zkopírovali.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>Přidejte kód pro inicializaci MSAL

V tomto kroku vytvoříte třídu pro zpracování interakce s MSAL, jako je například manipulace s tokeny.

1. Otevřete soubor *App.XAML.cs* a přidejte odkaz pro MSAL do třídy:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Aktualizujte třídu aplikace na následující:

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>Vytvoření uživatelského rozhraní aplikace

V této části se dozvíte, jak může aplikace zadat dotaz na chráněný back-end Server, jako je například Microsoft Graph.

Soubor *MainWindow. XAML* by měl být automaticky vytvořen jako součást šablony projektu. Otevřete tento soubor a potom nahraďte uzel vaší aplikace *\<Grid>* následujícím kódem:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Získání tokenu pro rozhraní Microsoft Graph API pomocí MSAL

V této části použijete MSAL k získání tokenu pro rozhraní Microsoft Graph API.

1. Do souboru *MainWindow.XAML.cs* přidejte odkaz pro MSAL do třídy:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. `MainWindow`Kód třídy nahraďte následujícím kódem:

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };


        public MainWindow()
        {
            InitializeComponent();
        }

      /// <summary>
        /// Call AcquireToken - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();
            var firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await app.AcquireTokenSilent(scopes, firstAccount)
                    .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilent.
                // This indicates you need to call AcquireTokenInteractive to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await app.AcquireTokenInteractive(scopes)
                        .WithAccount(accounts.FirstOrDefault())
                        .WithPrompt(Prompt.SelectAccount)
                        .ExecuteAsync();
                }
                catch (MsalException msalex)
                {
                    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
                }
            }
            catch (Exception ex)
            {
                ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
                return;
            }

            if (authResult != null)
            {
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
        }
    ```

### <a name="more-information"></a>Další informace

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Výsledkem volání `AcquireTokenInteractive` metody je okno, které vyzve uživatele k přihlášení. Aplikace obvykle vyžadují, aby se uživatelé přihlásili interaktivně, když potřebují přístup k chráněnému prostředku. Můžou se taky muset přihlásit, když se tichá operace získání tokenu nezdařila (například když vypršela platnost hesla uživatele).

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

`AcquireTokenSilent`Metoda zpracovává získání a obnovení tokenů bez zásahu uživatele. Po `AcquireTokenInteractive` prvním spuštění `AcquireTokenSilent` je obvyklý způsob, jak použít k získání tokenů, které přistupují k chráněným prostředkům pro následná volání, protože volání požadavků na požadavky nebo obnovení tokenů se provádí v tichém režimu.

Nakonec `AcquireTokenSilent` bude metoda neúspěšná. Důvodem selhání může být to, že uživatel buď odhlásil nebo změnil heslo na jiném zařízení. Pokud MSAL zjistí, že problém lze vyřešit vyžadováním interaktivní akce, vyvolá `MsalUiRequiredException` výjimku. Vaše aplikace může tuto výjimku zpracovat dvěma způsoby:

* Může provést volání proti `AcquireTokenInteractive` okamžitému. Výsledkem tohoto volání je dotazování uživatele na přihlášení. Tento model se obvykle používá v online aplikacích, kde není k dispozici žádný offline obsah pro uživatele. Ukázka vygenerovaná tímto procesem instalace se řídí tímto modelem, který můžete vidět v akci při prvním spuštění ukázky.

* Vzhledem k tomu, že aplikace nepoužila žádného uživatele, `PublicClientApp.Users.FirstOrDefault()` obsahuje hodnotu null a `MsalUiRequiredException` je vyvolána výjimka.

* Kód v ukázce pak zpracovává výjimku voláním metody `AcquireTokenInteractive` , která má za následek zobrazení výzvy k přihlášení uživatele.

* Může místo toho prezentovat vizuální indikaci, že se vyžaduje interaktivní přihlášení, aby mohli vybrat správný čas pro přihlášení. Nebo se aplikace může pokus opakovat `AcquireTokenSilent` později. Tento model se často používá, když uživatelé můžou používat jiné funkce aplikace bez přerušení – například když je offline obsah k dispozici v aplikaci. V takovém případě se uživatelé můžou rozhodnout, kdy se chtějí přihlásit k chráněnému prostředku nebo aktualizovat zastaralé informace. Alternativně se aplikace může rozhodnout opakovat pokus `AcquireTokenSilent` po obnovení sítě po dočasné nedostupnosti.

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Volání rozhraní API Microsoft Graph pomocí tokenu, který jste právě získali

Přidejte následující novou metodu do `MainWindow.xaml.cs` . Metoda se používá k vytvoření požadavku na `GET` Graph API pomocí autorizační hlavičky:

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Další informace o tom, jak provést volání REST proti chráněnému rozhraní API

V této ukázkové aplikaci použijete `GetHttpContentWithToken` metodu k vytvoření `GET` požadavku HTTP proti chráněnému prostředku, který vyžaduje token, a pak vrátíte obsah volajícímu. Tato metoda přidá získaný token v autorizační hlavičce protokolu HTTP. V této ukázce je prostředkem koncový bod Microsoft Graph API *já* , který zobrazuje informace o profilu uživatele.

## <a name="add-a-method-to-sign-out-a-user"></a>Přidání metody pro odhlášení uživatele

Pokud chcete odhlásit uživatele, přidejte do souboru následující metodu `MainWindow.xaml.cs` :

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    var accounts = await App.PublicClientApp.GetAccountsAsync();

    if (accounts.Any())
    {
        try
        {
            await App.PublicClientApp.RemoveAsync(accounts.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```

### <a name="more-information-about-user-sign-out"></a>Další informace o odhlášení uživatele

`SignOutButton_Click`Metoda odebere uživatele z mezipaměti uživatelů MSAL, což efektivně dává MSAL k zapomenutí aktuálního uživatele, takže budoucí požadavek na získání tokenu bude úspěšný pouze v případě, že bude vytvořen jako interaktivní.

I když aplikace v této ukázce podporuje jednotlivé uživatele, MSAL podporuje scénáře, ve kterých může být současně přihlášeno více účtů. Příkladem je e-mailová aplikace, kde má uživatel více účtů.

## <a name="display-basic-token-information"></a>Zobrazit základní informace o tokenu

Chcete-li zobrazit základní informace o tokenu, přidejte do souboru *MainWindow.XAML.cs* následující metodu:

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Username: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

### <a name="more-information"></a>Další informace

Kromě přístupového tokenu, který se používá k volání rozhraní Microsoft Graph API po přihlášení uživatele, MSAL také získá token ID. Tento token obsahuje malou podmnožinu informací, které jsou relevantní pro uživatele. `DisplayBasicTokenInfo`Metoda zobrazí základní informace, které jsou obsaženy v tokenu. Například zobrazuje zobrazované jméno a ID uživatele a také datum vypršení platnosti tokenu a řetězec představující samotný přístupový token. Můžete vybrat tlačítko *rozhraní API pro volání Microsoft Graph* několikrát a zjistit, že se stejný token znovu použil pro následné požadavky. Můžete také zobrazit datum vypršení platnosti, kdy MSAL rozhodne, že je čas obnovit token.

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o vytváření desktopových aplikací, které volají chráněná webová rozhraní API v naší řadě scénářů s více částmi:

> [!div class="nextstepaction"]
> [Scénář: desktopová aplikace, která volá webová rozhraní API](scenario-desktop-overview.md)
