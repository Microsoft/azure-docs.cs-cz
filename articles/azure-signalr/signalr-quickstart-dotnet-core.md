---
title: Rychlý Start, kde se dozvíte, jak používat službu Azure Signal
description: Rychlý start popisující použití služby Azure SignalR k vytvoření chatovací místnosti s využitím aplikací ASP.NET Core MVC.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: zhshang
ms.openlocfilehash: f87625fe4f56b369f2bf4aade3ef5424084b6fe8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81254882"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Rychlý Start: vytvoření chatovací místnosti pomocí služby Signal


Azure SignalR je služba Azure, která vývojářům pomáhá snadno vytvářet webové aplikace s funkcemi v reálném čase. Tato služba je založená na nástroji [Signal pro ASP.NET Core 2,1](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-2.1), ale podporuje také [signalizaci pro ASP.NET Core 3,0](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.0).

V tomto článku se dozvíte, jak začít se službou Azure SignalR. V tomto rychlém startu vytvoříte aplikaci Chat pomocí webové aplikace ASP.NET Core MVC. Tato aplikace naváže připojení k vašemu prostředku služby Azure SignalR a umožní tak aktualizace obsahu v reálném čase. Webovou aplikaci budete hostovat místně a připojíte se s více klienty prohlížeče. Každý klient bude moct nabízet aktualizace obsahu do všech ostatních klientů. 

