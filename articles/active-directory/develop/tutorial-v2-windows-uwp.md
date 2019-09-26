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
ms.date: 09/24/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0cafc439a24c10c4c5a678219a0e0dce84476ff
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71290856"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Volání rozhraní API Microsoft Graph z aplikace Univerzální platforma Windows (XAML)

> [!div renderon="docs"]

V této příručce se dozvíte, jak může aplikace pro nativní Univerzální platforma Windows (UWP) požádat o přístupový token. Aplikace pak zavolá rozhraní API Microsoft Graph. Tato příručka platí i pro další rozhraní API, která vyžadují přístupové tokeny z koncového bodu Microsoft Identity Platform.

Na konci tohoto průvodce vaše aplikace volá chráněné rozhraní API pomocí osobních účtů. Příklady jsou outlook.com, live.com a další. Vaše aplikace také volá pracovní a školní účty z libovolné společnosti nebo organizace, která má Azure Active Directory (Azure AD).

>[!NOTE]
> Tato příručka vyžaduje Visual Studio s nainstalovanou Univerzální platforma Windows vývoj. Pokyny ke stažení a konfiguraci sady Visual Studio pro vývoj Univerzální platforma Windowsch aplikací najdete v tématu věnovaném [získání](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) .

## <a name="how-this-guide-works"></a>Jak funguje tento průvodce

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Tato příručka vytvoří ukázkovou aplikaci UWP, která se dotazuje Microsoft Graph rozhraní API. V tomto scénáři se token přidá do požadavků HTTP pomocí autorizační hlavičky. Knihovna Microsoft Authentication Library (MSAL) zpracovává získání a obnovení tokenů.

## <a name="nuget-packages"></a>Balíčky NuGet

Tato příručka používá následující balíček NuGet:

|Knihovna|Popis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Identity a ověřování Microsoftu|

## <a name="set-up-your-project"></a>Nastavení projektu

Tato část obsahuje podrobné pokyny pro integraci aplikace Windows Desktop .NET (XAML) s přihlašováním s Microsoftem. Pak se aplikace může dotazovat na webová rozhraní API, která vyžadují token, například Microsoft Graph API.

Tato příručka vytvoří aplikaci, která zobrazí tlačítko s dotazem Graph API a tlačítkem k odhlášení. Zobrazuje také textová pole obsahující výsledky volání.

