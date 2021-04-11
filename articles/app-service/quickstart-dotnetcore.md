---
title: 'Rychlý Start: Vytvoření aplikace ASP.NET Core v C#'
description: Naučte se spouštět webové aplikace v Azure App Service nasazením první aplikace ASP.NET Core.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 77e0768a617ef79ab8510f88bfdcd41d5647f9bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701633"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Rychlý Start: Vytvoření webové aplikace v ASP.NET Core v Azure

::: zone pivot="platform-windows"  

V tomto rychlém startu se dozvíte, jak vytvořit a nasadit první webovou aplikaci ASP.NET Core do [Azure App Service](overview.md). App Service podporuje aplikace .NET 5,0.

Až budete hotovi, budete mít skupinu prostředků Azure, která se skládá z App Service plánu hostování a App Service s nasazenou webovou aplikací.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/dotnet/).
- Nainstalujte <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> s úlohou **vývoje ASP.NET a webu** .

  Pokud jste již nainstalovali Visual Studio 2019:

  - Nainstalujte nejnovější aktualizace v aplikaci Visual Studio tak, že vyberete **nápovědu** vyhledat  >  **aktualizace**. Nejnovější aktualizace obsahují sadu .NET 5,0 SDK.
  - Přidejte úlohy tak, že vyberete **nástroje**  >  **získat nástroje a funkce**.


## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

Pomocí následujících kroků vytvořte webovou aplikaci ASP.NET Core v aplikaci Visual Studio:

# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Otevřete Visual Studio a vyberte **vytvořit nový projekt**.

1. V části **vytvořit nový projekt** vyberte **ASP.NET Core webová aplikace** a ověřte, že je **jazyk C#** uveden v jazycích pro tuto volbu, a pak vyberte **Další**.

