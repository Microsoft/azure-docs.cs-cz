---
title: Azure AD v2 UWP Začínáme | Microsoft Docs
description: Jak aplikace pro univerzální platformu Windows (UWP) můžete volat rozhraní API, které vyžaduje přístupových tokenů v koncovém bodě v2 Azure Active Directory
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
ms.openlocfilehash: c2d5681e30651aac7a09a8ead923015e9a892d42
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796610"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Volání Microsoft Graph API z aplikace pro univerzální platformu Windows (XAML)

Tato příručka vysvětluje, jak požádat o token přístupu a pak zavolají Microsoft Graph API nativní aplikace pro univerzální platformu Windows (UWP). V Průvodci platí také pro jiná rozhraní API, které vyžadují přístupové tokeny z koncového bodu v2 Azure Active Directory.

Na konci tohoto průvodce aplikace volá chráněné rozhraní API pomocí osobní účty. Příklady jsou live.com, outlook.com a dalších. Aplikace také zavolat pracovním a školním účtům v jakémkoli společnosti nebo organizace, která má Azure Active Directory.

>[!NOTE]
> Tato příručka vyžaduje Visual Studio 2017 s vývojem pro univerzální platformu Windows nainstalována. V tématu [získat nastavení](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) pokyny týkající se stáhnout a nakonfigurovat Visual Studio pro vývoj aplikací pro univerzální platformu Windows.

### <a name="how-this-guide-works"></a>Jak funguje tato příručka

![Jak funguje grafu v této příručce](media/active-directory-mobileanddesktopapp-windowsuniversalplatform-introduction/uwp-intro.png)

Tento průvodce vytvoří ukázkovou aplikaci UPW, který se dotazuje rozhraní Microsoft Graph API nebo webového rozhraní API, které přijímá tokeny z koncového bodu v2 Azure Active Directory. V tomto scénáři se token přidá na požadavky HTTP přes autorizační hlavičky. Microsoft ověřování knihovny (MSAL) zpracovává tokenu pořízení a obnovení.

### <a name="nuget-packages"></a>Balíčky NuGet

Tato příručka používá následující balíčky NuGet:

|Knihovna|Popis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Knihovna Microsoft ověřování|


## <a name="set-up-your-project"></a>Nastavení projektu

Tato část obsahuje podrobné pokyny k integraci aplikace Windows Desktop .NET (XAML) s *přihlášení se společností Microsoft*. Potom můžete zadat dotaz webovým rozhraním API, které vyžadují token, jako je například Microsoft Graph API.

Tento průvodce vytvoří aplikaci, která se zobrazí tlačítko této dotazů rozhraní Graph API, odhlašování tlačítka a textová pole, které zobrazují výsledky volání.