K dokončení kroků v tomto rychlém startu můžete použít jakýkoli editor kódu. Jedna z možností je [Visual Studio Code](https://code.visualstudio.com/), která je k dispozici na platformách Windows, MacOS a Linux.

Kód pro tento kurz je k dispozici ke stažení v [úložišti GitHub AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Prostředky Azure používané v tomto rychlém startu můžete také vytvořit pomocí [skriptu vytvoření skriptu služby signalizace](scripts/signalr-cli-create-service.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Požadavky

* Nainstalujte [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* Stáhněte nebo naklonujte úložiště GitHub [AzureSignalR-Sample](https://github.com/aspnet/AzureSignalR-samples) . 

## <a name="create-an-azure-signalr-resource"></a>Vytvořené prostředku služby Azure SignalR

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

V této části použijete [rozhraní příkazového řádku .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) pro vytvoření projektu webové aplikace ASP.NET Core MVC. Výhodou použití .NET Core CLI přes Visual Studio je to, že je k dispozici na platformách Windows, macOS a Linux. 

1. Vytvořte složku pro váš projekt. V tomto rychlém startu se používá složka *E:\Testing\chattest* .

2. V nové složce spusťte následující příkaz, který vytvoří projekt:

        dotnet new mvc


## <a name="add-secret-manager-to-the-project"></a>Přidání nástroje Secret Manager do projektu

V této části přidáte do svého projektu [Nástroj Správce tajných klíčů](https://docs.microsoft.com/aspnet/core/security/app-secrets) . Nástroj Správce tajných klíčů ukládá citlivá data pro vývojovou práci mimo strom projektu. Tento přístup pomáhá zabránit nechtěnému sdílení tajných kódů aplikací ve zdrojovém kódu.

1. Otevřete soubor *.csproj*. Přidejte element `DotNetCliToolReference` zahrnující *Microsoft.Extensions.SecretManager.Tools*. Přidejte také `UserSecretsId` element, jak je znázorněno v následujícím kódu pro *chatovat. csproj*a uložte soubor.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
    </ItemGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
    </Project>    
    ```

## <a name="add-azure-signalr-to-the-web-app"></a>Přidání služby Azure SignalR do webové aplikace

1. Přidejte odkaz na balíček `Microsoft.Azure.SignalR` NuGet spuštěním následujícího příkazu:

        dotnet add package Microsoft.Azure.SignalR

2. Spusťte následující příkaz pro obnovení balíčků pro váš projekt:

        dotnet restore

3. Do nástroje Secret Manager přidejte tajný kód *Azure:SignalR:ConnectionString*. 

    Tento tajný kód bude obsahovat připojovací řetězec pro přístup k vašemu prostředku služby SignalR. *Azure: signaler: připojovací řetězec* je výchozí konfigurační klíč, který signalizuje, aby se navázalo připojení. Hodnotu v následujícím příkazu nahraďte připojovacím řetězcem pro prostředek služby Signal.

    Tento příkaz musíte spustit ve stejném adresáři jako soubor *. csproj* .

    ```
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"    
    ```

    Správce tajného klíče se použije jenom pro testování webové aplikace, když je hostovaný místně. V pozdějším kurzu nasadíte webovou aplikaci Chat do Azure. Po nasazení webové aplikace do Azure použijete nastavení aplikace místo uložení připojovacího řetězce pomocí Správce tajných klíčů.

    K tomuto tajnému kódu se dostanete pomocí konfiguračního rozhraní API. Dvojtečka (:) funguje v názvu konfigurace s rozhraním API konfigurace na všech podporovaných platformách. Viz [Konfigurace podle prostředí](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0). 


4. Otevřete soubor *Startup.cs* a aktualizujte metodu `ConfigureServices` tak, aby používala službu Azure SignalR, a to zavoláním metody `services.AddSignalR().AddAzureSignalR()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```

    Když nepředá parametr do `AddAzureSignalR()`, tento kód použije výchozí konfigurační klíč pro připojovací řetězec prostředků služby Signal. Výchozí konfigurační klíč je *Azure: signaler: ConnectionString*.

5. Také v *Startup.cs*aktualizujte `Configure` metodu tak, že nahradíte volání `app.UseStaticFiles()` s následujícím kódem a uložíte soubor, pouze pro ASP.NET Core 2.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            
    Pro ASP.NET Core 3 + nahraďte výše uvedený kód:

    ```csharp
    app.UseFileServer();
    app.UseRouting();
    app.UseAuthorization();

    app.UseEndpoints(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```

### <a name="add-a-hub-class"></a>Přidání třídy centra

V nástroji Signal je rozbočovač základní komponentou, která zveřejňuje sadu metod, které mohou být volány z klienta. V této části nadefinujete třídu centra se dvěma metodami: 

* `Broadcast`: Tato metoda rozešle zprávu do všech klientů.
* `Echo`: Tato metoda odešle zprávu zpět volajícímu.

Obě metody používají `Clients` rozhraní, které poskytuje sada SDK signalizace ASP.NET Core. Toto rozhraní vám umožní přístup ke všem připojeným klientům, takže můžete nabízet obsah vašim klientům.

1. Do adresáře vašeho projektu přidejte novou složku *Hub*. Do nové složky přidejte nový soubor s kódem centra *Chat.cs*.

2. Přidáním následujícího kódu do *chat.cs* Definujte třídu centra a uložte soubor. 

    Pokud jste použili jiný název projektu než *chattest*, aktualizujte obor názvů pro tuto třídu.

    ```csharp
    using Microsoft.AspNetCore.SignalR;

    namespace chattest
    {

        public class Chat : Hub
        {
            public void BroadcastMessage(string name, string message)
            {
                Clients.All.SendAsync("broadcastMessage", name, message);
            }

            public void Echo(string name, string message)
            {
                Clients.Client(Context.ConnectionId).SendAsync("echo", name, message + " (echo from server)");
            }
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>Přidání rozhraní klienta pro webovou aplikaci

Uživatelské rozhraní klienta pro tuto aplikaci chatovací místnosti se bude skládat z HTML a JavaScriptu v souboru s názvem *index. html* v adresáři *wwwroot* .

Zkopírujte soubor *index. html* , složku *CSS* a složku *skripty* ze složky *wwwroot* v [úložišti ukázek](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot). Vložte je do složky *wwwroot* vašeho projektu.

Tady je hlavní kód souboru *index. html*: 

```javascript
var connection = new signalR.HubConnectionBuilder()
                            .withUrl('/chat')
                            .build();
bindConnectionMessage(connection);
connection.start()
    .then(function () {
        onConnected(connection);
    })
    .catch(function (error) {
        console.error(error.message);
    });
```    

Kód v souboru *index. html* volá `HubConnectionBuilder.build()` připojení HTTP k prostředku nástroje Azure Signal.

Pokud je připojení úspěšné, předá se do metody `bindConnectionMessage`, která přidá obslužné rutiny událostí pro příchozí nabídky obsahu do klienta. 

`HubConnection.start()` naváže komunikaci s centrem. Pak `onConnected()` přidá obslužné rutiny událostí tlačítka. Tyto obslužné rutiny prostřednictvím připojení umožní tomuto klientovi nabízet aktualizace obsahu do všech připojených klientů.

## <a name="add-a-development-runtime-profile"></a>Přidání profilu vývojového modulu runtime

V této části přidáte prostředí pro vývoj pro ASP.NET Core. Další informace najdete v tématu [práce s více prostředími v ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments).

1. Vytvořte ve svém projektu složku s názvem *Properties* .

2. Přidejte do složky nový soubor s názvem *launchSettings. JSON* s následujícím obsahem a uložte soubor.

    ```json
    {
        "profiles" : 
        {
            "ChatRoom": 
            {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": 
                {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```


## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Pokud chcete aplikaci vytvořit pomocí .NET Core CLI, spusťte v příkazovém prostředí následující příkaz:

        dotnet build

2. Po úspěšném dokončení sestavení spusťte následující příkaz pro místní spuštění webové aplikace:

        dotnet run

    Aplikace bude hostována místně na portu 5000, jak je nakonfigurováno v našem profilu vývojového modulu runtime:

        E:\Testing\chattest>dotnet run
        Hosting environment: Development
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

3. Otevřete dvě okna prohlížeče. V každém prohlížeči přejít na `http://localhost:5000`. Budete vyzváni k zadání jména. Zadejte název klienta pro oba klienty a otestujte obsah zprávy mezi klienty pomocí tlačítka **Odeslat** .

    ![Příklad chatu skupiny signalizace v Azure](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat k dalšímu kurzu, můžete zachovat prostředky vytvořené v rámci tohoto rychlého startu a znovu je použít.

Pokud jste hotovi s ukázkovou aplikací pro rychlé zprovoznění, můžete odstranit prostředky Azure vytvořené v rámci tohoto rychlého startu, abyste se vyhnuli poplatkům. 

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a zahrnuje všechny prostředky v této skupině. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste vytvořili prostředky pro hostování této ukázky v existující skupině prostředků, která obsahuje prostředky, které chcete zachovat, můžete každý prostředek z jeho okna odstranit jednotlivě, místo aby se odstranila skupina prostředků.
> 
> 

Přihlaste se k webu [Azure Portal](https://portal.azure.com) a potom vyberte **Skupiny prostředků**.

Do textového pole **filtrovat podle názvu** zadejte název vaší skupiny prostředků. V pokynech v tomto rychlém startu se používala skupina prostředků *SignalRTestResources*. Ve vaší skupině prostředků v seznamu výsledků vyberte tři tečky (**...**) > **Odstranit skupinu prostředků**.

   
![Výběry pro odstranění skupiny prostředků](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Zadejte název vaší skupiny prostředků, který chcete potvrdit, a vyberte **Odstranit**.
   
Po chvíli se skupina prostředků včetně všech prostředků, které obsahuje, odstraní.



## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nový prostředek služby Azure Signal Service. Pak jste ho použili s ASP.NET Core webovou aplikací k odesílání aktualizací obsahu v reálném čase do více připojených klientů. Další informace o používání služby signalizace Azure najdete v kurzu, který ukazuje ověřování.

> [!div class="nextstepaction"]
> [Ověřování pomocí služby Azure SignalR](./signalr-concept-authenticate-oauth.md)


