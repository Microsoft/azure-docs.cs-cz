---
title: 'Rychlý Start: Vytvoření aplikace ASP.NET Core v C#'
description: Naučte se spouštět webové aplikace v Azure App Service nasazením první aplikace ASP.NET Core.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e6675dbc4381a3a21e9daa651948af17f1d70d7d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748375"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Rychlý Start: Vytvoření webové aplikace v ASP.NET Core v Azure

::: zone pivot="platform-windows"  

V tomto rychlém startu se dozvíte, jak vytvořit a nasadit první webovou aplikaci ASP.NET Core do <abbr title="Služba založená na protokolu HTTP pro hostování webových aplikací, rozhraní REST API a mobilní back-endové aplikace.">Azure App Service</abbr>. App Service podporuje aplikace .NET 5,0.

Až budete hotovi, budete mít Azure <abbr title="Logický kontejner pro související prostředky Azure, které můžete spravovat jako jednotku.">skupina prostředků</abbr>, skládající se z <abbr title="Plán, který určuje umístění, velikost a funkce webové serverové farmy, která je hostitelem vaší aplikace.">Plán služby App Service</abbr> a <abbr title="Reprezentace vaší webové aplikace, která obsahuje kód vaší aplikace, názvy hostitelů DNS, certifikáty a související prostředky.">Aplikace služby App Service</abbr> s nasazenou ukázkovou ASP.NET Core aplikací.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Příprava prostředí

- **Získat účet Azure** s aktivním <abbr title="Základní organizační struktura, ve které spravujete prostředky v Azure, obvykle přidružené k jednotlivcům nebo oddělením v rámci organizace.">předplatné</abbr>. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/dotnet/).
- **Nainstalujte <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>** s úlohou **vývoje ASP.NET a webu** .

<details>
<summary>Již máte Visual Studio 2019?</summary>
  Pokud jste již nainstalovali Visual Studio 2019:

  - **Nainstalujte nejnovější aktualizace** v aplikaci Visual Studio tak, že vyberete **nápovědu** vyhledat  >  **aktualizace**. Nejnovější aktualizace obsahují sadu .NET 5,0 SDK.
  - **Přidejte úlohy** tak, že vyberete **nástroje**  >  **získat nástroje a funkce**.
</details>

<hr/> 

## <a name="2-create-an-aspnet-core-web-app"></a>2. vytvoření webové aplikace v ASP.NET Core

1. Otevřete Visual Studio a vyberte **vytvořit nový projekt**.

1. V části **vytvořit nový projekt** vyberte **ASP.NET Core webová aplikace** a ověřte, že je **jazyk C#** uveden v jazycích pro tuto volbu, a pak vyberte **Další**.

