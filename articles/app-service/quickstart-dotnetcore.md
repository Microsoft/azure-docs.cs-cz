---
title: 'Rychlý Start: Vytvoření aplikace ASP.NET Core v C#'
description: Naučte se spouštět webové aplikace v Azure App Service nasazením první aplikace ASP.NET Core.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 09/24/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperfq1
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: aa8eb945ba77e1a4ac5215acf3bdbc12cac0c4c9
ms.sourcegitcommit: 487a9f5272300d60df2622c3d13e794d54680f90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91661121"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Rychlý Start: Vytvoření webové aplikace v ASP.NET Core v Azure

::: zone pivot="platform-windows"  

V tomto rychlém startu se dozvíte, jak vytvořit a nasadit první webovou aplikaci ASP.NET Core do [Azure App Service](overview.md). 

Až budete hotovi, budete mít skupinu prostředků Azure, která se skládá z App Service plánu hostování a App Service s nasazenou webovou aplikací.

## <a name="prerequisites"></a>Požadované součásti

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/dotnet/).
- V tomto rychlém startu se nasadí aplikace, která se App Service ve Windows. Pokud chcete nasadit nástroj na App Service v systému _Linux_, přečtěte si téma [Vytvoření webové aplikace .net Core v App Service](./quickstart-dotnetcore.md).
- Nainstalujte <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> s úlohou **vývoje ASP.NET a webu** .

  Pokud jste již nainstalovali Visual Studio 2019:

  - Nainstalujte nejnovější aktualizace v aplikaci Visual Studio tak, že vyberete **nápovědu**vyhledat  >  **aktualizace**.
  - Přidejte úlohy tak, že vyberete **nástroje**  >  **získat nástroje a funkce**.


## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

Pomocí následujících kroků vytvořte webovou aplikaci ASP.NET Core v aplikaci Visual Studio:

1. Otevřete Visual Studio a vyberte **vytvořit nový projekt**.

1. V části **vytvořit nový projekt**vyberte **ASP.NET Core webová aplikace** a ověřte, že je **jazyk C#** uveden v jazycích pro tuto volbu, a pak vyberte **Další**.

