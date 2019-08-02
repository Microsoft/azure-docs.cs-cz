---
title: Začínáme s Microsoft Identity platformou UWP | Azure
description: Jak aplikace Univerzální platforma Windows (UWP) můžou volat rozhraní API, které vyžaduje přístupové tokeny koncovým bodem Microsoft Identity Platform.
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dae3f9f2f4a9f6966d3453123773887c244cf4e2
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334072"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Volání rozhraní API Microsoft Graph z aplikace Univerzální platforma Windows (XAML)

> [!div renderon="docs"]

Tato příručka vysvětluje, jak může nativní aplikace Univerzální platforma Windows (UWP) požádat o přístupový token a pak volat rozhraní API Microsoft Graph. Tato příručka platí i pro další rozhraní API, která vyžadují přístupové tokeny z koncového bodu Microsoft Identity Platform.

Na konci tohoto průvodce vaše aplikace volá chráněné rozhraní API pomocí osobních účtů. Příklady jsou outlook.com, live.com a další. Vaše aplikace také volá pracovní a školní účty z libovolné společnosti nebo organizace, která má Azure Active Directory (Azure AD).

>[!NOTE]
> Tato příručka vyžaduje Visual Studio 2017 s nainstalovanou Univerzální platforma Windows vývoj. Pokyny ke stažení a konfiguraci sady Visual Studio pro vývoj Univerzální platforma Windowsch aplikací najdete v tématu věnovaném [získání](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) .

## <a name="how-this-guide-works"></a>Jak funguje tento průvodce

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Tato příručka vytvoří ukázkovou aplikaci UWP, která se dotazuje Microsoft Graph rozhraní API nebo webovým rozhraním API, které přijímá tokeny z koncového bodu Microsoft Identity Platform. V tomto scénáři se token přidá do požadavků HTTP přes autorizační hlavičku. Knihovna Microsoft Authentication Library (MSAL) zpracovává získání a obnovení tokenů.

## <a name="nuget-packages"></a>Balíčky NuGet

Tato příručka používá následující balíčky NuGet:

|Knihovna|Popis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Identity a ověřování Microsoftu|

## <a name="set-up-your-project"></a>Nastavení projektu

Tato část obsahuje podrobné pokyny pro integraci aplikace Windows Desktop .NET (XAML) s přihlašováním *s Microsoftem*. Pak se může dotazovat na webová rozhraní API, která vyžadují token, například Microsoft Graph API.

Tato příručka vytvoří aplikaci, která zobrazí tlačítko, které se dotazuje Graph API, tlačítko pro odhlášení a textová pole, která zobrazují výsledky volání.