>[!NOTE]
> Opravdu chcete stáhnout projekt Visual Studio Tato ukázka místo? [Stažení projektu](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip) a pokračujte [registrace aplikace](#register-your-application "kroku registrace aplikace") krok konfigurace ukázka kódu před jeho spuštěním.


### <a name="create-your-application"></a>Vytvoření aplikace
1. V sadě Visual Studio, vyberte **soubor** > **nový** > **projektu**.
2. V části **šablony**, vyberte **Visual C#**.
3. Vyberte **Prázdná aplikace (univerzální pro Windows)**.
4. Název aplikace a vyberte **OK**.
5. Pokud se zobrazí výzva, vyberte všechny verze pro **cíl** a **minimální** verze a vyberte **OK**.

    >![Minimální a cílové verze](media/active-directory-uwp-v2.md/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Do projektu přidejte knihovny pro ověřování Microsoft
1. V sadě Visual Studio, vyberte **nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**.
2. Zkopírujte a vložte následující příkaz v **Konzola správce balíčků** okno:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

> [!NOTE]
> Tento příkaz nainstaluje [knihovny pro ověřování Microsoft](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet). MSAL získá, ukládá do mezipaměti a aktualizuje uživatele tokeny, které přístup k rozhraní API, které jsou chráněné službou Azure Active Directory v2.

## <a name="initialize-msal"></a>Inicializace MSAL
Tento krok vám pomůže vytvořit třídu pro zpracování interakci s MSAL, jako je například zpracování tokeny.

1. Otevřete **App.xaml.cs** souboru a přidat odkaz pro MSAL pro třídu:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Přidejte následující dva řádky do třídy aplikace (uvnitř <code>sealed partial class App : Application</code> bloku):

    ```csharp
    // Below is the clientId of your app registration. 
    // You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

## <a name="create-your-applications-ui"></a>Vytvoření uživatelského rozhraní aplikace

A **MainPage.xaml** soubor se vytvoří automaticky v rámci šablony projektu. Umožňuje otevřít tento soubor a pak postupujte podle pokynů:

* Nahraďte aplikace **mřížky** uzlu s následujícím kódem:

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
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Použít MSAL k získání tokenu pro Microsoft Graph API

V této části ukazuje, jak používat MSAL k získání tokenu pro Microsoft Graph API.

1.  V **MainPage.xaml.cs**, přidejte odkaz na pro MSAL do třídy:

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. Nahraďte kód vaší <code>MainPage</code> třídy následujícím kódem:

    ```csharp
    public sealed partial class MainPage : Page
    {
        // Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";
    
        // Set the scope for API call to user.read
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
Volání `AcquireTokenAsync` metoda výsledky v okně, které vyzve uživatele k přihlášení. Aplikace obvykle vyžadují uživatelům interaktivní přihlášení při prvním potřebují přístup k chráněnému prostředku. Potřebují může také k přihlášení během bezobslužné operace získat token se nezdaří. Je například pokud vypršela platnost hesla uživatele.

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění
`AcquireTokenSilentAsync` Metoda zpracovává tokenu pořízení a obnovení bez nutnosti zásahu uživatele. Po `AcquireTokenAsync` se spustí při prvním a uživatel je vyzván k zadání přihlašovacích údajů, `AcquireTokenSilentAsync` metoda má být použita pro požadavek tokeny pro následující volání, protože získávat tokeny bezobslužně. MSAL zpracuje mezipamětí tokenů a obnovení.

Nakonec `AcquireTokenSilentAsync` metoda selže. Příčiny chyby může být, že uživatelé mají buď odhlášení nebo změnit své heslo na jiném zařízení. Když MSAL zjistí, že problém lze vyřešit tím, že vyžaduje interaktivní akce, aktivuje se ho `MsalUiRequiredException` výjimka. Aplikace může zpracovat výjimku dvěma způsoby:

* Provádět volání proti `AcquireTokenAsync` okamžitě. Toto volání se výsledkem výzvy pro uživatele k přihlášení. Za normálních okolností se tento vzor používá v online aplikace tam, kde není žádná dostupné offline obsah pro uživatele. Následující ukázka generované touto s průvodcem instalací. Zobrazení v době akce první spuštěním ukázky. 
    * Vzhledem k tomu, že se žádný uživatel používá aplikaci, `PublicClientApp.Users.FirstOrDefault()` obsahuje hodnotu null a `MsalUiRequiredException` je vyvolána výjimka.
    * Kód v ukázce pak ošetří výjimku voláním `AcquireTokenAsync`. Toto volání se výsledkem výzvy pro uživatele k přihlášení.

* Nebo místo toho uvede vizuální označení pro uživatele, že je požadováno interaktivní přihlášení. Pak mohou vybrat správný čas k přihlášení. Nebo můžete zkusit aplikaci `AcquireTokenSilentAsync` později. Tento vzor často, se používá, když uživatelé mohou používat další funkce aplikace bez přerušení. Příkladem je při offline obsah je dostupný v aplikaci. V takovém případě uživatelé mohou rozhodnout, když chtějí Přihlaste se k přístupu k chráněnému prostředku nebo aktualizovat zastaralé informace. Nebo jiné aplikace můžete rozhodnout, opakujte `AcquireTokenSilentAsync` když se v síti obnoví po byl dočasně nedostupná.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Pro token, který jste obdrželi volání Microsoft Graph API

* Přidat novou metodu **MainPage.xaml.cs**. Tato metoda se používá, chcete-li `GET` požadavek na rozhraní Graph API pomocí hlavičku [Authorize]:

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
            // Add the token in Authorization header
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

V této ukázkové aplikaci `GetHttpContentWithToken` metoda se používá k zajištění HTTP `GET` požadavku vůči chráněný prostředek, který vyžaduje token. Metoda pak vrátí obsah volajícímu. Tato metoda přidá získal token v **HTTP autorizace** záhlaví. Tato ukázka je prostředek Microsoft Graph API **mi** koncový bod, který zobrazí informace o profilu uživatele.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Přidání metody se odhlásit uživatele

* Odhlášení uživatele, přidejte následující metodu do **MainPage.xaml.cs**:

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

### <a name="more-information-on-sign-out"></a>Další informace o odhlášení

`SignOutButton_Click` Metoda odebere uživatele z mezipaměti MSAL uživatele. Tato metoda efektivně informuje MSAL zapomenutí aktuálního uživatele. Potom budoucí žádost o získání tokenu úspěšné pouze v případě, že udělal interaktivní.
Aplikace v této ukázce podporuje jenom jednoho konkrétního uživatele. Ale MSAL podporuje scénáře, kde můžete více než jeden účet přihlášení ve stejnou dobu. Příkladem je e-mailové aplikace, kde uživatel, který má několik účtů.

## <a name="display-basic-token-information"></a>Zobrazit základní informace o tokenu

* Přidejte následující metodu do **MainPage.xaml.cs** zobrazit základní informace o token:

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

ID tokeny získaných prostřednictvím **OpenID Connect** také obsahovat malou podmnožinu informace, které jsou relevantní pro uživatele. `DisplayBasicTokenInfo` Zobrazí základních informací obsažených v tokenu. Příklady jsou uživatele zobrazovaný název a ID, datum vypršení platnosti tokenu a řetězec, který reprezentuje přístupový token, sám sebe. Pokud jste vybrali **volání Microsoft Graph API** tlačítko několikrát, uvidíte, že stejný token byl znovu použít pro následné požadavky. Zobrazí se také datum vypršení platnosti rozšířené při MSAL rozhodne, že je na čase obnovte token.

## <a name="register-your-application"></a>Registrace vaší aplikace

Nyní je třeba registrace vaší aplikace v portálu pro registraci aplikace společnosti Microsoft:
1. Přejděte na [portálu pro registraci aplikace Microsoft](https://apps.dev.microsoft.com/portal/register-app) zaregistrovat aplikaci.
2. Zadejte název pro vaši aplikaci.
3. Ujistěte se, že možnost pro **instalace na základě** je *není vybrán*.
4. Vyberte **přidat platformy**, vyberte **nativní aplikace**a potom vyberte **Uložit**.
5. Zkopírovat identifikátor GUID v **ID aplikace**, přejděte zpět na Visual Studio, otevřete **App.xaml.cs**a nahraďte `your_client_id_here` s ID aplikace, který jste právě zaregistrovali:

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Povolit integrované ověřování na federované domény (volitelné)

Chcete-li povolit integrované ověřování systému Windows, pokud se používá s federované domény Azure Active Directory, musíte povolit manifest aplikace další možnosti:

1. Klikněte dvakrát na **Package.appxmanifest**.
2. Vyberte **možnosti** kartě a ujistěte se, že jsou povoleny následující nastavení:

    - Enterprise Authentication
    - Privátní sítě (klient a Server)
    - Sdílené uživatelské certifikáty

3. Otevřete **App.xaml.cs** a přidejte následující řádek v konstruktoru aplikace:

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> Ve výchozím nastavení tato ukázka není nakonfigurované integrované ověřování systému Windows. Aplikace, které požadují *Enterprise Authentication* nebo *sdílené uživatelské certifikáty* možnosti vyžadují vyšší úroveň ověřování ve Windows Store. Ne všechny vývojáře také chtít vyšší úroveň ověřování. Toto nastavení povolte, pouze pokud je potřeba integrované ověřování systému Windows s federované domény Azure Active Directory.


## <a name="test-your-code"></a>Otestujte svůj kód

Chcete-li otestovat aplikaci, vyberte F5 a spusťte projekt v sadě Visual Studio. Hlavní okno se zobrazí:

![Uživatelské rozhraní aplikace](media/active-directory-uwp-v2.md/testapp-ui.png)

Až budete připraveni k testování, vyberte **volání Microsoft Graph API**. Pak pomocí účtu organizace Microsoft Azure Active Directory nebo účet Microsoft, jako je live.com nebo outlook.com, přihlaste se. Pokud je poprvé, zobrazí se okno s dotazem k přihlášení:

![Přihlašovací stránka](media/active-directory-uwp-v2.md/sign-in-page.png)

### <a name="consent"></a>Souhlas
Při prvním přihlášení k aplikaci, nabízí souhlasu obrazovky, který je podobný následujícímu. Vyberte **Ano** o explicitně souhlas pro přístup:

![Obrazovka souhlasu přístupu](media/active-directory-uwp-v2.md/consentscreen.png)
### <a name="expected-results"></a>Očekávané výsledky
Zobrazí informace o profilu uživatele vrátila voláním Microsoft Graph API **výsledky volání rozhraní API** obrazovky:

![Výsledky volání rozhraní API obrazovky](media/active-directory-uwp-v2.md/uwp-results-screen.PNG)

Můžete také zobrazit základní informace o tokenu získaných prostřednictvím `AcquireTokenAsync` nebo `AcquireTokenSilentAsync` v **informace o tokenu** pole:

|Vlastnost  |Formát  |Popis |
|---------|---------|---------|
|**Název** |Jméno a příjmení uživatele|Jméno a příjmení uživatele.|
|**Uživatelské jméno** |<span>user@domain.com</span> |Uživatelské jméno, které identifikují uživatele.|
|**Platnost tokenu vyprší** |DateTime |Čas, kdy vyprší platnost tokenu. MSAL rozšiřuje datum vypršení platnosti obnovením token podle potřeby.|
|**Přístupový Token** |Řetězec |Řetězec tokenu, který je odeslán protokolu HTTP požadavků, které vyžadují *autorizační hlavičky*.|

#### <a name="see-whats-in-the-access-token-optional"></a>Zjistit, co je v tokenu přístupu (volitelné)
Volitelně můžete zkopírovat hodnotu v **tokenu přístupu** a vložte jej do https://jwt.ms dekódovat a zobrazit seznam deklarací identity.

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a přidělená oprávnění

Vyžaduje Microsoft Graph API *user.read* obory a čtení profilu uživatele. Tento obor je automaticky přidán ve výchozím nastavení každá aplikace, které je registrované v portálu pro registraci aplikace. Ostatní rozhraní API pro Microsoft Graph a vlastní rozhraní API pro váš server back-end může vyžadovat další obory. Vyžaduje Microsoft Graph API *Calendars.Read* oboru seznam kalendářích uživatele.

Chcete-li získat přístup k kalendářům uživatele v rámci aplikace, přidejte *Calendars.Read* delegovaná oprávnění k registraci informace o aplikaci. Pak přidejte *Calendars.Read* obor na `acquireTokenSilent` volání. 

> [!NOTE]
> Uživatelé může se zobrazit výzva pro další souhlas všech uživatelů, jak zvýšit počet oborů.

## <a name="known-issues"></a>Známé problémy

### <a name="issue-1"></a>Problém 1
Zobrazí jednu z následujících chybových zpráv při přihlášení ve vaší aplikaci federované domény Azure Active Directory:
 - V žádosti se našel žádný platný klientský certifikát.
 - Nenašly se žádné platné certifikáty v úložišti certifikátů uživatele.
 - Zkuste to znovu výběr různá ověřovací metody.

**Příčina:** nejsou povoleny možnosti organizace a certifikátu.

**Řešení:** postupujte podle kroků v [integrované ověřování na federované domény](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problém 2
Povolíte [integrované ověřování na federované domény](#enable-integrated-authentication-on-federated-domains-optional) a pokuste se použít Windows Hello na počítači s Windows 10 na přihlášení prostředí s konfigurace vícefaktorového ověřování. Zobrazí seznam certifikátů. Ale pokud se rozhodnete použít svůj PIN kód, okno kód PIN nikdy zobrazí.

**Příčina:** tento problém se o známé omezení zprostředkovatele webového ověření v aplikacích pro UPW, které běží na Windows 10 desktop. V systému Windows 10 Mobile pracuje správně.

**Alternativní řešení:** vyberte **přihlásit pomocí jiné možnosti**. Potom vyberte **Přihlaste se pomocí uživatelského jména a hesla**. Vyberte **zadejte heslo**. Potom projděte proces ověřování phone.
