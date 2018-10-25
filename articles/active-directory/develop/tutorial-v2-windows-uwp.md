---
title: Azure AD v2.0 UPW Začínáme | Dokumentace Microsoftu
description: Jak aplikace univerzální platformy Windows (UPW) může volat rozhraní API, které vyžaduje přístupové tokeny pomocí koncového bodu Azure Active Directory v2.0
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 4ba4e844ed6bb01204b7a0adf5020aec255147dd
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986538"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Volání rozhraní Microsoft Graph API z aplikace pro univerzální platformu Windows (XAML)

> [!div renderon="docs"]
> [!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Tato příručka vysvětluje, jak požádat o přístupový token a pak volat Microsoft Graph API nativní aplikace pro univerzální platformu Windows (UPW). V Průvodci platí také pro jiná rozhraní API, které vyžadují přístupových tokenů z koncového bodu Azure Active Directory v2.0.

Na konci tohoto průvodce vaše aplikace volá chráněné rozhraní API pomocí osobních účtů. Příklady jsou outlook.com, live.com a další. Vaše aplikace volá pracovním a školním účtům také z libovolného společnosti nebo organizace, která má Azure Active Directory.

>[!NOTE]
> Tato příručka vyžaduje Visual Studio 2017 s vývoj pro univerzální platformu Windows nainstalované. Zobrazit [nastavení](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) pokyny ke stažení a konfigurace sady Visual Studio pro vývoj aplikací pro univerzální platformu Windows.

## <a name="how-this-guide-works"></a>Jak funguje tento průvodce

![Jak funguje tato příručka grafu](./media/tutorial-v2-windows-uwp/uwp-intro.png)

Tento průvodce vytvoří ukázkovou aplikaci UPW, který se dotazuje rozhraní Microsoft Graph API nebo webové rozhraní API, které přijímá tokeny z koncového bodu Azure Active Directory v2.0. V tomto scénáři token se přidá na požadavky HTTP přes autorizační hlavičky. Microsoft Authentication Library (MSAL) zpracovává token pořízení a obnovení.

## <a name="nuget-packages"></a>Balíčky NuGet

Tato příručka používá následující balíčky NuGet:

|Knihovna|Popis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Identity a ověřování Microsoftu|

## <a name="set-up-your-project"></a>Nastavení projektu

Tato část obsahuje podrobné pokyny k integraci aplikace Windows Desktop .NET (XAML) s *ve službě Microsoft*. Potom můžete zadat dotaz webová rozhraní API, které vyžadují token, jako je například Microsoft Graph API.

Tento průvodce vytvoří aplikaci, která se zobrazí tlačítko tohoto dotazy rozhraní Graph API, tlačítkem pro odhlášení a textová pole, které zobrazují výsledky volání.

> [!NOTE]
> Chcete místo toho stáhněte si tento ukázkový projekt sady Visual Studio? [Stáhnete projekt](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip) a pokračujte [registrace aplikace](#register-your-application "kroku registrace aplikace") krok konfigurace vzorový kód před jejím spuštěním.


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
    Install-Package Microsoft.Identity.Client -Pre -Version 1.1.4-preview0002
    ```

> [!NOTE]
> Tento příkaz nainstaluje [knihovna Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet). Knihovna MSAL získá, ukládá do mezipaměti a aktualizuje tokeny uživatele, které přístup k rozhraním API chráněné službou Azure Active Directory v2.0.

> [!NOTE]
> V tomto kurzu tak není, ale použijte nejnovější verzi MSAL.NET, ale pracujeme na jeho aktualizace.

## <a name="initialize-msal"></a>Inicializovat MSAL
Tento krok vám pomůže vytvořit třídu pro zpracování interakci s MSAL, jako je zpracování tokenů.

1. Otevřít **App.xaml.cs** a přidejte odkaz pro MSAL do třídy:

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

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Volání `AcquireTokenAsync` metodu vede okno, které vyzve uživatele k přihlášení. Aplikace obvykle vyžadují uživatelům umožní přihlásit interaktivně poprvé, které potřebují přístup k chráněnému prostředku. Také může být potřeba přihlášení při selhání pasivní operace k získání tokenu. Příkladem je, když platnost hesla uživatele.

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

`AcquireTokenSilentAsync` Obsluhovala token pořízení a obnovení bez nutnosti zásahu uživatele. Po `AcquireTokenAsync` provádí poprvé a uživatel je vyzván k zadání přihlašovacích údajů, `AcquireTokenSilentAsync` metoda by měla sloužit požádat o tokeny pro následná volání, protože získávat tokeny bezobslužně. Knihovna MSAL zpracuje mezipaměť tokenu a obnovení.

Nakonec `AcquireTokenSilentAsync` metoda selže. Důvody pro selhání může být, že uživatelé mají odhlášení nebo změnit své heslo na jiném zařízení. Knihovna MSAL zjistí, že problém lze vyřešit tak, že vyžaduje interaktivní akci, vyvolá-li `MsalUiRequiredException` výjimky. Vaše aplikace dokáže zpracovat tuto výjimku dvěma způsoby:

* To lze uskutečnit volání proti `AcquireTokenAsync` okamžitě. Toto volání za následek výzvy k přihlášení. Za normálních okolností se tento model používá v online aplikace tam, kde není žádný k dispozici offline obsah pro uživatele. Následující ukázka vygeneroval tento instalační program s asistencí. Zobrazí se v akci první čas spuštění ukázky. 
    * Vzhledem k tomu, že žádný uživatel použil aplikace, `PublicClientApp.Users.FirstOrDefault()` obsahuje hodnotu null a `MsalUiRequiredException` je vyvolána výjimka.
    * Kód v ukázce pak zpracovává výjimku při volání `AcquireTokenAsync`. Toto volání za následek výzvy k přihlášení.

* Nebo místo toho prezentuje vizuální označení pro uživatele, že interaktivní přihlášení je povinné. Potom může vybrat správný čas pro přihlášení. Nebo můžete opakovat aplikace `AcquireTokenSilentAsync` později. Tento model se často používá při uživatelé mohou používat další funkce aplikace bez výpadků. Příkladem je při offline obsah je k dispozici v aplikaci. V takovém případě může uživatel rozhodne ji k přihlášení k přístup k chráněnému prostředku nebo aktualizujte zastaralé informace. Nebo jiná aplikace se můžete rozhodnout používat opakovat `AcquireTokenSilentAsync` po síti obnovení poté, co byla dočasně nedostupný.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Volání rozhraní Microsoft Graph API s využitím, které jste získali token

* Přidejte následující novou metodu pro **MainPage.xaml.cs**. Tato metoda se používá, aby `GET` požadavek s využitím rozhraní Graph API s využitím hlavičku [Authorize]:

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Další informace o provádění volání REST chráněné rozhraní API

V této ukázkové aplikaci `GetHttpContentWithToken` aby protokolu HTTP se používá metoda `GET` požadavku na chráněný prostředek, který se vyžaduje token. Metoda pak vrátí obsah volajícímu. Tato metoda přidá tokenu získaného v **HTTP autorizace** záhlaví. Pro tuto ukázku, prostředek je v rozhraní Microsoft Graph API **mě** koncový bod, který se zobrazí informace o profilu uživatele.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Přidejte metodu k neodhlásí uživatele

* Odhlásit uživatele, přidejte následující metodu do **MainPage.xaml.cs**:

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

`SignOutButton_Click` Metoda odebere uživatele z mezipaměti MSAL uživatele. Tato metoda efektivně říká MSAL zapomenout aktuálního uživatele. Potom budoucí požadavek na získání tokenu úspěšné pouze v případě, že provedl být interaktivní.
V této ukázce podporuje jenom jednoho konkrétního uživatele. Ale MSAL podporuje scénáře, ve kterém můžete více než jeden účet přihlášení ve stejnou dobu. Příkladem je e-mailové aplikace, kde má uživatel několik účtů.

## <a name="display-basic-token-information"></a>Zobrazit základní informace o tokenu

* Přidejte následující metodu do **MainPage.xaml.cs** zobrazit základní informace o tokenu:

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

Tokeny typu ID opatřené **OpenID Connect** také obsahovat malou část informace, které jsou relevantní pro uživatele. `DisplayBasicTokenInfo` Zobrazí základních informací obsažených v tokenu. Příklady jsou zobrazované jméno a ID, datum vypršení platnosti tokenu a řetězec, který představuje přístupový token, samotného. Pokud vyberete **volat Microsoft Graph API** tlačítko několikrát, uvidíte, že stejný token byl znovu použít pro následné požadavky. Zobrazí se také datum vypršení platnosti při MSAL rozhodne, že je čas k obnovení tokenu.

## <a name="register-your-application"></a>Registrace vaší aplikace

Teď budete muset zaregistrovat aplikaci v portálu pro registraci aplikace Microsoftu:
1. Abyste mohli zaregistrovat aplikaci, přejděte na [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/portal/register-app).
2. Zadejte název své aplikace.
3. Ujistěte se, že možnost **instalační program s asistencí** je *nevybraných*.
4. Vyberte **přidat platformy**vyberte **nativní aplikace**a pak vyberte **Uložit**.
5. Zkopírujte identifikátor GUID v **ID aplikace**, vraťte se zpět do sady Visual Studio, otevřete **App.xaml.cs**a nahraďte `your_client_id_here` s ID aplikace, který jste právě zaregistrovali:

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Povolit integrované ověřování u federovaných domén (volitelné)

Povolit integrované ověřování Windows, když se použije s parametrem federované domény Azure Active Directory, manifest aplikace musí povolit další možnosti:

1. Dvakrát klikněte na panel **Package.appxmanifest**.
2. Vyberte **možnosti** kartu a ujistěte se, že jsou povolené následující nastavení:

    - Podnikové ověřování
    - Privátní sítě (klient a Server)
    - Sdílené uživatelské certifikáty

3. Otevřít **App.xaml.cs** a přidejte následující řádek v konstruktoru aplikace:

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> Integrované ověřování Windows není nakonfigurována ve výchozím nastavení pro tuto ukázku. Aplikace, které vyžadují *podnikové ověřování* nebo *sdílené uživatelské certifikáty* možnosti vyžadovat vyšší úroveň ověření ve Windows Store. Také ne všechny vývojáři chtějí provádět vyšší úroveň ověřování. Toto nastavení povolte pouze v případě, že potřebujete integrované ověřování Windows ve federované domény Azure Active Directory.

## <a name="test-your-code"></a>Testování kódu

A otestujte aplikaci stisknutím klávesy F5 ke spuštění projektu v sadě Visual Studio. Hlavní okno se zobrazí:

![Uživatelské rozhraní aplikace](./media/tutorial-v2-windows-uwp/testapp-ui.png)

Jakmile budete připraveni k testování, vyberte **volat Microsoft Graph API**. Pak pomocí účtu organizace Microsoft Azure Active Directory nebo účet Microsoft, jako je live.com nebo outlook.com, přihlaste se. Pokud je poprvé, zobrazí se okno s výzvou uživateli umožní přihlásit:

![Přihlašovací stránka](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>Souhlas
Při prvním přihlášení do aplikace, zobrazí se obrazovka pro vyjádření souhlasu podobný následujícímu. Vyberte **Ano** výslovně souhlas pro přístup:

![Přístup k obrazovce pro vyjádření souhlasu](./media/tutorial-v2-windows-uwp/consentscreen.png)
### <a name="expected-results"></a>Očekávané výsledky
Zobrazí informace o profilu uživatele vrácený voláním rozhraní Microsoft Graph API na **výsledků volání rozhraní API** obrazovky:

![Výsledky volání rozhraní API obrazovky](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

Také si zobrazili základní informace o tokenu opatřené `AcquireTokenAsync` nebo `AcquireTokenSilentAsync` v **informace o tokenu** pole:

|Vlastnost  |Formát  |Popis |
|---------|---------|---------|
|**Název** |Celé jméno uživatele|Uživatel a příjmení název.|
|**Uživatelské jméno** |<span>user@domain.com</span> |Uživatelské jméno, který identifikuje uživatele.|
|**Platnost tokenu vyprší** |DateTime |Čas, kdy vyprší platnost tokenu. Knihovna MSAL je rozšířením datum vypršení platnosti obnovuje se token podle potřeby.|
|**Přístupový Token** |Řetězec |Řetězec tokenu, který je odeslán protokolu HTTP požadavků, které vyžadují *autorizační hlavičky*.|

#### <a name="see-whats-in-the-access-token-optional"></a>Podívejte se, co je v přístupovém tokenu (volitelné)
Volitelně můžete zkopírovat hodnotu v **přístupový Token** a vložte ji https://jwt.ms k dekódování ho a podívejte se, že seznam deklarací identity.

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a delegovaná oprávnění

Vyžaduje rozhraní Microsoft Graph API *user.read* obory a čtení profilu uživatele. V každé aplikaci, která je registrován v portálu pro registraci aplikace ve výchozím nastavení je automaticky přidán tento obor. Další rozhraní API pro Microsoft Graphu a vlastních rozhraní API pro back endového serveru může vyžadovat další obory. Vyžaduje rozhraní Microsoft Graph API *Calendars.Read* oboru seznam kalendářů uživatele.

Chcete-li přístup ke kalendářům uživatele v rámci aplikace, přidejte *Calendars.Read* delegovaná oprávnění aplikace informace o registraci. Pak přidejte *Calendars.Read* rozsah `acquireTokenSilent` volání.

> [!NOTE]
> Uživatelům může zobrazit výzva pro další souhlasy zvýšit počet oborů.

## <a name="known-issues"></a>Známé problémy

### <a name="issue-1"></a>Problém 1
Zobrazí jednu z následujících chybových zpráv při přihlášení na svou aplikaci na federované domény Azure Active Directory:
 - Nenašel se žádný platný klientský certifikát v požadavku.
 - Nenašly se žádné platné certifikáty v úložišti certifikátů uživatele.
 - Zkuste znovu vybrat různé ověřování.

**Příčina:** organizace a certifikát funkce nejsou povolené.

**Řešení:** postupujte podle kroků v [integrované ověřování u federovaných domén](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problém 2
Povolíte [integrované ověřování u federovaných domén](#enable-integrated-authentication-on-federated-domains-optional) a pokuste se použít Windows Hello na počítači s Windows 10 pro přihlášení v prostředí s nakonfigurované vícefaktorové ověřování. Zobrazí seznam certifikátů. Ale pokud budete chtít použít váš PIN kód, v okně kódu PIN se nikdy zobrazí.

**Příčina:** tento problém je známé omezení zprostředkovatele webového ověření v aplikacích UPW, které běží na Windows 10 desktop. Bez problémů funguje na Windows 10 Mobile.

**Alternativní řešení:** vyberte **přihlásit pomocí jiné možnosti**. Potom vyberte **Přihlaste se pomocí uživatelského jména a hesla**. Vyberte **zadejte heslo**. Potom projděte procesem ověřování telefonu.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]