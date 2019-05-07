---
title: Rychlý Start se dozvíte, jak pomocí služby Azure SignalR technologie ASP.NET
description: Rychlý start k vytvoření chatovací místnosti s ASP.NET framework pomocí služby Azure SignalR.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/20/2019
ms.author: zhshang
ms.openlocfilehash: cda1eddc5fa40e97038274cf9b4c3c2cb9552871
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154499"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Rychlý start: Vytvoření chatovací místnosti s technologií ASP.NET a služby SignalR

Službě Azure SignalR je na základě [SignalR pro ASP.NET Core 2.0](https://docs.microsoft.com/aspnet/core/signalr/introduction), což je **není** 100 % kompatibilní s knihovnou SignalR technologie ASP.NET. Službě Azure SignalR opětovně implementována funkce SignalR technologie ASP.NET data protokol založený na nejnovější technoledges ASP.NET Core. Při použití služby Azure SignalR pro funkci SignalR technologie ASP.NET, již nejsou podporovány některé funkce SignalR technologie ASP.NET, například Azure SignalR není přehrát zprávy při opětovném připojení klienta. Přenos navždy rámce a JSONP nejsou také podporu. Některé změny kódu a správná verze závislé knihovny jsou potřebné k tomu funkce SignalR technologie ASP.NET aplikace fungovaly se službou SignalR. 

Odkazovat [verze rozdíly doc](https://docs.microsoft.com/aspnet/core/signalr/version-differences?view=aspnetcore-2.2) pro úplný seznam porovnání funkcí mezi funkce SignalR technologie ASP.NET a ASP.NET Core SignalR.

V tomto rychlém startu se dozvíte, jak začít pracovat s ASP.NET a služby Azure SignalR pro podobná [chatovací místnosti aplikace](./signalr-quickstart-dotnet-core.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
## <a name="prerequisites"></a>Požadavky

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET 4.6.1](https://www.microsoft.com/net/download/windows)
* [Funkce SignalR technologie ASP.NET 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí svého účtu Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

*Bez serveru* režimu není podporováno pro aplikace ASP.NET SignalR. Vždy používejte *výchozí* nebo *Classic* pro instanci služby Azure SignalR.

Můžete také vytvářet prostředky Azure používané v tomto rychlém startu se [vytvořit skript služby SignalR](scripts/signalr-cli-create-service.md).

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Zatímco probíhá nasazování služby, pojďme se podívat na práci s kódem. Naklonujte [ukázkovou aplikaci z GitHubu](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), nastavte připojovací řetězec služby SignalR Service a spusťte aplikaci místně.

1. Otevřete okno terminálu Git. Přejděte do složky, kam chcete klonovat ukázkový projekt.

1. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="configure-and-run-chat-room-web-app"></a>Konfigurace a spuštění webové aplikace chatovací místnosti

1. Spusťte sadu Visual Studio a otevřete řešení v *aspnet-samples/ChatRoom/* složky naklonovaného úložiště.

1. V prohlížeči, kde je otevřít na webu Azure portal vyhledejte a vyberte instanci, kterou jste vytvořili.

1. Výběrem možnosti **Klíče** zobrazte připojovací řetězce instance služby SignalR.

1. Vyberte a zkopírujte primární připojovací řetězec.

1. Nyní nastavte připojovací řetězec v souboru web.config.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. V *Startup.cs*, namísto volání metody `MapSignalR()`, je třeba volat `MapAzureSignalR({your_applicationName})` a předejte mu připojovací řetězec k aplikaci připojit ke službě místo hostování SignalR samostatně. Nahraďte `{YourApplicationName}` na název vaší aplikace. Tento název je jedinečný název, který odlišit této aplikaci z jiné aplikace. Můžete použít `this.GetType().FullName` jako hodnotu.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Budete také muset odkaz sady SDK služby před použitím těchto rozhraní API. Otevřít **nástroje | Správce balíčků NuGet | Konzola správce balíčků** a spusťte příkaz:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Než tyto změny všechno ostatní zůstává stejná, můžete stále použít rozbočovač rozhraní, které jste již obeznámeni s psát obchodní logiku.

    > [!NOTE]
    > V implementaci koncový bod `/signalr/negotiate` je vystaven pro vyjednávání pomocí sady SDK služby Azure SignalR. Vrátí odpověď speciální vyjednávání když klienti pokusí připojit a klienti přesměrovat na koncový bod služby, které jsou definovány v připojovacím řetězci.

1. Stisknutím klávesy **F5** spusťte projekt v režimu ladění. Zobrazí se aplikace spouští místně. Místo hostování modulu runtime SignalR ve vlastní aplikace, nyní připojí ke službě Azure SignalR.

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]



> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a skupina prostředků včetně všech v ní obsažených prostředků bude trvale odstraněna. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste vytvořili prostředky pro hostování této ukázky ve stávající skupině prostředků obsahující prostředky, které chcete zachovat, můžete místo odstranění skupiny prostředků odstranit jednotlivé prostředky z jejich odpovídajících oken.
> 
> 

Přihlaste se na web [Azure Portal ](https://portal.azure.com) a klikněte na **Skupiny prostředků**.

Do textového pole **Filtrovat podle názvu** zadejte název vaší skupiny prostředků. V pokynech v tomto rychlém startu se používala skupina prostředků *SignalRTestResources*. Ve výsledcích hledání klikněte na **...** u vaší skupiny prostředků a pak na **Odstranit skupinu prostředků**.

   
![Odstranění](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili nový prostředek služby Azure SignalR a používat s webovou aplikaci ASP.NET. Dále se naučíte vyvíjet aplikace v reálném čase pomocí služby Azure SignalR technologie ASP.NET Core.

> [!div class="nextstepaction"]
> [Službě Azure SignalR s ASP.NET Core](./signalr-quickstart-dotnet-core.md)
