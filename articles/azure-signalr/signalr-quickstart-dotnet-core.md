---
title: Rychlý Start, kde se dozvíte, jak používat službu Azure Signal
description: Rychlý start popisující použití služby Azure SignalR k vytvoření chatovací místnosti s využitím aplikací ASP.NET Core MVC.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: 9099a8620f6e4d87ec38c10226d94b1b3cd3462f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865976"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Rychlý Start: vytvoření chatovací místnosti pomocí služby Signal

Azure SignalR je služba Azure, která vývojářům pomáhá snadno vytvářet webové aplikace s funkcemi v reálném čase. Tato služba byla původně založena na nástroji [Signal pro ASP.NET Core 2,1](/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1), ale nyní podporuje novější verze.

V tomto článku se dozvíte, jak začít se službou Azure SignalR. V tomto rychlém startu vytvoříte aplikaci Chat pomocí webové aplikace ASP.NET Core MVC. Tato aplikace naváže připojení k vašemu prostředku služby Azure SignalR a umožní tak aktualizace obsahu v reálném čase. Webovou aplikaci budete hostovat místně a připojíte se s více klienty prohlížeče. Každý klient bude moct nabízet aktualizace obsahu do všech ostatních klientů. 

K dokončení kroků v tomto rychlém startu můžete použít jakýkoli editor kódu. Jedna z možností je [Visual Studio Code](https://code.visualstudio.com/), která je k dispozici na platformách Windows, MacOS a Linux.

Kód pro tento kurz je k dispozici ke stažení v [úložišti GitHub AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Prostředky Azure používané v tomto rychlém startu můžete také vytvořit pomocí [skriptu vytvoření skriptu služby signalizace](scripts/signalr-cli-create-service.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>Požadavky

* Nainstalujte [.NET Core SDK](https://dotnet.microsoft.com/download).
* Stáhněte nebo naklonujte úložiště GitHub [AzureSignalR-Sample](https://github.com/aspnet/AzureSignalR-samples) . 

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsnetcore)prosím o tom.

## <a name="create-an-azure-signalr-resource"></a>Vytvořené prostředku služby Azure SignalR

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsnetcore)prosím o tom.

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

V této části použijete [rozhraní příkazového řádku .NET Core (CLI)](/dotnet/core/tools/) pro vytvoření projektu webové aplikace ASP.NET Core MVC. Výhodou použití .NET Core CLI přes Visual Studio je to, že je k dispozici na platformách Windows, macOS a Linux. 

1. Vytvořte složku pro váš projekt. V tomto rychlém startu se používá složka *E:\Testing\chattest* .

2. V nové složce spusťte následující příkaz, který vytvoří projekt:

    ```dotnetcli
    dotnet new mvc
    ```

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsnetcore)prosím o tom.

## <a name="add-secret-manager-to-the-project"></a>Přidání nástroje Secret Manager do projektu

V této části přidáte do svého projektu [Nástroj Správce tajných klíčů](/aspnet/core/security/app-secrets) . Nástroj Správce tajných klíčů ukládá citlivá data pro vývojovou práci mimo strom projektu. Tento přístup pomáhá zabránit nechtěnému sdílení tajných kódů aplikací ve zdrojovém kódu.

1. Otevřete soubor *.csproj*. Přidejte element `DotNetCliToolReference` zahrnující *Microsoft.Extensions.SecretManager.Tools*. Přidejte také `UserSecretsId` element, jak je znázorněno v následujícím kódu pro *chatovat. csproj* a uložte soubor.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.2" />
    </ItemGroup>

    </Project>
    ```

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsnetcore)prosím o tom.

## <a name="add-azure-signalr-to-the-web-app"></a>Přidání služby Azure SignalR do webové aplikace

1. Přidejte odkaz na `Microsoft.Azure.SignalR` balíček NuGet spuštěním následujícího příkazu:

    ```dotnetcli
    dotnet add package Microsoft.Azure.SignalR
    ```

2. Spusťte následující příkaz pro obnovení balíčků pro váš projekt:

    ```dotnetcli
    dotnet restore
    ```

3. Do nástroje Secret Manager přidejte tajný kód *Azure:SignalR:ConnectionString*. 

    Tento tajný kód bude obsahovat připojovací řetězec pro přístup k vašemu prostředku služby SignalR. *Azure: signaler: připojovací řetězec* je výchozí konfigurační klíč, který signalizuje, aby se navázalo připojení. Hodnotu v následujícím příkazu nahraďte připojovacím řetězcem pro prostředek služby Signal.

    Tento příkaz musíte spustit ve stejném adresáři jako soubor *. csproj* .

    ```dotnetcli
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"
    ```

    Správce tajného klíče se použije jenom pro testování webové aplikace, když je hostovaný místně. V pozdějším kurzu nasadíte webovou aplikaci Chat do Azure. Po nasazení webové aplikace do Azure použijete nastavení aplikace místo uložení připojovacího řetězce pomocí Správce tajných klíčů.

    K tomuto tajnému kódu se dostanete pomocí konfiguračního rozhraní API. Dvojtečka (:) funguje v názvu konfigurace s rozhraním API konfigurace na všech podporovaných platformách. Viz [Konfigurace podle prostředí](/dotnet/core/extensions/configuration-providers#environment-variable-configuration-provider).


4. Otevřete *Startup. cs* a aktualizujte `ConfigureServices` metodu, aby používala službu Azure Signal Service, voláním `AddSignalR()` `AddAzureSignalR()` metod a:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR()
                .AddAzureSignalR();
    }
    ```

    Když nepředá parametr do `AddAzureSignalR()` , tento kód použije výchozí konfigurační klíč pro připojovací řetězec prostředků služby Signal. Výchozí konfigurační klíč je *Azure: signaler: ConnectionString*.

5. V části *Startup. cs* aktualizujte `Configure` metodu tak, že ji nahradíte následujícím kódem.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseRouting();
        app.UseFileServer();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ChatHub>("/chat");
        });
    }
    ```

### <a name="add-a-hub-class"></a>Přidání třídy centra

V nástroji Signal je rozbočovač základní komponentou, která zveřejňuje sadu metod, které mohou být volány z klienta. V této části nadefinujete třídu centra se dvěma metodami:

* `Broadcast`: Tato metoda rozešle zprávu do všech klientů.
* `Echo`: Tato metoda odešle zprávu zpět volajícímu.

Obě metody používají `Clients` rozhraní, které poskytuje sada SDK signalizace ASP.NET Core. Toto rozhraní vám umožní přístup ke všem připojeným klientům, takže můžete nabízet obsah vašim klientům.

1. Do adresáře vašeho projektu přidejte novou složku *Hub*. Přidejte nový soubor s kódem centra s názvem *ChatHub. cs* do nové složky.

2. Přidejte následující kód do souboru *ChatHub. cs* a Definujte třídu centra a uložte soubor.

    Aktualizujte obor názvů pro tuto třídu, pokud jste použili název projektu, který se liší od *signalizace. Mvc*.

    ```csharp
    using Microsoft.AspNetCore.SignalR;
    using System.Threading.Tasks;
    
    namespace SignalR.Mvc
    {
        public class ChatHub : Hub
        {
            public Task BroadcastMessage(string name, string message) =>
                Clients.All.SendAsync("broadcastMessage", name, message);
    
            public Task Echo(string name, string message) =>
                Clients.Client(Context.ConnectionId)
                       .SendAsync("echo", name, $"{message} (echo from server)");
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>Přidání rozhraní klienta pro webovou aplikaci

Uživatelské rozhraní klienta pro tuto aplikaci chatovací místnosti se bude skládat z HTML a JavaScriptu v souboru s názvem *index.html* v adresáři *wwwroot* .

Zkopírujte soubor *CSS/Web. CSS* ze složky *wwwroot* [úložiště ukázek](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot). Nahraďte *šablonu CSS/Web. CSS* projektu, kterou jste zkopírovali.

Tady je hlavní kód *index.html*:

Vytvořte nový soubor v adresáři *wwwroot* s názvem *index.html*, zkopírujte a vložte následující kód HTML do nově vytvořeného souboru:

```html
<!DOCTYPE html>
<html>
<head>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" rel="stylesheet" />
    <link href="css/site.css" rel="stylesheet" />
    <title>Azure SignalR Group Chat</title>
</head>
<body>
    <h2 class="text-center" style="margin-top: 0; padding-top: 30px; padding-bottom: 30px;">Azure SignalR Group Chat</h2>
    <div class="container" style="height: calc(100% - 110px);">
        <div id="messages" style="background-color: whitesmoke; "></div>
        <div style="width: 100%; border-left-style: ridge; border-right-style: ridge;">
            <textarea id="message"
                      style="width: 100%; padding: 5px 10px; border-style: hidden;"
                      placeholder="Type message and press Enter to send..."></textarea>
        </div>
        <div style="overflow: auto; border-style: ridge; border-top-style: hidden;">
            <button class="btn-warning pull-right" id="echo">Echo</button>
            <button class="btn-success pull-right" id="sendmessage">Send</button>
        </div>
    </div>
    <div class="modal alert alert-danger fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <div>Connection Error...</div>
                    <div><strong style="font-size: 1.5em;">Hit Refresh/F5</strong> to rejoin. ;)</div>
                </div>
            </div>
        </div>
    </div>

    <!--Reference the SignalR library. -->
    <script src="https://cdn.jsdelivr.net/npm/@microsoft/signalr@3.1.8/dist/browser/signalr.min.js"></script>

    <!--Add script to update the page and send messages.-->
    <script type="text/javascript">
        document.addEventListener('DOMContentLoaded', function () {

            const generateRandomName = () =>
                Math.random().toString(36).substring(2, 10);

            let username = generateRandomName();
            const promptMessage = 'Enter your name:';
            do {
                username = prompt(promptMessage, username);
                if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
                    username = '';
                    promptMessage = 'Invalid input. Enter your name:';
                }
            } while (!username)

            const messageInput = document.getElementById('message');
            messageInput.focus();

            function createMessageEntry(encodedName, encodedMsg) {
                var entry = document.createElement('div');
                entry.classList.add("message-entry");
                if (encodedName === "_SYSTEM_") {
                    entry.innerHTML = encodedMsg;
                    entry.classList.add("text-center");
                    entry.classList.add("system-message");
                } else if (encodedName === "_BROADCAST_") {
                    entry.classList.add("text-center");
                    entry.innerHTML = `<div class="text-center broadcast-message">${encodedMsg}</div>`;
                } else if (encodedName === username) {
                    entry.innerHTML = `<div class="message-avatar pull-right">${encodedName}</div>` +
                        `<div class="message-content pull-right">${encodedMsg}<div>`;
                } else {
                    entry.innerHTML = `<div class="message-avatar pull-left">${encodedName}</div>` +
                        `<div class="message-content pull-left">${encodedMsg}<div>`;
                }
                return entry;
            }

            function bindConnectionMessage(connection) {
                var messageCallback = function (name, message) {
                    if (!message) return;
                    var encodedName = name;
                    var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
                    var messageEntry = createMessageEntry(encodedName, encodedMsg);

                    var messageBox = document.getElementById('messages');
                    messageBox.appendChild(messageEntry);
                    messageBox.scrollTop = messageBox.scrollHeight;
                };
                connection.on('broadcastMessage', messageCallback);
                connection.on('echo', messageCallback);
                connection.onclose(onConnectionError);
            }

            function onConnected(connection) {
                console.log('connection started');
                connection.send('broadcastMessage', '_SYSTEM_', username + ' JOINED');
                document.getElementById('sendmessage').addEventListener('click', function (event) {
                    if (messageInput.value) {
                        connection.send('broadcastMessage', username, messageInput.value);
                    }

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
                    connection.send('echo', username, messageInput.value);

                    messageInput.value = '';
                    messageInput.focus();
                    event.preventDefault();
                });
            }

            function onConnectionError(error) {
                if (error && error.message) {
                    console.error(error.message);
                }
                var modal = document.getElementById('myModal');
                modal.classList.add('in');
                modal.style = 'display: block;';
            }

            const connection = new signalR.HubConnectionBuilder()
                .withUrl('/chat')
                .build();
            bindConnectionMessage(connection);
            connection.start()
                .then(() => onConnected(connection))
                .catch(error => console.error(error.message));
        });
    </script>
</body>
</html>
```

Kód v *index.html* volá, `HubConnectionBuilder.build()` aby se provedlo připojení HTTP k prostředku nástroje Azure Signal.

Pokud je připojení úspěšné, předá se do metody `bindConnectionMessage`, která přidá obslužné rutiny událostí pro příchozí nabídky obsahu do klienta. 

`HubConnection.start()` naváže komunikaci s centrem. Pak `onConnected()` přidá obslužné rutiny událostí tlačítka. Tyto obslužné rutiny prostřednictvím připojení umožní tomuto klientovi nabízet aktualizace obsahu do všech připojených klientů.

## <a name="add-a-development-runtime-profile"></a>Přidání profilu vývojového modulu runtime

V této části přidáte prostředí pro vývoj pro ASP.NET Core. Další informace najdete v tématu [práce s více prostředími v ASP.NET Core](/aspnet/core/fundamentals/environments).

1. Vytvořte ve svém projektu složku s názvem *Properties* .

2. Do složky přidejte nový soubor s názvem *launchSettings.js* s následujícím obsahem a uložte soubor.

    ```json
    {
        "profiles" : {
            "ChatRoom": {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsnetcore)prosím o tom.

## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Pokud chcete aplikaci vytvořit pomocí .NET Core CLI, spusťte v příkazovém prostředí následující příkaz:

    ```dotnetcli
    dotnet build
    ```

1. Po úspěšném dokončení sestavení spusťte následující příkaz pro místní spuštění webové aplikace:

    ```dotnetcli
    dotnet run
    ```

    Aplikace bude hostována místně na portu 5000, jak je nakonfigurováno v našem profilu vývojového modulu runtime:

    ```output
    info: Microsoft.Hosting.Lifetime[0]
          Now listening on: https://localhost:5001
    info: Microsoft.Hosting.Lifetime[0]
          Now listening on: http://localhost:5000
    info: Microsoft.Hosting.Lifetime[0]
          Application started. Press Ctrl+C to shut down.
    info: Microsoft.Hosting.Lifetime[0]
          Hosting environment: Development
    info: Microsoft.Hosting.Lifetime[0]
          Content root path: E:\Testing\chattest
    ```

1. Otevřete dvě okna prohlížeče. V každém prohlížeči přejít na `http://localhost:5000` . Budete vyzváni k zadání jména. Zadejte název klienta pro oba klienty a otestujte obsah zprávy mezi klienty pomocí tlačítka **Odeslat** .

    ![Příklad chatu skupiny signalizace v Azure](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsnetcore)prosím o tom.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat k dalšímu kurzu, můžete zachovat prostředky vytvořené v rámci tohoto rychlého startu a znovu je použít.

Pokud jste hotovi s ukázkovou aplikací pro rychlé zprovoznění, můžete odstranit prostředky Azure vytvořené v rámci tohoto rychlého startu, abyste se vyhnuli poplatkům. 

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a zahrnuje všechny prostředky v této skupině. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste vytvořili prostředky pro hostování této ukázky v existující skupině prostředků, která obsahuje prostředky, které chcete zachovat, můžete každý prostředek z jeho okna odstranit jednotlivě, místo aby se odstranila skupina prostředků.

Přihlaste se k webu [Azure Portal](https://portal.azure.com) a potom vyberte **Skupiny prostředků**.

Do textového pole **filtrovat podle názvu** zadejte název vaší skupiny prostředků. V pokynech v tomto rychlém startu se používala skupina prostředků *SignalRTestResources*. Ve vaší skupině prostředků v seznamu výsledků vyberte tři tečky (**...**) > **Odstranit skupinu prostředků**.

![Výběry pro odstranění skupiny prostředků](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Zadejte název vaší skupiny prostředků, který chcete potvrdit, a vyberte **Odstranit**.

Po chvíli se skupina prostředků včetně všech prostředků, které obsahuje, odstraní.

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsnetcore)prosím o tom.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nový prostředek služby Azure Signal Service. Pak jste ho použili s ASP.NET Core webovou aplikací k odesílání aktualizací obsahu v reálném čase do více připojených klientů. Další informace o používání služby signalizace Azure najdete v kurzu, který ukazuje ověřování.

> [!div class="nextstepaction"]
> [Ověřování pomocí služby Azure SignalR](./signalr-concept-authenticate-oauth.md)
