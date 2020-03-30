---
title: Vývoj s ASP.NET – služba Azure SignalR
description: Rychlý start pro použití služby Azure SignalR k vytvoření chatovací místnosti s ASP.NET frameworkem.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/20/2019
ms.author: zhshang
ms.openlocfilehash: ec5b7a75bced4b7cd81a120925558b8c1be57818
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74158172"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Úvodní příručka: Vytvoření chatovací místnosti se službou ASP.NET a SignalR

Služba Azure SignalR je založena na [SignalR pro ASP.NET Core 2.0](https://docs.microsoft.com/aspnet/core/signalr/introduction), který **není** 100% kompatibilní s ASP.NET SignalR. Služba Azure SignalR znovu implementovaná ASP.NET datový protokol SignalR založená na nejnovějších technologiích ASP.NET Core. Při použití služby Azure SignalR pro ASP.NET SignalR, některé funkce ASP.NET SignalR již nejsou podporovány, například Azure SignalR nepřehraje zprávy při opětovném připojení klienta. Také forever frame přenosu a JSONP nejsou podporovány. Některé změny kódu a správná verze závislých knihoven jsou potřebné k tomu, aby aplikace signalr ASP.NET fungovala se službou SignalR. 

Úplný seznam porovnání funkcí mezi ASP.NET SignalR a ASP.NET Core SignalR naleznete v [dokumentu rozdílů verzí.](https://docs.microsoft.com/aspnet/core/signalr/version-differences?view=aspnetcore-2.2)

V tomto rychlém startu se dozvíte, jak začít s ASP.NET a službou Azure SignalR pro podobnou [aplikaci chatovací místnosti](./signalr-quickstart-dotnet-core.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
## <a name="prerequisites"></a>Požadavky

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET 4.6.1](https://www.microsoft.com/net/download/windows)
* [ASP.NET signalizátor 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí svého účtu Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

*Režim bez serveru* není podporován pro aplikace ASP.NET SignalR. Pro instanci služby Azure SignalR vždy používejte *výchozí* nebo *klasické.*

Můžete také vytvořit prostředky Azure používané v tomto rychlém startu pomocí [skriptu Vytvořit službu SignalR](scripts/signalr-cli-create-service.md).

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Zatímco probíhá nasazování služby, pojďme se podívat na práci s kódem. Naklonujte [ukázkovou aplikaci z GitHubu](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), nastavte připojovací řetězec služby SignalR Service a spusťte aplikaci místně.

1. Otevřete okno terminálu Git. Přejděte do složky, kam chcete klonovat ukázkový projekt.

1. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="configure-and-run-chat-room-web-app"></a>Konfigurace a spuštění webové aplikace Chat Room

1. Spusťte Visual Studio a otevřete řešení ve složce *aspnet-samples/ChatRoom/* klonovaného úložiště.

1. V prohlížeči, kde se otevře portál Azure, najděte a vyberte instanci, kterou jste vytvořili.

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

1. V *Startup.cs*, `MapSignalR()`místo volání , `MapAzureSignalR({your_applicationName})` je třeba volat a předávat v připojovacířetězec, aby se aplikace připojit ke službě namísto hostování SignalR sám. Nahraďte `{YourApplicationName}` název aplikace. Tento název je jedinečný název odlišit tuto aplikaci od ostatních aplikací. Můžete použít `this.GetType().FullName` jako hodnotu.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Před použitím těchto api je také třeba odkazovat na službu SDK. Otevřít **nástroje | Správce balíčků NuGet | Konzola správce balíčků** a příkaz spustit:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Kromě těchto změn, všechno ostatní zůstává stejné, můžete stále používat rozhraní rozbočovače, které jste již obeznámeni s psát obchodní logiku.

    > [!NOTE]
    > Při implementaci koncový `/signalr/negotiate` bod je vystavenpro vyjednávání pomocí Azure SignalR Service SDK. Vrátí zvláštní odpověď vyjednávání, když se klienti pokusí připojit a přesměrovat klienty do koncového bodu služby definovaného v připojovacím řetězci.

1. Stisknutím **klávesy F5** spusťte projekt v režimu ladění. Můžete vidět, že aplikace běží místně. Místo hostování runtime SignalR podle samotné aplikace se teď připojuje ke službě Azure SignalR.

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]



> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a skupina prostředků včetně všech v ní obsažených prostředků bude trvale odstraněna. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste vytvořili prostředky pro hostování této ukázky ve stávající skupině prostředků obsahující prostředky, které chcete zachovat, můžete místo odstranění skupiny prostředků odstranit jednotlivé prostředky z jejich odpovídajících oken.
> 
> 

Přihlaste se na web [Azure Portal ](https://portal.azure.com) a klikněte na **Skupiny prostředků**.

Do textového pole **Filtrovat podle názvu** zadejte název vaší skupiny prostředků. V pokynech v tomto rychlém startu se používala skupina prostředků *SignalRTestResources*. Ve výsledcích hledání klikněte na **...** u vaší skupiny prostředků a pak na **Odstranit skupinu prostředků**.

   
![Odstranění](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nový prostředek služby Azure SignalR a použili ho s ASP.NET webovou aplikací. Dále se dozvíte, jak vyvíjet aplikace v reálném čase pomocí služby Azure SignalR se službou ASP.NET Core.

> [!div class="nextstepaction"]
> [Služba Azure SignalR s ASP.NET jádrem](./signalr-quickstart-dotnet-core.md)