1. V **konfiguraci nového projektu** zadejte název projektu webové aplikace *MyFirstAzureWebApp* a vyberte **vytvořit**.

   ![Konfigurace projektu webové aplikace](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. Do Azure můžete nasadit jakýkoli typ ASP.NET Core webové aplikace, ale pro účely tohoto rychlého startu vyberte šablonu **webové aplikace** . Ujistěte se, že je **ověřování** nastaveno na **bez ověřování** a že není vybrána žádná jiná možnost. Potom vyberte **Vytvořit**.

   ![Vytvoření nové ASP.NET Core webové aplikace](./media/quickstart-dotnetcore/create-aspnet-core-web-app.png) 
   
1. V nabídce sady Visual Studio vyberte **ladit**  >  **Spustit bez ladění** , aby se vaše webová aplikace spouštěla místně.

   ![Webová aplikace spuštěná místně](./media/quickstart-dotnetcore/web-app-running-locally.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

1. Otevřete Visual Studio a vyberte **vytvořit nový projekt**.

1. V části **vytvořit nový projekt** vyberte **ASP.NET Core webová aplikace** a ověřte, že je **jazyk C#** uveden v jazycích pro tuto volbu, a pak vyberte **Další**.

1. V **konfiguraci nového projektu** zadejte název projektu webové aplikace *MyFirstAzureWebApp* a vyberte **vytvořit**.

   ![Konfigurace projektu webové aplikace](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. V případě aplikace .NET 5,0 vyberte v rozevíracím seznamu položku **ASP.NET Core 5,0** .

1. Do Azure můžete nasadit jakýkoli typ ASP.NET Core webové aplikace, ale pro účely tohoto rychlého startu vyberte šablonu **ASP.NET Core webové aplikace** . Ujistěte se, že je **ověřování** nastaveno na **bez ověřování** a že není vybrána žádná jiná možnost. Potom vyberte **Vytvořit**.

   ![Vytvoření nové ASP.NET Core webové aplikace](./media/quickstart-dotnetcore/create-aspnet-core-web-app-5.png) 
   
1. V nabídce sady Visual Studio vyberte **ladit**  >  **Spustit bez ladění** , aby se vaše webová aplikace spouštěla místně.

   ![Webová aplikace spuštěná místně](./media/quickstart-dotnetcore/web-app-running-locally.png)

---

## <a name="publish-your-web-app"></a>Publikování webové aplikace

Pokud chcete publikovat webovou aplikaci, musíte nejdřív vytvořit a nakonfigurovat nové App Service, do které můžete aplikaci publikovat. 

Jako součást nastavení App Service vytvoříte:

- Nová [Skupina prostředků](../azure-resource-manager/management/overview.md#terminology) , která bude obsahovat všechny prostředky Azure pro službu.
- Nový [plán hostování](./overview-hosting-plans.md) , který určuje umístění, velikost a funkce webové serverové farmy, která je hostitelem vaší aplikace.

Při vytváření App Service a publikování webové aplikace postupujte podle těchto kroků:

1. V **Průzkumník řešení** klikněte pravým tlačítkem na projekt **MyFirstAzureWebApp** a vyberte **publikovat**. 

1. V nabídce **publikovat** vyberte **Azure** a klikněte na **Další**.

1. Vaše možnosti závisí na tom, jestli už jste přihlášení k Azure, a jestli máte účet sady Visual Studio propojený s účtem Azure. Vyberte buď **Přidat účet** , nebo se **přihlaste** , abyste se mohli přihlásit k předplatnému Azure. Pokud jste už přihlášení, vyberte účet, který chcete.

   ![Přihlášení k Azure](./media/quickstart-dotnetcore/sign-in-azure-vs2019.png)

1. Napravo od **instancí App Service** klikněte na **+** .

   ![Nová aplikace App Service](./media/quickstart-dotnetcore/publish-new-app-service.png)

1. U **předplatného** přijměte uvedené předplatné nebo v rozevíracím seznamu vyberte nový.

1. V případě **skupiny prostředků** vyberte **Nový**. Do **nového názvu skupiny prostředků** zadejte *MyResourceGroup* a vyberte **OK**. 

1. V případě **plánu hostování** vyberte možnost **Nový**. 

1. V dialogovém okně **plán hostování: vytvořit nové** zadejte hodnoty uvedené v následující tabulce:

   | Nastavení  | Navrhovaná hodnota | Description |
   | -------- | --------------- | ----------- |
   | **Plán hostování**  | *myFirstAzureWebAppPlan* | Název plánu služby App Service. |
   | **Umístění**      | *Západní Evropa* | Datacentrum, které je hostitelem webové aplikace. |
   | **Velikost**          | *Free* | [Cenová úroveň](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) určuje funkce hostování. |
   
   ![Vytvořit nový plán hostování](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. Do pole **název** zadejte jedinečný název aplikace, který bude obsahovat pouze platné znaky `a-z` :,, `A-Z` `0-9` a `-` . Můžete přijmout automaticky generovaný jedinečný název. Adresa URL webové aplikace je `http://<app-name>.azurewebsites.net`, kde `<app-name>` je název vaší aplikace.

2. Vyberte **vytvořit** a vytvořte prostředky Azure.

   ![Vytvoření prostředků aplikace](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

   Po dokončení průvodce se prostředky Azure vytvoří za vás a budete připraveni ho publikovat.

3. Kliknutím na tlačítko **Dokončit** zavřete průvodce.

1. Na stránce **publikovat** klikněte na **publikovat**. Visual Studio sestaví, zabalí a publikuje aplikaci do Azure a pak aplikaci spustí ve výchozím prohlížeči.

   ![Publikovaná webová aplikace v ASP.NET běžící v Azure](./media/quickstart-dotnetcore/web-app-running-live.png)

**Blahopřejeme!** Vaše webová aplikace v ASP.NET Core běží živě v Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Aktualizace a opětovné nasazení aplikace

Pomocí těchto kroků aktualizujete a znovu nasadíte webovou aplikaci:

1. V **Průzkumník řešení** v rámci projektu otevřete **stránky**  >  **index. cshtml**.

1. Celou značku nahraďte `<div>` následujícím kódem:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Opětovné nasazení do služby Azure provedete tak, že v **Průzkumníku řešení** kliknete pravým tlačítkem na projekt **myFirstAzureWebApp** a vyberete **Publikovat**.

1. Na stránce **publikovat** Souhrn vyberte **publikovat**.

   <!-- ![Publish update to web app](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png) -->

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
[App Service v systému Linux](overview.md#app-service-on-linux) poskytuje vysoce škálovatelnou službu s automatickými opravami pro hostování webů pomocí operačního systému Linux. V tomto rychlém startu se dozvíte, jak vytvořit aplikaci [.NET Core](/aspnet/core/) a nasadit ji do hostovaného App Service se systémem Linux pomocí rozhraní příkazového [řádku Azure CLI](/cli/azure/get-started-with-azure-cli).

![Ukázková aplikace spuštěná v Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Postup v tomto článku můžete použít v případě počítačů se systémem Mac, Windows nebo Linux.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-initial-environment"></a>Nastavení počátečního prostředí

# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Nainstalujte si nejnovější verzi sady .NET Core 3,1 SDK</a>.
* <a href="/cli/azure/install-azure-cli" target="_blank">Instalace nejnovější verze Azure CLI</a>.

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">Nainstalujte nejnovější sadu .net 5,0 SDK</a>.
* <a href="/cli/azure/install-azure-cli" target="_blank">Instalace nejnovější verze Azure CLI</a>.

---

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

```bash
dotnet run
```

Otevřete webový prohlížeč a přejděte do aplikace na adrese `http://localhost:5000`.

Na stránce se zobrazí zpráva **Hello World** z ukázkové aplikace.

![Testování pomocí prohlížeče](media/quickstart-dotnetcore/dotnet-browse-local.png)

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="sign-into-azure"></a>Přihlášení do Azure
V okně terminálu se do Azure přihlaste pomocí následujícího příkazu:

```azurecli
az login
```

## <a name="deploy-the-app"></a>Nasazení aplikace

Nasaďte kód do místní složky (*hellodotnetcore*) pomocí `az webapp up` příkazu:

```azurecli
az webapp up --sku F1 --name <app-name>
```

- Pokud `az` příkaz není rozpoznaný, ujistěte se, že máte nainstalované rozhraní příkazového řádku Azure, jak je popsané v tématu [Nastavení počátečního prostředí](#set-up-your-initial-environment).
- Nahraďte `<app-name>` názvem, který je jedinečný v rámci všech Azure (*platné znaky jsou `a-z` , `0-9` a `-`*). Dobrým vzorem je použití kombinace názvu vaší společnosti a identifikátoru aplikace.
- `--sku F1`Argument vytvoří webovou aplikaci na cenové úrovni Free. Vynechejte tento argument pro použití rychlejší úrovně Premium, což stojí za hodinu.
- Volitelně můžete zahrnout argument `--location <location-name>` , kde `<location-name>` je dostupná oblast Azure. Seznam povolených oblastí pro váš účet Azure můžete načíst spuštěním [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) příkazu.

Dokončení příkazu může trvat několik minut. Při spuštění poskytuje zpráva o vytváření skupiny prostředků, App Service plánování a hostování aplikace, konfiguraci protokolování a následném nasazení souboru ZIP. Pak mu zobrazí zprávu "aplikaci můžete spustit v http:// &lt; App-name &gt; . azurewebsites.NET", což je adresa URL aplikace v Azure.

# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

![Příklad výstupu příkazu AZ WebApp up](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

<!-- Deploy the code in your local folder (*hellodotnetcore*) using the `az webapp up` command:

```azurecli
az webapp up --sku B1 --name <app-name> --os-type linux
```

- If the `az` command isn't recognized, be sure you have the Azure CLI installed as described in [Set up your initial environment](#set-up-your-initial-environment).
- Replace `<app-name>` with a name that's unique across all of Azure (*valid characters are `a-z`, `0-9`, and `-`*). A good pattern is to use a combination of your company name and an app identifier.
- The `--sku B1` argument creates the web app in the Basic pricing tier, which incurs an hourly cost. Omit this argument to use a faster premium tier, which costs more.
- You can optionally include the argument `--location <location-name>` where `<location-name>` is an available Azure region. You can retrieve a list of allowable regions for your Azure account by running the [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) command.

The command may take a few minutes to complete. While running, it provides messages about creating the resource group, the App Service plan and hosting app, configuring logging, then performing ZIP deployment. It then gives the message, "You can launch the app at http://&lt;app-name&gt;.azurewebsites.net", which is the app's URL on Azure. -->

![Příklad výstupu příkazu AZ WebApp up](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Přechod do aplikace

V prohlížeči zadejte adresu nasazené aplikace.

```bash
http://<app_name>.azurewebsites.net
```

Vzorový kód .NET Core je spuštěný v App Service v systému Linux s předdefinovaným obrázkem.

![Ukázková aplikace spuštěná v Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Blahopřejeme!** Nasadili jste svoji první aplikaci .NET Core do služby App Service v Linuxu.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="update-and-redeploy-the-code&quot;></a>Aktualizace a opětovné nasazení kódu

V místním adresáři otevřete soubor _Startup.cs_. Proveďte malou změnu textu ve volání metody `context.Response.WriteAsync`:

```csharp
await context.Response.WriteAsync(&quot;Hello Azure!");
```

Uložte změny a pak znovu nasaďte aplikaci pomocí `az webapp up` příkazu:

```azurecli
az webapp up --os-type linux
```

Tento příkaz používá hodnoty, které jsou uložené místně v souboru *. Azure/config* , včetně názvu aplikace, skupiny prostředků a plánu App Service.

Po dokončení nasazení přepněte zpátky do okna prohlížeče, které se otevřelo v kroku **přechod do aplikace** , a stiskněte tlačítko Aktualizovat.

![Aktualizovaná ukázková aplikace spuštěná ve službě Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="manage-your-new-azure-app"></a>Správa nové aplikace Azure

Chcete-li spravovat aplikaci, kterou jste vytvořili, otevřete <a href="https://portal.azure.com" target="_blank">Azure Portal</a> .

V nabídce vlevo klikněte na **App Services** a pak klikněte na název aplikace Azure.

:::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="Snímek obrazovky stránky App Services zobrazující ukázkovou aplikaci Azure":::

Zobrazí se stránka s přehledem vaší aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. 

![Stránka služby App Service na webu Azure Portal](media/quickstart-dotnetcore/portal-app-overview-up.png)

Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: ASP.NET Core aplikace s SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace ASP.NET Core](configure-language-dotnetcore.md)

::: zone-end