1. V **konfiguraci nového projektu** zadejte název projektu webové aplikace *MyFirstAzureWebApp* a vyberte **vytvořit**.

   ![Konfigurace projektu webové aplikace](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. V případě aplikace .NET 5,0 vyberte v rozevíracím seznamu položku **ASP.NET Core 5,0** . Jinak použijte výchozí hodnotu.

1. Do Azure můžete nasadit jakýkoli typ ASP.NET Core webové aplikace, ale pro účely tohoto rychlého startu vyberte šablonu **ASP.NET Core webové aplikace** . Ujistěte se, že je **ověřování** nastaveno na **bez ověřování** a že není vybrána žádná jiná možnost. Potom vyberte **Vytvořit**.

   ![Vytvoření nové ASP.NET Core webové aplikace](./media/quickstart-dotnetcore/create-aspnet-core-web-app-5.png) 
   
1. V nabídce sady Visual Studio vyberte **ladit**  >  **Spustit bez ladění** , aby se vaše webová aplikace spouštěla místně.

   ![Webová aplikace spuštěná místně](./media/quickstart-dotnetcore/web-app-running-locally.png)

<hr/> 

## <a name="3-publish-your-web-app"></a>3. publikování webové aplikace

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

   | Nastavení  | Navrhovaná hodnota |
   | -------- | --------------- |
   | **Plán hostování**  | *myFirstAzureWebAppPlan* |
   | **Umístění**      | *Západní Evropa* |
   | **Velikost**          | *Free* |
   
   ![Vytvořit nový plán hostování](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. Do **název** zadejte jedinečný název aplikace.

    <details>
        <summary>Které znaky můžu použít?</summary>
        Platné znaky jsou a-z, A-Z, 0-9 a-. Můžete přijmout automaticky generovaný jedinečný název. Adresa URL webové aplikace je http:// <code>&lt;app-name&gt;.azurewebsites.net</code> , kde <code>&lt;app-name&gt;</code> je název vaší aplikace.
    </details>

1. Vyberte **vytvořit** a vytvořte prostředky Azure. 

   ![Vytvoření prostředků aplikace](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

1. Počkejte, až průvodce dokončí vytváření prostředků Azure. Kliknutím na tlačítko **Dokončit** zavřete průvodce.

1. Na stránce **publikovat** klikněte na **publikovat** a nasaďte svůj projekt. 

    <details>
        <summary>Co je Visual Studio?</summary>
        Visual Studio sestaví, zabalí a publikuje aplikaci do Azure a pak aplikaci spustí ve výchozím prohlížeči.
    </details>

   ![Publikovaná webová aplikace v ASP.NET běžící v Azure](./media/quickstart-dotnetcore/web-app-running-live.png)

<hr/> 

## <a name="4-update-the-app-and-redeploy"></a>4. aktualizujte aplikaci a znovu nasaďte

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

<hr/> 

## <a name="5-manage-the-azure-app"></a>5. Správa aplikace Azure

1. Otevřete [Azure Portal](https://portal.azure.com)a vyhledejte a vyberte **App Services**.

    ![Vyberte App Services](./media/quickstart-dotnetcore/app-services.png)
    
1. Na stránce **App Services** vyberte název vaší webové aplikace.

    :::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Snímek obrazovky stránky App Services s vybranou ukázkovou webovou aplikací":::

1. Stránka **Přehled** pro vaši webovou aplikaci obsahuje možnosti pro základní správu, jako je procházení, zastavení, spuštění, restartování a odstranění. V nabídce vlevo najdete další stránky pro konfiguraci vaší aplikace.

    ![App Service v Azure Portal](./media/quickstart-dotnetcore/web-app-overview-page.png)
    
<hr/> 

## <a name="6-clean-up-resources"></a>6. vyčištění prostředků

1. V nabídce Azure Portal nebo na **domovské** stránce vyberte **skupiny prostředků**. Pak na stránce **skupiny prostředků** vyberte **myResourceGroup**.

1. Na stránce **myResourceGroup** se ujistěte, že uvedené prostředky jsou ty, které chcete odstranit.

1. Vyberte **Odstranit skupinu prostředků**, do textového pole zadejte **myResourceGroup** a pak vyberte **Odstranit**.

<hr/> 

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak vytvořit aplikaci .NET Core a jak ji připojit k SQL Database:

- [ASP.NET Core s SQL Database](tutorial-dotnetcore-sqldb-app.md)
- [Konfigurace aplikace ASP.NET Core](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
V tomto rychlém startu se dozvíte, jak vytvořit aplikaci [.NET Core](/aspnet/core/) na <abbr title="App Service v systému Linux poskytuje vysoce škálovatelnou službu s automatickými opravami pro hostování webů pomocí operačního systému Linux.">App Service v Linuxu</abbr>. Vytvoříte aplikaci pomocí rozhraní příkazového [řádku Azure CLI](/cli/azure/get-started-with-azure-cli)a pomocí Gitu nasadíte kód .NET Core do aplikace.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Příprava prostředí

- **Získejte účet Azure** s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/dotnet/).
- **Nainstalujte** nejnovější sadu <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">sdk .NET Core 3,1</a> nebo <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">.NET 5,0 SDK</a>.
- **<a href="/cli/azure/install-azure-cli" target="_blank">Nainstalujte nejnovější rozhraní příkazového řádku Azure</a>**.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="2-create-the-app-locally"></a>2. Vytvoření aplikace v místním prostředí

1. Spusťte příkaz `mkdir hellodotnetcore` k vytvoření adresáře.

    ```bash
    mkdir hellodotnetcore
    ```

1. Spusťte `cd hellodotnetcore` pro změnu adresáře. 

    ```bash
    cd hellodotnetcore
    ```

1. Spusťte `dotnet new web` pro vytvoření nové aplikace .NET Core.

    ```bash
    dotnet new web
    ```

<hr/> 

## <a name="3-run-the-app-locally"></a>3. Spusťte aplikaci místně.

1. Spusťte `dotnet run` , abyste viděli, jak vypadá, když ho nasadíte do Azure.

    ```bash
    dotnet run
    ```
    
1. **Otevřete webový prohlížeč** a přejděte do aplikace na adrese `http://localhost:5000` .

![Testování pomocí prohlížeče](media/quickstart-dotnetcore/dotnet-browse-local.png)

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="4-sign-into-azure"></a>4. Přihlaste se k Azure

Spusťte `az login` , abyste se přihlásili do Azure.

```azurecli
az login
```

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="5-deploy-the-app"></a>5. nasazení aplikace

1. **Spustit příkaz** `az webapp up` v místní složce. **Nahraďte** <název aplikace> globálně jedinečným názvem.

    ```azurecli
    az webapp up --sku F1 --name <app-name> --os-type linux
    ```
    
    <details>
    <summary>Řešení potíží</summary>
    <ul>
    <li>Pokud <code>az</code> příkaz není rozpoznaný, ujistěte se, že máte nainstalované rozhraní příkazového řádku Azure, jak je popsané v tématu <a href="#1-prepare-your-environment">Příprava prostředí</a>.</li>
    <li>Nahraďte <code>&lt;app-name&gt;</code> názvem, který je jedinečný v rámci všech Azure ( <em> platné znaky jsou <code>a-z</code> , <code>0-9</code> a <code>-</code> </em> ). Dobrým vzorem je použití kombinace názvu vaší společnosti a identifikátoru aplikace.</li>
    <li><code>--sku F1</code>Argument vytvoří webovou aplikaci na cenové úrovni Free. Vynechejte tento argument pro použití rychlejší úrovně Premium, což stojí za hodinu.</li>
    <li>Volitelně můžete zahrnout argument <code>--location &lt;location-name&gt;</code> , kde <code>&lt;location-name&gt;</code> je dostupná oblast Azure. Seznam povolených oblastí pro váš účet Azure můžete načíst spuštěním <a href="/cli/azure/appservice#az-appservice-list-locations"> <code>az account list-locations</code> </a> příkazu.</li>
    </ul>
    </details>
    
1. Počkejte, až se příkaz dokončí. Může to trvat několik minut a končí na "aplikaci můžete spustit v http:// &lt; App-name &gt; . azurewebsites.NET".

    <details>
    <summary>Co dělat <code>az webapp up</code> ?</summary>
    <p>Příkaz <code>az webapp up</code> provádí tyto akce:</p>
    <ul>
    <li>Vytvoření výchozí skupiny prostředků</li>
    <li>Vytvořte výchozí plán App Service.</li>
    <li><a href="/cli/azure/webapp?view=azure-cli-latest#az-webapp-create">Vytvoří aplikaci App Service</a> se zadaným názvem.</li>
    <li>Soubory <a href="/azure/app-service/deploy-zip">zip nasadí</a> z aktuálního pracovního adresáře do aplikace.</li>
    <li>Při spuštění poskytuje zpráva o vytváření prostředků, protokolování a nasazení souboru ZIP.</li>
    </ul>
    </details>
    
# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

![Příklad výstupu příkazu AZ WebApp up](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

![Příklad výstupu příkazu AZ WebApp up](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="6-browse-to-the-app"></a>6. Přejděte do aplikace.

**Vyhledejte nasazenou aplikaci** pomocí webového prohlížeče.

```bash
http://<app_name>.azurewebsites.net
```

![Ukázková aplikace spuštěná v Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="7-update-and-redeploy-the-code"></a>7. aktualizujte a znovu nasaďte kód.

1. **Otevřete soubor _Startup.cs_** v místním adresáři. 

1. **Udělejte malou změnu** textu ve volání metody `context.Response.WriteAsync` .

    ```csharp
    await context.Response.WriteAsync("Hello Azure!");
    ```
    
1. **Uložte změny**.

1. **Spustit příkaz** `az webapp up` opětovné nasazení:

```azurecli
az webapp up --os-type linux
```

<details>
<summary>Co <code>az webapp up</code> Tento čas dělá?</summary>
Při prvním spuštění příkazu byl uložen název aplikace, skupina prostředků a plán App Service v souboru <i>. Azure/config</i> z kořenového adresáře projektu. Když ho znovu spustíte z kořenového adresáře projektu, používá hodnoty uložené v <i>souboru. Azure/config</i>, zjistí, že App Service prostředky již existují a provede nasazení souboru ZIP znovu.
</details>

1. Po dokončení nasazení klikněte na tlačítko **aktualizovat** v okně prohlížeče, které už bylo otevřeno.

![Aktualizovaná ukázková aplikace spuštěná ve službě Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="8-manage-your-new-azure-app"></a>8. Správa nové aplikace Azure

1. Přejděte na <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

1. V nabídce vlevo klikněte na **App Services** a pak klikněte na název aplikace Azure.

    :::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="Snímek obrazovky stránky App Services zobrazující ukázkovou aplikaci Azure":::

1. Na stránce Přehled můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace. 

![Stránka služby App Service na webu Azure Portal](media/quickstart-dotnetcore/portal-app-overview-up.png)

<hr/> 

## <a name="9-clean-up-resources"></a>9. vyčištění prostředků

**Spustit příkaz** `az group delete --name myResourceGroup` odstranění skupiny prostředků.

```azurecli-interactive
az group delete --name myResourceGroup
```

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="next-steps"></a>Další kroky

- [Kurz: ASP.NET Core aplikace s SQL Database](tutorial-dotnetcore-sqldb-app.md)
- [Konfigurace aplikace ASP.NET Core](configure-language-dotnetcore.md)

::: zone-end
