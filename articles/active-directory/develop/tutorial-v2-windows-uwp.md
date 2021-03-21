---
title: 'Kurz: Vytvoření aplikace Univerzální platforma Windows (UWP), která používá Microsoft Identity Platform pro ověřování | Azure'
titleSuffix: Microsoft identity platform
description: V tomto kurzu vytvoříte aplikaci UWP, která používá Microsoft Identity Platform k přihlašování uživatelů a získání přístupového tokenu pro volání rozhraní API Microsoft Graph jménem.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40
ms.openlocfilehash: 6383f63d2118d8618f07bf3cb6cd08a0b16140f3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100102644"
---
# <a name="tutorial-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Kurz: volání rozhraní API Microsoft Graph z aplikace Univerzální platforma Windows (UWP)

V tomto kurzu vytvoříte nativní aplikaci Univerzální platforma Windows (UWP), která se přihlásí uživatelům a získá přístupový token pro volání rozhraní API Microsoft Graph. 

Na konci tohoto průvodce vaše aplikace volá chráněné rozhraní API pomocí osobních účtů. Příklady jsou outlook.com, live.com a další. Vaše aplikace také volá pracovní a školní účty z libovolné společnosti nebo organizace, která má Azure Active Directory (Azure AD).

V tomto kurzu:

> [!div class="checklist"]
> * Vytvoření projektu *Univerzální platforma Windows (UWP)* v aplikaci Visual Studio
> * Registrace aplikace v Azure Portal
> * Přidat kód pro podporu přihlášení a odhlášení uživatele
> * Přidat kód pro volání rozhraní API Microsoft Graph
> * Otestování aplikace

## <a name="prerequisites"></a>Předpoklady

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) s nainstalovanou úlohou [vývoj Univerzální platforma Windows](/windows/uwp/get-started/get-set-up)

## <a name="how-this-guide-works"></a>Jak funguje tento průvodce

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Tato příručka vytvoří ukázkovou aplikaci UWP, která se dotazuje na rozhraní Microsoft Graph API. V tomto scénáři se token přidá do požadavků HTTP pomocí autorizační hlavičky. Knihovna Microsoft Authentication Library zpracovává získání a obnovení tokenů.

## <a name="nuget-packages"></a>Balíčky NuGet

Tato příručka používá následující balíček NuGet:

|Knihovna|Description|
|---|---|
|[Microsoft. identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)| Identity a ověřování Microsoftu|
|[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph)|Klientská knihovna Microsoft Graph|

## <a name="set-up-your-project"></a>Nastavení projektu

Tato část obsahuje podrobné pokyny pro integraci aplikace Windows Desktop .NET (XAML) s přihlašováním s Microsoftem. Aplikace se pak může dotazovat na webová rozhraní API, která vyžadují token, jako je Microsoft Graph API.

Tato příručka vytvoří aplikaci, která zobrazí tlačítko s dotazem na rozhraní Microsoft Graph API a tlačítko pro odhlášení. Zobrazuje také textová pole obsahující výsledky volání.

> [!Tip]
> Pokud chcete zobrazit dokončenou verzi projektu, který sestavíte v tomto kurzu, můžete [si ho stáhnout z GitHubu](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip).

### <a name="create-your-application"></a>Vytvořte si svoji aplikaci

