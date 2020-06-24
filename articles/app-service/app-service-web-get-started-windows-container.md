---
title: 'Rychlý Start: kontejner Windows (Preview)'
description: Nasaďte první vlastní kontejner Windows do Azure App Service. Využijte možnost kontejneru, upravte kontejner Windows tak, jak vám to vyhovuje.
ms.topic: quickstart
ms.date: 08/30/2019
ms.custom: mvc, seodec18
ms.openlocfilehash: 0689e46556c4ddfddde073d79886d5cb556fde49
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85205658"
---
# <a name="run-a-custom-windows-container-in-azure-preview"></a>Spuštění vlastního kontejneru s Windows v Azure (Preview)

[Azure App Service](overview.md) poskytuje předdefinované zásobníky aplikací ve Windows, jako je ASP.NET nebo Node.js, které běží ve službě IIS. Předkonfigurované prostředí systému Windows zamkne operační systém od přístupu pro správu, instalací softwaru, změn v globální mezipaměti sestavení atd. Další informace najdete v tématu [funkce operačního systému na Azure App Service](operating-system-functionality.md). Pokud vaše aplikace vyžaduje vyšší úroveň přístupu, než předkonfigurované prostředí umožňuje, můžete místo toho nasadit vlastní kontejner s Windows.

V tomto rychlém startu se dozvíte, jak nasadit aplikaci ASP.NET v imagi Windows do [Docker Hub](https://hub.docker.com/) ze sady Visual Studio. Aplikaci spustíte ve vlastním kontejneru v Azure App Service.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- <a href="https://hub.docker.com/" target="_blank">Zaregistrovat si účet Centra Dockeru</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Nainstalujte Docker for Windows</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Přepnout Docker na spouštění kontejnerů Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Nainstalujte Visual Studio 2019</a> s pracovními procesy pro **vývoj ASP.NET a web** a **vývoj pro Azure** . Pokud jste již nainstalovali Visual Studio 2019:

    - Nainstalujte nejnovější aktualizace v aplikaci Visual Studio tak, že vyberete **nápovědu**vyhledat  >  **aktualizace**.
    - Přidejte úlohy do sady Visual Studio tak, že vyberete **nástroje**  >  **získat nástroje a funkce**.

## <a name="create-an-aspnet-web-app"></a>Vytvoření webové aplikace ASP.NET

Pomocí následujících kroků vytvořte webovou aplikaci v ASP.NET:

1. Otevřete Visual Studio a pak vyberte **vytvořit nový projekt**.

1. V možnosti **vytvořit nový projekt**vyhledejte a zvolte **Webová aplikace ASP.NET (.NET Framework)** pro jazyk C# a pak vyberte **Další**.

1. V části **Konfigurovat nový projekt**pojmenujte aplikaci _myfirstazurewebapp_a pak vyberte **vytvořit**.

   ![Konfigurace projektu webové aplikace](./media/app-service-web-get-started-windows-container/configure-web-app-project-container.png)

1. Do Azure můžete nasadit jakýkoli typ webové aplikace ASP.NET. Pro tento rychlý Start vyberte šablonu **MVC** .

1. Vyberte možnost **Podpora Docker**a ujistěte se, že je ověřování nastaveno na **bez ověřování**. Vyberte **Vytvořit**.

   ![Vytvoření webové aplikace v ASP.NET](./media/app-service-web-get-started-windows-container/select-mvc-template-for-container.png)

1. Pokud se soubor _Dockerfile_ neotevře automaticky, otevřete ho z **Průzkumníka řešení**.

1. Potřebujete [podporovanou nadřazenou image](#use-a-different-parent-image). Nadřazenou image změníte tak, že řádek `FROM` nahradíte následujícím kódem a soubor uložíte:

   ```dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. V nabídce sady Visual Studio vyberte **ladit**  >  **Spustit bez ladění** , aby se webová aplikace spouštěla místně.

   ![Místní spuštění aplikace](./media/app-service-web-get-started-windows-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Publikování do Centra Dockeru

1. V **Průzkumník řešení**klikněte pravým tlačítkem na projekt **Myfirstazurewebapp** a vyberte **publikovat**.

1. Zvolte **App Service** a pak vyberte **publikovat**.

1. V nabídce vyberte **cíl publikování**vyberte **Container Registry** a **Docker Hub**a potom klikněte na **publikovat**.

   ![Publikování ze stránky přehledu projektu](./media/app-service-web-get-started-windows-container/publish-to-docker-vs2019.png)

1. Zadejte přihlašovací údaje účtu Docker Hub a vyberte **Uložit**.

   Počkejte, než se nasazení dokončí. Na stránce **publikovat** se teď zobrazuje název úložiště, který se má použít později.

   ![Publikování ze stránky přehledu projektu](./media/app-service-web-get-started-windows-container/published-docker-repository-vs2019.png)

1. Zkopírujte si tento název úložiště pro pozdější použití.

## <a name="create-a-windows-container-app"></a>Vytvoření aplikace typu kontejner pro Windows

1. Přihlaste se k webu [Azure Portal]( https://portal.azure.com).

1. V levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.

1. Ve vyhledávacím poli nad seznamem prostředků Azure Marketplace vyhledejte **Web App for Containers**a vyberte **vytvořit**.

1. V možnosti **vytvořit webovou aplikaci**vyberte předplatné a **skupinu prostředků**. V případě potřeby můžete vytvořit novou skupinu prostředků.

1. Zadejte název aplikace, jako je například *Win-Container-demo* a vyberte **Windows** pro **operační systém**. Pokračujte výběrem **Další: Docker** .

   ![Vytvoření Web App for Containers](media/app-service-web-get-started-windows-container/create-web-app-continer.png)

1. Pro **zdroj obrázku**vyberte **Docker Hub** a pro **image a tag**zadejte název úložiště, který jste zkopírovali v části [publikovat do Docker Hub](#publish-to-docker-hub).

   ![Nakonfigurujte Web App for Containers](media/app-service-web-get-started-windows-container/configure-web-app-continer.png)

    Pokud máte vlastní image pro svou webovou aplikaci někde jinde, například ve službě [Azure Container Registry](/azure/container-registry/) nebo v jakémkoli jiném soukromém úložišti, tady ji můžete nakonfigurovat.

1. Vyberte **zkontrolovat a vytvořit** a pak **vytvořte** a počkejte, než Azure vytvoří požadované prostředky.

## <a name="browse-to-the-container-app"></a>Přechod do aplikace typu kontejner

Po dokončení operace Azure se zobrazí okno s oznámením.

![Nasazení bylo úspěšné.](media/app-service-web-get-started-windows-container/portal-create-finished.png)

1. Klikněte na **Přejít k prostředku**.

1. V přehledu tohoto prostředku použijte odkaz vedle **adresy URL**.

Otevře se nová stránka prohlížeče na následující stránce:

![Spouští se aplikace pro Windows Container.](media/app-service-web-get-started-windows-container/app-starting.png)

Počkejte několik minut a zkuste to znovu, dokud se nezobrazí úvodní stránka ASP.NET:

![Spuštěná aplikace kontejneru Windows](media/app-service-web-get-started-windows-container/app-running-vs.png)

**Gratulujeme!** Spustili jste svůj první vlastní kontejner s Windows ve službě Azure App Service.

## <a name="see-container-start-up-logs"></a>Zobrazení protokolů spuštění kontejneru

Načtení kontejneru s Windows může nějakou dobu trvat. Pokud chcete zobrazit průběh, přejděte na následující adresu URL nahrazením *\<app_name>* názvem vaší aplikace.
```
https://<app_name>.scm.azurewebsites.net/api/logstream
```

Streamované protokoly vypadají přibližně takto:

```
2018-07-27T12:03:11  Welcome, you are now connected to log-streaming service.
27/07/2018 12:04:10.978 INFO - Site: win-container-demo - Start container succeeded. Container: facbf6cb214de86e58557a6d073396f640bbe2fdec88f8368695c8d1331fc94b
27/07/2018 12:04:16.767 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.017 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.020 INFO - Site: win-container-demo - Container started successfully
```

## <a name="update-locally-and-redeploy"></a>Místní aktualizace a opětovné nasazení

1. V aplikaci Visual Studio v **Průzkumník řešení**otevřete **zobrazení**  >  **Domů**  >  **index. cshtml**.

1. Najděte HTML značku `<div class="jumbotron">` poblíž začátku a nahraďte celý element následujícím kódem:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Pokud se chcete znovu nasadit do Azure, klikněte pravým tlačítkem na projekt **myfirstazurewebapp** v **Průzkumník řešení** a vyberte **publikovat**.

1. Na stránce Publikovat vyberte **Publikovat** a počkejte, než se publikování dokončí.

1. Restartujte aplikaci, aby služba App Service dostala informaci, že má z Centra Dockeru načíst novou image. Zpátky na stránce aplikace na portálu klikněte na **restartovat**  >  **Ano**.

   ![Restartování webové aplikace v Azure](./media/app-service-web-get-started-windows-container/portal-restart-app.png)

Znovu [přejděte do aplikace typu kontejner](#browse-to-the-container-app). Po aktualizaci webové stránky by se aplikace měla napřed vrátit na stránku Spouštění a pak za pár minut by měla znovu zobrazit aktualizovanou webovou stránku.

![Aktualizovaná webová aplikace v Azure](./media/app-service-web-get-started-windows-container/azure-web-app-updated.png)

## <a name="use-a-different-parent-image"></a>Použití jiné nadřazené image

Pro spuštění vaší aplikace můžete použít jinou vlastní image Docker. Je však nutné zvolit správnou [nadřazenou Image (základní bitovou kopii)](https://docs.docker.com/develop/develop-images/baseimages/) pro rozhraní, které chcete:

- K nasazení aplikací .NET Framework použijte nadřazenou bitovou kopii na základě verze Windows Server Core 2019 [(LTSC) (dlouhodobá údržba kanálu)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) . 
- Pokud chcete nasadit aplikace .NET Core, použijte nadřazenou bitovou kopii založenou na vydání Windows Server nano 1809 [(konzola pro správu)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) . 

Stažení nadřazené image při spuštění aplikace nějakou dobu trvá. Čas spuštění však můžete zkrátit použitím některé z následujících nadřazených imagí, které jsou již uložené v mezipaměti ve službě Azure App Service:

- [MCR.Microsoft.com/DotNET/Framework/ASPNET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4.7.2-windowsservercore-ltsc2019
- [MCR.Microsoft.com/Windows/nanoserver](https://hub.docker.com/_/microsoft-windows-nanoserver/): 1809 – tento obrázek je základní kontejner používaný v Microsoft [ASP.NET Core](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/) imagí Microsoft Windows nano serveru.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Migrace na kontejner Windows v Azure](app-service-web-tutorial-windows-containers-custom-fonts.md)
