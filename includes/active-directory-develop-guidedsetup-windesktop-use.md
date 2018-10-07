---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 538b0c969d8c039079c09232e06f55e24aabf393
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843490"
---
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Použití MSAL k získání tokenu pro rozhraní Microsoft Graph API

V této části Použití MSAL k získání tokenu pro rozhraní Microsoft Graph API.

1.  V *MainWindow.xaml.cs* přidejte odkaz pro MSAL do třídy:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Nahraďte `MainWindow` třídy kód následujícím kódem:

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string _graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] _scopes = new string[] { "user.read" };

        public MainWindow()
        {
            InitializeComponent();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;

            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();

            try
            {
                authResult = await app.AcquireTokenSilentAsync(_scopes, accounts.FirstOrDefault());
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
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
                ResultText.Text = await GetHttpContentWithToken(_graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    }
    ```

<!--start-collapse-->
### <a name="more-information"></a>Další informace

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Volání `AcquireTokenAsync` metodu vede okno, které vyzve uživatele k přihlášení. Aplikace obvykle vyžadují uživatelům umožní přihlásit interaktivně poprvé, které potřebují přístup k chráněnému prostředku. Také může být potřeba přihlášení při selhání pasivní operace k získání tokenu (například pokud vypršela platnost hesla uživatele).

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

`AcquireTokenSilentAsync` Obsluhovala token pořízení a obnovení bez nutnosti zásahu uživatele. Po `AcquireTokenAsync` provádí poprvé, `AcquireTokenSilentAsync` je obvykle způsob, kterým získat tokeny, které přístup k chráněným prostředkům pro pozdější volání, protože volání požadavku nebo obnovení tokeny se provedou bez upozornění.

Nakonec `AcquireTokenSilentAsync` metoda se nezdaří. Důvody pro selhání může být, že uživatel má odhlásit nebo změnit své heslo na jiném zařízení. Knihovna MSAL zjistí, že problém lze vyřešit tak, že vyžaduje interaktivní akci, vyvolá-li `MsalUiRequiredException` výjimky. Vaše aplikace dokáže zpracovat tuto výjimku dvěma způsoby:

* To lze uskutečnit volání proti `AcquireTokenAsync` okamžitě. Toto volání za následek výzvy k přihlášení. Tento model se obvykle používá v online aplikace tam, kde není žádný k dispozici offline obsah pro uživatele. Ukázka vygeneroval tento instalační program s asistencí následuje tento model, který můžete vidět v akci první čas spuštění ukázky. 

* Vzhledem k tomu, že žádný uživatel použil aplikace, `PublicClientApp.Users.FirstOrDefault()` obsahuje hodnotu null a `MsalUiRequiredException` je vyvolána výjimka. 

* Kód v ukázce pak zpracovává výjimku při volání `AcquireTokenAsync`, výsledek bude výzvy k přihlášení.

* Místo toho ho můžete prezentovat vizuální označení pro uživatele, kteří interaktivnímu přihlášení je povinné, tak, že vyberou správný čas pro přihlášení. Nebo můžete opakovat aplikace `AcquireTokenSilentAsync` později. Tento model se často používá, uživatelé mohou budou používat další funkce aplikace bez výpadků – například při offline obsah je k dispozici v aplikaci. V takovém případě může uživatel rozhodne ji k přihlášení k přístup k chráněnému prostředku nebo aktualizujte zastaralé informace. Další možností, aplikace se můžete rozhodnout používat opakovat `AcquireTokenSilentAsync` obnovení po síti poté, co bylo dočasně nedostupný.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Pomocí tokenu, který jste získali volání rozhraní Microsoft Graph API

Přidejte následující novou metodu do vaší `MainWindow.xaml.cs`. Metoda se používá k Ujistěte se, `GET` požadavek s využitím rozhraní Graph API s využitím hlavičku autorizovat:

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
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Další informace o tom, že volání REST chráněné rozhraní API

V této ukázkové aplikaci, můžete použít `GetHttpContentWithToken` metoda aby HTTP `GET` požadavku na chráněný prostředek, který se vyžaduje token a potom vrátí obsah volajícímu. Tato metoda přidá pořízené token v hlavičce ověřování protokolu HTTP. Pro tuto ukázku, prostředek je v rozhraní Microsoft Graph API *mě* koncový bod, který se zobrazí informace o profilu uživatele.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Přidejte metodu k odhlášení uživatele

Odhlásit uživatele, přidejte následující metodu do vaší `MainWindow.xaml.cs` souboru:

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

`SignOutButton_Click` Metoda odstraní uživatele z mezipaměti MSAL uživatele, který efektivně informuje MSAL zapomenout aktuálního uživatele tak, že budoucí požadavek na získání tokenu bude úspěšné pouze v případě, že je k interaktivní.

I když aplikace v této ukázce podporuje jednoho uživatele, MSAL podporuje scénáře, kde můžete několik účtů přihlášení ve stejnou dobu. Příkladem je e-mailové aplikace, kde má uživatel více účtů.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Zobrazit základní informace o tokenu

Chcete-li zobrazit základní informace o tokenu, přidejte následující metodu do vaší *MainWindow.xaml.cs* souboru:

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
        TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Další informace

Kromě přístupový token, který se používá k volání rozhraní Microsoft Graph API po uživatel přihlásí získá MSAL také ID token. Tento token obsahovat malou část informace, které jsou relevantní pro uživatele. `DisplayBasicTokenInfo` Metoda zobrazuje základní informace, které se nachází v tokenu. Například zobrazí jeho zobrazované jméno a ID, jakož i datum vypršení platnosti tokenu a řetězec představující přístupový token, samotného. Můžete vybrat *volat Microsoft Graph API* tlačítko více než jednou a podívejte se, že stejný token byl znovu použít pro následné požadavky. Zobrazí se také datum vypršení platnosti, které se rozšíří, pokud se rozhodne MSAL je čas k obnovení tokenu.
<!--end-collapse-->