1. Otevřete Visual Studio a vyberte **vytvořit nový projekt**.
1. V možnosti **vytvořit nový projekt** zvolte **prázdná aplikace (univerzální pro Windows)** pro C# a vyberte **Další**.
1. V části **Konfigurovat nový projekt** zadejte název aplikace a vyberte **vytvořit**.
1. Pokud se zobrazí výzva, v **New Univerzální platforma Windows Project** vyberte libovolnou verzi pro **cíl** a **minimální** verzi a vyberte **OK**.

   ![Minimální a cílové verze](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-the-microsoft-authentication-library-to-your-project"></a>Přidejte do projektu knihovnu Microsoft Authentication Library.

1. V aplikaci Visual Studio vyberte **nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků**.
1. Zkopírujte a vložte následující příkazy v okně **konzoly Správce balíčků** :

    ```powershell
    Install-Package Microsoft.Identity.Client
    Install-Package Microsoft.Graph
    ```

   > [!NOTE]
   > První příkaz nainstaluje [knihovnu Microsoft Authentication Library (MSAL.NET)](https://aka.ms/msal-net). MSAL.NET získává, ukládá do mezipaměti a aktualizuje tokeny uživatelů, kteří přistupují k rozhraním API chráněným platformou Microsoft identity. Druhý příkaz nainstaluje [Microsoft Graph klientskou knihovnu rozhraní .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) pro ověření požadavků na Microsoft Graph a volání služby.

### <a name="create-your-applications-ui"></a>Vytvoření uživatelského rozhraní aplikace

Visual Studio vytvoří jako součást šablony projektu *MainPage. XAML* . Otevřete tento soubor a poté nahraďte uzel **mřížky** vaší aplikace následujícím kódem:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

### <a name="use-the-microsoft-authentication-library-to-get-a-token-for-the-microsoft-graph-api"></a>Získání tokenu pro rozhraní Microsoft Graph API pomocí knihovny Microsoft Authentication Library

V této části se dozvíte, jak pomocí knihovny Microsoft Authentication Library získat token pro rozhraní Microsoft Graph API. Proveďte změny v souboru *MainPage. XAML. cs* .

1. V souboru *MainPage. XAML. cs* přidejte následující odkazy:

    ```csharp
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. Třídu nahraďte `MainPage` následujícím kódem:

    ```csharp
    public sealed partial class MainPage : Page
    {

        //Set the scope for API call to user.read
        private string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        private const string ClientId = "[Application Id pasted from the application registration portal]";

        private const string Tenant = "common"; // Alternatively "[Enter your tenant, as obtained from the Azure portal, e.g. kko365.onmicrosoft.com]"
        private const string Authority = "https://login.microsoftonline.com/" + Tenant;

        // The MSAL Public client app
        private static IPublicClientApplication PublicClientApp;

        private static string MSGraphURL = "https://graph.microsoft.com/v1.0/";
        private static AuthenticationResult authResult;

        public MainPage()
        {
            this.InitializeComponent();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            try
            {
                // Sign in user using MSAL and obtain an access token for Microsoft Graph
                GraphServiceClient graphClient = await SignInAndInitializeGraphServiceClient(scopes);

                // Call the /me endpoint of Graph
                User graphUser = await graphClient.Me.Request().GetAsync();

                // Go back to the UI thread to make changes to the UI
                await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    ResultText.Text = "Display Name: " + graphUser.DisplayName + "\nBusiness Phone: " + graphUser.BusinessPhones.FirstOrDefault()
                                      + "\nGiven Name: " + graphUser.GivenName + "\nid: " + graphUser.Id
                                      + "\nUser Principal Name: " + graphUser.UserPrincipalName;
                    DisplayBasicTokenInfo(authResult);
                    this.SignOutButton.Visibility = Visibility.Visible;
                });
            }
            catch (MsalException msalEx)
            {
                await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalEx}");
            }
            catch (Exception ex)
            {
                await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
                return;
            }
        }
                /// <summary>
        /// Signs in the user and obtains an access token for Microsoft Graph
        /// </summary>
        /// <param name="scopes"></param>
        /// <returns> Access Token</returns>
        private static async Task<string> SignInUserAndGetTokenUsingMSAL(string[] scopes)
        {
            // Initialize the MSAL library by building a public client application
            PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .WithUseCorporateNetwork(false)
                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                 .WithLogging((level, message, containsPii) =>
                 {
                     Debug.WriteLine($"MSAL: {level} {message} ");
                 }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
                .Build();

            // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.
            IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
            IAccount firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                  .ExecuteAsync()
                                                  .ConfigureAwait(false);

            }
            return authResult.AccessToken;
        }
    }
    ```

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele<a name="more-information"></a>

`AcquireTokenInteractive`Výsledkem metody je okno, které vyzve uživatele k přihlášení. Aplikace obvykle vyžadují, aby se uživatelé přihlásili interaktivně při prvním přístupu k chráněnému prostředku. Můžou se taky muset přihlásit, když se nezdařila tichá operace získání tokenu. Příkladem je vypršení platnosti hesla uživatele.

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

`AcquireTokenSilent`Metoda zpracovává získání a obnovení tokenů bez zásahu uživatele. Po `AcquireTokenInteractive` prvním spuštění a vyzvání uživatele k zadání přihlašovacích údajů použijte `AcquireTokenSilent` metodu k vyžádání tokenů pro pozdější volání. Tato metoda získává tokeny tiše. Knihovna Microsoft Authentication Library zpracovává mezipaměť a obnovení tokenu.

Nakonec se `AcquireTokenSilent` Metoda nezdařila. Důvody k selhání zahrnují uživatele, který se odhlásil nebo změnil jeho heslo na jiném zařízení. Pokud knihovna ověřování společnosti Microsoft zjistí, že problém vyžaduje interaktivní akci, vyvolá `MsalUiRequiredException` výjimku. Vaše aplikace může tuto výjimku zpracovat dvěma způsoby:

* Vaše aplikace volá `AcquireTokenInteractive` okamžitě. Výsledkem tohoto volání je dotazování uživatele na přihlášení. Tento přístup se obvykle používá pro online aplikace, kde pro uživatele není k dispozici žádný obsah offline. Ukázka vygenerovaná tímto průvodcem Setup se řídí vzorem. Při prvním spuštění ukázky se zobrazí v akci.

   Vzhledem k tomu, že aplikace nepoužila žádného uživatele, `accounts.FirstOrDefault()` obsahuje hodnotu null a vyvolá `MsalUiRequiredException` výjimku.

   Kód v ukázce pak zpracovává výjimku voláním `AcquireTokenInteractive` . Výsledkem tohoto volání je dotazování uživatele na přihlášení.

* Vaše aplikace prezentuje vizuální označení uživatelům, kteří se potřebují přihlašovat. Pak si můžou vybrat správný čas pro přihlášení. Aplikace se může opakovat `AcquireTokenSilent` později. Tento postup použijte, když uživatelé můžou používat jiné funkce aplikace bez přerušení. Příkladem je, když je offline obsah k dispozici v aplikaci. V takovém případě se uživatelé můžou rozhodnout, kdy se chtějí přihlásit. Aplikace se může pokusit znovu `AcquireTokenSilent` po dočasné nedostupnosti sítě.

### <a name="instantiate-the-microsoft-graph-service-client-by-obtaining-the-token-from-the-signinuserandgettokenusingmsal-method"></a>Vytvoření instance klienta služby Microsoft Graph získáním tokenu z metody SignInUserAndGetTokenUsingMSAL

Do souboru *MainPage. XAML. cs* přidejte následující novou metodu:

```csharp
      /// <summary>
     /// Sign in user using MSAL and obtain a token for Microsoft Graph
     /// </summary>
     /// <returns>GraphServiceClient</returns>
     private async static Task<GraphServiceClient> SignInAndInitializeGraphServiceClient(string[] scopes)
     {
         GraphServiceClient graphClient = new GraphServiceClient(MSGraphURL,
             new DelegateAuthenticationProvider(async (requestMessage) =>
             {
                 requestMessage.Headers.Authorization = new AuthenticationHeaderValue("bearer", await SignInUserAndGetTokenUsingMSAL(scopes));
             }));

         return await Task.FromResult(graphClient);
     }
