---
title: Azure AD v2 UWP Začínáme | Microsoft Docs
description: Jak aplikace pro univerzální platformu Windows (XAML) můžete volat rozhraní API, které vyžadují přístupové tokeny bodem v2 Azure Active Directory
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/20/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 4db14bc250bf9d6740380f3c4376f43d6f315b01
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Volání rozhraní Microsoft Graph API z aplikace pro univerzální platformu Windows (UWP)

Tato příručka ukazuje, jak získat přístupový token a pak použít tento toke přístup k volání Microsoft Graph API nebo jiná rozhraní API, které vyžadují přístupové tokeny z koncového bodu Azure Active Directory v2 nativní aplikace pro univerzální platformu Windows (XAML).

Na konci tohoto průvodce bude moci volat chráněné API používání osobních účtů (včetně live.com, outlook.com a dalších) a také pracovní a školní účty z jakékoli společnosti nebo organizace, která má Azure Active Directory vaší aplikace.  

> Tato příručka vyžaduje Visual Studio 2017 s vývojem pro univerzální platformu Windows nainstalována. Zaškrtněte toto políčko [článku](https://docs.microsoft.com/windows/uwp/get-started/get-set-up "sady Visual Studio pro UPW") pokyny o tom, jak stáhnout a nakonfigurovat Visual Studio pro vývoj univerzálních aplikací pro platformu Windows.

### <a name="how-this-guide-works"></a>Jak funguje tato příručka

![Jak funguje tato příručka](media/active-directory-mobileanddesktopapp-windowsuniversalplatform-introduction/uwp-intro.png)

Ukázkové aplikace vytvořené v této příručce umožňuje univerzální platformu Windows aplikace k dotazování Microsoft Graph API nebo webového rozhraní API, které přijímá tokeny z koncového bodu v2 Azure Active Directory. V tomto scénáři se token přidá na požadavky HTTP přes autorizační hlavičky. Získání tokenu a obnovení, jsou zpracovávány pomocí knihovny Microsoft ověřování (MSAL).

### <a name="nuget-packages"></a>Balíčky NuGet

Tato příručka používá následující balíčky NuGet:

|Knihovna|Popis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Knihovna Microsoft ověřování (MSAL)|


## <a name="set-up-your-project"></a>Nastavení projektu

Tato část obsahuje podrobné pokyny pro vytvoření nového projektu do ukazují, jak integrovat aplikace Windows Desktop .NET (XAML) s *přihlášení se společností Microsoft* , může se dotázat webovým rozhraním API, které vyžadují token.

Aplikace vytvořené v této příručce zpřístupní tlačítko graf a zobrazit výsledky na obrazovce a odhlášení tlačítko.

> Místo toho stáhněte projekt Visual Studio Tato ukázka dávají přednost? [Stažení projektu](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip) a pokračujte [registrace aplikace](#register-your-application "kroku registrace aplikace") krok před provedením konfigurace ukázka kódu.


### <a name="create-your-application"></a>Vytvoření aplikace
1. V sadě Visual Studio: **soubor** > **nové** > **projektu**<br/>
2. V části *šablony*, vyberte **Visual C#**
3. Vyberte **prázdná aplikace (univerzální pro Windows)**
4. Pojmenujte ho a klikněte na 'Ok'.
5. Pokud se zobrazí výzva, klesl volné a vyberte všechny verze pro *cíl* a *minimální* verzi a klikněte na 'Ok':<br/><br/>![Minimální a cílové verze](media/active-directory-uwp-v2.md/vs-minimum-target.png)

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>Do projektu přidejte knihovny ověřování společnosti Microsoft (MSAL)
1. V sadě Visual Studio: **nástroje** > **Správce balíčků Nuget** > **Konzola správce balíčků**
2. Zkopírujte a vložte následující příkaz v okně konzoly Správce balíčků:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

> [!NOTE]
> Balíček výše nainstaluje [Microsoft ověřování knihovny (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet). MSAL zpracovává získávání, ukládání do mezipaměti a aktualizaci tokeny uživatel používá pro přístup k rozhraní API, které jsou chráněné službou Azure Active Directory v2.

## <a name="initialize-msal"></a>Inicializace MSAL
Tento krok vám pomůže vytvořit třídu pro zpracování interakci s MSAL knihovnu, jako je například zpracování tokenů.

1. Otevřete **App.xaml.cs** souboru a odkaz na MSAL knihovny přidejte do třídy:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Přidejte následující dva řádky do třídy aplikace (uvnitř <code>sealed partial class App : Application</code> bloku):

    ```csharp
    //Below is the clientId of your app registration. 
    //You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

## <a name="create-your-applications-ui"></a>Vytvoření uživatelského rozhraní aplikace

A **MainPage.xaml** soubor by měl být automaticky vytvoří jako součást vaše šablona projektu. Umožňuje otevřít tento soubor a pak postupujte podle pokynů:

1.  Nahraďte aplikace **<Grid>** uzel s:

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
    
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Použití knihovny ověřování společnosti Microsoft (MSAL) k získání tokenu pro rozhraní Microsoft Graph API

V této části ukazuje, jak používat MSAL k získání tokenu pro rozhraní Microsoft Graph API.

1.  V **MainPage.xaml.cs**, přidejte odkaz na MSAL knihovny do třídy:

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. Nahraďte kód vaší <code>MainPage</code> třídy následujícím kódem:

    ```csharp
    public sealed partial class MainPage : Page
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";
    
        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };
    
        public MainPage()
        {
            this.InitializeComponent();
        }
    
        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;
    
            try
            {
                authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, App.PublicClientApp.Users.FirstOrDefault());
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
    
                try
                {
                    authResult = await App.PublicClientApp.AcquireTokenAsync(scopes);
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
#### <a name="get-a-user-token-interactively"></a>Získání tokenu uživatele interaktivně
Volání `AcquireTokenAsync` metoda výsledky v okně, které vyzve uživatele k přihlášení. Aplikace obvykle vyžadují uživatelům interaktivní přihlášení při prvním potřebují přístup k chráněnému prostředku. Potřebují může také k přihlášení během bezobslužné operace k získání tokenu selže (například pokud vypršela platnost hesla uživatele).

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění
`AcquireTokenSilentAsync` Metoda zpracovává tokenu pořízení a obnovení bez nutnosti zásahu uživatele. Po `AcquireTokenAsync` se spustí poprvé, `AcquireTokenSilentAsync` je obvykle způsob, kterým se získat tokeny, které přístup k chráněným prostředkům pro následující volání, protože k vyžádání nebo obnovení tokeny volání bezobslužně.

Nakonec `AcquireTokenSilentAsync` metoda se nezdaří. Příčiny chyby může být, že uživatel má odhlášení nebo změnit své heslo na jiném zařízení. Když MSAL zjistí, že problém lze vyřešit tím, že vyžaduje interaktivní akce, aktivuje se ho `MsalUiRequiredException` výjimka. Aplikace může zpracovat výjimku dvěma způsoby:

* Provádět volání proti `AcquireTokenAsync` okamžitě. Toto volání se výsledkem výzvy pro uživatele k přihlášení. Tento vzor se obvykle používá v online aplikace tam, kde není žádná dostupné offline obsah pro uživatele. Ukázka generované touto s průvodcem instalací následuje tento vzor, který se zobrazí v čase akce první spuštění ukázky. 
    * Vzhledem k tomu, že se žádný uživatel používá aplikaci, `PublicClientApp.Users.FirstOrDefault()` obsahuje hodnotu null a `MsalUiRequiredException` je vyvolána výjimka. 
    * Kód v ukázce pak ošetří výjimku voláním `AcquireTokenAsync`, výsledkem výzvy pro uživatele k přihlášení.

* Vizuální označení se místo toho může být pro uživatele, kteří interaktivní přihlášení je povinné, tak, aby mohou vybrat správný čas k přihlášení. Nebo můžete zkusit aplikaci `AcquireTokenSilentAsync` později. Tento vzor se často používá, kdy mohou uživatelé používat další funkce aplikace bez přerušení – například, když offline obsah je dostupný v aplikaci. V takovém případě uživatelé mohou rozhodnout, když chtějí Přihlaste se k přístupu k chráněnému prostředku nebo aktualizovat zastaralé informace. Alternativně můžete určit aplikace, aby opakujte `AcquireTokenSilentAsync` obnovení po síti poté, co bylo dočasně nedostupná.

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Volání rozhraní Graph API Microsoft pomocí tokenu, který jste obdrželi

1. Přidejte následující metodu nové do vaší **MainPage.xaml.cs**. Aby se používá metoda `GET` požadavek na rozhraní Graph API pomocí hlavičku autorizovat:

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Další informace o volání REST chráněné rozhraní API

V této ukázkové aplikaci `GetHttpContentWithToken` metoda se používá k zajištění HTTP `GET` požadavku pro chráněný prostředek, který vyžaduje token a pak se vraťte obsah volajícímu. Tato metoda přidá získal token v *HTTP autorizační hlavičky*. Tato ukázka je prostředek Microsoft Graph API *mi* koncového bodu – zobrazí informace o profilu uživatele.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Přidání metody se odhlásit uživatele

1. Odhlášení uživatele, přidejte následující metodu do **MainPage.xaml.cs**:

    ```csharp
    /// <summary>
    /// Sign out the current user
    /// </summary>
    private void SignOutButton_Click(object sender, RoutedEventArgs e)
    {
        if (App.PublicClientApp.Users.Any())
        {
            try
            {
                App.PublicClientApp.Remove(App.PublicClientApp.Users.FirstOrDefault());
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

### <a name="more-info-on-sign-out"></a>Další informace o odhlášení

Metoda `SignOutButton_Click` odebere uživatele z mezipaměti uživatele MSAL – sdělí efektivně MSAL zapomenutí aktuálního uživatele, takže budoucí žádost o získání tokenu můžete úspěšné, pouze pokud je k interaktivní.
I když aplikace v této ukázce podporuje jenom jednoho konkrétního uživatele, MSAL podporuje scénáře, kde více účtů může být přihlášeného ve stejnou dobu – příklad je e-mailové aplikace, které má uživatel více účtů.

## <a name="display-basic-token-information"></a>Zobrazit základní informace o tokenu

1. Přidejte následující metodu do vaší **MainPage.xaml.cs** zobrazit základní informace o token:

    ```csharp
    /// <summary>
    /// Display basic information contained in the token
    /// </summary>
    private void DisplayBasicTokenInfo(AuthenticationResult authResult)
    {
        TokenInfoText.Text = "";
        if (authResult != null)
        {
            TokenInfoText.Text += $"Name: {authResult.User.Name}" + Environment.NewLine;
            TokenInfoText.Text += $"Username: {authResult.User.DisplayableId}" + Environment.NewLine;
            TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
            TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
        }
    }
    ```

### <a name="more-information"></a>Další informace

ID tokeny získaných prostřednictvím *OpenID Connect* také obsahovat malou podmnožinu informace, které jsou relevantní pro uživatele. `DisplayBasicTokenInfo` Zobrazí základních informací obsažených v tokenu: například uživatele zobrazovaný název a ID, a také datum vypršení platnosti tokenu a řetězec představující přístup token sám sebe. Zobrazí se tato informace je zobrazen. Můžete dosáhl **volání Microsoft Graph API** tlačítko víckrát a zjistí, že byl znovu stejný token pro následné požadavky. Zobrazí se také datum vypršení platnosti rozšiřovanou při MSAL rozhodne ho je čas k obnovení tokenu.

## <a name="register-your-application"></a>Registrace vaší aplikace

Nyní je nutné zaregistrovat aplikaci v *portálu pro registraci aplikace Microsoft*:
1. Přejděte na [portálu pro registraci aplikace Microsoft](https://apps.dev.microsoft.com/portal/register-app) zaregistrovat aplikaci
2. Zadejte název pro vaši aplikaci 
3. Ujistěte se, že není zaškrtnuto políčko pro instalaci na základě
4. Klikněte na tlačítko **přidat platformy**, pak vyberte **nativní aplikace** a klikněte na tlačítko Uložit
5. Zkopírovat identifikátor GUID v ID aplikace, přejděte zpět do Visual Studio, otevřete **App.xaml.cs** a nahraďte `your_client_id_here` s ID aplikace, který jste právě zaregistrovali:

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Povolit integrované ověřování na federované domény (volitelné)

Pokud chcete povolit integrované ověřování systému Windows při použití s federované domény Azure Active Directory, musíte povolit manifest aplikace další možnosti:

1. Dvakrát klikněte na tlačítko **Package.appxmanifest**
2. Vyberte **možnosti** kartě a ujistěte se, že jsou povoleny následující nastavení:

    - Enterprise Authentication
    - Privátní sítě (klient a Server)
    - Sdílené uživatelské certifikáty 

3. Potom otevřete **App.xaml.cs**a přidejte následující v konstruktoru aplikace:

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> Integrované ověřování systému Windows není nakonfigurována ve výchozím nastavení pro tuto ukázku, protože aplikace požaduje *Enterprise Authentication* nebo *sdílené uživatelské certifikáty* funkce vyžadují vyšší úroveň verificationby Windows Store a ne všechny vývojáře chcete vyšší úroveň ověřování. Povolte toto nastavení, pouze pokud je potřeba integrované ověřování systému Windows s federované domény Azure Active Directory.


## <a name="test-your-code"></a>Otestujte svůj kód

Chcete-li otestovat aplikaci, stiskněte klávesu `F5` spustit projekt v sadě Visual Studio. Hlavní okno by měl zobrazit:

![Uživatelské rozhraní aplikace](media/active-directory-uwp-v2.md/testapp-ui.png)

Až budete připraveni k testování, klikněte na tlačítko *volání Microsoft Graph API* a přihlaste se pomocí Microsoft Azure Active Directory (účet organizace) nebo účet Account Microsoft (live.com, outlook.com). Pokud je poprvé, zobrazí se okno s výzvou, uživatel k přihlášení:

![Přihlašovací stránka](media/active-directory-uwp-v2.md/sign-in-page.png)

### <a name="consent"></a>Vyjádřit souhlas.
Při prvním přihlášení do aplikace, se zobrazí obrazovky souhlasu podobná následující příkaz, kde je potřeba explicitně přijmout:

![Souhlas obrazovky](media/active-directory-uwp-v2.md/consentscreen.png)
### <a name="expected-results"></a>Očekávané výsledky
Zobrazí informace o profilu uživatele vrácený Microsoft Graph API volání na obrazovce výsledky volání rozhraní API:

![Výsledky obrazovky](media/active-directory-uwp-v2.md/uwp-results-screen.PNG)

Měli byste taky vidět základní informace o tokenu získaných prostřednictvím `AcquireTokenAsync` nebo `AcquireTokenSilentAsync` informace o tokenu pole:

|Vlastnost  |Formát  |Popis |
|---------|---------|---------|
|**Název** |Jméno a příjmení uživatele |Jméno a příjmení uživatele.|
|**Uživatelské jméno** |<span>user@domain.com</span> |Uživatelské jméno, který se používá k identifikaci uživatele.|
|**Platnost tokenu vyprší** |DateTime |Čas, kdy vyprší platnost tokenu. MSAL rozšiřuje datum vypršení platnosti obnovením token podle potřeby.|
|**Přístupový Token** |Řetězec |Řetězec tokenu, který je odeslán protokolu HTTP požadavků, které vyžadují *autorizační hlavičky*.|

#### <a name="see-what-is-in-the-access-token-optional"></a>Zjistit, co je v tokenu přístupu (volitelné)
Volitelně můžete zkopírovat hodnotu v přístupu k tokenu a vložte jej do https://jwt.ms dekódovat a zobrazit seznam deklarací identity.

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a přidělená oprávnění

Vyžaduje rozhraní Microsoft Graph API *user.read* obory a čtení profilu uživatele. Tento obor je automaticky přidán ve výchozím nastavení každá aplikace, které je registrované v portálu pro registraci aplikace. Ostatní rozhraní API pro Microsoft Graph, jakož i vlastní rozhraní API pro váš server back-end může vyžadovat další obory. Vyžaduje rozhraní Microsoft Graph API *Calendars.Read* oboru seznam kalendářích uživatele.

Chcete-li získat přístup k kalendářům uživatele v rámci aplikace, přidejte *Calendars.Read* delegovaná oprávnění k registraci informace o aplikaci. Poté, přidejte *Calendars.Read* obor na `acquireTokenSilent` volání. 

> [!NOTE]
> Uživatel může být vyzvání pro další souhlas všech uživatelů, jak zvýšit počet oborů.

## <a name="known-issues"></a>Známé problémy

### <a name="issue-1"></a>Problém 1:
Může zobrazit jedna z těchto chyb při přihlášení ve vaší aplikaci na federovanou doménu Active Directory Azure:
 - "V žádosti se našel žádný platný klientský certifikát.
 - Nenašly se žádné platné certifikáty v úložišti certifikátů uživatele.
 - Zkuste to prosím znovu výběr různá ověřovací metody. "

**Příčina:** funkce Enterprise a certifikáty nejsou povolené.

**Řešení:** postupujte podle kroků v [integrované ověřování na federované domény](#enable-integrated-authentication-on-federated-domains-optional)

### <a name="issue-2"></a>Problém 2:
Hřívací zařízení povolíte [integrované ověřování na federované domény](#enable-integrated-authentication-on-federated-domains-optional) a pokuste se použít Windows Hello na počítači s Windows 10 k přihlášení prostředí s více-factor-konfigurace ověřování, se zobrazí seznam certifikátů , ale pokud se rozhodnete použít svůj PIN kód, okno kód PIN nikdy zobrazí.

**Příčina:** jedná se o známé omezení s zprostředkovatele webového ověření v UWP aplikací běžících na Windows 10 desktop (funguje dobře v systému Windows 10 Mobile)

**Alternativní řešení:** jako alternativní řešení, uživatelé musí vybrat přihlásit pomocí jiné možnosti a potom vyberte *Přihlaste se pomocí uživatelského jména a hesla* místo toho vyberte zadejte heslo a potom projděte phone ověřování.