> [!NOTE]
> Chcete místo toho stáhnout tuto ukázkovou aplikaci Visual Studio? [Stáhněte si projekt](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) a přejděte ke kroku [](#register-your-application "registrace") aplikace registrace aplikace, abyste před spuštěním nakonfigurovali vzorek kódu.

### <a name="create-your-application"></a>Vytvoření aplikace

1. V sadě Visual Studio, vyberte **souboru** > **nový** > **projektu**.
2. V části **šablony**vyberte **možnost C#vizuál** .
3. Vyberte **Prázdná aplikace (univerzální pro Windows)** .
4. Pojmenujte aplikaci a vyberte **OK**.
5. Pokud se zobrazí výzva, vyberte verzi pro **cíl** a **minimální** verzi a vyberte **OK**.

    >![Minimální a cílové verze](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Přidat do projektu knihovnu Microsoft Authentication Library
1. V sadě Visual Studio vyberte **Nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků**.
2. Zkopírujte a vložte následující příkaz v okně **konzoly Správce balíčků** :

    ```powershell
    Install-Package Microsoft.Identity.Client -IncludePrerelease
    ```

> [!NOTE]
> Tento příkaz nainstaluje [knihovnu Microsoft Authentication Library](https://aka.ms/msal-net). MSAL získává, ukládá do mezipaměti a aktualizuje tokeny uživatelů, které přistupují k rozhraním API chráněným platformou Microsoft identity.

## <a name="create-your-applications-ui"></a>Vytvoření uživatelského rozhraní aplikace

Soubor **MainPage. XAML** se vytvoří automaticky jako součást šablony projektu. Otevřete tento soubor a pak postupujte podle pokynů:

* Nahraďte uzel **mřížky** vaší aplikace následujícím kódem:

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
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Získání tokenu pro rozhraní Microsoft Graph API pomocí MSAL

V této části se dozvíte, jak pomocí MSAL získat token pro rozhraní Microsoft Graph API.

1.  Do **MainPage.XAML.cs**přidejte odkaz pro MSAL do třídy:

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

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - Te content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use organizations
        //   - for any Work or School accounts, or Microsoft personal account, use common
        //   - for Microsoft Personal account, use consumers
        private const string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";        

        public IPublicClientApplication PublicClientApp { get; } 

        public MainPage()
        {
          this.InitializeComponent();

          PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AadAuthorityAudience.AzureAdAndPersonalMicrosoftAccount)
                .WithLogging((level, message, containsPii) =>
                {
                    Debug.WriteLine($"MSAL: {level} {message} ");
                }, LogLevel.Warning, enablePiiLogging:false,enableDefaultPlatformLogging:true)
                .WithUseCorporateNetwork(true)
                .Build();
        }

        /// <summary>
        /// Call AcquireTokenInteractive - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
         AuthenticationResult authResult = null;
         ResultText.Text = string.Empty;
         TokenInfoText.Text = string.Empty;

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
          // A MsalUiRequiredException happened on AcquireTokenSilent.
          // This indicates you need to call AcquireTokenInteractive to acquire a token
          System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

          try
          {
           authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                      .ExecuteAsync()
                                                      .ConfigureAwait(false);
           }
           catch (MsalException msalex)
           {
            await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalex}");
           }
          }
          catch (Exception ex)
          {
           await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
           return;
          }

          if (authResult != null)
          {
           var content = await GetHttpContentWithToken(graphAPIEndpoint,
                                                       authResult.AccessToken).ConfigureAwait(false);

           // Go back to the UI thread to make changes to the UI
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
            ResultText.Text = content;
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
           });
          }
        }
    ```

### <a name="more-information"></a>Další informace

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Výsledkem volání `AcquireTokenInteractive` metody je okno, které vyzve uživatele k přihlášení. Aplikace obvykle vyžadují, aby se uživatelé přihlásili interaktivně, když potřebují přístup k chráněnému prostředku. Můžou se taky muset přihlásit, když se nezdařila tichá operace získání tokenu. Příkladem je vypršení platnosti hesla uživatele.

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

`AcquireTokenSilent` Metoda zpracovává získání a obnovení tokenů bez zásahu uživatele. Po `AcquireTokenInteractive` prvním spuštění se uživateli zobrazí výzva k zadání přihlašovacích údajů `AcquireTokenSilent` , metoda by se měla použít k vyžádání tokenů pro následná volání, protože získává tokeny v tichém režimu. MSAL zpracuje mezipaměť a obnovení tokenu.

Nakonec se `AcquireTokenSilent` metoda nezdařila. Důvody k selhání můžou být, že se uživatelé buď odhlásili nebo změnili heslo na jiném zařízení. Pokud MSAL zjistí, že problém lze vyřešit vyžadováním interaktivní akce, vyvolá `MsalUiRequiredException` výjimku. Vaše aplikace může tuto výjimku zpracovat dvěma způsoby:

* Může provést volání proti `AcquireTokenInteractive` okamžitému. Výsledkem tohoto volání je dotazování uživatele na přihlášení. Obvykle se tento model používá v online aplikacích, kde pro uživatele není k dispozici žádný obsah offline. Ukázka vygenerovaná tímto průvodcem Setup se řídí vzorem. Při prvním spuštění ukázky se zobrazí v akci.
  * Vzhledem k tomu, že aplikace nepoužila žádného uživatele, `accounts.FirstOrDefault()` obsahuje hodnotu null `MsalUiRequiredException` a je vyvolána výjimka.
  * Kód v ukázce pak zpracovává výjimku voláním `AcquireTokenInteractive`. Výsledkem tohoto volání je dotazování uživatele na přihlášení.

* Nebo místo toho prezentuje vizuální indikaci, že se vyžaduje interaktivní přihlašování. Pak si můžou vybrat správný čas pro přihlášení. Nebo se aplikace může pokus `AcquireTokenSilent` opakovat později. Tento model se často používá, když uživatelé můžou používat jiné funkce aplikace bez přerušení. Příkladem je, když je offline obsah k dispozici v aplikaci. V takovém případě se uživatelé můžou rozhodnout, kdy se chtějí přihlásit k chráněnému prostředku nebo aktualizovat zastaralé informace. Nebo jinak aplikace může rozhodnout, že se `AcquireTokenSilent` bude opakovat po obnovení sítě, až bude dočasně nedostupné.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Volání rozhraní API Microsoft Graph pomocí tokenu, který jste právě získali

* Do **MainPage.XAML.cs**přidejte následující novou metodu. Tato metoda slouží k vytvoření `GET` požadavku na Graph API `Authorization` pomocí hlavičky:

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
            request.Headers.Authorization = 
              new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Další informace o tom, jak provést volání REST proti chráněnému rozhraní API

V této ukázkové aplikaci `GetHttpContentWithToken` metoda slouží k vytvoření požadavku HTTP `GET` proti chráněnému prostředku, který vyžaduje token. Pak metoda vrátí obsah volajícímu. Tato metoda přidá získaný token v autorizační hlavičce **protokolu HTTP** . V této ukázce je prostředkem koncový bod Microsoft Graph API **já** , který zobrazuje informace o profilu uživatele.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Přidejte metodu pro odhlášení uživatele.

* Pokud chcete uživatele odhlásit, přidejte následující metodu do **MainPage.XAML.cs**:

    ```csharp
    /// <summary>
    /// Sign out the current user
    /// </summary>
    private async void SignOutButton_Click(object sender, RoutedEventArgs e)
    {
        IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync
                                                              .ConfigureAwait(false);
        IAccount firstAccount = accounts.FirstOrDefault();

        try
        {
            await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
            await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
            {
                ResultText.Text = "User has signed-out";
                this.CallGraphButton.Visibility = Visibility.Visible;
                    this.SignOutButton.Visibility = Visibility.Collapsed;
                });
            }
            catch (MsalException ex)
            {
                ResultText.Text = $"Error signing-out user: {ex.Message}";
            }
        }
    ```

> [!NOTE]
> MSAL.NET používá asynchronní metody k získání tokenů nebo manipulaci s účty, a proto je nutné pořídit akce `Dispatcher.RunAsync`, které se zobrazí v uživatelském rozhraní, a to proto, a preventivní opatření pro volání`ConfigureAwait(false)`

### <a name="more-information-on-sign-out"></a>Další informace o odhlášení

`SignOutButton_Click` Metoda odebere uživatele z uživatelské mezipaměti MSAL. Tato metoda efektivně oznamuje MSAL, že aktuálnímu uživateli zapomene. Budoucí požadavek na získání tokenu bude úspěšný jenom v případě, že je vytvořený jako interaktivní.
Aplikace v této ukázce podporuje jednoho uživatele. Ale MSAL podporuje scénáře, ve kterých může být současně přihlášen více než jeden účet. Příkladem je e-mailová aplikace, kde má uživatel několik účtů.

## <a name="display-basic-token-information"></a>Zobrazit základní informace o tokenu

* Přidejte následující metodu do **MainPage.XAML.cs** k zobrazení základních informací o tokenu:

    ```csharp
    /// <summary>
    /// Display basic information contained in the token. Needs to be called from the UI thead.
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