```

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Další informace o tom, jak provést volání REST proti chráněnému rozhraní API

V této ukázkové aplikaci je `GetGraphServiceClient` Metoda vytvořena `GraphServiceClient` pomocí přístupového tokenu. Pak `GraphServiceClient` slouží k získání informací o profilu uživatele z koncového bodu **já** .

### <a name="add-a-method-to-sign-out-the-user"></a>Přidejte metodu pro odhlášení uživatele.

Pokud chcete uživatele odhlásit, přidejte do souboru *MainPage. XAML. cs* následující metodu:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
    IAccount firstAccount = accounts.FirstOrDefault();

    try
    {
        await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            ResultText.Text = "User has signed out";
            this.CallGraphButton.Visibility = Visibility.Visible;
                this.SignOutButton.Visibility = Visibility.Collapsed;
            });
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing out user: {ex.Message}";
        }
    }
```

MSAL.NET používá asynchronní metody k získání tokenů nebo manipulaci s účty. V takovém případě podporují akce uživatelského rozhraní ve vlákně UI. To je důvod `Dispatcher.RunAsync` volání a preventivních opatření k volání `ConfigureAwait(false)` .

#### <a name="more-information-about-signing-out"></a>Další informace o odhlášení<a name="more-information-on-sign-out"></a>

