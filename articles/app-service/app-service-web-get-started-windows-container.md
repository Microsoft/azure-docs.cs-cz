---
title: 'Rychlý start: Kontejner windows (náhled)'
description: Nasaďte svůj první vlastní kontejner Windows do služby Azure App Service. Využijte výhod kontejnerizace a přizpůsobte kontejner Windows tak, jak se vám líbí.
ms.topic: quickstart
ms.date: 08/30/2019
ms.custom: mvc, seodec18
ms.openlocfilehash: cd6b78e5fd824cc013cc946d23677237923f485e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80047103"
---
# <a name="run-a-custom-windows-container-in-azure-preview"></a>Spuštění vlastního kontejneru s Windows v Azure (Preview)

[Azure App Service](overview.md) poskytuje předdefinované zásobníky aplikací ve Windows, jako je ASP.NET nebo Node.js, které běží ve službě IIS. Předkonfigurované prostředí systému Windows uzamkne operační systém před přístupem pro správu, instalacemi softwaru, změnami globální mezipaměti sestavení a tak dále. Další informace najdete [v tématu Funkce operačního systému ve službě Azure App Service](operating-system-functionality.md). Pokud vaše aplikace vyžaduje vyšší úroveň přístupu, než předkonfigurované prostředí umožňuje, můžete místo toho nasadit vlastní kontejner s Windows.