1. V **konfiguraci nového projektu**zadejte název projektu webové aplikace *MyFirstAzureWebApp*a vyberte **vytvořit**.

   ![Konfigurace projektu webové aplikace](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. Do Azure můžete nasadit jakýkoli typ ASP.NET Core webové aplikace, ale pro účely tohoto rychlého startu vyberte šablonu **webové aplikace** . Ujistěte se, že je **ověřování** nastaveno na **bez ověřování**a že není vybrána žádná jiná možnost. Potom vyberte **Vytvořit**.

   ![Vytvoření nové ASP.NET Core webové aplikace](./media/quickstart-dotnetcore/create-aspnet-core-web-app.png) 
   
1. V nabídce sady Visual Studio vyberte **ladit**  >  **Spustit bez ladění** , aby se vaše webová aplikace spouštěla místně.

   ![Webová aplikace spuštěná místně](./media/quickstart-dotnetcore/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Publikování webové aplikace

Pokud chcete publikovat webovou aplikaci, musíte nejdřív vytvořit a nakonfigurovat nové App Service, do které můžete aplikaci publikovat. 

Jako součást nastavení App Service vytvoříte:

- Nová [Skupina prostředků](../azure-resource-manager/management/overview.md#terminology) , která bude obsahovat všechny prostředky Azure pro službu.
- Nový [plán hostování](./overview-hosting-plans.md) , který určuje umístění, velikost a funkce webové serverové farmy, která je hostitelem vaší aplikace.

Při vytváření App Service a publikování webové aplikace postupujte podle těchto kroků:

1. V **Průzkumník řešení**klikněte pravým tlačítkem na projekt **MyFirstAzureWebApp** a vyberte **publikovat**. Pokud jste se ještě přihlásili ke svému účtu Azure ze sady Visual Studio, vyberte buď **Přidat účet** , nebo **se přihlaste**. Můžete si také vytvořit bezplatný účet Azure.

1. V dialogovém okně **vybrat cíl publikování** zvolte možnost **App Service**, vyberte **vytvořit novou**a potom vyberte **vytvořit profil**.

   ![Výběr cíle publikování](./media/quickstart-dotnetcore/pick-publish-target-vs2019.png)

1. V dialogovém okně **App Service: vytvořit nové** zadejte globálně jedinečný **název** vaší aplikace, a to tak, že přijmete výchozí název nebo zadáte nový název. Platné znaky jsou: `a-z` , `A-Z` , a `0-9` `-` . Tento **název** se používá jako předpona adresy URL vaší webové aplikace ve formátu `http://<app_name>.azurewebsites.net` .

1. U **předplatného**přijměte uvedené předplatné nebo v rozevíracím seznamu vyberte nový.

1. V **skupiny prostředků**vyberte **Nový**. Do **nového názvu skupiny prostředků**zadejte *MyResourceGroup* a vyberte **OK**. 

1. V případě **plánu hostování**vyberte možnost **Nový**. 

1. V dialogovém okně **plán hostování: vytvořit nové** zadejte hodnoty uvedené v následující tabulce:

   | Nastavení  | Navrhovaná hodnota | Description |
   | -------- | --------------- | ----------- |
   | **Plán hostování**  | *myFirstAzureWebAppPlan* | Název plánu služby App Service. |
   | **Umístění**      | *Západní Evropa* | Datacentrum, které je hostitelem webové aplikace. |
   | **Velikost**          | Zadejte možnost pro *bezplatnou* SKU. | [Cenová úroveň](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) určuje funkce hostování. |
   
   ![Vytvořit nový plán hostování](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. Ponechte **Application Insights** nastavenou na *žádný*.

1. V dialogovém okně **App Service: vytvořit nové** vyberte **vytvořit** a začněte vytvářet prostředky Azure.

   ![Vytvořit novou službu App Service](./media/quickstart-dotnetcore/create-new-app-service-vs2019.png)

1. Po dokončení průvodce vyberte **publikovat**.

   ![Publikování webové aplikace do Azure](./media/quickstart-dotnetcore/publish-web-app-vs2019.png)

   Visual Studio publikuje vaši webovou aplikaci v ASP.NET Core do Azure a spustí ji ve výchozím prohlížeči. 

   ![Publikovaná webová aplikace v ASP.NET běžící v Azure](./media/quickstart-dotnetcore/web-app-running-live.png)

**Blahopřejeme!** Vaše webová aplikace v ASP.NET Core běží živě v Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Aktualizace a opětovné nasazení aplikace

Pomocí těchto kroků aktualizujete a znovu nasadíte webovou aplikaci:

1. V **Průzkumník řešení**v rámci projektu otevřete **stránky**  >  **index. cshtml**.

1. Celou značku nahraďte `<div>` následujícím kódem:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Opětovné nasazení do služby Azure provedete tak, že v **Průzkumníku řešení** kliknete pravým tlačítkem na projekt **myFirstAzureWebApp** a vyberete **Publikovat**.

1. Na stránce **publikovat** Souhrn vyberte **publikovat**.

   ![Publikování aktualizace webové aplikace](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png)

Po dokončení publikování spustí Visual Studio prohlížeč na adrese URL webové aplikace.

![Aktualizovaná webová aplikace v ASP.NET běžící v Azure](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>Správa aplikace Azure

Pokud chcete spravovat webovou aplikaci, použijte [Azure Portal](https://portal.azure.com)a vyhledejte a vyberte **App Services**.

![Vyberte App Services](./media/quickstart-dotnetcore/app-services.png)

Na stránce **App Services** vyberte název vaší webové aplikace.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Snímek obrazovky stránky App Services s vybranou ukázkovou webovou aplikací":::

Stránka **Přehled** pro vaši webovou aplikaci obsahuje možnosti pro základní správu, jako je procházení, zastavení, spuštění, restartování a odstranění. V nabídce vlevo najdete další stránky pro konfiguraci vaší aplikace.

![App Service v Azure Portal](./media/quickstart-dotnetcore/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili aplikaci Visual Studio k vytvoření a nasazení webové aplikace ASP.NET Core k Azure App Service.

V dalším článku se dozvíte, jak vytvořit aplikaci .NET Core a jak ji připojit k SQL Database:

> [!div class="nextstepaction"]
> [ASP.NET Core s SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace ASP.NET Core](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
[App Service v systému Linux](overview.md#app-service-on-linux) poskytuje vysoce škálovatelnou službu s automatickými opravami pro hostování webů pomocí operačního systému Linux. V tomto rychlém startu se dozvíte, jak vytvořit aplikaci [.NET Core](/aspnet/core/) ve službě App Service v Linuxu. Vytvoříte aplikaci pomocí rozhraní příkazového [řádku Azure CLI](/cli/azure/get-started-with-azure-cli)a pomocí Gitu nasadíte kód .NET Core do aplikace.

![Ukázková aplikace spuštěná v Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Postup v tomto článku můžete použít v případě počítačů se systémem Mac, Windows nebo Linux.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadované součásti

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* <a href="https://git-scm.com/" target="_blank">Nainstalovat Git</a>.
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Instalace nejnovější sady .NET Core 3,1 SDK</a>

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="create-the-app-locally"></a>Místní vytvoření aplikace

V okně terminálu na svém počítači vytvořte adresář `hellodotnetcore` a přejděte do něj.

```bash
mkdir hellodotnetcore
cd hellodotnetcore
```

Vytvořte novou aplikaci .NET Core.

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>Místní spuštění aplikace

Spusťte aplikaci místně, abyste viděli, jak by měla vypadat po nasazení do Azure. 

Obnovte balíčky NuGet a spusťte aplikaci.

```bash
dotnet run
```

Otevřete webový prohlížeč a přejděte do aplikace na adrese `http://localhost:5000`.

Na stránce se zobrazí zpráva **Hello World** z ukázkové aplikace.

![Testování pomocí prohlížeče](media/quickstart-dotnetcore/dotnet-browse-local.png)

V okně terminálu ukončete webový server stisknutím **Ctrl + C**. Inicializujte úložiště Git pro projekt .NET Core.

```bash
git init
git add .
git commit -m "first commit"
```

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

Přejděte k nově vytvořené aplikaci. Nahraďte _ &lt; název aplikace>_ názvem vaší aplikace.

```bash
https://<app-name>.azurewebsites.net
```

Vaše nová aplikace by měla vypadat takto:

![Prázdná stránka aplikace](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;app-name&gt;.scm.azurewebsites.net:443/&lt;app-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="browse-to-the-app"></a>Přechod do aplikace

V prohlížeči zadejte adresu nasazené aplikace.

```bash
http://<app_name>.azurewebsites.net
```

Vzorový kód .NET Core je spuštěný v App Service v systému Linux s předdefinovaným obrázkem.

![Ukázková aplikace spuštěná v Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Blahopřejeme!** Nasadili jste svoji první aplikaci .NET Core do služby App Service v Linuxu.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="update-and-redeploy-the-code"></a>Aktualizace a opětovné nasazení kódu

V místním adresáři otevřete soubor _Startup.cs_. Proveďte malou změnu textu ve volání metody `context.Response.WriteAsync`:

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

Potvrďte změny v Gitu a potom odešlete změny kódu do Azure.

```bash
git commit -am "updated output"
git push azure master
```

Po dokončení nasazení přepněte zpátky do okna prohlížeče, které se otevřelo v kroku **přechod do aplikace** , a stiskněte tlačítko Aktualizovat.

![Aktualizovaná ukázková aplikace spuštěná ve službě Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="manage-your-new-azure-app"></a>Správa nové aplikace Azure

Chcete-li spravovat aplikaci, kterou jste vytvořili, otevřete <a href="https://portal.azure.com" target="_blank">Azure Portal</a> .

V nabídce vlevo klikněte na **App Services**a pak klikněte na název aplikace Azure.

:::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list.png" alt-text="Snímek obrazovky stránky App Services s vybranou ukázkovou webovou aplikací":::

Zobrazí se stránka s přehledem vaší aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. 

![Stránka služby App Service na webu Azure Portal](media/quickstart-dotnetcore/portal-app-overview.png)

Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: ASP.NET Core aplikace s SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace ASP.NET Core](configure-language-dotnetcore.md)

::: zone-end