`SignOutButton_Click`Metoda odebere uživatele z mezipaměti uživatelů knihovny ověřování společnosti Microsoft. Tato metoda efektivně oznamuje službě Microsoft Authentication Library, aby zapomněla aktuálního uživatele. Budoucí požadavek na získání tokenu je úspěšný jenom v případě, že je interaktivní.

Aplikace v této ukázce podporuje jednoho uživatele. Knihovna Microsoft Authentication Library podporuje scénáře, ve kterých se uživatel může přihlásit k více než jednomu účtu. Příkladem je e-mailová aplikace, kde má uživatel několik účtů.

### <a name="display-basic-token-information"></a>Zobrazit základní informace o tokenu

Přidejte následující metodu do souboru *MainPage. XAML. cs* a zobrazte základní informace o tokenu:

```csharp
/// <summary>
/// Display basic information contained in the token. Needs to be called from the UI thread.
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

#### <a name="more-information"></a>Další informace<a name="more-information-1"></a>

Tokeny ID získané pomocí **OpenID Connect** také obsahují malou podmnožinu informací, které se vztahují k uživateli. `DisplayBasicTokenInfo` Zobrazí základní informace obsažené v tokenu. Tyto informace zahrnují zobrazované jméno a ID uživatele. Zahrnuje také datum vypršení platnosti tokenu a řetězec, který představuje samotný přístupový token. Pokud si vyberete tlačítko **rozhraní API pro volání Microsoft Graph** několikrát, uvidíte, že se stejný token znovu použil pro pozdější požadavky. Můžete také zobrazit datum vypršení platnosti, které se prodlouží, když knihovna Microsoft Authentication Library rozhodne, že má obnovit token.

### <a name="display-message"></a>Zobrazit zprávu

Do souboru *MainPage. XAML. cs* přidejte následující novou metodu:

```csharp
/// <summary>
/// Displays a message in the ResultText. Can be called from any thread.
/// </summary>
private async Task DisplayMessageAsync(string message)
{
     await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
         () =>
         {
             ResultText.Text = message;
         });
     }
```

## <a name="register-your-application"></a>Registrace aplikace

Nyní Zaregistrujte svou aplikaci:

1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
1. Zadejte **název** vaší aplikace, například `UWP-App-calling-MSGraph` . Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
1. V části **podporované typy účtů** vyberte **účty v jakémkoli adresáři organizace (libovolný adresář Azure AD – víceklientské) a osobní účty Microsoft (např. Skype, Xbox)**. 
1. Vyberte **Zaregistrovat**.
1. Na stránce Přehled vyhledejte hodnotu **ID aplikace (klienta)** a zkopírujte ji. Vraťte se do sady Visual Studio, otevřete *MainPage. XAML. cs* a nahraďte hodnotu `ClientId` touto hodnotou.

Konfigurace ověřování pro vaši aplikaci:

1. Zpátky v <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>v části **Spravovat** vyberte **ověřování**  >  **Přidat platformu** a pak vyberte **mobilní a desktopové aplikace**.
1. V části **identifikátory URI pro přesměrování** zadejte `https://login.microsoftonline.com/common/oauth2/nativeclient` .
1. Vyberte **Konfigurovat**.