Tento úvodní příručka ukazuje, jak nasadit ASP.NET aplikaci v bitové kopii Windows do [Centra Docker u](https://hub.docker.com/) Windows z Visual Studia. Aplikaci spustíte ve vlastním kontejneru ve službě Azure App Service.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- <a href="https://hub.docker.com/" target="_blank">Zaregistrovat si účet Centra Dockeru</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Nainstalujte Docker for Windows</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Přepnout Docker na spouštění kontejnerů Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Nainstalujte SiVisual Studio 2019</a> s **ASP.NET a vývoj webových aplikací** a **úlohy vývoje Azure.** Pokud už jste Visual Studio 2019 nainstalovali:

    - Nainstalujte nejnovější aktualizace v sadě Visual Studio výběrem **možnosti Kontrola nápovědy** > **k aktualizacím**.
    - Přidejte úlohy v sadě Visual Studio výběrem **možnosti Nástroje** > **získat nástroje a funkce**.

## <a name="create-an-aspnet-web-app"></a>Vytvoření webové aplikace ASP.NET

Vytvořte ASP.NET webovou aplikaci takto:

1. Otevřete Visual Studio a vyberte **Vytvořit nový projekt**.

1. V **okně Vytvořit nový projekt**vyhledejte a zvolte ASP.NET **webovou aplikaci (.NET Framework)** pro c# a pak vyberte **Další**.

1. V **části Konfigurace nového projektu**pojmenujte aplikaci _myFirstAzureWebApp_a pak vyberte **Vytvořit**.

   ![Konfigurace projektu webové aplikace](./media/app-service-web-get-started-windows-container/configure-web-app-project-container.png)

1. Do Azure můžete nasadit jakýkoli typ webové aplikace ASP.NET. Pro tento rychlý start zvolte šablonu **MVC.**

1. Vyberte **podporu Dockeru**a ujistěte se, že je ověřování nastaveno na **žádné ověřování**. Vyberte **Vytvořit**.

   ![Vytvořit ASP.NET webovou aplikaci](./media/app-service-web-get-started-windows-container/select-mvc-template-for-container.png)

1. Pokud se soubor _Dockerfile_ neotevře automaticky, otevřete ho z **Průzkumníka řešení**.

1. Potřebujete [podporovaný nadřazený obrázek](#use-a-different-parent-image). Nadřazenou image změníte tak, že řádek `FROM` nahradíte následujícím kódem a soubor uložíte:

   ```Dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. V nabídce Visual Studio vyberte **Ladění** > **start bez ladění** spustit webovou aplikaci místně.

   ![Místní spuštění aplikace](./media/app-service-web-get-started-windows-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Publikování do Centra Dockeru

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt **myFirstAzureWebApp** a vyberte **publikovat**.

1. Zvolte **App Service** a pak vyberte **Publikovat**.

1. V pick a **publish target**vyberte **Container Registry** a **Docker Hub**a klikněte na **Publikovat**.

   ![Publikování ze stránky přehledu projektu](./media/app-service-web-get-started-windows-container/publish-to-docker-vs2019.png)

1. Zadejte přihlašovací údaje účtu Docker Hub a vyberte **Uložit**.

   Počkejte, než se nasazení dokončí. Na stránce **Publikovat** se nyní zobrazí název úložiště, který se má použít později.

   ![Publikování ze stránky přehledu projektu](./media/app-service-web-get-started-windows-container/published-docker-repository-vs2019.png)

1. Zkopírujte si tento název úložiště pro pozdější použití.

## <a name="create-a-windows-container-app"></a>Vytvoření aplikace typu kontejner pro Windows

1. Přihlaste se k [portálu Azure]( https://portal.azure.com).

1. V levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.

1. Ve vyhledávacím poli nad seznamem prostředků Azure Marketplace vyhledejte **Web App pro kontejnery**a vyberte **Vytvořit**.

1. V **okně Vytvoření webové aplikace**zvolte předplatné a **skupinu prostředků**. V případě potřeby můžete vytvořit novou skupinu prostředků.

1. Zadejte název aplikace, například *ukázku win-container,* a zvolte **Windows** pro **operační systém**. Vyberte **Další: Docker** pokračovat.

   ![Vytvoření webové aplikace pro kontejnery](media/app-service-web-get-started-windows-container/create-web-app-continer.png)

1. V **části Zdroj bitových obrázků**zvolte Docker **Hub** a pro Image **and tag**zadejte název úložiště, který jste zkopírovali v centru Publikovat do [Docker Hubu](#publish-to-docker-hub).

   ![Konfigurace webové aplikace pro kontejnery](media/app-service-web-get-started-windows-container/configure-web-app-continer.png)

    Pokud máte vlastní image pro svou webovou aplikaci někde jinde, například ve službě [Azure Container Registry](/azure/container-registry/) nebo v jakémkoli jiném soukromém úložišti, tady ji můžete nakonfigurovat.

1. Vyberte **Zkontrolovat a vytvořit** a pak **vytvořit** a počkejte na Azure k vytvoření požadovaných prostředků.

## <a name="browse-to-the-container-app"></a>Přechod do aplikace typu kontejner

Po dokončení operace Azure se zobrazí okno s oznámením.

![Nasazení proběhlo úspěšně.](media/app-service-web-get-started-windows-container/portal-create-finished.png)

1. Klikněte na **Přejít k prostředku**.

1. V přehledu tohoto prostředku přejděte na odkaz vedle **adresy URL**.

Na následující stránku se otevře nová stránka prohlížeče:

![Spuštění aplikace kontejneru windows](media/app-service-web-get-started-windows-container/app-starting.png)

Počkejte několik minut a zkuste to znovu, dokud se nezobrazí úvodní stránka ASP.NET:

![Aplikace Windows Container je spuštěna](media/app-service-web-get-started-windows-container/app-running-vs.png)

**Blahopřejeme!** Spustili jste svůj první vlastní kontejner s Windows ve službě Azure App Service.

## <a name="see-container-start-up-logs"></a>Zobrazení protokolů spuštění kontejneru

Načtení kontejneru s Windows může nějakou dobu trvat. Chcete-li zobrazit průběh, přejděte na * \<* následující adresu URL nahrazením app_name>názvem aplikace.
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

1. V sadě Visual Studio otevřete v **Průzkumníku řešení** **zobrazení** > **Home** > **Index.cshtml**.

1. Najděte HTML značku `<div class="jumbotron">` poblíž začátku a nahraďte celý element následujícím kódem:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Pokud chcete znovu nasadit do Azure, klikněte pravým tlačítkem myši na projekt **myFirstAzureWebApp** v **Průzkumníku řešení** a zvolte **Publikovat**.

1. Na stránce Publikovat vyberte **Publikovat** a počkejte, než se publikování dokončí.

1. Restartujte aplikaci, aby služba App Service dostala informaci, že má z Centra Dockeru načíst novou image. Na stránce aplikace na portálu klikněte na **Restartovat** > **ano**.

   ![Restartování webové aplikace v Azure](./media/app-service-web-get-started-windows-container/portal-restart-app.png)

Znovu [přejděte do aplikace typu kontejner](#browse-to-the-container-app). Po aktualizaci webové stránky by se aplikace měla napřed vrátit na stránku Spouštění a pak za pár minut by měla znovu zobrazit aktualizovanou webovou stránku.

![Aktualizovaná webová aplikace v Azure](./media/app-service-web-get-started-windows-container/azure-web-app-updated.png)

## <a name="use-a-different-parent-image"></a>Použití jiné nadřazené image

Ke spuštění aplikace můžete použít jinou vlastní image Dockeru. Musíte však zvolit správný [nadřazený obrázek (základní obrázek)](https://docs.docker.com/develop/develop-images/baseimages/) pro požadovanou architekturu:

- Chcete-li nasadit aplikace rozhraní .NET Framework, použijte nadřazenou bitovou kopii založenou na verzi [LTSC (Long Term Servicing Channel) systému](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) Windows Server Core 2019. 
- Chcete-li nasadit aplikace .NET Core, použijte nadřazenou bitovou kopii založenou na verzi [SAC (Semi-Annual Servicing Channel) systému](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) Windows Server Nano 1809. 

Stažení nadřazené image při spuštění aplikace nějakou dobu trvá. Čas spuštění však můžete zkrátit použitím některé z následujících nadřazených imagí, které jsou již uložené v mezipaměti ve službě Azure App Service:

- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.7.2-windowsservercore-ltsc2019
- [mcr.microsoft.com/windows/nanoserver](https://hub.docker.com/_/microsoft-windows-nanoserver/):1809 - tato bitová kopie je základní kontejner používaný v rámci microsoft [ASP.NET Core](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/) Microsoft Windows Nano Server image.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Migrace na kontejner Windows v Azure](app-service-web-tutorial-windows-containers-custom-fonts.md)
