---
title: 'Rychlý Start: spuštění vlastního kontejneru v App Service'
description: Začněte s kontejnery na Azure App Service nasazením prvního vlastního kontejneru.
author: msangapu-msft
ms.author: msangapu
ms.date: 10/21/2019
ms.topic: quickstart
ms.custom: devx-track-csharp
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 360da015f012822593dbb6390cb7df0017ba85b1
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745073"
---
# <a name="run-a-custom-container-in-azure"></a>Spuštění vlastního kontejneru v Azure

::: zone pivot="container-windows"
[Azure App Service](overview.md) poskytuje předdefinované zásobníky aplikací ve Windows, jako je ASP.NET nebo Node.js, které běží ve službě IIS. Předem nakonfigurované prostředí kontejnerů Windows zamkne operační systém od přístupu pro správu, instalací softwaru, změn v globální mezipaměti sestavení atd. Další informace najdete v tématu [funkce operačního systému na Azure App Service](operating-system-functionality.md). Pokud vaše aplikace vyžaduje vyšší úroveň přístupu, než předkonfigurované prostředí umožňuje, můžete místo toho nasadit vlastní kontejner s Windows.

V tomto rychlém startu se dozvíte, jak nasadit aplikaci ASP.NET v imagi Windows do [Docker Hub](https://hub.docker.com/) ze sady Visual Studio. Aplikaci spustíte ve vlastním kontejneru v Azure App Service.

> [!NOTE]
> Kontejnery Windows jsou omezené na soubory Azure a aktuálně nepodporují Azure Blob.


## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu potřebujete:

- <a href="https://hub.docker.com/" target="_blank">Zaregistrovat si účet Centra Dockeru</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Nainstalujte Docker for Windows</a>.
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Přepnout Docker na spouštění kontejnerů Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Nainstalujte Visual Studio 2019</a> s pracovními procesy pro **vývoj ASP.NET a web** a **vývoj pro Azure** . Pokud jste již nainstalovali Visual Studio 2019:

    - Nainstalujte nejnovější aktualizace v aplikaci Visual Studio tak, že vyberete **nápovědu** vyhledat  >  **aktualizace**.
    - Přidejte úlohy do sady Visual Studio tak, že vyberete **nástroje**  >  **získat nástroje a funkce**.

## <a name="create-an-aspnet-web-app"></a>Vytvoření webové aplikace ASP.NET

Pomocí následujících kroků vytvořte webovou aplikaci v ASP.NET:

1. Otevřete Visual Studio a pak vyberte **vytvořit nový projekt**.

1. V možnosti **vytvořit nový projekt** vyhledejte a zvolte **Webová aplikace ASP.NET (.NET Framework)** pro jazyk C# a pak vyberte **Další**.

1. V části **Konfigurovat nový projekt** pojmenujte aplikaci _myfirstazurewebapp_ a pak vyberte **vytvořit**.

   ![Konfigurace projektu webové aplikace](./media/quickstart-custom-container/configure-web-app-project-container.png)

1. Do Azure můžete nasadit jakýkoli typ webové aplikace ASP.NET. Pro tento rychlý Start vyberte šablonu **MVC** .

1. Vyberte možnost **Podpora Docker** a ujistěte se, že je ověřování nastaveno na **bez ověřování**. Vyberte **Vytvořit**.

   ![Vytvoření webové aplikace v ASP.NET](./media/quickstart-custom-container/select-mvc-template-for-container.png)

1. Pokud se soubor _Dockerfile_ neotevře automaticky, otevřete ho z **Průzkumníka řešení**.

1. Potřebujete [podporovanou nadřazenou image](configure-custom-container.md#supported-parent-images). Nadřazenou image změníte tak, že řádek `FROM` nahradíte následujícím kódem a soubor uložíte:

   ```dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. V nabídce sady Visual Studio vyberte **ladit**  >  **Spustit bez ladění** , aby se webová aplikace spouštěla místně.

   ![Místní spuštění aplikace](./media/quickstart-custom-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Publikování do Centra Dockeru

1. V **Průzkumník řešení** klikněte pravým tlačítkem na projekt **Myfirstazurewebapp** a vyberte **publikovat**.

1. Zvolte **App Service** a pak vyberte **publikovat**.

1. V nabídce vyberte **cíl publikování** vyberte **Container Registry** a **Docker Hub** a potom klikněte na **publikovat**.

   ![Publikování ze stránky přehledu projektu](./media/quickstart-custom-container/publish-to-docker-vs2019.png)

1. Zadejte přihlašovací údaje účtu Docker Hub a vyberte **Uložit**.

   Počkejte, až se nasazení dokončí. Na stránce **publikovat** se teď zobrazuje název úložiště, který se má použít později.

   ![Snímek obrazovky, který zvýrazní název úložiště.](./media/quickstart-custom-container/published-docker-repository-vs2019.png)

1. Zkopírujte si tento název úložiště pro pozdější použití.

## <a name="create-a-windows-container-app"></a>Vytvoření aplikace typu kontejner pro Windows

1. Přihlaste se k [portálu Azure Portal]( https://portal.azure.com).

1. V levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.

1. Ve vyhledávacím poli nad seznamem prostředků Azure Marketplace vyhledejte **Web App for Containers** a vyberte **vytvořit**.

1. V možnosti **vytvořit webovou aplikaci** vyberte předplatné a **skupinu prostředků**. V případě potřeby můžete vytvořit novou skupinu prostředků.

1. Zadejte název aplikace, jako je například *Win-Container-demo* a vyberte **Windows** pro **operační systém**. Pokračujte výběrem **Další: Docker** .

   ![Vytvoření Web App for Containers](media/quickstart-custom-container/create-web-app-continer.png)

1. Pro **zdroj obrázku** vyberte **Docker Hub** a pro **image a tag** zadejte název úložiště, který jste zkopírovali v části [publikovat do Docker Hub](#publish-to-docker-hub).

   ![Nakonfigurujte Web App for Containers](media/quickstart-custom-container/configure-web-app-continer.png)

    Pokud máte vlastní image pro svou webovou aplikaci někde jinde, například ve službě [Azure Container Registry](../container-registry/index.yml) nebo v jakémkoli jiném soukromém úložišti, tady ji můžete nakonfigurovat.

1. Vyberte **zkontrolovat a vytvořit** a pak **vytvořte** a počkejte, než Azure vytvoří požadované prostředky.

## <a name="browse-to-the-container-app"></a>Přechod do aplikace typu kontejner

Po dokončení operace Azure se zobrazí okno s oznámením.

![Nasazení bylo úspěšné.](media/quickstart-custom-container/portal-create-finished.png)

1. Klikněte na **Přejít k prostředku**.

1. V přehledu tohoto prostředku použijte odkaz vedle **adresy URL**.

Otevře se nová stránka prohlížeče na následující stránce:

![Spouští se aplikace pro Windows Container.](media/quickstart-custom-container/app-starting.png)

Počkejte několik minut a zkuste to znovu, dokud se nezobrazí úvodní stránka ASP.NET:

![Spuštěná aplikace kontejneru Windows](media/quickstart-custom-container/app-running-vs.png)

**Blahopřejeme!** Spustili jste svůj první vlastní kontejner s Windows ve službě Azure App Service.

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

1. V aplikaci Visual Studio v **Průzkumník řešení** otevřete **zobrazení**  >  **Domů**  >  **index. cshtml**.

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

   ![Restartování webové aplikace v Azure](./media/quickstart-custom-container/portal-restart-app.png)

Znovu [přejděte do aplikace typu kontejner](#browse-to-the-container-app). Po aktualizaci webové stránky by se aplikace měla napřed vrátit na stránku Spouštění a pak za pár minut by měla znovu zobrazit aktualizovanou webovou stránku.

![Aktualizovaná webová aplikace v Azure](./media/quickstart-custom-container/azure-web-app-updated.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Migrace na kontejner Windows v Azure](tutorial-custom-container.md)

Nebo si prohlédněte další zdroje informací:

> [!div class="nextstepaction"]
> [Konfigurace vlastního kontejneru](configure-custom-container.md)

::: zone-end  

::: zone pivot="container-linux"
App Service v systému Linux poskytuje předdefinované zásobníky aplikací v systému Linux s podporou pro jazyky, jako je například .NET, PHP, Node.js a další. Můžete také použít vlastní image Dockeru a spouštět webovou aplikaci v zásobníku aplikací, který ještě není v Azure definovaný. V tomto rychlém startu se dozvíte, jak nasadit image z [Azure Container Registry](../container-registry/index.yml) (ACR) do App Service.

## <a name="prerequisites"></a>Předpoklady

* [Účet Azure](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure App Service rozšíření pro vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Toto rozšíření můžete použít k vytváření, správě a nasazování Web Apps pro Linux na platformě Azure jako službu (PaaS).
* [Rozšíření Docker pro vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker). Pomocí tohoto rozšíření můžete zjednodušit správu místních imagí a příkazů Docker a nasazovat do Azure sestavené image aplikací.

## <a name="create-an-image"></a>Vytvoření image

K dokončení tohoto rychlého startu budete potřebovat vhodnou image webové aplikace uloženou v [Azure Container Registry](../container-registry/index.yml). Postupujte podle pokynů v tématu [rychlý Start: Vytvoření privátního registru kontejnerů pomocí Azure Portal](../container-registry/container-registry-get-started-portal.md), ale použijte `mcr.microsoft.com/azuredocs/go` obrázek místo `hello-world` obrázku. Pro referenci se [v úložišti ukázek Azure najdete v ukázce souboru Dockerfile](https://github.com/Azure-Samples/go-docs-hello-world).

> [!IMPORTANT]
> Nezapomeňte nastavit možnost uživatel s **oprávněními správce** , aby se **povolil** při vytváření registru kontejneru. Můžete ji také nastavit v části **přístupové klíče** stránky registru v Azure Portal. Toto nastavení se vyžaduje pro App Service přístup.

## <a name="sign-in"></a>Přihlásit se

Dále spusťte VS Code a přihlaste se k účtu Azure pomocí rozšíření App Service. Pokud to chcete provést, vyberte na řádku aktivity logo Azure, přejděte do Průzkumníka **služby App Service** , vyberte možnost **Přihlásit se k Azure** a postupujte podle pokynů.

![Přihlaste se k Azure](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Kontrola požadavků

Nyní můžete zkontrolovat, zda máte správně nainstalované a nakonfigurované požadavky.

V VS Code byste měli na stavovém řádku a v rámci vašeho předplatného v Průzkumníkovi **služby App Service** vidět vaši e-mailovou adresu Azure.

Pak ověřte, že máte nainstalovaný a spuštěný Docker. V následujícím příkazu se zobrazí verze Docker, pokud je spuštěná.

```bash
docker --version
```

Nakonec zajistěte, aby byl váš Azure Container Registry připojený. Pokud to chcete provést, vyberte na panelu aktivity logo Docker a pak přejděte do části **Registry**.

![Snímek obrazovky ukazuje hodnotu Registry s rozšířenou službou Azure a souborem s příponou tečka i o filename.](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>Nasaďte bitovou kopii na Azure App Service

Teď, když je všechno nakonfigurované, můžete image nasadit do [Azure App Service](https://azure.microsoft.com/services/app-service/) přímo z Průzkumníka rozšíření Docker.

Najděte obrázek pod uzlem **Registry** v Průzkumníkovi **Docker** a rozbalte ho, aby se zobrazily jeho značky. Klikněte pravým tlačítkem na značku a pak vyberte **nasadit obrázek do Azure App Service**.

Tady můžete podle pokynů zvolit předplatné, globálně jedinečný název aplikace, skupinu prostředků a plán App Service. Pro cenovou úroveň vyberte **B1 Basic** a oblast.

Po nasazení je vaše aplikace k dispozici na adrese `http://<app name>.azurewebsites.net` .

**Skupina prostředků** je pojmenovaná kolekce všech prostředků vaší aplikace v Azure. Například skupina prostředků může obsahovat odkaz na web, databázi a funkci Azure Functions.

**Plán App Service** definuje fyzické prostředky, které se budou používat k hostování vašeho webu. V tomto rychlém startu se používá **základní** plán hostování v infrastruktuře **Linux** , což znamená, že lokalita bude hostována v počítači se systémem Linux společně s ostatními weby. Pokud začnete s plánem **Basic** , můžete k horizontálnímu navýšení kapacity použít Azure Portal, aby se na počítači používala jediná lokalita.

## <a name="browse-the-website"></a>Procházet web

Panel **výstup** se otevře během nasazování, aby označoval stav operace. Až se operace dokončí, Najděte aplikaci, kterou jste vytvořili v Průzkumníkovi **služby App Service** , klikněte na ni pravým tlačítkem myši a vyberte **Procházet web** a otevřete web v prohlížeči.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Další kroky

Blahopřejeme, úspěšně jste dokončili tento rychlý Start.

Pak se podívejte na další rozšíření Azure.

* [Databáze Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Nástroje rozhraní příkazového řádku Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Nástroje Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Nebo si je všechny nainstalujte pomocí balíčku rozšíření [nástroje Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) .

Podívejte se na další zdroje informací:

> [!div class="nextstepaction"]
> [Konfigurace vlastního kontejneru](configure-custom-container.md)

::: zone-end
