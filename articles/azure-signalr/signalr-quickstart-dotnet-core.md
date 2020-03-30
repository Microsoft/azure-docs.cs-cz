---
title: Úvodní příručka se dozvíte, jak používat službu Azure SignalR
description: Rychlý start popisující použití služby Azure SignalR k vytvoření chatovací místnosti s využitím aplikací ASP.NET Core MVC.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: zhshang
ms.openlocfilehash: 022780f2b37c8bed49c81774d443b69bae41e5e7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73476747"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Úvodní příručka: Vytvoření chatovací místnosti pomocí služby SignalR


Azure SignalR je služba Azure, která vývojářům pomáhá snadno vytvářet webové aplikace s funkcemi v reálném čase. Tato služba je založena na [SignalR pro ASP.NET Core 2.1](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-2.1), ale také podporuje [SignalR pro ASP.NET Core 3.0](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.0).

V tomto článku se dozvíte, jak začít se službou Azure SignalR. V tomto rychlém startu vytvoříte chatovací aplikaci pomocí webové aplikace ASP.NET Core MVC. Tato aplikace naváže připojení k vašemu prostředku služby Azure SignalR a umožní tak aktualizace obsahu v reálném čase. Budete hostovat webovou aplikaci místně a spojit se s více klienty prohlížeče. Každý klient bude moct nabízet aktualizace obsahu do všech ostatních klientů. 

