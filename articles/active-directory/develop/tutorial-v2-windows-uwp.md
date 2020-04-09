---
title: Platforma microsoftu pro identitu UPW začíná | Azure
description: Jak univerzální platformy Windows aplikace (UPW) můžete volat rozhraní API, které vyžaduje přístupové tokeny koncovým bodem platformy identity Microsoftu.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f6e7ee1b03dda30bea15a837b6daf6469e00e46d
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886394"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Volání rozhraní Microsoft Graph API z aplikace platformy Universal Windows (XAML)

> [!div renderon="docs"]

Tato příručka vysvětluje, jak může nativní aplikace univerzální platformy Windows (UPW) požádat o přístupový token. Aplikace pak volá rozhraní Microsoft Graph API. Příručka platí také pro další rozhraní API, které vyžadují přístupové tokeny z koncového bodu platformy identit y Microsoft.

Na konci této příručky aplikace volá chráněné rozhraní API pomocí osobních účtů. Příklady jsou outlook.com, live.com a další. Vaše aplikace také volá pracovní a školní účty z jakékoli společnosti nebo organizace, která má Azure Active Directory (Azure AD).

>[!NOTE]
> Tato příručka vyžaduje Visual Studio s nainstalovaným vývojem univerzální platformy Windows. Pokyny ke stažení a konfiguraci Visual Studia pro vývoj aplikací pro univerzální platformu Windows najdete v článku [Nastavení.](https://docs.microsoft.com/windows/uwp/get-started/get-set-up)

>[!NOTE]
> Pokud s platformou identit Microsoftu teče, doporučujeme začít s [rychlým spuštěním rozhraní Call the Microsoft Graph API z aplikace Univerzální platforma Windows (UPW).](quickstart-v2-uwp.md)

## <a name="how-this-guide-works"></a>Jak funguje tento průvodce

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Tato příručka vytvoří ukázkovou aplikaci UPW, která se dotazuje rozhraní Microsoft Graph API. V tomto scénáři je token přidán do požadavků HTTP pomocí hlavičky Autorizace. Microsoft Authentication Library (MSAL) zpracovává token y a obnovení.

## <a name="nuget-packages"></a>Balíčky NuGet

Tato příručka používá následující balíček NuGet:

|Knihovna|Popis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Identity a ověřování Microsoftu|

## <a name="set-up-your-project"></a>Nastavení projektu

Tato část obsahuje podrobné pokyny k integraci aplikace XAML (Windows Desktop Desktop) s přihlášením se společností Microsoft. Potom aplikace může dotaz webových rozhraní API, které vyžadují token, jako je například rozhraní MICROSOFT Graph API.

Tato příručka vytvoří aplikaci, která zobrazí tlačítko, které se dotazuje rozhraní GRAPH API a tlačítko pro odhlášení. Zobrazí také textová pole, která obsahují výsledky volání.

> [!NOTE]
> Chcete stáhnout tento ukázkový projekt sady Visual Studio namísto jeho vytvoření? [Stáhněte si projekt](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) a přeskočte ke kroku [registrace aplikace](#register-your-application "krok registrace aplikace") a nakonfigurujte ukázku kódu před jeho spuštěním.

### <a name="create-your-application"></a>Vytvořte si svoji aplikaci

1. Otevřete Visual Studio a vyberte **Vytvořit nový projekt**.
1. V **okně Vytvořit nový projekt**zvolte Blank App **(Universal Windows)** pro C# a vyberte **Další**.
1. V **části Konfigurace nového projektu**aplikaci pojmenujte a vyberte **Vytvořit**.
1. Pokud se zobrazí výzva, vyberte v **aplikaci New Universal Platform Project**libovolnou verzi pro **cílové** a **minimální** verze a vyberte **ok**.

   ![Minimální a cílové verze](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-microsoft-authentication-library-to-your-project"></a>Přidání knihovny Microsoft Authentication Library do projektu

1. V sadě Visual Studio vyberte **nástroje, ve kterých** > nástroj**NuGet Správce** > **balíčků konzola Správce balíčků**.
1. Zkopírujte a vložte následující příkaz v okně **Konzola správce balíčků:**

    ```powershell
    Install-Package Microsoft.Identity.Client
    ```

   > [!NOTE]
   > Tento příkaz nainstaluje [knihovnu Microsoft Authentication Library](https://aka.ms/msal-net). MSAL získává, ukládá do mezipaměti a aktualizuje uživatelské tokeny, které přistupují k rozhraním API chráněným platformou identit společnosti Microsoft.

### <a name="create-your-applications-ui"></a>Vytvoření uj.

Visual Studio vytvoří *soubor MainPage.xaml* jako součást šablony projektu. Otevřete tento soubor a nahraďte uzel **Mřížka** aplikace následujícím kódem:

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

### <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Použití služby MSAL k získání tokenu pro rozhraní MICROSOFT Graph API

Tato část ukazuje, jak pomocí msal získat token pro rozhraní API Microsoft Graph. Proveďte změny v *souboru MainPage.xaml.cs.*

1. V *MainPage.xaml.cs*přidejte tyto odkazy:

    ```csharp
    using Microsoft.Identity.Client;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. Nahraďte třídu `MainPage` následujícím kódem:

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
        // - the content of Tenant with the information about the accounts allowed to sign in in your application:
        //   - for Work or School account in your org, use your tenant ID, or domain
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
    }
    ```

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele<a name="more-information"></a>

Metoda `AcquireTokenInteractive` má za následek okno, které vyzve uživatele k přihlášení. Aplikace obvykle vyžadují, aby se uživatelé při prvním přihlášení k chráněnému prostředku interaktivně přihlašují. Mohou také nutné přihlásit, když tiché operace získat token selže. Příkladem je, když vypršela platnost hesla uživatele.

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

Metoda `AcquireTokenSilent` zpracovává token akvizice a obnovení bez jakékoli interakce uživatele. Po `AcquireTokenInteractive` prvním spuštění a vyzve uživatele k zadání pověření, použijte metodu `AcquireTokenSilent` k vyžádání tokenů pro pozdější volání. Tato metoda získává tokeny tiše. MSAL zpracovává mezipaměti tokenů a obnovení.

Nakonec `AcquireTokenSilent` metoda selže. Důvody selhání zahrnují uživatele, který se odhlásil nebo změnil své heslo na jiném zařízení. Když MSAL zjistí, že problém vyžaduje interaktivní akci, vyvolá výjimku. `MsalUiRequiredException` Aplikace může tuto výjimku zpracovat dvěma způsoby:

* Vaše aplikace `AcquireTokenInteractive` volá okamžitě. Výsledkem tohoto volání je výzva k přihlášení uživatele. Obvykle použijte tento přístup pro online aplikace, kde není k dispozici offline obsah pro uživatele. Vzorek generovaný tímto řízeným nastavením se řídí vzorem. Vidíte to v akci při prvním spuštění ukázky.

   Protože žádný uživatel nepoužil `accounts.FirstOrDefault()` aplikaci, obsahuje hodnotu `MsalUiRequiredException` null a vyvolá výjimku.

   Kód v ukázce pak zpracovává `AcquireTokenInteractive`výjimku voláním . Výsledkem tohoto volání je výzva k přihlášení uživatele.

* Aplikace představuje vizuální indikaci pro uživatele, že je třeba se přihlásit. Pak si mohou vybrat správný čas pro přihlášení. Aplikace může opakovat `AcquireTokenSilent` později. Tento postup použijte, pokud uživatelé mohou používat jiné funkce aplikace bez přerušení. Příkladem je, když je v aplikaci k dispozici obsah offline. V takovém případě se uživatelé mohou rozhodnout, kdy se chtějí přihlásit. Aplikace může opakovat `AcquireTokenSilent` po síti byl dočasně nedostupný.

### <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Volání rozhraní Microsoft Graph API pomocí tokenu, který jste právě získali

Do *MainPage.xaml.cs*přidejte následující novou metodu :

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

 Tato metoda `GET` provede požadavek z `Authorization` rozhraní Graph API pomocí záhlaví.

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Další informace o volání REST proti chráněnému rozhraní API

V této ukázkové `GetHttpContentWithToken` aplikaci `GET` metoda provést požadavek HTTP proti chráněnému prostředku, který vyžaduje token. Potom metoda vrátí obsah volajícímu. Tato metoda přidá získaný token v **hlavičce autorizace PROTOKOLU HTTP.** Pro tuto ukázku je prostředek koncový bod **rozhraní** MICROSOFT Graph API, který zobrazuje informace o profilu uživatele.

### <a name="add-a-method-to-sign-out-the-user"></a>Přidání metody pro odhlášení uživatele

Chcete-li uživatele odhlásit, přidejte k *MainPage.xaml.cs*následující metodu :

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

> [!NOTE]
> MSAL.NET používá asynchronní metody k získání tokenů nebo manipulaci s účty. Musíte podporovat akce uživatelského rozhraní ve vlákně uživatelského rozhraní. To je důvod `Dispatcher.RunAsync` pro volání a opatření `ConfigureAwait(false)`k volání .

#### <a name="more-information-about-signing-out"></a>Další informace o odhlášení<a name="more-information-on-sign-out"></a>

Metoda `SignOutButton_Click` odebere uživatele z mezipaměti uživatele MSAL. Tato metoda efektivně říká MSAL zapomenout na aktuálního uživatele. Budoucí požadavek na získání tokenu je úspěšný pouze v případě, že je interaktivní.

Aplikace v této ukázce podporuje jednoho uživatele. MSAL podporuje scénáře, kde se uživatel může přihlásit na více než jeden účet. Příkladem je e-mailová aplikace, kde má uživatel několik účtů.

### <a name="display-basic-token-information"></a>Zobrazení základních informací o tokenech

Přidejte do *MainPage.xaml.cs* následující metodu, chcete-li zobrazit základní informace o tokenu:

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

ID tokeny získané pomocí **OpenID Connect** také obsahují malou podmnožinu informací týkajících se uživatele. `DisplayBasicTokenInfo`zobrazí základní informace obsažené v tokenu. Tyto informace zahrnují zobrazované jméno uživatele a ID. Obsahuje také datum vypršení platnosti tokenu a řetězec, který představuje samotný přístupový token. Pokud několikrát vyberete tlačítko Volat rozhraní **Microsoft Graph API,** uvidíte, že stejný token byl znovu použit pro pozdější požadavky. Můžete také zobrazit datum vypršení platnosti prodloužena, když MSAL rozhodne, že je čas na obnovení tokenu.

### <a name="display-message"></a>Zobrazit zprávu

Do *MainPage.xaml.cs*přidejte následující novou metodu :

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

## <a name="register-your-application"></a>Registrace vaší aplikace

Nyní je třeba zaregistrovat svou žádost:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyberte**registrace aplikací**Azure **Active Directory** > .
1. Vyberte **možnost Nová registrace**. Zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například *UWP-App-calling-MSGraph*.
1. V části **Podporované typy účtů**vyberte Účty v **libovolném organizačním adresáři a osobních účtech Microsoft (např. skype, Xbox)** a pokračujte v možnosti **Registrovat.**
1. Na stránce s přehledem najděte hodnotu **ID aplikace (klienta)** a zkopírujte ji. Vraťte se do *MainPage.xaml.cs*sady Visual Studio, `ClientId` otevřete MainPage.xaml.cs a nahraďte hodnotu této hodnoty.

Konfigurace ověřování pro vaši aplikaci:

1. Zpátky na [webu Azure Portal](https://portal.azure.com)vyberte v části **Správa** **možnost Ověření**.
1. V části **Přesměrování identifikátorů** | URI**Suggested Redirect UR pro veřejné klienty (mobilní zařízení, stolní počítače)** zkontrolujte **https://login.microsoftonline.com/common/oauth2/nativeclient**.
1. Vyberte **Uložit**.

Konfigurace oprávnění rozhraní API pro vaši aplikaci:

1. V části **Manage**vyberte **oprávnění rozhraní API**.
1. Vyberte **Přidat oprávnění** a ujistěte se, že jste vybrali **rozhraní Microsoft API**.
1. Vyberte **microsoft graph**.
1. Vyberte **delegovaná oprávnění**, vyhledejte *položku User.Read* a ověřte, zda je vybránsoubor **User.Read.**
1. Pokud jste provedli nějaké změny, vyberte **Přidat oprávnění** k jejich uložení.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Povolení integrovaného ověřování ve federovaných doménách (volitelné)

Chcete-li povolit integrované ověřování systému Windows při použití s federovovovovnou doménou Azure AD, manifest aplikace musí povolit další možnosti. Vraťte se do aplikace v sadě Visual Studio.

1. Otevřete *soubor Package.appxmanifest*.
1. Vyberte **možnosti** a povolte následující nastavení:

   * **Podnikové ověřování**
   * **Privátní sítě (server & klienta)**
   * **Sdílené uživatelské certifikáty**

> [!IMPORTANT]
> [Integrované ověřování systému Windows](https://aka.ms/msal-net-iwa) není pro tuto ukázku ve výchozím nastavení nakonfigurováno. Aplikace, `Enterprise Authentication` které `Shared User Certificates` požadují nebo poskytují možnosti, vyžadují vyšší úroveň ověření ve Windows Storu. Také ne všichni vývojáři chtějí provést vyšší úroveň ověření. Toto nastavení povolte jenom v případě, že potřebujete integrované ověřování systému Windows s federovovovnou doménou Azure AD.

## <a name="test-your-code"></a>Testování kódu

Chcete-li aplikaci otestovat, vyberte možnost F5 a spusťte projekt v sadě Visual Studio. Zobrazí se hlavní okno:

![Uživatelské rozhraní aplikace](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Až budete připraveni k testování, vyberte **volat rozhraní Microsoft Graph API**. Pak se přihlaste pomocí účtu organizace Azure AD nebo účtu Microsoft, například live.com nebo outlook.com. Při prvním spuštění aplikace zobrazí okno s žádostí o přihlášení.

### <a name="consent"></a>Souhlasu

Při prvním přihlášení k aplikaci se zobrazí obrazovka souhlasu podobná následující. Výběrem **možnosti Ano,** chcete-li výslovně souhlasit s přístupem:

![Obrazovka souhlasu s přístupem](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Očekávané výsledky

Informace o profilu uživatele vrácené voláním rozhraní MICROSOFT Graph API se zobrazí na obrazovce **Výsledky volání rozhraní API:**

![Obrazovka Výsledky volání rozhraní API](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

Zobrazí se také základní informace `AcquireTokenInteractive` o `AcquireTokenSilent` tokenu získaném prostřednictvím nebo v poli **Informace o tokenu:**

|Vlastnost  |Formát  |Popis |
|---------|---------|---------|
|`Username` |`user@domain.com` |Uživatelské jméno, které identifikuje uživatele.|
|`Token Expires` |`DateTime` |Čas, kdy vyprší platnost tokenu. MSAL prodlouží datum vypršení platnosti obnovením tokenu podle potřeby.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a delegovaných oprávněních

Rozhraní Microsoft Graph `user.read` API vyžaduje obor ke čtení profilu uživatele. Tento obor se ve výchozím nastavení přidává v každé aplikaci, která je registrována na portálu registrace aplikací. Další rozhraní API pro Microsoft Graph a vlastní rozhraní API pro server back-end může vyžadovat další obory. Například rozhraní Microsoft Graph `Calendars.Read` API vyžaduje obor pro seznam kalendářů uživatele.

Chcete-li získat přístup ke kalendářům uživatele v `Calendars.Read` kontextu aplikace, přidejte delegovaná oprávnění k informacím o registraci aplikace. Potom přidejte `Calendars.Read` obor `acquireTokenSilent` volání.

> [!NOTE]
> Při zvyšování počtu oborů mohou být uživatelé vyzváni k zadání dalších souhlasů.

## <a name="known-issues"></a>Známé problémy

### <a name="issue-1"></a>Problém 1

Při přihlášení k aplikaci ve federované doméně Azure AD se zobrazí jedna z následujících chybových zpráv:

* V požadavku nebyl nalezen žádný platný klientský certifikát.
* V úložišti certifikátů uživatele nebyly nalezeny žádné platné certifikáty.
* Zkuste znovu zvolit jinou metodu ověřování.

Příčina: Možnosti rozlehlé sítě a certifikátů nejsou povoleny.

Řešení: Postupujte podle kroků v části [Povolit integrované ověřování ve federovaných doménách (volitelné).](#enable-integrated-authentication-on-federated-domains-optional)

### <a name="issue-2"></a>Problém 2

Povolíte [integrované ověřování ve federovaných doménách](#enable-integrated-authentication-on-federated-domains-optional) a pokusíte se pomocí Windows Hello v počítači se systémem Windows 10 přihlásit do prostředí s nakonfigurovaným vícefaktorovým ověřováním. Zobrazí se seznam certifikátů. Pokud se však rozhodnete kód PIN použít, okno PIN se nikdy nezobrazí.

Příčina: Tento problém je známé omezení zprostředkovatele webového ověřování v aplikacích UPW, které běží na ploše systému Windows 10. Funguje to dobře na Windows 10 Mobile.

Řešení: Vyberte **Přihlásit se pomocí dalších možností**. Pak vyberte **Přihlásit pomocí uživatelského jména a hesla**. Vyberte **Zadat heslo**. Pak projděte procesem ověřování telefonu.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Pomozte nám vylepšit platformu identit Microsoftu. Řekněte nám, co si myslíte, že dokončení krátké hod-průzkum dvou otázek:

> [!div class="nextstepaction"]
> [Průzkum platformy identity Microsoftu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