Konfigurace oprávnění rozhraní API pro aplikaci:

1. V části **Spravovat** vyberte **oprávnění rozhraní API**  >  **Přidat oprávnění**.
1. Vyberte **Microsoft Graph**.
1. Vyberte **delegovaná oprávnění**, vyhledejte *uživatele. číst* a ověřte, zda je vybrána možnost **uživatel. čtení** .
1. Pokud jste provedli nějaké změny, vyberte **Přidat oprávnění** a uložte je.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Povolit integrované ověřování u federovaných domén (volitelné)

Pokud chcete povolit integrované ověřování systému Windows, když se používá s doménou federované služby Azure AD, manifest aplikace musí povolit další funkce. Vraťte se do aplikace v aplikaci Visual Studio.

1. Otevřete soubor *Package. appxmanifest*.
1. Vyberte **Možnosti** a povolit následující nastavení:

   * **Podnikové ověřování**
   * **Privátní sítě (klient & Server)**
   * **Sdílené uživatelské certifikáty**

> [!IMPORTANT]
> [Integrované ověřování systému Windows](https://aka.ms/msal-net-iwa) není ve výchozím nastavení pro tuto ukázku nakonfigurováno. Aplikace, které `Enterprise Authentication` `Shared User Certificates` vyžadují nebo poskytují požadavky na vyšší úroveň ověřování ve Windows Storu. Ne všichni vývojáři chtějí provádět vyšší úroveň ověřování. Toto nastavení povolte pouze v případě, že potřebujete integrované ověřování systému Windows s doménou federované služby Azure AD.

## <a name="alternate-approach-to-using-withdefaultredirecturi"></a>Alternativní přístup k použití WithDefaultRedirectURI ()

V aktuální ukázce `WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")` je použita metoda. Chcete-li použít `WithDefaultRedirectURI()` , proveďte tyto kroky:

1. V souboru *MainPage. XAML. cs* nahraďte `WithRedirectUri` `WithDefaultRedirectUri` :

   **Aktuální kód**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority(Authority)
       .WithUseCorporateNetwork(false)
       .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();

   ```

   **Aktualizovaný kód**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority("https://login.microsoftonline.com/common")
       .WithUseCorporateNetwork(false)
       .WithDefaultRedirectUri()
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();
   ```

2.  Vyhledejte identifikátor URI zpětného volání pro vaši aplikaci přidáním `redirectURI` pole v souboru *MainPage. XAML. cs* a nastavením zarážky:

    ```csharp

    public sealed partial class MainPage : Page
    {
            ...

            string redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker
                                .GetCurrentApplicationCallbackUri().ToString();
            public MainPage()
            {
                ...
            }
           ...
    }

    ```

    Spusťte aplikaci a potom zkopírujte hodnotu `redirectUri` při dosažení zarážky. Hodnota by měla vypadat nějak podobně jako následující hodnota: `ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/`

    Řádek kódu pak můžete odebrat, protože je požadován pouze jednou pro načtení hodnoty.

3. V portálu pro registraci aplikací přidejte vrácenou hodnotu do **RedirectUri** v podokně **ověřování** .

## <a name="test-your-code"></a>Testování kódu

Chcete-li otestovat aplikaci, vyberte klávesu **F5** ke spuštění projektu v aplikaci Visual Studio. Zobrazí se hlavní okno:

![Uživatelské rozhraní aplikace](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Až budete připraveni na test, vyberte **zavolat Microsoft Graph API**. K přihlášení použijte účet organizace Azure AD nebo účet Microsoft, jako je například live.com nebo outlook.com. Když uživatel poprvé spustí tento test, aplikace zobrazí okno s výzvou, aby se uživatel přihlásil.

### <a name="consent"></a>Souhlas

Při prvním přihlášení k aplikaci se zobrazí obrazovka pro vyjádření souhlasu podobná následujícímu obrázku. Vyberte **Ano** , pokud chcete výslovně udělit přístup k přístupu:

![Obrazovka souhlasu s přístupem](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Očekávané výsledky

Na obrazovce **výsledky volání rozhraní API** se zobrazí informace o profilu uživatele vracené voláním Microsoft Graph API:

![Obrazovka výsledků volání rozhraní API](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

Zobrazí se také základní informace o tokenu získaném prostřednictvím `AcquireTokenInteractive` nebo `AcquireTokenSilent` v poli **informace o tokenu** :

|Vlastnost  |Formát  |Description |
|---------|---------|---------|
|`Username` |`user@domain.com` |Uživatelské jméno, které uživatele identifikuje.|
|`Token Expires` |`DateTime` |Čas vypršení platnosti tokenu Knihovna Microsoft Authentication Library rozšiřuje datum vypršení platnosti tím, že podle potřeby obnoví token.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a delegovaných oprávněních

Rozhraní Microsoft Graph API vyžaduje, `user.read` aby se v oboru četl profil uživatele. Tento obor se ve výchozím nastavení přidá v každé aplikaci, která je zaregistrovaná na portálu pro registraci aplikací. Další rozhraní API pro Microsoft Graph a vlastní rozhraní API pro back-end Server můžou vyžadovat další obory. Například rozhraní Microsoft Graph API vyžaduje `Calendars.Read` rozsah pro výpis kalendářů uživatele.

Chcete-li získat přístup k kalendářům uživatele v kontextu aplikace, přidejte `Calendars.Read` delegované oprávnění k informacím o registraci aplikace. Pak přidejte `Calendars.Read` obor do `acquireTokenSilent` volání.

Uživatelům se může zobrazit výzva k dalšímu souhlasu při zvýšení počtu oborů.

## <a name="known-issues"></a>Známé problémy

### <a name="issue-1"></a>Problém 1

Při přihlášení aplikace ve federované doméně Azure AD se zobrazí jedna z následujících chybových zpráv:

* V žádosti se nenašel žádný platný klientský certifikát.
* V úložišti certifikátů uživatele se nenašly žádné platné certifikáty.
* "Zkuste znovu zvolit jinou metodu ověřování."

**Příčina:** Funkce Enterprise a Certificate nejsou povolené.

**Řešení:** Postupujte podle kroků v části [povolení integrovaného ověřování u federovaných domén (volitelné)](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problém 2

Povolíte [integrované ověřování pro federované domény](#enable-integrated-authentication-on-federated-domains-optional) a zkusíte použít Windows Hello v počítači s Windows 10 pro přihlášení k prostředí s nakonfigurovaným vícefaktorového ověřování. Zobrazí se seznam certifikátů. Pokud se rozhodnete použít PIN kód, okno PIN se nikdy nezobrazí.

**Příčina:** Tento problém je známým omezením zprostředkovatele webového ověřování v aplikacích UWP, které běží na stolních počítačích s Windows 10. V systému Windows 10 Mobile funguje správně.

**Alternativní řešení:** Vyberte **Přihlásit se s dalšími možnostmi**. Pak vyberte **Přihlásit se pomocí uživatelského jména a hesla**. Vyberte **zadat heslo**. Pak Projděte proces ověřování pro telefon.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Další informace o použití knihovny Microsoft Authentication Library (MSAL) pro autorizaci a ověřování v aplikacích .NET:

> [!div class="nextstepaction"]
> [Přehled knihovny Microsoft Authentication Library (MSAL)](msal-overview.md)
