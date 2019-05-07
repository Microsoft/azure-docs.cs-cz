---
title: Platforma identit Microsoft UPW Začínáme | Azure
description: Jak aplikace univerzální platformy Windows (UPW) může volat rozhraní API, které vyžaduje Microsoft identity platform endpoint přístupové tokeny.
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
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 448858efeaae4c3e2a41d41181e9ec74b03223f6
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138258"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Volání rozhraní Microsoft Graph API z aplikace pro univerzální platformu Windows (XAML)

> [!div renderon="docs"]
> [!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Tato příručka vysvětluje, jak požádat o přístupový token a pak volat Microsoft Graph API nativní aplikace pro univerzální platformu Windows (UPW). V Průvodci platí také pro jiná rozhraní API, které vyžadují přístupových tokenů z koncového bodu Microsoft identity platform.

Na konci tohoto průvodce vaše aplikace volá chráněné rozhraní API pomocí osobních účtů. Příklady jsou outlook.com, live.com a další. Vaše aplikace volá pracovním a školním účtům také z libovolného společnosti nebo organizace, která má Azure Active Directory (Azure AD).

>[!NOTE]
> Tato příručka vyžaduje Visual Studio 2017 s vývoj pro univerzální platformu Windows nainstalované. Zobrazit [nastavení](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) pokyny ke stažení a konfigurace sady Visual Studio pro vývoj aplikací pro univerzální platformu Windows.

## <a name="how-this-guide-works"></a>Jak funguje tento průvodce

![Ukazuje, jak ukázková aplikace vygenerované v tomto kurzu funguje](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Tento průvodce vytvoří ukázkovou aplikaci UPW, který se dotazuje rozhraní Microsoft Graph API nebo webové rozhraní API, které přijímá tokeny z koncového bodu Microsoft identity platform. V tomto scénáři token se přidá na požadavky HTTP přes autorizační hlavičky. Microsoft Authentication Library (MSAL) zpracovává token pořízení a obnovení.

## <a name="nuget-packages"></a>Balíčky NuGet

Tato příručka používá následující balíčky NuGet:

|Knihovna|Popis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Identity a ověřování Microsoftu|

## <a name="set-up-your-project"></a>Nastavení projektu

Tato část obsahuje podrobné pokyny k integraci aplikace Windows Desktop .NET (XAML) s *ve službě Microsoft*. Potom můžete zadat dotaz webová rozhraní API, které vyžadují token, jako je například Microsoft Graph API.

Tento průvodce vytvoří aplikaci, která se zobrazí tlačítko tohoto dotazy rozhraní Graph API, tlačítkem pro odhlášení a textová pole, které zobrazují výsledky volání.

> [!NOTE]
> Chcete místo toho stáhněte si tento ukázkový projekt sady Visual Studio? [Stáhnete projekt](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) a pokračujte [registrace aplikace](#register-your-application "kroku registrace aplikace") krok konfigurace vzorový kód před jejím spuštěním.

### <a name="create-your-application"></a>Vytvoření aplikace

1. V sadě Visual Studio, vyberte **souboru** > **nový** > **projektu**.
2. V části **šablony**vyberte **Visual C#**.
3. Vyberte **Prázdná aplikace (univerzální pro Windows)**.
4. Název aplikace a vyberte **OK**.
5. Pokud se zobrazí výzva, vyberte všechny verze pro **cílové** a **minimální** verze a vyberte **OK**.

    >![Minimální a cílové verze](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Do projektu přidejte knihovny Microsoft Authentication Library
1. V sadě Visual Studio vyberte **Nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků**.
2. Zkopírujte a vložte následující příkaz v **Konzola správce balíčků** okno:

    ```powershell
    Install-Package Microsoft.Identity.Client -IncludePrerelease
    ```

> [!NOTE]
> Tento příkaz nainstaluje [knihovna Microsoft Authentication Library](https://aka.ms/msal-net). Knihovna MSAL získá, ukládá do mezipaměti a aktualizuje tokeny uživatele, které přístup k rozhraním API chráněné službou Microsoft identity platform.

## <a name="create-your-applications-ui"></a>Vytvoření uživatelského rozhraní aplikace

A **MainPage.xaml** soubor se vytvoří automaticky jako součást šablony projektu. Tento soubor otevřít a pak postupujte podle pokynů:

* Nahraďte vaší aplikace **mřížky** uzel s následujícím kódem:

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
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Použití MSAL k získání tokenu pro rozhraní Microsoft Graph API

Tato část ukazuje použití MSAL k získání tokenu pro rozhraní Microsoft Graph API.

1.  V **MainPage.xaml.cs**, přidejte odkaz pro MSAL do třídy:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Nahraďte kód metody vaše <code>MainPage</code> třídy následujícím kódem:

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
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
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

Volání `AcquireTokenInteractive` metodu vede okno, které vyzve uživatele k přihlášení. Aplikace obvykle vyžadují uživatelům umožní přihlásit interaktivně poprvé, které potřebují přístup k chráněnému prostředku. Také může být potřeba přihlášení při selhání pasivní operace k získání tokenu. Příkladem je, když platnost hesla uživatele.

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

`AcquireTokenSilent` Obsluhovala token pořízení a obnovení bez nutnosti zásahu uživatele. Po `AcquireTokenInteractive` provádí poprvé a uživatel je vyzván k zadání přihlašovacích údajů, `AcquireTokenSilent` metoda by měla sloužit požádat o tokeny pro následná volání, protože získá tokeny bezobslužně. Knihovna MSAL zpracuje mezipaměť tokenu a obnovení.

Nakonec `AcquireTokenSilent` metoda selže. Důvody pro selhání může být, že uživatelé mají odhlášení nebo změnit své heslo na jiném zařízení. Knihovna MSAL zjistí, že problém lze vyřešit tak, že vyžaduje interaktivní akci, vyvolá-li `MsalUiRequiredException` výjimky. Vaše aplikace dokáže zpracovat tuto výjimku dvěma způsoby:

* To lze uskutečnit volání proti `AcquireTokenInteractive` okamžitě. Toto volání za následek výzvy k přihlášení. Za normálních okolností se tento model používá v online aplikace tam, kde není žádný k dispozici offline obsah pro uživatele. Následující ukázka vygeneroval tento instalační program s asistencí. Zobrazí se v akci první čas spuštění ukázky.
  * Vzhledem k tomu, že žádný uživatel použil aplikace, `accounts.FirstOrDefault()` obsahuje hodnotu null a `MsalUiRequiredException` je vyvolána výjimka.
  * Kód v ukázce pak zpracovává výjimku při volání `AcquireTokenInteractive`. Toto volání za následek výzvy k přihlášení.

* Nebo místo toho prezentuje vizuální označení pro uživatele, že interaktivní přihlášení je povinné. Potom může vybrat správný čas pro přihlášení. Nebo můžete opakovat aplikace `AcquireTokenSilent` později. Tento model se často používá při uživatelé mohou používat další funkce aplikace bez výpadků. Příkladem je při offline obsah je k dispozici v aplikaci. V takovém případě může uživatel rozhodne ji k přihlášení k přístup k chráněnému prostředku nebo aktualizujte zastaralé informace. Nebo jiná aplikace se můžete rozhodnout používat opakovat `AcquireTokenSilent` po síti obnovení poté, co byla dočasně nedostupný.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Volání rozhraní Microsoft Graph API s využitím, které jste získali token

* Přidejte následující novou metodu pro **MainPage.xaml.cs**. Tato metoda se používá, aby `GET` požadavek s využitím rozhraní Graph API s využitím `Authorization` záhlaví:

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Další informace o provádění volání REST chráněné rozhraní API

V této ukázkové aplikaci `GetHttpContentWithToken` aby protokolu HTTP se používá metoda `GET` požadavku na chráněný prostředek, který se vyžaduje token. Metoda pak vrátí obsah volajícímu. Tato metoda přidá tokenu získaného v **HTTP autorizace** záhlaví. Pro tuto ukázku, prostředek je v rozhraní Microsoft Graph API **mě** koncový bod, který se zobrazí informace o profilu uživatele.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Přidejte metodu k neodhlásí uživatele

* Odhlásit uživatele, přidejte následující metodu do **MainPage.xaml.cs**:

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
> MSAL.NET používá asynchronní metody k získání tokenů nebo pracovat s účty, a proto je potřeba starat o provedení akce uživatelského rozhraní ed ve vlákně uživatelského rozhraní, proto `Dispatcher.RunAsync`a opatření pro volání `ConfigureAwait(false)`

### <a name="more-information-on-sign-out"></a>Další informace o odhlášení

`SignOutButton_Click` Metoda odebere uživatele z mezipaměti MSAL uživatele. Tato metoda efektivně říká MSAL zapomenout aktuálního uživatele. Potom budoucí požadavek na získání tokenu úspěšné pouze v případě, že provedl být interaktivní.
V této ukázce podporuje jenom jednoho konkrétního uživatele. Ale MSAL podporuje scénáře, ve kterém můžete více než jeden účet přihlášení ve stejnou dobu. Příkladem je e-mailové aplikace, kde má uživatel několik účtů.

## <a name="display-basic-token-information"></a>Zobrazit základní informace o tokenu

* Přidejte následující metodu do **MainPage.xaml.cs** zobrazit základní informace o tokenu:

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

Tokeny typu ID opatřené **OpenID Connect** také obsahovat malou část informace, které jsou relevantní pro uživatele. `DisplayBasicTokenInfo` Zobrazí základních informací obsažených v tokenu. Příklady jsou zobrazované jméno a ID, datum vypršení platnosti tokenu a řetězec, který představuje přístupový token, samotného. Pokud vyberete **volat Microsoft Graph API** tlačítko několikrát, uvidíte, že stejný token byl znovu použít pro následné požadavky. Zobrazí se také datum vypršení platnosti při MSAL rozhodne, že je čas k obnovení tokenu.

## <a name="register-your-application"></a>Registrace vaší aplikace

Teď budete muset zaregistrovat aplikaci v portálu pro registraci aplikace Microsoftu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud je váš účet součástí více než jednoho tenanta Azure AD, vyberte `Directory + Subscription` v pravém horním rohu v nabídce na stránce a přepínač portálu relace k požadované službě Azure AD tenanta.
1. Přejděte na Microsoft identity platform pro vývojáře [registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránky.
1. Vyberte **registrace nové**.
   - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `UWP-App-calling-MSGraph`.
   - V **podporovaných typů účtu** vyberte **účty v jakékoli organizaci adresáři a osobní účty Microsoft (třeba Skype, Xbox, Outlook.com)**.
   - Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. V aplikaci **přehled** stránky, vyhledejte **ID aplikace (klient)** hodnotu a uložte ho pro pozdější. Vraťte se zpět do sady Visual Studio, otevřete **MainPage.xaml.cs**a nahraďte hodnoty ClientId ID aplikace, který jste právě zaregistrovali:
1. V seznamu stránek pro aplikaci vyberte **Ověřování**.
   1. V **identifikátory URI přesměrování** části, v seznamu identifikátorů URI pro přesměrování:
   1. V **typ** vybrat sloupec **veřejným klientem (mobilních a desktopových)**.
   1. Zadejte `urn:ietf:wg:oauth:2.0:oob` v **identifikátor URI pro PŘESMĚROVÁNÍ** sloupce.
1. Vyberte **Uložit**.
1. V seznamu stránek pro aplikace, vyberte **oprávnění k rozhraní API**
   - Klikněte na tlačítko **přidat oprávnění** tlačítko a pak,
   - Ujistěte se, **Microsoft API** vybraná karta
   - V *běžně používá Microsoft APIs* části, klikněte na **Microsoft Graphu**
   - V **delegovaná oprávnění** části, ověřte, že je zaškrtnuté políčko správná oprávnění: **User.Read**. V případě potřeby použijte vyhledávací pole.
   - Vyberte **přidat oprávnění** tlačítko

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Povolit integrované ověřování u federovaných domén (volitelné)

Povolení ověřování Windows-Integrated při použití s federovaným Azure AD domain, manifest aplikace musí povolit další možnosti:

1. Dvakrát klikněte na panel **Package.appxmanifest**.
2. Vyberte **možnosti** kartu a ujistěte se, že jsou povolené následující nastavení:

    - Podnikové ověřování
    - Privátní sítě (klient a Server)
    - Sdílené uživatelské certifikáty

> [!IMPORTANT]
> [Integrované ověřování Windows](https://aka.ms/msal-net-iwa) není nakonfigurovaná ve výchozím nastavení pro tuto ukázku. Aplikace, které vyžadují *podnikové ověřování* nebo *sdílené uživatelské certifikáty* možnosti vyžadovat vyšší úroveň ověření ve Windows Store. Také ne všechny vývojáři chtějí provádět vyšší úroveň ověřování. Povolte toto nastavení, pouze pokud je potřeba integrované ověřování Windows pomocí federovaného Azure AD domain.

## <a name="test-your-code"></a>Testování kódu

A otestujte aplikaci stisknutím klávesy F5 ke spuštění projektu v sadě Visual Studio. Hlavní okno se zobrazí:

![Uživatelské rozhraní aplikace](./media/tutorial-v2-windows-uwp/testapp-ui.png)

Jakmile budete připraveni k testování, vyberte **volat Microsoft Graph API**. Pak pomocí účtu organizace služby Azure AD nebo účtu Microsoft, jako je live.com nebo outlook.com, přihlaste se. Pokud je poprvé, zobrazí se okno s výzvou uživateli umožní přihlásit:

![Přihlašovací stránka](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>Souhlas

Při prvním přihlášení do aplikace, zobrazí se obrazovka pro vyjádření souhlasu podobný následujícímu. Vyberte **Ano** výslovně souhlas pro přístup:

![Přístup k obrazovce pro vyjádření souhlasu](./media/tutorial-v2-windows-uwp/consentscreen.png)

### <a name="expected-results"></a>Očekávané výsledky

Zobrazí informace o profilu uživatele vrácený voláním rozhraní Microsoft Graph API na **výsledků volání rozhraní API** obrazovky:

![Výsledky volání rozhraní API obrazovky](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

Také si zobrazili základní informace o tokenu opatřené `AcquireTokenInteractive` nebo `AcquireTokenSilent` v **informace o tokenu** pole:

|Vlastnost  |Formát  |Popis |
|---------|---------|---------|
|**Uživatelské jméno** |<span>user@domain.com</span> |Uživatelské jméno, který identifikuje uživatele.|
|**Platnost tokenu vyprší** |DateTime |Čas, kdy vyprší platnost tokenu. Knihovna MSAL je rozšířením datum vypršení platnosti obnovuje se token podle potřeby.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a delegovaná oprávnění

Vyžaduje rozhraní Microsoft Graph API *user.read* obory a čtení profilu uživatele. V každé aplikaci, která je registrován v portálu pro registraci aplikace ve výchozím nastavení je automaticky přidán tento obor. Další rozhraní API pro Microsoft Graphu a vlastních rozhraní API pro back endového serveru může vyžadovat další obory. Vyžaduje rozhraní Microsoft Graph API *Calendars.Read* oboru seznam kalendářů uživatele.

Chcete-li přístup ke kalendářům uživatele v rámci aplikace, přidejte *Calendars.Read* delegovaná oprávnění aplikace informace o registraci. Pak přidejte *Calendars.Read* rozsah `acquireTokenSilent` volání.

> [!NOTE]
> Uživatelům může zobrazit výzva pro další souhlasy zvýšit počet oborů.

## <a name="known-issues"></a>Známé problémy

### <a name="issue-1"></a>Problém 1

Zobrazí jedna z následujících chybových zpráv při přihlášení na svou aplikaci na federované Azure AD domain:

* Nenašel se žádný platný klientský certifikát v požadavku.
* Nenašly se žádné platné certifikáty v úložišti certifikátů uživatele.
* Zkuste znovu vybrat různé ověřování.

**Příčina:** Funkce Enterprise a certifikátů nejsou povolené.

**Řešení:** Postupujte podle kroků v [integrované ověřování u federovaných domén](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problém 2

Povolíte [integrované ověřování u federovaných domén](#enable-integrated-authentication-on-federated-domains-optional) a pokuste se použít Windows Hello na počítači s Windows 10 pro přihlášení v prostředí s ověřováním Multi-Factor Authentication nakonfigurován. Zobrazí seznam certifikátů. Ale pokud budete chtít použít váš PIN kód, v okně kódu PIN se nikdy zobrazí.

**Příčina:** Tento problém je známé omezení zprostředkovatele webového ověření v aplikacích UPW, které běží na Windows 10 desktop. Bez problémů funguje na Windows 10 Mobile.

**Alternativní řešení:** Vyberte **přihlásit pomocí jiné možnosti**. Potom vyberte **Přihlaste se pomocí uživatelského jména a hesla**. Vyberte **zadejte heslo**. Potom projděte procesem ověřování telefonu.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