K dokončení kroků v tomto rychlém startu můžete použít jakýkoli editor kódu. Jednou z možností je [Visual Studio Code](https://code.visualstudio.com/), který je k dispozici na platformách Windows, macOS a Linux.

Kód pro tento kurz je k dispozici ke stažení v [úložišti GitHub AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Prostředky Azure použité v tomto rychlém startu můžete také vytvořit podle [příkazu Vytvořit skript služby SignalR](scripts/signalr-cli-create-service.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Požadavky

* Nainstalujte sadu [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* Stáhněte nebo naklonujte úložiště [GitHub uvzorku AzureSignalR.](https://github.com/aspnet/AzureSignalR-samples) 

## <a name="create-an-azure-signalr-resource"></a>Vytvořené prostředku služby Azure SignalR

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

V této části použijete [rozhraní příkazového řádku .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) k vytvoření ASP.NET projektu webové aplikace Core MVC. Výhodou použití rozhraní PŘÍKAZOVÉHO PŘÍKAZU .NET Core oproti Visual Studiu je, že je k dispozici na platformách Windows, macOS a Linux. 

1. Vytvořte složku pro projekt. Tento rychlý start používá složku *E:\Testing\chattest.*

2. V nové složce vytvořte projekt následujícím příkazem:

        dotnet new mvc


## <a name="add-secret-manager-to-the-project"></a>Přidání nástroje Secret Manager do projektu

V této části přidáte [nástroj Správce tajných barev](https://docs.microsoft.com/aspnet/core/security/app-secrets) do projektu. Nástroj Správce tajných barev ukládá citlivá data pro vývojovou práci mimo strom projektu. Tento přístup pomáhá zabránit náhodnému sdílení tajných kódů aplikací ve zdrojovém kódu.

1. Otevřete soubor *.csproj*. Přidejte element `DotNetCliToolReference` zahrnující *Microsoft.Extensions.SecretManager.Tools*. Také přidejte prvek, `UserSecretsId` jak je znázorněno v následujícím kódu pro *chattest.csproj*, a uložte soubor.

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

1. Přidejte odkaz `Microsoft.Azure.SignalR` na balíček NuGet spuštěním následujícího příkazu:

        dotnet add package Microsoft.Azure.SignalR

2. Chcete-li obnovit balíčky pro projekt, spusťte následující příkaz:

        dotnet restore

3. Do nástroje Secret Manager přidejte tajný kód *Azure:SignalR:ConnectionString*. 

    Tento tajný kód bude obsahovat připojovací řetězec pro přístup k vašemu prostředku služby SignalR. *Azure:SignalR:ConnectionString* je výchozí konfigurační klíč, který SignalR hledá pro navázání připojení. Nahraďte hodnotu v následujícím příkazu připojovacím řetězcem pro prostředek služby SignalR.

    Tento příkaz je nutné spustit ve stejném adresáři jako soubor *.csproj.*

    ```
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"    
    ```

    Správce tajných služeb se bude používat pouze k testování webové aplikace, když je hostovaná místně. V pozdějším kurzu nasadíte webovou aplikaci chatu do Azure. Po nasazení webové aplikace do Azure použijete místo ukládání připojovacího řetězce pomocí Správce tajných služeb nastavení aplikace.

    K tomuto tajnému klíči se přistupuje pomocí konfiguračního rozhraní API. Dvojtečka (:) pracuje v názvu konfigurace s konfiguračním rozhraním API na všech podporovaných platformách. Viz [Konfigurace podle prostředí](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0). 


4. Otevřete *Startup.cs* `ConfigureServices` a aktualizujte metodu pro `services.AddSignalR().AddAzureSignalR()` použití služby Azure SignalR voláním metody pouze pro ASP.NET Core 2:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```
    Pro ASP.NET Core 3+ není nutná `ConfigureServices` žádná změna pro metodu.

    Tím, že tento `AddAzureSignalR()`kód nepředá parametr , používá výchozí konfigurační klíč pro připojovací řetězec prostředků služby SignalR. Výchozí konfigurační klíč je *Azure:SignalR:ConnectionString*.

5. Také v *Startup.cs* `Configure` , aktualizujte metodu `app.UseStaticFiles()` nahrazením volání s následujícím kódem a uložte soubor, pouze pro ASP.NET Core 2.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            
    Pro ASP.NET Jádra 3+ nahraďte výše uvedený kód:

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

V SignalR rozbočovač je základní součást, která zveřejňuje sadu metod, které lze volat z klienta. V této části nadefinujete třídu centra se dvěma metodami: 

* `Broadcast`: Tato metoda rozešle zprávu do všech klientů.
* `Echo`: Tato metoda odešle zprávu zpět volajícímu.

Obě metody `Clients` používají rozhraní, které poskytuje ASP.NET Core SignalR SDK. Toto rozhraní umožňuje přístup ke všem připojeným klientům, takže můžete svým klientům dotlačit obsah.

1. Do adresáře vašeho projektu přidejte novou složku *Hub*. Do nové složky přidejte nový soubor s kódem centra *Chat.cs*.

2. Přidejte následující kód, který *Chat.cs* k definování třídy rozbočovače a uložení souboru. 

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

### <a name="add-the-client-interface-for-the-web-app"></a>Přidání klientského rozhraní pro webovou aplikaci

Uživatelské rozhraní klienta pro tuto aplikaci chatovací místnosti se bude skládat z HTML a JavaScriptu v souboru s názvem *index.html* v adresáři *wwwroot.*

Zkopírujte soubor *index.html,* složku *css* a složku *skriptů* ze složky *wwwroot* [v úložišti ukázek](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot). Vložte je do složky *wwwroot* projektu.

Zde je hlavní kód *index.html*: 

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

Kód v *index.html* volání `HubConnectionBuilder.build()` k vytvoření připojení HTTP k prostředku Azure SignalR.

Pokud je připojení úspěšné, předá se do metody `bindConnectionMessage`, která přidá obslužné rutiny událostí pro příchozí nabídky obsahu do klienta. 

`HubConnection.start()` naváže komunikaci s centrem. Potom `onConnected()` přidá obslužné rutiny události tlačítka. Tyto obslužné rutiny prostřednictvím připojení umožní tomuto klientovi nabízet aktualizace obsahu do všech připojených klientů.

## <a name="add-a-development-runtime-profile"></a>Přidání profilu vývojového modulu runtime

V této části přidáte vývojové prostředí runtime pro ASP.NET Core. Další informace naleznete [v tématu Práce s více prostředími v ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments).

1. Vytvořte složku s názvem *Vlastnosti* v projektu.

2. Přidejte do složky nový soubor s názvem *launchSettings.json* s následujícím obsahem a soubor uložte.

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


## <a name="build-and-run-the-app-locally"></a>Sestavení a spuštění aplikace místně

1. Chcete-li vytvořit aplikaci pomocí rozhraní PŘÍKAZU .NET Core CLI, spusťte v příkazovém prostředí následující příkaz:

        dotnet build

2. Po úspěšném dokončení sestavení spusťte následující příkaz a spusťte webovou aplikaci místně:

        dotnet run

    Aplikace bude hostována místně na portu 5000, jak je nakonfigurováno v našem profilu vývojového běhu:

        E:\Testing\chattest>dotnet run
        Hosting environment: Development
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

3. Otevřete dvě okna prohlížeče. V každém prohlížeči `http://localhost:5000`přejděte na . Budete vyzváni k zadání svého jména. Zadejte název klienta pro klienty a otestujte obsah zprávy mezi oběma klienty pomocí tlačítka **Odeslat.**

    ![Příklad skupinového chatu Azure SignalR](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat k dalšímu kurzu, můžete zachovat prostředky vytvořené v tomto rychlém startu a znovu je použít.

Pokud jste hotovi s ukázkovou aplikací rychlého startu, můžete odstranit prostředky Azure vytvořené v tomto rychlém startu, abyste se vyhnuli poplatkům. 

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a zahrnuje všechny prostředky v této skupině. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste vytvořili prostředky pro hostování této ukázky v existující skupině prostředků, která obsahuje prostředky, které chcete zachovat, můžete odstranit každý prostředek jednotlivě z jeho okna namísto odstranění skupiny prostředků.
> 
> 

Přihlaste se k webu [Azure Portal](https://portal.azure.com) a potom vyberte **Skupiny prostředků**.

Do textového pole **Filtr podle názvu** zadejte název skupiny prostředků. V pokynech v tomto rychlém startu se používala skupina prostředků *SignalRTestResources*. Ve skupině prostředků v seznamu výsledků vyberte tři tečky (**...**) > **Odstranit skupinu prostředků**.

   
![Výběry pro odstranění skupiny prostředků](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Zadejte název skupiny prostředků, kterou chcete potvrdit, a vyberte **Odstranit**.
   
Po chvíli se skupina prostředků včetně všech prostředků, které obsahuje, odstraní.



## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nový prostředek služby Azure SignalR. Poté jste ji použili s ASP.NET webovou aplikací Core k nabízení aktualizací obsahu v reálném čase více připojeným klientům. Další informace o používání služby Azure SignalR, pokračujte v kurzu, který ukazuje ověřování.

> [!div class="nextstepaction"]
> [Ověřování pomocí služby Azure SignalR](./signalr-concept-authenticate-oauth.md)


