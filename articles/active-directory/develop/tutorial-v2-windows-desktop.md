---
title: Začínáme s platformou Microsoft identit Windows desktop | Dokumenty společnosti Microsoft
description: Jak může aplikace Windows Desktop .NET (XAML) získat přístupový token a volat rozhraní API chráněné platformou identit microsoftu.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 422251da25ae0ef911eb723bb0342a84fe99559c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79129923"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Volání rozhraní Microsoft Graph API z aplikace Pro Plochu Windows

Tato příručka ukazuje, jak nativní aplikace Windows Desktop .NET (XAML) používá přístupový token k volání rozhraní Microsoft Graph API. Aplikace může také přistupovat k dalším rozhraním API, která vyžadují přístupové tokeny z platformy identit microsoftu pro vývojáře v2.0 koncového bodu. Tato platforma byla dříve pojmenována Azure AD.

Po dokončení průvodce bude vaše aplikace moct volat chráněné rozhraní API, které používá osobní účty (včetně outlook.com, live.com a dalších). Aplikace bude také používat pracovní a školní účty od jakékoli společnosti nebo organizace, která používá Azure Active Directory.  

> [!NOTE]
> Průvodce vyžaduje Visual Studio 2015 Update 3, Visual Studio 2017 nebo Visual Studio 2019. Nemáte žádnou z těchto verzí? [Stáhněte si Visual Studio 2019 zdarma](https://www.visualstudio.com/downloads/).

>[!NOTE]
> Pokud s platformou identit Microsoftu teče, doporučujeme začít pomocí nástroje [Získat token a volat rozhraní Microsoft Graph API z desktopové aplikace pro Windows](quickstart-v2-windows-desktop.md).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak ukázková aplikace vygenerovaná touto příručkou funguje

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

Ukázková aplikace, kterou vytvoříte pomocí této příručky, umožňuje aplikaci plochy systému Windows, která se dotazuje rozhraní Microsoft Graph API nebo webového rozhraní API, které přijímá tokeny z koncového bodu platformy identity společnosti Microsoft. V tomto scénáři přidáte token do požadavků HTTP prostřednictvím hlavičky Autorizace. Microsoft Authentication Library (MSAL) zpracovává získávání a obnovu tokenů.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Zpracování získávání tokenů pro přístup k chráněným webovým řešením API

Po ověření uživatele obdrží ukázková aplikace token, který můžete použít k dotazování rozhraní Microsoft Graph API nebo webového rozhraní API, které je zabezpečeno platformou identit microsoftu pro vývojáře.

Rozhraní API, jako je například Microsoft Graph, vyžadují token umožňující přístup k určitým prostředkům. Token je například vyžadován ke čtení profilu uživatele, přístupu k kalendáři uživatele nebo odesílání e-mailů. Vaše aplikace může požádat o přístupový token pomocí MSAL pro přístup k těmto prostředkům zadáním oborů rozhraní API. Tento přístupový token je pak přidán do hlavičky autorizace PROTOKOLU HTTP pro každé volání, které je provedeno proti chráněnému prostředku.

MSAL spravuje ukládání do mezipaměti a aktualizace přístupových tokenů za vás, takže vaše aplikace nemusí.

## <a name="nuget-packages"></a>Balíčky NuGet

Tato příručka používá následující balíčky NuGet:

|Knihovna|Popis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Knihovna ověřování společnosti Microsoft (MSAL.NET)|

## <a name="set-up-your-project"></a>Nastavení projektu

V této části vytvoříte nový projekt, který předvede, jak integrovat aplikaci Windows Desktop .NET (XAML) s *přihlášením se společností Microsoft,* aby aplikace mohla dotazovat webová rozhraní API, která vyžadují token.

Aplikace, kterou vytvoříte pomocí této příručky, zobrazí tlačítko, které se používá k volání grafu, oblast pro zobrazení výsledků na obrazovce a tlačítko pro odhlášení.

> [!NOTE]
> Chcete si místo toho stáhnout projekt sady Visual Studio této ukázky? [Stáhněte si projekt](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)a přejděte na [krok Konfigurace](#register-your-application) a nakonfigurujte ukázku kódu před jeho spuštěním.
>

Chcete-li vytvořit aplikaci, postupujte takto:

1. V sadě Visual Studio vyberte **Soubor** > **nový** > **projekt**.
2. V části **Šablony**vyberte **Visual C#**.
3. Vyberte **WPF App (.NET Framework)**, v závislosti na verzi visual studio verze, kterou používáte.

## <a name="add-msal-to-your-project"></a>Přidání msal do projektu

1. V sadě Visual Studio vyberte **nástroje, ve kterých** > nástroj**NuGet Správce**> **balíčků konzola Správce balíčků**.
2. V okně Konzola Správce balíčků vložte následující příkaz Azure PowerShell:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > Tento příkaz nainstaluje knihovnu Microsoft Authentication Library. MSAL zpracovává získávání, ukládání do mezipaměti a aktualizace uživatelských tokenů, které se používají pro přístup k rozhraní API, které jsou chráněny službou Azure Active Directory v2.0
    >

## <a name="register-your-application"></a>Registrace vaší aplikace

Aplikaci můžete zaregistrovat dvěma způsoby.

### <a name="option-1-express-mode"></a>Možnost 1: Expresní režim

Aplikaci můžete rychle zaregistrovat následujícím způsobem:
1. Přejděte na [portál Azure – registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.

### <a name="option-2-advanced-mode"></a>Možnost 2: Rozšířený režim

Postup při registraci aplikace a přidání informací o registraci aplikace k řešení:
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
1. Přejděte na platformu identit Microsoftpro vývojáře [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránky.
1. Vyberte **možnost Nová registrace**.
   - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `Win-App-calling-MsGraph`.
   - V části **Podporované typy účtu** vyberte **Účty v libovolném organizačním adresáři a osobní účty Microsoft (například Skype, Xbox, Outlook.com)**.
   - Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. V seznamu stránek pro aplikaci vyberte **Ověřování**.
   1. V části **Přesměrování identifikátorů URI** v seznamu Přesměrování identifikátorů URI:
   1. Ve sloupci **TYP** vyberte **Možnost Veřejný klient/nativní (mobilní & plocha).**
   1. Ve sloupci **IDENTIFIKÁTOR IDENTIFIKÁTOR REDIRECT** zadejte`https://login.microsoftonline.com/common/oauth2/nativeclient`
1. Vyberte **Zaregistrovat**.
1. Přejděte do sady Visual Studio, otevřete soubor *App.xaml.cs* a potom ve fragmentu kódu níže nahraďte `Enter_the_Application_Id_here` ID aplikace, které jste právě zaregistrovali a zkopírovali.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>Přidání kódu pro inicializaci MSAL

V tomto kroku vytvoříte třídu pro zpracování interakce s MSAL, jako je například zpracování tokenů.

1. Otevřete soubor *App.xaml.cs* a přidejte odkaz pro MSAL do třídy:

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

## <a name="create-the-application-ui"></a>Vytvoření uj.

Tato část ukazuje, jak může aplikace dotazovat chráněný server back-end, například Microsoft Graph. 

Soubor *MainWindow.xaml* by měl být automaticky vytvořen jako součást šablony projektu. Otevřete tento soubor a nahraďte uzel * \<gridu aplikace>* následujícím kódem:

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

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Použití služby MSAL k získání tokenu pro rozhraní MICROSOFT Graph API

V této části pomocí služby MSAL získáte token pro rozhraní Microsoft Graph API.

1. V *souboru MainWindow.xaml.cs* přidejte odkaz pro MSAL do třídy:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Nahraďte `MainWindow` kód třídy následujícím:

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
    ```

<!--start-collapse-->
### <a name="more-information"></a>Další informace

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Volání `AcquireTokenInteractive` metody má za následek okno, které vyzve uživatele k přihlášení. Aplikace obvykle vyžadují, aby se uživatelé při prvním přístupu k chráněnému prostředku přihlašovali interaktivně. Mohou také nutné přihlásit, když tiché operace k získání tokenu selže (například při vypršení platnosti hesla uživatele).

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

Metoda `AcquireTokenSilent` zpracovává token akvizice a obnovení bez jakékoli interakce uživatele. Po `AcquireTokenInteractive` je spuštěn poprvé, `AcquireTokenSilent` je obvyklá metoda, která se používá k získání tokeny, které přístup k chráněným prostředkům pro následná volání, protože volání požadovat nebo obnovit tokeny jsou provedeny tiše.

Nakonec `AcquireTokenSilent` metoda se nezdaří. Důvodem selhání může být, že uživatel se odhlásil nebo změnil své heslo na jiném zařízení. Když MSAL zjistí, že problém lze vyřešit vyžadováním `MsalUiRequiredException` interaktivní akce, vyvolá výjimku. Aplikace může tuto výjimku zpracovat dvěma způsoby:

* Může okamžitě zavolat `AcquireTokenInteractive` proti. Výsledkem tohoto volání je výzva k přihlášení uživatele. Tento vzor se obvykle používá v online aplikacích, kde není k dispozici offline obsah pro uživatele. Ukázka vygenerovaná tímto řízeným nastavením se řídí tímto vzorem, který můžete vidět v akci při prvním spuštění vzorku. 

* Protože žádný uživatel nepoužil `PublicClientApp.Users.FirstOrDefault()` aplikaci, obsahuje `MsalUiRequiredException` hodnotu null a je vyvolána výjimka. 

* Kód v ukázce pak zpracovává `AcquireTokenInteractive`výjimku voláním , což má za následek výzvu uživatele k přihlášení.

* Místo toho může uživatelům představovat vizuální indikaci, že je vyžadováno interaktivní přihlášení, aby mohli vybrat správný čas pro přihlášení. Nebo aplikace může `AcquireTokenSilent` opakovat později. Tento vzor se často používá, když uživatelé mohou používat jiné funkce aplikace bez přerušení – například když je v aplikaci k dispozici obsah offline. V takovém případě se uživatelé mohou rozhodnout, kdy se chtějí přihlásit k přístupu k chráněnému prostředku nebo k aktualizaci zastaralých informací. Alternativně aplikace může rozhodnout `AcquireTokenSilent` o opakování po obnovení sítě poté, co byl dočasně nedostupný.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Volání rozhraní Microsoft Graph API pomocí tokenu, který jste právě získali

Přidejte do aplikace `MainWindow.xaml.cs`další novou metodu. Metoda se používá k `GET` vytvoření požadavku proti rozhraní GRAPH API pomocí hlavičky Authorize:

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

<!--start-collapse-->
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Další informace o volání REST proti chráněnému rozhraní API

V této ukázkové aplikaci použijete `GetHttpContentWithToken` `GET` metodu k vytvoření požadavku HTTP proti chráněnému prostředku, který vyžaduje token, a potom vrátíte obsah volajícímu. Tato metoda přidá získaný token v hlavičce autorizace PROTOKOLU HTTP. Pro tuto ukázku je prostředek koncový bod *rozhraní* MICROSOFT Graph API, který zobrazuje informace o profilu uživatele.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Přidání metody odhlášení uživatele

Chcete-li uživatele odhlásit, přidejte `MainWindow.xaml.cs` do souboru následující metodu:

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

<!--start-collapse-->
### <a name="more-information-about-user-sign-out"></a>Další informace o odhlášení uživatele

Metoda `SignOutButton_Click` odebere uživatele z mezipaměti uživatele MSAL, který efektivně říká MSAL zapomenout na aktuálního uživatele tak, aby budoucí požadavek na získání tokenu bude úspěšné pouze v případě, že je provedena jako interaktivní.

Přestože aplikace v této ukázce podporuje jednoho uživatele, MSAL podporuje scénáře, kde lze přihlásit více účtů současně. Příkladem je e-mailová aplikace, kde má uživatel více účtů.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Zobrazení základních informací o tokenech

Chcete-li zobrazit základní informace o tokenu, přidejte do *MainWindow.xaml.cs* souboru následující metodu:

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

<!--start-collapse-->
### <a name="more-information"></a>Další informace

Kromě přístupového tokenu, který se používá k volání rozhraní MICROSOFT Graph API, po přihlášení uživatele msal také získá token ID. Tento token obsahuje malou podmnožinu informací, která je relevantní pro uživatele. Metoda `DisplayBasicTokenInfo` zobrazí základní informace, které jsou obsaženy v tokenu. Například zobrazí zobrazované jméno uživatele a ID, stejně jako datum vypršení platnosti tokenu a řetězec představující samotný přístupový token. Můžete vybrat *tlačítko Volání microsoft graph rozhraní API* vícekrát a uvidíte, že stejný token byl znovu použit pro následné požadavky. Můžete také zobrazit datum vypršení platnosti, které se prodlužuje, když msal rozhodne, že je čas obnovit token.
<!--end-collapse-->

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

Pomozte nám vylepšit platformu identit Microsoftu. Řekněte nám, co si myslíte, že dokončení krátké hod-průzkum u dvou otázek.

> [!div class="nextstepaction"]
> [Průzkum platformy identity Microsoftu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