> [!NOTE]
> Chcete stáhnout tuto ukázkovou aplikaci Visual Studio, místo jejího vytváření? [Stáhněte si projekt](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) a přejděte k kroku [Registrace aplikace](#register-your-application "akrok registrace plikace) , abyste před spuštěním nakonfigurovali vzorek kódu.

### <a name="create-your-application"></a>Vytvoření aplikace

1. Otevřete Visual Studio a vyberte **vytvořit nový projekt**.
1. V možnosti **vytvořit nový projekt**zvolte možnost **prázdná aplikace (univerzální pro Windows)** pro a klikněte na C# tlačítko **Další**.
1. V části **Konfigurovat nový projekt**zadejte název aplikace a vyberte **vytvořit**.
1. Pokud se zobrazí výzva, v **New Univerzální platforma Windows Project**vyberte libovolnou verzi pro **cíl** a **minimální** verzi a vyberte **OK**.

   ![Minimální a cílové verze](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-microsoft-authentication-library-to-your-project"></a>Přidat do projektu knihovnu Microsoft Authentication Library

1. V sadě Visual Studio vyberte **Nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků**.
1. Zkopírujte a vložte následující příkaz v okně **konzoly Správce balíčků** :

    ```powershell
    Install-Package Microsoft.Identity.Client
    ```

   > [!NOTE]
   > Tento příkaz nainstaluje [knihovnu Microsoft Authentication Library](https://aka.ms/msal-net). MSAL získává, ukládá do mezipaměti a aktualizuje tokeny uživatelů, které přistupují k rozhraním API chráněným platformou Microsoft identity.

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

### <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Získání tokenu pro rozhraní Microsoft Graph API pomocí MSAL

V této části se dozvíte, jak pomocí MSAL získat token pro rozhraní Microsoft Graph API. Proveďte změny v souboru *MainPage.XAML.cs* .

1. Do *MainPage.XAML.cs*přidejte následující odkazy:

    ```csharp
    using Microsoft.Identity.Client;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. `MainPage` Třídu nahraďte následujícím kódem:

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

#### Interaktivní získání tokenu uživatele<a name="more-information"></a>

Výsledkem `AcquireTokenInteractive` metody je okno, které vyzve uživatele k přihlášení. Aplikace obvykle vyžadují, aby se uživatelé přihlásili interaktivně při prvním přístupu k chráněnému prostředku. Můžou se taky muset přihlásit, když se nezdařila tichá operace získání tokenu. Příkladem je vypršení platnosti hesla uživatele.

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

`AcquireTokenSilent` Metoda zpracovává získání a obnovení tokenů bez zásahu uživatele. Po `AcquireTokenInteractive` prvním spuštění a vyzvání uživatele k zadání přihlašovacích údajů `AcquireTokenSilent` použijte metodu k vyžádání tokenů pro pozdější volání. Tato metoda získává tokeny tiše. MSAL zpracovává mezipaměť a obnovení tokenu.

Nakonec se `AcquireTokenSilent` metoda nezdařila. Důvody k selhání zahrnují uživatele, který se odhlásil nebo změnil jeho heslo na jiném zařízení. Pokud MSAL zjistí, že problém vyžaduje interaktivní akci, vyvolá `MsalUiRequiredException` výjimku. Vaše aplikace může tuto výjimku zpracovat dvěma způsoby:

* Vaše aplikace volá `AcquireTokenInteractive` okamžitě. Výsledkem tohoto volání je dotazování uživatele na přihlášení. Tento přístup se obvykle používá pro online aplikace, kde pro uživatele není k dispozici žádný obsah offline. Ukázka vygenerovaná tímto průvodcem Setup se řídí vzorem. Při prvním spuštění ukázky se zobrazí v akci.

   Vzhledem k tomu, že aplikace nepoužila žádného uživatele, `accounts.FirstOrDefault()` obsahuje hodnotu null a `MsalUiRequiredException` vyvolá výjimku.

   Kód v ukázce pak zpracovává výjimku voláním `AcquireTokenInteractive`. Výsledkem tohoto volání je dotazování uživatele na přihlášení.

* Vaše aplikace prezentuje vizuální označení uživatelům, kteří se potřebují přihlašovat. Pak si můžou vybrat správný čas pro přihlášení. Aplikace se může opakovat `AcquireTokenSilent` později. Tento postup použijte, když uživatelé můžou používat jiné funkce aplikace bez přerušení. Příkladem je, když je offline obsah k dispozici v aplikaci. V takovém případě se uživatelé můžou rozhodnout, kdy se chtějí přihlásit. Aplikace se může pokusit znovu `AcquireTokenSilent` po dočasné nedostupnosti sítě.

### <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Volání rozhraní API Microsoft Graph pomocí tokenu, který jste právě získali

Do *MainPage.XAML.cs*přidejte následující novou metodu:

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

 Tato metoda vytvoří `GET` požadavek od Graph API `Authorization` pomocí hlavičky.

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Další informace o tom, jak provést volání REST proti chráněnému rozhraní API

V této ukázkové aplikaci `GetHttpContentWithToken` metoda vytvoří požadavek HTTP `GET` proti chráněnému prostředku, který vyžaduje token. Pak metoda vrátí obsah volajícímu. Tato metoda přidá získaný token v autorizační hlavičce **protokolu HTTP** . V této ukázce je prostředkem koncový bod Microsoft Graph API **já** , který zobrazuje informace o profilu uživatele.

### <a name="add-a-method-to-sign-out-the-user"></a>Přidejte metodu pro odhlášení uživatele.

Pokud chcete uživatele odhlásit, přidejte následující metodu do *MainPage.XAML.cs*:

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
> MSAL.NET používá asynchronní metody k získání tokenů nebo manipulaci s účty. Ve vlákně UI musíte podporovat akce uživatelského rozhraní. To je důvod `Dispatcher.RunAsync` volání a preventivních opatření k volání `ConfigureAwait(false)`.

#### Další informace o odhlášení<a name="more-information-on-sign-out"></a>

`SignOutButton_Click` Metoda odebere uživatele z uživatelské mezipaměti MSAL. Tato metoda efektivně oznamuje MSAL, že aktuálnímu uživateli zapomene. Budoucí požadavek na získání tokenu je úspěšný jenom v případě, že je interaktivní.

Aplikace v této ukázce podporuje jednoho uživatele. MSAL podporuje scénáře, ve kterých se uživatel může přihlásit k více než jednomu účtu. Příkladem je e-mailová aplikace, kde má uživatel několik účtů.

### <a name="display-basic-token-information"></a>Zobrazit základní informace o tokenu

Přidejte následující metodu do *MainPage.XAML.cs* k zobrazení základních informací o tokenu:

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

#### Další informace<a name="more-information-1"></a>

Tokeny ID získané pomocí **OpenID Connect** také obsahují malou podmnožinu informací, které se vztahují k uživateli. `DisplayBasicTokenInfo`Zobrazí základní informace obsažené v tokenu. Tyto informace zahrnují zobrazované jméno a ID uživatele. Zahrnuje také datum vypršení platnosti tokenu a řetězec, který představuje samotný přístupový token. Pokud si vyberete tlačítko **rozhraní API pro volání Microsoft Graph** několikrát, uvidíte, že se stejný token znovu použil pro pozdější požadavky. Můžete také zobrazit datum vypršení platnosti, pokud MSAL rozhodne, že je čas obnovit token.

### <a name="display-message"></a>Zobrazit zprávu

Do *MainPage.XAML.cs*přidejte následující novou metodu:

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

## <a name="register-your-application"></a>Zaregistrujte svoji aplikaci.

Nyní je nutné zaregistrovat aplikaci:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyberte **Azure Active Directory** > **registrace aplikací**.
1. Vyberte **Nová registrace**. Zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například *UWP-App-Call-MSGraph*.
1. V části **podporované typy účtů**vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft (např. Skype, Xbox)** a pokračujte kliknutím na **Registrovat** .
1. Na stránce Přehled vyhledejte hodnotu **ID aplikace (klienta)** a zkopírujte ji. Vraťte se do sady `ClientId` Visual Studio, otevřete *MainPage.XAML.cs*a nahraďte hodnotu touto hodnotou.

Konfigurace ověřování pro vaši aplikaci:

1. Zpátky v [Azure Portal](https://portal.azure.com)v části **Spravovat**vyberte **ověřování**.
1. V seznamu **identifikátorů URI pro přesměrování** vyberte pro **typ**možnost **veřejný klient (mobilní & plocha)** a `urn:ietf:wg:oauth:2.0:oob` zadejte **identifikátor URI pro přesměrování**.
1. Vyberte **Uložit**.

Konfigurace oprávnění rozhraní API pro aplikaci:

1. V části **Spravovat**vyberte **oprávnění rozhraní API**.
1. Vyberte **Přidat oprávnění** a pak ověřte, že jste vybrali **rozhraní Microsoft API**.
1. Vyberte **Microsoft Graph**.
1. Vyberte **delegovaná oprávnění**, vyhledejte *uživatele. Přečtěte si* a ověřte, jestli je vybraná možnost **uživatel. čtení** .
1. Pokud jste provedli nějaké změny, vyberte **Přidat oprávnění** a uložte je.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Povolit integrované ověřování u federovaných domén (volitelné)

Pokud chcete povolit integrované ověřování Windows, když se používá s doménou federované služby Azure AD, manifest aplikace musí povolit další funkce. Vraťte se do aplikace v aplikaci Visual Studio.

1. Otevřete soubor *Package. appxmanifest*.
1. Vyberte **Možnosti** a povolte následující nastavení:

   * **Podnikové ověřování**
   * **Privátní sítě (klient & Server)**
   * **Sdílené uživatelské certifikáty**

> [!IMPORTANT]
> [Integrované ověřování systému Windows](https://aka.ms/msal-net-iwa) není pro tuto ukázku standardně nakonfigurováno. Aplikace, které `Enterprise Authentication` vyžadují `Shared User Certificates` nebo poskytují požadavky na vyšší úroveň ověřování ve Windows Storu. Ne všichni vývojáři chtějí provádět vyšší úroveň ověřování. Toto nastavení povolte jenom v případě, že potřebujete integrované ověřování Windows s doménou federované služby Azure AD.

## <a name="test-your-code"></a>Testování kódu

Chcete-li otestovat aplikaci, vyberte F5 pro spuštění projektu v aplikaci Visual Studio. Zobrazí se hlavní okno:

![Uživatelské rozhraní aplikace](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Až budete připraveni na test, vyberte **zavolat Microsoft Graph API**. K přihlášení použijte účet organizace Azure AD nebo účet Microsoft, jako je například live.com nebo outlook.com. Když uživatel poprvé spustí, aplikace zobrazí okno s výzvou, aby se uživatel přihlásil.

### <a name="consent"></a>Souhlas

Při prvním přihlášení do aplikace se zobrazí obrazovka pro vyjádření souhlasu, která je podobná následující. Vyberte **Ano** , pokud chcete výslovně udělit přístup k přístupu:

![Obrazovka souhlasu s přístupem](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Očekávané výsledky

Na obrazovce **výsledky volání rozhraní API** se zobrazí informace o profilu uživatele vracené voláním Microsoft Graph API:

![Obrazovka výsledků volání rozhraní API](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

Zobrazí se také základní informace o tokenu získaném `AcquireTokenInteractive` prostřednictvím `AcquireTokenSilent` nebo v poli **informace** o tokenu:

|Vlastnost  |Formát  |Popis |
|---------|---------|---------|
|`Username` |`user@domain.com` |Uživatelské jméno, které uživatele identifikuje.|
|`Token Expires` |`DateTime` |Čas vypršení platnosti tokenu MSAL rozšiřuje datum vypršení platnosti tím, že podle potřeby obnoví token.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a delegovaných oprávněních

Rozhraní Microsoft Graph API vyžaduje `user.read` , aby se v oboru četl profil uživatele. Tento obor se ve výchozím nastavení přidá v každé aplikaci, která je zaregistrovaná na portálu pro registraci aplikací. Další rozhraní API pro Microsoft Graph a vlastní rozhraní API pro back-end Server můžou vyžadovat další obory. Například Microsoft Graph rozhraní API vyžaduje `Calendars.Read` rozsah pro výpis kalendářů uživatele.

Chcete-li získat přístup k kalendářům uživatele v kontextu aplikace, přidejte `Calendars.Read` delegované oprávnění k informacím o registraci aplikace. Pak přidejte `Calendars.Read` obor `acquireTokenSilent` do volání.

> [!NOTE]
> Uživatelům se může zobrazit výzva k dalšímu souhlasu při zvýšení počtu oborů.

## <a name="known-issues"></a>Známé problémy

### <a name="issue-1"></a>Problém 1

Při přihlášení aplikace ve federované doméně Azure AD se zobrazí jedna z následujících chybových zpráv:

* V požadavku se nenašel žádný platný klientský certifikát.
* V úložišti certifikátů uživatele nebyly nalezeny žádné platné certifikáty.
* Zkuste znovu vybrat jinou metodu ověřování.

Příčina: Funkce Enterprise a Certificate nejsou povolené.

Řešení Postupujte podle kroků v části [povolení integrovaného ověřování u federovaných domén (volitelné)](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problém 2

Povolíte [integrované ověřování pro federované domény](#enable-integrated-authentication-on-federated-domains-optional) a zkusíte použít Windows Hello v počítači s Windows 10 pro přihlášení k prostředí s nakonfigurovaným ověřováním službou Multi-Factor Authentication. Zobrazí se seznam certifikátů. Pokud se ale rozhodnete použít PIN kód, okno PIN se nikdy neprezentuje.

Příčina: Tento problém je známým omezením zprostředkovatele webového ověřování v aplikacích UWP, které běží na Windows 10 Desktop. V systému Windows 10 Mobile funguje správně.

Alternativní řešení: Vyberte **Přihlásit se s dalšími možnostmi**. Pak vyberte **Přihlásit se pomocí uživatelského jména a hesla**. Vyberte **zadat heslo**. Pak Projděte proces ověřování pro telefon.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Pomůžeme nám vylepšit platformu Microsoft identity. Řekněte nám, co si myslíte, díky krátkému průzkumu dvou dotazů:

> [!div class="nextstepaction"]
> [Microsoft Identity Platform Survey](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