### <a name="more-information"></a>Další informace

Tokeny ID získané prostřednictvím **OpenID Connect** také obsahují malou podmnožinu informací, které se vztahují k uživateli. `DisplayBasicTokenInfo`Zobrazí základní informace obsažené v tokenu. Příklady jsou zobrazované jméno a ID uživatele, datum vypršení platnosti tokenu a řetězec, který představuje samotný přístupový token. Pokud si vyberete tlačítko pro **volání rozhraní API Microsoft Graph** několikrát, uvidíte, že se stejný token znovu použil pro následné požadavky. Můžete také zobrazit datum vypršení platnosti, pokud MSAL rozhodne, že je čas obnovit token.

## <a name="register-your-application"></a>Registrace vaší aplikace

Teď je potřeba zaregistrovat aplikaci na portálu pro registraci aplikací Microsoftu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud je váš účet přítomen ve více než jednom tenantovi služby Azure AD, `Directory + Subscription` vyberte v pravém horním rohu v nabídce v horní části stránky a přepněte relaci portálu na požadovaného tenanta Azure AD.
1. Přejděte na stránku [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) Microsoft Identity Platform for Developers.
1. Vyberte **Nová registrace**.
   - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `UWP-App-calling-MSGraph`.
   - V části **podporované typy účtů** vyberte **účty v libovolném organizačním adresáři a v osobních účtech Microsoft (např. Skype, Xbox, Outlook.com)** .
   - Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. Na stránce **Přehled** aplikace vyhledejte hodnotu **ID aplikace (klienta)** a zaznamenejte ji pro pozdější použití. Vraťte se do sady Visual Studio, otevřete **MainPage.XAML.cs**a nahraďte hodnotu ClientID ID aplikace, kterou jste právě zaregistrovali:
1. V seznamu stránek pro aplikaci vyberte **Ověřování**.
   1. V části **identifikátory URI pro přesměrování** v seznamu identifikátorů URI pro přesměrování:
   1. Ve sloupci **typ** vyberte **veřejný klient (mobilní & Desktop)** .
   1. Do `urn:ietf:wg:oauth:2.0:oob` sloupce **URI pro přesměrování** zadejte.
1. Vyberte **Uložit**.
1. V seznamu stránek aplikace vyberte **oprávnění rozhraní API** .
   - Klikněte na tlačítko **Přidat oprávnění** a pak na
   - Ujistěte se, že je vybraná karta **rozhraní API Microsoftu** .
   - V části *běžně používaná rozhraní Microsoft API* klikněte na **Microsoft Graph**
   - V části **delegovaná oprávnění** zkontrolujte, že jsou zaškrtnutá správná oprávnění: **Uživatel. číst**. V případě potřeby použijte vyhledávací pole.
   - Vyberte tlačítko **Přidat oprávnění** .

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Povolit integrované ověřování u federovaných domén (volitelné)

Pokud chcete povolit integrované ověřování Windows, když se používá s doménou federované služby Azure AD, manifest aplikace musí povolit další možnosti:

1. Poklikejte na **Package. appxmanifest**.
2. Vyberte kartu **Možnosti** a ujistěte se, že jsou povolena následující nastavení:

    - Podnikové ověřování
    - Privátní sítě (klient & Server)
    - Sdílené uživatelské certifikáty

