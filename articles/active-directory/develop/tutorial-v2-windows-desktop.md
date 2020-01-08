---
title: Začínáme s platformou Microsoft Identity Platform Windows Desktop | Microsoft Docs
description: Jak aplikace Windows Desktop .NET (XAML) může získat přístupový token a volat rozhraní API chráněné platformou Microsoft identity.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: c29a06496bb1303849250f049e4e7444a5a5ddf3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423362"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Volání rozhraní API pro Microsoft Graph z desktopové aplikace pro Windows

Tato příručka ukazuje, jak nativní aplikace Windows Desktop .NET (XAML) používá přístupový token pro volání rozhraní Microsoft Graph API. Aplikace může také přistupovat k dalším rozhraním API, která vyžadují přístupové tokeny z koncového bodu Microsoft Identity Platform pro vývojáře v 2.0. Tato platforma se dřív jmenovala jako Azure AD.

Po dokončení průvodce bude vaše aplikace moci volat chráněné rozhraní API, které používá osobní účty (včetně outlook.com, live.com a dalších). Aplikace bude také používat pracovní a školní účty z jakékoli společnosti nebo organizace, která používá Azure Active Directory.  

> [!NOTE]
> Průvodce vyžaduje Visual Studio 2015 Update 3, Visual Studio 2017 nebo Visual Studio 2019. Nemáte žádné z těchto verzí? [Stáhněte si Visual Studio 2019 zdarma](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak ukázková aplikace vygenerovaná touto příručkou funguje

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

Ukázková aplikace, kterou vytvoříte pomocí této příručky, umožňuje desktopovou aplikaci pro Windows, která se dotazuje na rozhraní Microsoft Graph API nebo webové rozhraní API, které přijímá tokeny z koncového bodu Microsoft Identity-Platform. V tomto scénáři přidáte token do požadavků HTTP prostřednictvím autorizační hlavičky. Knihovna Microsoft Authentication Library (MSAL) zpracovává získání a obnovení tokenu.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Zpracování získání tokenu pro přístup k chráněným webovým rozhraním API

Po ověření uživatele obdrží ukázková aplikace token, který můžete použít k dotazování Microsoft Graph rozhraní API nebo webového rozhraní API, které je zabezpečené platformou Microsoft identity pro vývojáře.

Rozhraní API, například Microsoft Graph, vyžadují token, který umožňuje přístup ke konkrétním prostředkům. Například token je vyžadován ke čtení profilu uživatele, přístupu k kalendáři uživatele nebo k odeslání e-mailu. Vaše aplikace může požádat o přístupový token pomocí MSAL pro přístup k těmto prostředkům zadáním oborů rozhraní API. Tento přístupový token se pak přidá do hlavičky Authorization protokolu HTTP pro každé volání provedené proti chráněnému prostředku.

MSAL spravuje ukládání do mezipaměti a aktualizace přístupových tokenů za vás, takže vaše aplikace nemusí.

## <a name="nuget-packages"></a>Balíčky NuGet

Tato příručka používá následující balíčky NuGet:

|Knihovna|Popis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Knihovna Microsoft Authentication Library (MSAL.NET)|

## <a name="set-up-your-project"></a>Nastavení projektu

V této části vytvoříte nový projekt, který předvede integraci aplikace Windows Desktop .NET (XAML) s *přihlášením do společnosti Microsoft* , aby se aplikace mohla dotazovat webová rozhraní API, která vyžadují token.

Aplikace, kterou vytvoříte pomocí této příručky, zobrazuje tlačítko, které se používá k volání grafu, oblasti pro zobrazení výsledků na obrazovce a tlačítka pro odhlášení.

> [!NOTE]
> Chcete raději stáhnout tuto ukázkovou aplikaci Visual Studio? [Stáhněte si projekt](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)a přejděte k [konfiguračnímu kroku](#register-your-application) , abyste před spuštěním nakonfigurovali ukázku kódu.
>

Chcete-li vytvořit aplikaci, postupujte následovně:

1. V sadě Visual Studio, vyberte **souboru** > **nový** > **projektu**.
2. V části **šablony**vyberte **možnost C#vizuál** .
3. Vyberte možnost **aplikace WPF (.NET Framework)** v závislosti na verzi sady Visual Studio, kterou používáte.

## <a name="add-msal-to-your-project"></a>Přidání MSAL do projektu

1. V sadě Visual Studio vyberte **Nástroje** > **Správce balíčků NuGet**> **Konzola správce balíčků**.
2. V okně konzoly Správce balíčků vložte následující příkaz Azure PowerShell:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > Tento příkaz nainstaluje knihovnu Microsoft Authentication Library. MSAL zpracovává získání, ukládání do mezipaměti a aktualizace uživatelských tokenů, které se používají pro přístup k rozhraním API chráněným Azure Active Directory v 2.0
    >

## <a name="register-your-application"></a>Registrace vaší aplikace

Aplikaci můžete zaregistrovat oběma způsoby.

### <a name="option-1-express-mode"></a>Možnost 1: Expresní režim

Aplikaci můžete rychle zaregistrovat pomocí následujícího postupu:
1. Přejít k [registraci aplikace Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.

### <a name="option-2-advanced-mode"></a>Možnost 2: rozšířený režim

Postup při registraci aplikace a přidání informací o registraci aplikace k řešení:
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
1. Přejděte na stránku [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) Microsoft Identity Platform for Developers.
1. Vyberte **Nová registrace**.
   - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `Win-App-calling-MsGraph`.
   - V části **Podporované typy účtu** vyberte **Účty v libovolném organizačním adresáři a osobní účty Microsoft (například Skype, Xbox, Outlook.com)** .
   - Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. V seznamu stránek pro aplikaci vyberte **Ověřování**.
   1. V části **identifikátory URI pro přesměrování** v seznamu identifikátorů URI pro přesměrování:
   1. Ve sloupci **typ** vyberte **veřejný klient/nativní (mobilní & Desktop)** .
   1. Do sloupce **URI pro přesměrování** zadejte `https://login.microsoftonline.com/common/oauth2/nativeclient`
1. Vyberte **Zaregistrovat**.
1. Do sady Visual Studio otevřete soubor *App.XAML.cs* a potom v následujícím fragmentu kódu nahraďte položku `Enter_the_Application_Id_here` ID aplikace, kterou jste právě zaregistrovali a zkopírovali.

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

Soubor *MainWindow. XAML* by měl být automaticky vytvořen jako součást šablony projektu. Otevřete tento soubor a potom nahraďte uzel *\<mřížka* vaší aplikace následujícím kódem:

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

2. Kód třídy `MainWindow` nahraďte následujícím kódem:

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

Volání metody `AcquireTokenInteractive` má za následek okno, které vyzve uživatele k přihlášení. Aplikace obvykle vyžadují, aby se uživatelé přihlásili interaktivně, když potřebují přístup k chráněnému prostředku. Můžou se taky muset přihlásit, když se tichá operace získání tokenu nezdařila (například když vypršela platnost hesla uživatele).

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

Metoda `AcquireTokenSilent` zpracovává získání a obnovení tokenů bez zásahu uživatele. Po prvním spuštění `AcquireTokenInteractive` `AcquireTokenSilent` je obvyklá metoda, jak použít k získání tokenů, které přistupují k chráněným prostředkům pro následná volání, protože volání požadavků na požadavky nebo obnovení tokenů se provádí v tichém režimu.

Nakonec nebude metoda `AcquireTokenSilent` úspěšná. Důvodem selhání může být to, že uživatel buď odhlásil nebo změnil heslo na jiném zařízení. Pokud MSAL zjistí, že problém lze vyřešit vyžadováním interaktivní akce, vyvolá výjimku `MsalUiRequiredException`. Vaše aplikace může tuto výjimku zpracovat dvěma způsoby:

* Může provést volání proti `AcquireTokenInteractive` okamžitě. Výsledkem tohoto volání je dotazování uživatele na přihlášení. Tento model se obvykle používá v online aplikacích, kde není k dispozici žádný offline obsah pro uživatele. Ukázka vygenerovaná tímto procesem instalace se řídí tímto modelem, který můžete vidět v akci při prvním spuštění ukázky. 

* Protože aplikace nepoužila žádného uživatele, `PublicClientApp.Users.FirstOrDefault()` obsahuje hodnotu null a je vyvolána výjimka `MsalUiRequiredException`. 

* Kód v ukázce pak zpracovává výjimku voláním `AcquireTokenInteractive`, což má za následek zobrazení výzvy uživateli, aby se přihlásil.

* Může místo toho prezentovat vizuální indikaci, že se vyžaduje interaktivní přihlášení, aby mohli vybrat správný čas pro přihlášení. Nebo může aplikace opakovat `AcquireTokenSilent` později. Tento model se často používá, když uživatelé můžou používat jiné funkce aplikace bez přerušení – například když je offline obsah k dispozici v aplikaci. V takovém případě se uživatelé můžou rozhodnout, kdy se chtějí přihlásit k chráněnému prostředku nebo aktualizovat zastaralé informace. Případně se může aplikace pokusit o opakování `AcquireTokenSilent` po obnovení sítě po dočasné nedostupnosti.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Volání rozhraní API Microsoft Graph pomocí tokenu, který jste právě získali

Do `MainWindow.xaml.cs`přidejte následující novou metodu. Metoda se používá k vytvoření žádosti `GET` proti Graph API pomocí autorizační hlavičky:

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
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Další informace o tom, jak provést volání REST proti chráněnému rozhraní API

V této ukázkové aplikaci pomocí metody `GetHttpContentWithToken` vytvoříte požadavek HTTP `GET` proti chráněnému prostředku, který vyžaduje token, a potom vrátíte obsah volajícímu. Tato metoda přidá získaný token v autorizační hlavičce protokolu HTTP. V této ukázce je prostředkem koncový bod Microsoft Graph API *já* , který zobrazuje informace o profilu uživatele.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Přidání metody pro odhlášení uživatele

Pokud se chcete odhlásit uživatele, přidejte do souboru `MainWindow.xaml.cs` následující metodu:

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

Metoda `SignOutButton_Click` odebere uživatele z mezipaměti uživatelů MSAL, což efektivně informuje MSAL o zapomenutí aktuálního uživatele, takže budoucí požadavek na získání tokenu bude úspěšný pouze v případě, že bude vytvořen jako interaktivní.

I když aplikace v této ukázce podporuje jednotlivé uživatele, MSAL podporuje scénáře, ve kterých může být současně přihlášeno více účtů. Příkladem je e-mailová aplikace, kde má uživatel více účtů.
<!--end-collapse-->

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

<!--start-collapse-->
### <a name="more-information"></a>Další informace

Kromě přístupového tokenu, který se používá k volání rozhraní Microsoft Graph API po přihlášení uživatele, MSAL také získá token ID. Tento token obsahuje malou podmnožinu informací, které jsou relevantní pro uživatele. Metoda `DisplayBasicTokenInfo` zobrazí základní informace, které jsou obsaženy v tokenu. Například zobrazuje zobrazované jméno a ID uživatele a také datum vypršení platnosti tokenu a řetězec představující samotný přístupový token. Můžete vybrat tlačítko *rozhraní API pro volání Microsoft Graph* několikrát a zjistit, že se stejný token znovu použil pro následné požadavky. Můžete také zobrazit datum vypršení platnosti, kdy MSAL rozhodne, že je čas obnovit token.
<!--end-collapse-->

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

Pomůžeme nám vylepšit platformu Microsoft identity. Řekněte nám, co si myslíte, díky krátkému průzkumu dvou dotazů.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform Survey](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
