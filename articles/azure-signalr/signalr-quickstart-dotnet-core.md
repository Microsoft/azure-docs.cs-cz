---
title: Rychlý Start se dozvíte, jak pomocí služby Azure SignalR
description: Rychlý start popisující použití služby Azure SignalR k vytvoření chatovací místnosti s využitím aplikací ASP.NET Core MVC.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 3dc893ea10e47e867110f674a458498a6bd24a4f
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560690"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Rychlý start: Vytvoření chatovací místnosti s použitím služby SignalR


Azure SignalR je služba Azure, která vývojářům pomáhá snadno vytvářet webové aplikace s funkcemi v reálném čase. Tato služba je založená na knihovně [SignalR pro ASP.NET Core 2.0](https://docs.microsoft.com/aspnet/core/signalr/introduction).

V tomto článku se dozvíte, jak začít se službou Azure SignalR. V tomto rychlém startu vytvoříte chatovací aplikaci s použitím webovou aplikaci ASP.NET Core MVC. Tato aplikace naváže připojení k vašemu prostředku služby Azure SignalR a umožní tak aktualizace obsahu v reálném čase. Budete hostovat webové aplikace v místním prostředí a spojte se s více klienty prohlížeče. Každý klient bude moct nabízet aktualizace obsahu do všech ostatních klientů. 

K dokončení kroků v tomto rychlém startu můžete použít jakýkoli editor kódu. Jednou z možností je [Visual Studio Code](https://code.visualstudio.com/), která je k dispozici ve Windows, macOS a Linux platformy.

Kód pro tento kurz je k dispozici ke stažení v [úložišti GitHub AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Můžete také vytvořit prostředky Azure používané v tomto rychlém startu pomocí následujícího [vytvořit skript služby SignalR](scripts/signalr-cli-create-service.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Požadavky

* Nainstalujte [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* Stáhněte nebo naklonujte [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) úložiště GitHub. 

## <a name="create-an-azure-signalr-resource"></a>Vytvořené prostředku služby Azure SignalR

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

V této části použijete [rozhraní příkazového řádku .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) k vytvoření projektu webové aplikace ASP.NET Core MVC. Výhodou použití rozhraní příkazového řádku .NET Core v sadě Visual Studio je, že je k dispozici ve Windows, macOS a Linux platformy. 

1. Vytvořte složku pro váš projekt. Tento rychlý start využívá *E:\Testing\chattest* složky.

2. V nové složce spusťte následující příkaz pro vytvoření projektu:

        dotnet new mvc


## <a name="add-secret-manager-to-the-project"></a>Přidání nástroje Secret Manager do projektu

V této části přidáte [nástroj tajný klíč správce](https://docs.microsoft.com/aspnet/core/security/app-secrets) do projektu. Tajný klíč správce nástroj ukládá citlivá data při vývojových pracích vně vašeho projektu stromu. Tento přístup pomáhá zabránit náhodnému sdílení tajných kódů aplikace ve zdrojovém kódu.

1. Otevřete soubor *.csproj*. Přidejte element `DotNetCliToolReference` zahrnující *Microsoft.Extensions.SecretManager.Tools*. Také přidat `UserSecretsId` jak je znázorněno v následujícím kódu pro element *chattest.csproj*a soubor uložte.

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

1. Přidejte odkaz na `Microsoft.Azure.SignalR` balíček NuGet spuštěním následujícího příkazu:

        dotnet add package Microsoft.Azure.SignalR

2. Spusťte následující příkaz k obnovení balíčků pro váš projekt:

        dotnet restore

3. Do nástroje Secret Manager přidejte tajný kód *Azure:SignalR:ConnectionString*. 

    Tento tajný kód bude obsahovat připojovací řetězec pro přístup k vašemu prostředku služby SignalR. *Azure: SignalR:ConnectionString* je výchozí konfigurační klíč, který hledá SignalR k navázání připojení. Hodnota v následujícím příkazu nahraďte připojovací řetězec pro váš prostředek služby SignalR.

    Tento příkaz musíte spustit ve stejném adresáři jako *.csproj* souboru.

    ```
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"    
    ```

    Tajný klíč správce se použije pouze pro testování webové aplikace, zatímco je hostovaný místně. V pozdějších kurzech nasadíte chat webové aplikace do Azure. Po nasazení webové aplikace do Azure pomocí nastavení aplikace místo uložení připojovacího řetězce, pomocí tajného klíče správce.

    Tento tajný kód je přistupováno pomocí rozhraní API pro konfiguraci. Dvojtečka (:) lze použít v názvu konfigurace pomocí rozhraní API pro konfiguraci na všech podporovaných platformách. Zobrazit [konfigurace podle prostředí](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0). 


4. Otevřete soubor *Startup.cs* a aktualizujte metodu `ConfigureServices` tak, aby používala službu Azure SignalR, a to zavoláním metody `services.AddSignalR().AddAzureSignalR()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```

    Tím, že není předáte parametr `AddAzureSignalR()`, tento kód používá výchozí konfigurace klíče pro připojovací řetězec prostředku služby SignalR. Je výchozí konfigurace klíče *Azure: SignalR:ConnectionString*.

5. V souboru *Startup.cs* aktualizujte také metodu `Configure` nahrazením volání metody `app.UseStaticFiles()` následujícím kódem a uložte soubor.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            

### <a name="add-a-hub-class"></a>Přidání třídy centra

Rozbočovač signalr, je základní součástí, která zveřejňuje sadu metod, které lze volat z klienta. V této části nadefinujete třídu centra se dvěma metodami: 

* `Broadcast`: Tato metoda vysílá zprávy na všechny klienty.
* `Echo`: Tato metoda odešle zprávu zpět volajícímu.

Obě metody používají `Clients` rozhraní, které poskytuje sady SDK technologie ASP.NET Core SignalR. Toto rozhraní poskytuje přístup k všichni připojení klienti, takže obsah můžete nabízet svým klientům.

1. Do adresáře vašeho projektu přidejte novou složku *Hub*. Do nové složky přidejte nový soubor s kódem centra *Chat.cs*.

2. Přidejte následující kód, který *Chat.cs* definovat třídu centra a soubor uložte. 

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

### <a name="add-the-client-interface-for-the-web-app"></a>Přidat rozhraní klienta pro webovou aplikaci

Uživatelské rozhraní klienta pro tuto aplikaci chatovací místnosti se skládají z kódu HTML a JavaScriptu do souboru s názvem *index.html* v *wwwroot* adresáře.

Kopírování *index.html* souboru *šablon stylů css* složky a *skripty* složku z *wwwroot* složky [ukázky úložiště](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot). Vložte je do svého projektu *wwwroot* složky.

Tady je hlavní kód *index.html*: 

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

Kód v *index.html* volání `HubConnectionBuilder.build()` aby připojení HTTP k prostředku Azure SignalR.

Pokud je připojení úspěšné, předá se do metody `bindConnectionMessage`, která přidá obslužné rutiny událostí pro příchozí nabídky obsahu do klienta. 

`HubConnection.start()` naváže komunikaci s centrem. Potom `onConnected()` přidá tlačítko obslužné rutiny událostí. Tyto obslužné rutiny prostřednictvím připojení umožní tomuto klientovi nabízet aktualizace obsahu do všech připojených klientů.

## <a name="add-a-development-runtime-profile"></a>Přidání profilu vývojového modulu runtime

V této části přidáte prostředí modulu runtime pro vývoj pro ASP.NET Core. Další informace najdete v tématu [práce v různých prostředích v ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments).

1. Vytvořte složku s názvem *vlastnosti* ve vašem projektu.

2. Přidejte nový soubor s názvem *launchSettings.json* ke složce s následujícím obsahem a uložte soubor.

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


## <a name="build-and-run-the-app-locally"></a>Sestavte a spusťte aplikaci místně

1. Pokud chcete vytvořit aplikaci pomocí rozhraní příkazového řádku .NET Core, spusťte následující příkaz v příkazovém prostředí:

        dotnet build

2. Po sestavení úspěšně dokončí, spusťte následující příkaz pro místní spuštění webové aplikace:

        dotnet run

    Aplikace se dají hostovat místně na portu 5000 gurovaný náš vývojový profil modulu runtime:

        E:\Testing\chattest>dotnet run
        Hosting environment: Development
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

3. Otevře dvě okna prohlížeče. V každé prohlížeč, přejděte na `http://localhost:5000`. Budete vyzváni k zadání vašeho názvu. Zadejte název klienta pro klienty a doručením (push) obsah zprávy mezi oběma klienty pomocí testu **odeslat** tlačítko.

    ![Příklad konverzace skupiny Azure SignalR](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat k dalšímu kurzu, můžete zachovat prostředky vytvořené v rámci tohoto rychlého startu a je znovu použít.

Pokud budete hotovi s ukázkovou aplikaci rychlý start, můžete odstranit prostředky Azure vytvořené v tomto rychlém startu se vyhnout poplatkům za. 

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a zahrnuje odstranění všech prostředků v této skupině. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste vytvořili prostředky pro hostování této ukázky ve stávající skupině prostředků obsahující prostředky, které chcete zachovat, můžete odstranit jednotlivé prostředky jednotlivě z příslušné okno místo odstraněním skupiny prostředků.
> 
> 

Přihlaste se k webu [Azure Portal](https://portal.azure.com) a potom vyberte **Skupiny prostředků**.

V **filtrovat podle názvu** textové pole, zadejte název vaší skupiny prostředků. V pokynech v tomto rychlém startu se používala skupina prostředků *SignalRTestResources*. Ve vaší skupině prostředků. v seznamu výsledků vyberte tři tečky (**...** ) > **Odstranit skupinu prostředků**.

   
![Výběry pro odstranění skupiny prostředků](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Zadejte název vaší skupiny prostředků a potvrdit, vyberte **odstranit**.
   
Po chvíli se skupina prostředků včetně všech prostředků, které obsahuje, odstraní.



## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili nový prostředek služby Azure SignalR. Můžete pak použít ho s webovou aplikaci ASP.NET Core aktualizace obsahu v reálném čase více připojeným klientům. Další informace o používání služby Azure SignalR, pokračujte kurzem, který předvádí ověření.

> [!div class="nextstepaction"]
> [Ověřování pomocí služby Azure SignalR](./signalr-concept-authenticate-oauth.md)