> [!IMPORTANT]
> [Integrované ověřování systému Windows](https://aka.ms/msal-net-iwa) není pro tuto ukázku standardně nakonfigurováno. Aplikace, které požadují možnosti *podnikového ověřování* nebo *sdílených uživatelských certifikátů* , vyžadují vyšší úroveň ověřování ve Windows Storu. Ne všichni vývojáři chtějí provádět vyšší úroveň ověřování. Toto nastavení povolte jenom v případě, že potřebujete integrované ověřování Windows s doménou federované služby Azure AD.

## <a name="test-your-code"></a>Testování kódu

Chcete-li otestovat aplikaci, vyberte F5 pro spuštění projektu v aplikaci Visual Studio. Zobrazí se hlavní okno:

![Uživatelské rozhraní aplikace](./media/tutorial-v2-windows-uwp/testapp-ui.png)

Až budete připraveni na test, vyberte **zavolat Microsoft Graph API**. K přihlášení použijte účet organizace Azure AD nebo účet Microsoft, jako je například live.com nebo outlook.com. Pokud je to poprvé, zobrazí se okno s výzvou, aby se uživatel přihlásil:

![Přihlašovací stránka](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>Souhlas

Při prvním přihlášení do aplikace se zobrazí obrazovka pro vyjádření souhlasu, která je podobná následující. Vyberte **Ano** , pokud chcete výslovně udělit přístup k přístupu:

![Obrazovka souhlasu s přístupem](./media/tutorial-v2-windows-uwp/consentscreen.png)

### <a name="expected-results"></a>Očekávané výsledky

Na obrazovce **výsledky volání rozhraní API** se zobrazí informace o profilu uživatele vracené voláním Microsoft Graph API:

![Obrazovka výsledků volání rozhraní API](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

Zobrazí se také základní informace o tokenu získaném `AcquireTokenInteractive` prostřednictvím `AcquireTokenSilent` nebo v poli **informace** o tokenu:

|Vlastnost  |Formát  |Popis |
|---------|---------|---------|
|**Uživatelské jméno** |<span>user@domain.com</span> |Uživatelské jméno, které uživatele identifikuje.|
|**Vypršení platnosti tokenu** |Datetime |Čas vypršení platnosti tokenu MSAL rozšiřuje datum vypršení platnosti tím, že podle potřeby obnoví token.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a delegovaných oprávněních

Rozhraní Microsoft Graph API vyžaduje, aby *uživatel. přečetl* obor pro čtení profilu uživatele. Tento obor se ve výchozím nastavení přidá automaticky v každé aplikaci, která je zaregistrovaná na portálu pro registraci aplikací. Další obory můžou vyžadovat další rozhraní API pro Microsoft Graph a vlastní rozhraní API pro back-end Server. Rozhraní Microsoft Graph API vyžaduje pro výpis kalendářů uživatelů rozsah *.*

Chcete-li získat přístup k kalendářům uživatele v kontextu aplikace, přidejte *kalendáře. Přečtěte si* delegované oprávnění k informacím o registraci aplikace. Pak přidejte *kalendáře. Přečtěte* si rozsah `acquireTokenSilent` volání.

> [!NOTE]
> Uživatelům se může zobrazit výzva k dalšímu souhlasu při zvýšení počtu oborů.

## <a name="known-issues"></a>Známé problémy

### <a name="issue-1"></a>Problém 1

Při přihlášení aplikace ve federované doméně Azure AD se zobrazí jedna z následujících chybových zpráv:

* V požadavku se nenašel žádný platný klientský certifikát.
* V úložišti certifikátů uživatele nebyly nalezeny žádné platné certifikáty.
* Zkuste znovu vybrat jinou metodu ověřování.

**Způsobit** Funkce Enterprise a Certificate nejsou povolené.

**Řešení** Postupujte podle kroků v části [integrované ověřování u federovaných domén](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problém 2

Povolíte [integrované ověřování pro federované domény](#enable-integrated-authentication-on-federated-domains-optional) a zkusíte použít Windows Hello v počítači s Windows 10 pro přihlášení k prostředí s nakonfigurovaným ověřováním službou Multi-Factor Authentication. Zobrazí se seznam certifikátů. Pokud se ale rozhodnete použít PIN kód, okno PIN se nikdy neprezentuje.

**Způsobit** Tento problém je známým omezením zprostředkovatele webového ověřování v aplikacích UWP, které běží na Windows 10 Desktop. V systému Windows 10 Mobile funguje správně.

**Odstraníte** Vyberte **Přihlásit se s dalšími možnostmi**. Pak vyberte **Přihlásit se pomocí uživatelského jména a hesla**. Vyberte **zadat heslo**. Pak Projděte proces ověřování pro telefon.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Pomůžeme nám vylepšit platformu Microsoft identity. Řekněte nám, co si myslíte, díky krátkému průzkumu dvou dotazů.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform Survey](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
