---
title: Kurz pro ověřování klientů služby Azure SignalR
description: V tomto kurzu se naučíte ověřovat klienty služby Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/13/2018
ms.author: zhshang
ms.openlocfilehash: beaedf754df2b1c4739c5dfb2abcdc40c163dc81
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53254118"
---
# <a name="tutorial-azure-signalr-service-authentication"></a>Kurz: Ověřování pomocí služby Azure SignalR Service

V tomto kurzu se používá aplikace chatovací místnosti zavedená v rychlém startu. Pokud jste ještě nedokončili cvičení [Vytvoření chatovací místnosti s využitím služby SignalR](signalr-quickstart-dotnet-core.md), nejprve ho dokončete.

V tomto kurzu se naučíte implementovat vlastní ověřování a integrovat ho se službou Microsoft Azure SignalR.

Ověřování původně použité v aplikaci chatovací místnosti z rychlého startu je pro reálné scénáře moc jednoduché. Tato aplikace umožňuje každému klientovi, aby deklaroval svou identitu, a server ji jednoduše přijme. Tento přístup ale není moc užitečný pro reálné aplikace, kde by neautorizovaný uživatel mohl zosobnit jiného uživatele a zajistit si tak přístup k citlivým datům.

[GitHub](https://github.com/) poskytuje ověřovací rozhraní API založená na oblíbeném standardním protokolu nazvaném [OAuth](https://oauth.net/). Tato rozhraní API umožňují aplikacím třetích stran ověřovat účty GitHub. V tomto kurzu pomocí těchto rozhraní API implementujete ověřování prostřednictvím účtu GitHubu před tím, než se klientům povolí přihlášení do aplikace chatovací místnosti. Po ověření účtu GitHub se informace o něm přidají jako soubor cookie, který bude webový klient používat k ověřování informací o tomto účtu.

Další informace o rozhraních API pro ověřování OAuth poskytovaných prostřednictvím GitHubu najdete v tématu [Základy ověřování](https://developer.github.com/v3/guides/basics-of-authentication/).

K dokončení kroků v tomto rychlém startu můžete použít jakýkoli editor kódu. Skvělou volbou je však editor [Visual Studio Code](https://code.visualstudio.com/), který je dostupný pro platformy Windows, macOS a Linux.

Kód pro tento kurz je k dispozici ke stažení v [úložišti GitHub AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/GitHubChat).

![Protokol OAuth kompletně hostovaný v Azure](media/signalr-authenticate-oauth/signalr-oauth-complete-azure.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zaregistrovat novou aplikaci OAuth pomocí účtu GitHub
> * Přidat ověřovací kontroler pro podporu ověřování Githubu
> * Nasadit webovou aplikaci ASP.NET Core do Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* Účet vytvořený na [GitHubu](https://github.com/)
* [Git](https://git-scm.com/)
* [Sada .NET Core SDK](https://www.microsoft.com/net/download/windows)
* [Nakonfigurovaná služba Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)
* Stáhněte nebo naklonujte [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) úložiště GitHub.

## <a name="create-an-oauth-app"></a>Vytvoření aplikace OAuth

1. Otevřete webový prohlížeč, přejděte na `https://github.com` a přihlaste se k účtu.

2. Přejděte k **Nastavení** > **Nastavení pro vývojáře** a klikněte na **Register a new application (Registrovat novou aplikaci)** nebo **New OAuth App (Nová aplikace OAuth)** v části *OAuth Apps (Aplikace OAuth)*.

3. Pro tuto novou aplikaci OAuth použijte následující nastavení a potom klikněte na **Zaregistrovat aplikaci**:

    | Název nastavení | Navrhovaná hodnota | Popis |
    | ------------ | --------------- | ----------- |
    | Název aplikace | *Azure SignalR Chat* | GitHub uživatel měl nerozpozná a nenahraje a aplikace, které se ověřují pomocí vztahu důvěryhodnosti.   |
    | Adresa URL domovské stránky | *http://localhost:5000/home* | |
    | Popis aplikace | *Konverzační ukázky Githubu ověřování pomocí služby Azure SignalR* | Popis aplikace je užitečný, protože pomůže uživatelům vaší aplikace zjistit, v jakém kontextu se ověřování používá. |
    | Adresa URL zpětného volání autorizace | *http://localhost:5000/signin-github* | Toto nastavení je nejdůležitějším nastavením vaší aplikace OAuth. Je to adresa URL pro zpětné volání, na kterou GitHub po úspěšném ověření uživatele vrátí. V tomto kurzu musíte použít výchozí adresu URL pro zpětné volání pro balíček *AspNet.Security.OAuth.GitHub*, */signin-github*.  |

4. Jakmile je registrace nové aplikace OAuth hotová, přidejte *ID klienta* a *tajný klíč klienta* do nástroje Secret Manager, a to pomocí následujících příkazů. Hodnoty *Your_GitHub_Client_Id* a *Your_GitHub_Client_Secret* nahraďte hodnotami pro vaši aplikaci OAuth.

        dotnet user-secrets set GitHubClientId Your_GitHub_Client_Id
        dotnet user-secrets set GitHubClientSecret Your_GitHub_Client_Secret

## <a name="implement-the-oauth-flow"></a>Implementace toku OAuth

### <a name="update-the-startup-class-to-support-github-authentication"></a>Aktualizace třídy Startup pro podporu ověřování GitHubu

1. Přidejte odkaz na nejnovější balíčky *Microsoft.AspNetCore.Authentication.Cookies* a *AspNet.Security.OAuth.GitHub* a obnovte všechny balíčky.

        dotnet add package Microsoft.AspNetCore.Authentication.Cookies -v 2.1.0-rc1-30656
        dotnet add package AspNet.Security.OAuth.GitHub -v 2.0.0-rc2-final
        dotnet restore

1. Otevřete *Startup.cs* a přidejte příkazy `using` pro následující obory názvů:

    ```csharp
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Security.Claims;
    using Microsoft.AspNetCore.Authentication.Cookies;
    using Microsoft.AspNetCore.Authentication.OAuth;
    using Newtonsoft.Json.Linq;
    ```

2. V horní části třídy `Startup` přidejte konstanty pro klíče nástroje Secret Manager, ve který jsou uložené tajné kódy aplikace OAuth GitHubu.

    ```csharp
    private const string GitHubClientId = "GitHubClientId";
    private const string GitHubClientSecret = "GitHubClientSecret";
    ```

3. Do metody `ConfigureServices` přidejte následující kód pro podporu ověřování pomocí aplikace OAuth GitHubu:

    ```csharp
    services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
        .AddCookie()
        .AddGitHub(options =>
        {
            options.ClientId = Configuration[GitHubClientId];
            options.ClientSecret = Configuration[GitHubClientSecret];
            options.Scope.Add("user:email");
            options.Events = new OAuthEvents
            {
                OnCreatingTicket = GetUserCompanyInfoAsync
            };
        });
    ```

4. Do třídy `Startup` přidejte pomocnou metodu `GetUserCompanyInfoAsync`.

    ```csharp
    private static async Task GetUserCompanyInfoAsync(OAuthCreatingTicketContext context)
    {
        var request = new HttpRequestMessage(HttpMethod.Get, context.Options.UserInformationEndpoint);
        request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", context.AccessToken);

        var response = await context.Backchannel.SendAsync(request,
            HttpCompletionOption.ResponseHeadersRead, context.HttpContext.RequestAborted);

        var user = JObject.Parse(await response.Content.ReadAsStringAsync());
        if (user.ContainsKey("company"))
        {
            var company = user["company"].ToString();
            var companyIdentity = new ClaimsIdentity(new[]
            {
                new Claim("Company", company)
            });
            context.Principal.AddIdentity(companyIdentity);
        }
    }
    ```

5. Aktualizujte metodu `Configure` třídy Startup následujícím řádkem kódu a potom soubor uložte.

    ```csharp
    app.UseAuthentication();
    ```

### <a name="add-an-authentication-controller"></a>Přidání kontroleru ověřování

V této části budete implementovat rozhraní API `Login`, které ověřuje klienty pomocí aplikace OAuth GitHubu. Po ověření toto rozhraní API přidá do odpovědi webového klienta soubor cookie a potom klienta přesměruje zpátky do chatovací aplikace. Tento soubor cookie se pak bude používat k identifikaci příslušného klienta.

1. Do adresáře *chattest\Controllers* přidejte soubor s kódem nového kontroleru. Tento soubor pojmenujte *AuthController.cs*.

2. Do kontroleru ověřování přidejte následující kód. Pokud jako adresář projektu nepoužíváte *chattest*, nezapomeňte aktualizovat obor názvů:

    ```csharp
    using AspNet.Security.OAuth.GitHub;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.AspNetCore.Mvc;

    namespace chattest.Controllers
    {
        [Route("/")]
        public class AuthController : Controller
        {
            [HttpGet("login")]
            public IActionResult Login()
            {
                if (!User.Identity.IsAuthenticated)
                {
                    return Challenge(GitHubAuthenticationDefaults.AuthenticationScheme);
                }

                HttpContext.Response.Cookies.Append("githubchat_username", User.Identity.Name);
                HttpContext.SignInAsync(User);
                return Redirect("/");
            }
        }
    }
    ```

3. Uložte provedené změny.

### <a name="update-the-hub-class"></a>Aktualizace třídy Hub

Pokud se ve výchozím nastavení webový klient pokusí připojit ke službě SignalR, připojení se udělí na základě přístupového tokenu, který se poskytuje interně. Tento přístupový token není přidružený k žádné ověřené identitě. Ve skutečnosti jde o anonymní přístup.

V této části zapnete reálné ověřování přidáním atributu `Authorize` do třídy Hub a aktualizací metod centra tak, aby načítaly uživatelské jméno z deklarace identity ověřeného uživatele.

1. Otevřete *Hub\Chat.cs* a přidejte odkazy na tyto obory názvů:

    ```csharp
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    ```

2. Kód centra aktualizujte následujícím způsobem. Tento kód přidá atribut `Authorize` do třídy `Chat` a v metodách centra používá ověřenou identitu uživatelů. Přidá se také metody `OnConnectedAsync`, která při každém připojení nového klienta zaprotokoluje systémovou zprávu do chatovací místnosti.

    ```csharp
    [Authorize]
    public class Chat : Hub
    {
        public override Task OnConnectedAsync()
        {
            return Clients.All.SendAsync("broadcastMessage", "_SYSTEM_", $"{Context.User.Identity.Name} JOINED");
        }

        // Uncomment this line to only allow user in Microsoft to send message
        //[Authorize(Policy = "Microsoft_Only")]
        public void BroadcastMessage(string message)
        {
            Clients.All.SendAsync("broadcastMessage", Context.User.Identity.Name, message);
        }

        public void Echo(string message)
        {
            var echoMessage = $"{message} (echo from server)";
            Clients.Client(Context.ConnectionId).SendAsync("echo", Context.User.Identity.Name, echoMessage);
        }
    }
    ```

3. Uložte provedené změny.

### <a name="update-the-web-client-code"></a>Aktualizace kódu webového klienta

1. Otevřete *wwwroot\index.html* a kód, který vyzývá k zadání uživatelského jména, nahraďte kódem pro použití souboru cookie vráceného kontrolerem ověřování.

    Ze souboru *index.html* odeberte následující kód:

    ```javascript
    // Get the user name and store it to prepend to messages.
    var username = generateRandomName();
    var promptMessage = 'Enter your name:';
    do {
        username = prompt(promptMessage, username);
        if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
            username = '';
            promptMessage = 'Invalid input. Enter your name:';
        }
    } while(!username)
    ```

    Místo kódu uvedeného výše přidejte následující kód pro použití souboru cookie:

    ```javascript
    // Get the user name cookie.
    function getCookie(key) {
        var cookies = document.cookie.split(';').map(c => c.trim());
        for (var i = 0; i < cookies.length; i++) {
            if (cookies[i].startsWith(key + '=')) return unescape(cookies[i].slice(key.length + 1));
        }
        return '';
    }
    var username = getCookie('githubchat_username');
    ```

2. Přímo pod přidaný řádek kódu pro použití souboru cookie přidejte následující definici pro funkci `appendMessage`:

    ```javascript
    function appendMessage(encodedName, encodedMsg) {
        var messageEntry = createMessageEntry(encodedName, encodedMsg);
        var messageBox = document.getElementById('messages');
        messageBox.appendChild(messageEntry);
        messageBox.scrollTop = messageBox.scrollHeight;
    }
    ```

3. Pomocí následujícího kódu aktualizujte funkce `bindConnectionMessage` a `onConnected`, aby využívaly `appendMessage`.

    ```javascript
    function bindConnectionMessage(connection) {
        var messageCallback = function(name, message) {
            if (!message) return;
            // Html encode display name and message.
            var encodedName = name;
            var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
            appendMessage(encodedName, encodedMsg);
        };
        // Create a function that the hub can call to broadcast messages.
        connection.on('broadcastMessage', messageCallback);
        connection.on('echo', messageCallback);
        connection.onclose(onConnectionError);
    }

    function onConnected(connection) {
        console.log('connection started');
        document.getElementById('sendmessage').addEventListener('click', function (event) {
            // Call the broadcastMessage method on the hub.
            if (messageInput.value) {
                connection
                    .invoke('broadcastMessage', messageInput.value)
                    .catch(e => appendMessage('_BROADCAST_', e.message));
            }

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
        document.getElementById('message').addEventListener('keypress', function (event) {
            if (event.keyCode === 13) {
                event.preventDefault();
                document.getElementById('sendmessage').click();
                return false;
            }
        });
        document.getElementById('echo').addEventListener('click', function (event) {
            // Call the echo method on the hub.
            connection.send('echo', messageInput.value);

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
    }
    ```

4. V dolní části souboru *index.html* aktualizujte obslužnou rutinu chyb pro `connection.start()`, aby vyzývala uživatele k přihlášení.

    ```javascript
    connection.start()
        .then(function () {
            onConnected(connection);
        })
        .catch(function (error) {
            if (error) {
                if (error.message) {
                    console.error(error.message);
                }
                if (error.statusCode && error.statusCode === 401) {
                    appendMessage('_BROADCAST_', 'You\'re not logged in. Click <a href="/login">here</a> to login with GitHub.');
                }
            }
        });
    ```

5. Uložte provedené změny.

## <a name="build-and-run-the-app-locally"></a>Sestavení a místní spuštění aplikace

1. Uložte změny všech souborů.

2. Pokud chcete aplikaci sestavit pomocí .NET Core CLI, spusťte v příkazovém prostředí následující příkaz:

        dotnet build

3. Po úspěšném dokončení sestavení spuštěním následujícího příkazu místně spusťte webovou aplikaci:

        dotnet run

    Ve výchozím nastavení se tato aplikace bude místně hostovat na portu 5000:

        E:\Testing\chattest>dotnet run
        Hosting environment: Production
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.

4. Spusťte okno prohlížeče a přejděte na adresu `http://localhost:5000`. Kliknutím na odkaz **tady** v horní části se přihlaste s využitím GitHubu.

    ![Protokol OAuth kompletně hostovaný v Azure](media/signalr-authenticate-oauth/signalr-oauth-complete-azure.png)

    Zobrazí se výzva k autorizaci přístupu chatovací aplikace k vašemu účtu GitHub. Klikněte na tlačítko **Authorize (Autorizovat)**.

    ![Autorizace aplikace OAuth](media/signalr-authenticate-oauth/signalr-authorize-oauth-app.png)

    Budete přesměrováni do chatovací aplikace a přihlášeni pomocí vašeho účtu GitHub. Webová aplikace určila název vašeho účtu tak, že vás ověřila pomocí nového ověřování, které jste přidali.

    ![Identifikace účtu](media/signalr-authenticate-oauth/signalr-oauth-account-identified.png)

    Teď když chatovací aplikace provádí ověřování pomocí GitHubu a ukládá ověřovací informace jako soubory cookie, měli byste ji nasadit do Azure, aby se i ostatní uživatelé mohli ověřovat pomocí svých účtů a komunikovat z dalších pracovních stanic.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-the-app-to-azure"></a>Nasadit aplikaci do Azure

V této části pomocí rozhraní příkazového řádku (CLI) Azure z Azure Cloud Shellu vytvoříte novou [webovou aplikaci Azure](https://docs.microsoft.com/azure/app-service/) pro hostování vaší aplikace ASP.NET v Azure. Tato webová aplikace bude nakonfigurovaná tak, aby využívala místní nasazení z Gitu. Tato webová aplikace bude také nakonfigurovaná s využitím vašeho připojovacího řetězce SignalR, tajných kódů aplikace OAuth GitHubu a uživatele nasazení.

Kroky v této části používají rozšíření *signalr* pro rozhraní příkazového řádku Azure. Spuštěním následujícího příkazu nainstalujte rozšíření *signalr* pro Azure CLI:

```azurecli-interactive
az extension add -n signalr
```

Při vytváření v následujících prostředků nezapomeňte použít stejnou skupinu prostředků, ve které jsou umístěné prostředky služby SignalR. Tento přístup vám později, až budete chtít odebrat všechny prostředky, výrazně usnadní čištění. Uvedený příklad předpokládá, že jste použili název skupiny doporučený v předchozích kurzech, *SignalRTestResources*.

### <a name="create-the-web-app-and-plan"></a>Vytvoření webové aplikace a plánu

Zkopírujte text pro níže uvedené příkazy a aktualizujte parametry. Vložte aktualizovaný skript do Azure Cloud Shellu a stisknutím **Enter** vytvořte novou webovou aplikaci a plán služby App Service.

```azurecli-interactive
#========================================================================
#=== Update these variable for your resource group name.              ===
#========================================================================
ResourceGroupName=SignalRTestResources

#========================================================================
#=== Update these variable for your web app.                          ===
#========================================================================
WebAppName=myWebAppName
WebAppPlan=myAppServicePlanName

# Create an App Service plan.
az appservice plan create --name $WebAppPlan --resource-group $ResourceGroupName \
    --sku FREE

# Create the new Web App
az webapp create --name $WebAppName --resource-group $ResourceGroupName \
    --plan $WebAppPlan
```

| Parametr | Popis |
| -------------------- | --------------- |
| ResourceGroupName | Tento název skupiny prostředků byl navržený v předchozích kurzech. Je vhodné udržovat všechny prostředky pro kurzy seskupené. Použijte stejnou skupinu prostředků, kterou jste používali v předchozích kurzech. |
| WebAppPlan | Zadejte nový jedinečný název plánu služby App Service. |
| WebAppName | To bude název nové webové aplikace a část adresy URL. Použijte jedinečný název. Příklad: signalrtestwebapp22665120.   |

### <a name="add-app-settings-to-the-web-app"></a>Přidat aplikačních nastavení do webové aplikace

V této části přidáte aplikační nastavení pro následující komponenty:

* Připojovací řetězec prostředku služby SignalR
* ID klienta aplikace OAuth GitHubu
* Tajný kód klienta aplikace OAuth GitHubu

Zkopírujte text pro níže uvedené příkazy a aktualizujte parametry. Vložte aktualizovaný skript do služby Azure Cloud Shell a stisknutím **Enter** přidejte tato aplikační nastavení:

```azurecli-interactive
#========================================================================
#=== Update these variables for your GitHub OAuth App.                ===
#========================================================================
GitHubClientId=1234567890
GitHubClientSecret=1234567890

#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
SignalRServiceResource=mySignalRresourcename
WebAppName=myWebAppName

# Get the SignalR Service resource hostName
signalRhostname=$(az signalr show --name $SignalRServiceResource \
    --resource-group $ResourceGroupName --query hostName -o tsv)

# Get the SignalR primary key
signalRprimarykey=$(az signalr key list --name $SignalRServiceResource \
    --resource-group $ResourceGroupName --query primaryKey -o tsv)

# Form the connection string to the service resource
connstring="Endpoint=https://$signalRhostname;AccessKey=$signalRprimarykey;"

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "Azure__SignalR__ConnectionString=$connstring"

#Add the app settings to use with GitHub authentication
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientId=$GitHubClientId"
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientSecret=$GitHubClientSecret"
```

| Parametr | Popis |
| -------------------- | --------------- |
| GitHubClientId | Této proměnné přiřaďte tajné ID klienta pro vaši aplikaci OAuth GitHubu. |
| GitHubClientSecret | Této proměnné přiřaďte tajné heslo pro vaši aplikaci OAuth GitHubu. |
| ResourceGroupName | Tuto proměnnou aktualizujte tak, aby to byl stejný název skupiny prostředků, který jste použili v předchozí části. |
| SignalRServiceResource | Tuto proměnnou aktualizujte názvem prostředku služby SignalR Service, který jste vytvořili v rychlém startu. Příklad: signalrtestsvc48778624. |
| WebAppName | Tuto proměnnou aktualizujte názvem nové webové aplikace, kterou jste vytvořili v předchozí části. |

### <a name="configure-the-web-app-for-local-git-deployment"></a>Konfigurace webové aplikace pro místní nasazení z Gitu

Do Azure Cloud Shellu vložte následující skript. Tento skript vytvoří jméno a heslo nového uživatele nasazení, které budete používat při nasazování vašeho kódu do webové aplikace pomocí Gitu. Tento skript také pro tuto webovou aplikaci nakonfiguruje nasazení s využitím místního úložiště Git a vrátí adresu URL nasazení z Gitu.

```azurecli-interactive
#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
WebAppName=myWebAppName

#========================================================================
#=== Update these variables for your deployment user.                 ===
#========================================================================
DeploymentUserName=myUserName
DeploymentUserPassword=myPassword

# Add the desired deployment user name and password
az webapp deployment user set --user-name $DeploymentUserName \
    --password $DeploymentUserPassword

# Configure Git deployment and note the deployment URL in the output
az webapp deployment source config-local-git --name $WebAppName \
    --resource-group $ResourceGroupName \
    --query [url] -o tsv
```

| Parametr | Popis |
| -------------------- | --------------- |
| DeploymentUserName | Zvolte jméno nového uživatele nasazení. |
| DeploymentUserPassword | Zvolte heslo pro nového uživatele nasazení. |
| ResourceGroupName | Použijte stejnou skupinu prostředků, kterou jste použili v předchozí části. |
| WebAppName | To bude název nové webové aplikace, kterou jste vytvořili. |

Poznamenejte si adresu URL nasazení Gitu vrácenou tímto příkazem. Tuto adresu URL použijete později.

### <a name="deploy-your-code-to-the-azure-web-app"></a>Nasazení vašeho kódu do webové aplikace Azure

Pokud chcete nasadit váš kód, spusťte následující příkazy v Git Shellu.

1. Přejděte do kořenového adresáře vašeho projektu. Pokud jste projekt neinicializovali s využitím úložiště Git, spusťte následující příkaz:

    ```bash
    git init
    ```

2. Přidejte příkaz remote pro adresu URL nasazení z Gitu, kterou jste si poznamenali dřív:

    ```bash
    git remote add Azure <your git deployment url>
    ```

3. Připravte všechny soubory v inicializovaném úložišti a přidejte příkaz commit.

    ```bash
    git add -A
    git commit -m "init commit"
    ```

4. Nasaďte váš kód do webové aplikace v Azure.

    ```bash
    git push Azure master
    ```

    Abyste mohli nasadit kód do Azure, zobrazí se výzva k ověření. Zadejte uživatelské jméno a heslo uživatele nasazení, které jste vytvořili výš.

### <a name="update-the-github-oauth-app"></a>Aktualizace aplikace OAuth GitHubu

Poslední věcí, která zbývá, je aktualizace nastavení **Adresa URL domovské stránky** a **Adresa URL zpětného volání autorizace** aplikace OAuth GitHubu, aby odkazovaly na novou hostovanou aplikaci.

1. Otevřete [http://github.com](http://github.com) v prohlížeči a přejděte k **Nastavení** > **Nastavení pro vývojáře** > **Oauth Apps (Aplikace OAuth)**.

2. Klikněte na vaši ověřovací aplikaci a aktualizujte nastavení **Adresa URL domovské stránky** a **Adresa URL zpětného volání autorizace**, jak je uvedeno dál:

    | Nastavení | Příklad: |
    | ------- | ------- |
    | Adresa URL domovské stránky | https://signalrtestwebapp22665120.azurewebsites.net/home |
    | Adresa URL zpětného volání autorizace | https://signalrtestwebapp22665120.azurewebsites.net/signin-github |

3. Přejděte na adresu URL webové aplikace a otestujte aplikaci.

    ![Protokol OAuth kompletně hostovaný v Azure](media/signalr-authenticate-oauth/signalr-oauth-complete-azure.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat k dalšímu kurzu, můžete prostředky vytvořené v tomto rychlém startu zachovat pro pozdější použití.

V opačném případě, pokud jste už s ukázkovou aplikací v tomto rychlém startu skončili, můžete prostředky Azure vytvořené v tomto rychlém startu odstranit, abyste se vyhnuli poplatkům.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a skupina prostředků včetně všech v ní obsažených prostředků bude trvale odstraněna. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste vytvořili prostředky pro hostování této ukázky ve stávající skupině prostředků obsahující prostředky, které chcete zachovat, můžete místo odstranění skupiny prostředků odstranit jednotlivé prostředky z jejich odpovídajících oken.

Přihlaste se na web [Azure Portal ](https://portal.azure.com) a klikněte na **Skupiny prostředků**.

Do textového pole **Filtrovat podle názvu...** zadejte název vaší skupiny prostředků. V pokynech v tomto článku se používala skupina prostředků *SignalRTestResources*. Ve výsledcích hledání klikněte na **...** u vaší skupiny prostředků a pak na **Odstranit skupinu prostředků**.

![Odstranění](./media/signalr-authenticate-oauth/signalr-delete-resource-group.png)

Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění zadáním názvu vaší skupiny prostředků a klikněte na **Odstranit**.

Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste přidali ověřování pomocí OAuth pro zajištění lepšího přístupu k ověřování s využitím služby Azure SignalR. Další informace o použití služby Azure SignalR najdete v ukázkách Azure CLI pro službu SignalR.

> [!div class="nextstepaction"]
> [Ukázky rozhraní příkazového řádku pro službu Azure SignalR](./signalr-cli-samples.md)