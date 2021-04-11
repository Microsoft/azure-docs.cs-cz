---
title: 'Rychlý Start: nasazení webové aplikace v ASP.NET'
description: Naučte se spouštět webové aplikace v Azure App Service nasazením první aplikace v ASP.NET.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 03/30/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-ide
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 7f538f5accb533b01c5ea685e424c70bfeb44f00
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058204"
---
<!-- NOTES:

I'm a .NET developer who wants to deploy my web app to App Service. I may develop apps with
Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET SDK/CLI. This article
should be able to guide .NET devs, whether they're app is .NET Core, .NET, or .NET Framework.

As a .NET developer, when choosing an IDE and .NET TFM - you map to various OS requirements.
For example, if you choose Visual Studio - you're developing the app on Windows, but you can still
target cross-platform with .NET Core 3.1 or .NET 5.0.

| .NET / IDE         | Visual Studio | Visual Studio for Mac | Visual Studio Code | Command line   |
|--------------------|---------------|-----------------------|--------------------|----------------|
| .NET Core 3.1      | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET 5.0           | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET Framework 4.8 | Windows       | N/A                   | Windows            | Windows        |

-->

# <a name="quickstart-deploy-an-aspnet-web-app"></a>Rychlý Start: nasazení webové aplikace v ASP.NET

V tomto rychlém startu se dozvíte, jak vytvořit a nasadit první webovou aplikaci v ASP.NET do [Azure App Service](overview.md). App Service podporuje různé verze aplikací .NET a poskytuje vysoce škálovatelnou službu s automatickými opravami pro hostování webů. Webové aplikace ASP.NET jsou různé platformy a můžou být hostované v systému Linux nebo Windows. Až budete hotovi, budete mít skupinu prostředků Azure, která se skládá z App Service plánu hostování a App Service s nasazenou webovou aplikací.

> [!TIP]
> .NET Core 3,1 je aktuální Dlouhodobá podpora (LTS) verze .NET. Další informace najdete v tématu [zásady podpory rozhraní .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

## <a name="prerequisites"></a>Požadavky

:::zone target="docs" pivot="development-environment-vs"

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2019</a> s úlohou **vývoje ASP.NET a webu** .

    Pokud jste již nainstalovali Visual Studio 2019:

    - Nainstalujte nejnovější aktualizace v aplikaci Visual Studio tak, že vyberete **nápovědu** vyhledat  >  **aktualizace**.
    - Přidejte úlohy tak, že vyberete **nástroje**  >  **získat nástroje a funkce**.

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio Code</a>.
- Rozšíření <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack" target="_blank">nástrojů Azure</a> .

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Nainstalujte si nejnovější verzi sady .NET Core 3,1 SDK. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Nainstalujte nejnovější sadu .NET 5,0 SDK. </a>

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> Nainstalujte sadu .NET Framework 4,8 Developer Pack. </a>

> [!NOTE]
> Visual Studio Code je více platforem, .NET Framework ale není. Pokud vyvíjíte .NET Framework aplikace pomocí Visual Studio Code, zvažte použití počítače s Windows k uspokojení závislostí sestavení.

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/dotnet).
- Rozhraní příkazového <a href="/cli/azure/install-azure-cli" target="_blank">řádku Azure</a>
- Sada .NET SDK (zahrnuje modul runtime a rozhraní příkazového řádku).

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Nainstalujte si nejnovější verzi sady .NET Core 3,1 SDK. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Nainstalujte nejnovější sadu .NET 5,0 SDK. </a>

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Nainstalujte nejnovější sadu .net 5,0 SDK. </a> a <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> sadu .NET Framework 4,8 Developer Pack. </a>

> [!NOTE]
> [Rozhraní .NET CLI](/dotnet/core/tools) je ale pro různé platformy, ale .NET Framework ne. Pokud vyvíjíte .NET Framework aplikace pomocí rozhraní .NET CLI, zvažte použití počítače s Windows pro splnění závislostí sestavení.

---

:::zone-end

## <a name="create-an-aspnet-web-app"></a>Vytvoření webové aplikace ASP.NET

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Otevřete Visual Studio a pak vyberte **vytvořit nový projekt**.
1. V části **vytvořit nový projekt** vyhledejte a zvolte možnost **webová aplikace v ASP.NET** a pak vyberte **Další**.
1. V části **Konfigurovat nový projekt** pojmenujte aplikaci _MyFirstAzureWebApp_ a pak vyberte **Další**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Konfigurace webové aplikace ASP.NET Core 3,1" border="true":::

1. Vyberte **.NET Core 3,1 (dlouhodobá podpora)**.
1. Ujistěte se, že je **typ ověřování** nastavený na **žádná**. Vyberte **Vytvořit**.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-netcoreapp31.png" alt-text="Visual Studio – pro typ ověřování vyberte .NET Core 3,1 a None." border="true":::

1. V nabídce sady Visual Studio vyberte **ladit**  >  **Spustit bez ladění** , aby se webová aplikace spouštěla místně.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio – .NET Core 3,1 – místní procházení" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

1. Otevřete Visual Studio a pak vyberte **vytvořit nový projekt**.
1. V části **vytvořit nový projekt** vyhledejte a zvolte možnost **webová aplikace v ASP.NET** a pak vyberte **Další**.
1. V části **Konfigurovat nový projekt** pojmenujte aplikaci _MyFirstAzureWebApp_ a pak vyberte **Další**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Visual Studio – konfigurace webové aplikace v ASP.NET 5,0" border="true":::

1. Vyberte **.NET Core 5,0 (aktuální)**.
1. Ujistěte se, že je **typ ověřování** nastavený na **žádná**. Vyberte **Vytvořit**.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-net50.png" alt-text="Visual Studio – Další informace při výběru .NET Core 5,0." border="true":::

1. V nabídce sady Visual Studio vyberte **ladit**  >  **Spustit bez ladění** , aby se webová aplikace spouštěla místně.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio – ASP.NET Core 5,0 běží místně." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

1. Otevřete Visual Studio a pak vyberte **vytvořit nový projekt**.
1. V v možnosti **vytvořit nový projekt** vyhledejte a zvolte **Webová aplikace ASP.NET (.NET Framework)** a pak vyberte **Další**.
1. V části **Konfigurovat nový projekt** pojmenujte aplikaci _MyFirstAzureWebApp_ a pak vyberte **vytvořit**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-netframework48.png" alt-text="Visual Studio – konfigurace webové aplikace ASP.NET Framework 4,8" border="true":::

1. Vyberte šablonu **MVC** .
1. Ujistěte se, že je **ověřování** nastaveno na **bez ověřování**. Vyberte **Vytvořit**.

   :::image type="content" source="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" alt-text="Visual Studio – vyberte šablonu MVC." border="true":::

1. V nabídce sady Visual Studio vyberte **ladit**  >  **Spustit bez ladění** , aby se webová aplikace spouštěla místně.

   :::image type="content" source="media/quickstart-dotnet/vs-local-webapp-netframework48.png" alt-text="Visual Studio – ASP.NET Framework 4,8 běží místně." lightbox="media/quickstart-dotnet/vs-local-webapp-netframework48.png" border="true":::

---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Vytvořte novou složku s názvem _MyFirstAzureWebApp_ a otevřete ji v Visual Studio Code. Otevřete okno <a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">terminálu</a> a vytvořte novou webovou aplikaci .NET pomocí [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) příkazu.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -f netcoreapp3.1
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -f net5.0
```

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp --target-framework-override net48
```

> [!IMPORTANT]
> `--target-framework-override`Příznakem je nahrazující textový moniker cílového rozhraní (TFM) pro projekt a neposkytuje *žádné záruky* , že podpůrná šablona existuje nebo je zkompilována. Můžete vytvářet a spouštět .NET Framework aplikace jenom v systému Windows.

---

Z **terminálu** v Visual Studio Code spusťte aplikaci místně pomocí [`dotnet run`](/dotnet/core/tools/dotnet-run) příkazu.

```dotnetcli
dotnet run
```

Otevřete webový prohlížeč a přejděte do aplikace na adrese `https://localhost:5001`.


### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Na stránce se zobrazí webová aplikace v šabloně ASP.NET Core 3,1.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – spusťte rozhraní .NET Core 3,1 v prohlížeči místně." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Na stránce se zobrazí webová aplikace v šabloně ASP.NET Core 5,0.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – spusťte rozhraní .NET 5,0 v prohlížeči místně." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

Na stránce se zobrazí webová aplikace ASP.NET Framework 4,8.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code – spusťte rozhraní .NET 4,8 v prohlížeči místně." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Otevřete okno terminálu na svém počítači k pracovnímu adresáři. Pomocí příkazu vytvořte novou webovou aplikaci .NET [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) a pak změňte adresáře na nově vytvořenou aplikaci.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f netcoreapp3.1 && cd MyFirstAzureWebApp
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f net5.0 && cd MyFirstAzureWebApp
```

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp --target-framework-override net48 && cd MyFirstAzureWebApp
```

> [!IMPORTANT]
> `--target-framework-override`Příznakem je nahrazující textový moniker cílového rozhraní (TFM) pro projekt a neposkytuje *žádné záruky* , že podpůrná šablona existuje nebo je zkompilována. V systému Windows můžete vytvářet pouze aplikace .NET Framework.

---

Ze stejné relace Terminálové služby spusťte aplikaci místně pomocí [`dotnet run`](/dotnet/core/tools/dotnet-run) příkazu.

```dotnetcli
dotnet run
```

Otevřete webový prohlížeč a přejděte do aplikace na adrese `https://localhost:5001`.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Na stránce se zobrazí webová aplikace v šabloně ASP.NET Core 3,1.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – ASP.NET Core 3,1 v místním prohlížeči." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Na stránce se zobrazí webová aplikace v šabloně ASP.NET Core 5,0.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – ASP.NET Core 5,0 v místním prohlížeči." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

Na stránce se zobrazí webová aplikace ASP.NET Framework 4,8.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code – ASP.NET Framework 4,8 v místním prohlížeči." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

## <a name="publish-your-web-app"></a>Publikování webové aplikace

Pokud chcete publikovat webovou aplikaci, musíte nejdřív vytvořit a nakonfigurovat nové App Service, do které můžete aplikaci publikovat.

Jako součást nastavení App Service vytvoříte:

- Nová [Skupina prostředků](../azure-resource-manager/management/overview.md#terminology) , která bude obsahovat všechny prostředky Azure pro službu.
- Nový [plán hostování](overview-hosting-plans.md) , který určuje umístění, velikost a funkce webové serverové farmy, která je hostitelem vaší aplikace.

Při vytváření App Service a publikování webové aplikace postupujte podle těchto kroků:

:::zone target="docs" pivot="development-environment-vs"

1. V **Průzkumník řešení** klikněte pravým tlačítkem na projekt **MyFirstAzureWebApp** a vyberte **publikovat**.
1. V **publikování** vyberte **Azure** a pak **Další**.

    :::image type="content" source="media/quickstart-dotnet/vs-publish-target-Azure.png" alt-text="Visual Studio – publikujte webovou aplikaci a cílovou službu Azure." border="true":::

1. Vaše možnosti závisí na tom, jestli už jste přihlášení k Azure, a jestli máte účet sady Visual Studio propojený s účtem Azure. Vyberte buď **Přidat účet** , nebo se **přihlaste** , abyste se mohli přihlásit k předplatnému Azure. Pokud jste už přihlášení, vyberte účet, který chcete.

    :::image type="content" source="media/quickstart-dotnetcore/sign-in-Azure-vs2019.png" border="true" alt-text="Visual Studio – vyberte přihlásit se k Azure dialog.":::

1. Vyberte **konkrétní cíl**, buď **Azure App Service (Linux)** nebo **Azure App Service (Windows)**.

    > [!IMPORTANT]
    > Při cílení na ASP.NET Framework 4,8 budete používat **Azure App Service (Windows)**.

1. Napravo od **instancí App Service** vyberte **+** .

    :::image type="content" source="media/quickstart-dotnetcore/publish-new-app-service.png" border="true" alt-text="Visual Studio – dialog nové aplikace App Service.":::

1. U **předplatného** přijměte uvedené předplatné nebo v rozevíracím seznamu vyberte nový.
1. V případě **skupiny prostředků** vyberte **Nový**. Do **nového názvu skupiny prostředků** zadejte *MyResourceGroup* a vyberte **OK**.
1. V případě **plánu hostování** vyberte možnost **Nový**.
1. V dialogovém okně **plán hostování: vytvořit nové** zadejte hodnoty uvedené v následující tabulce:

    | Nastavení          | Navrhovaná hodnota          | Popis                                                           |
    |------------------|--------------------------|-----------------------------------------------------------------------|
    | **Plán hostování** | *MyFirstAzureWebAppPlan* | Název plánu služby App Service.                                         |
    | **Umístění**     | *Západní Evropa*            | Datacentrum, které je hostitelem webové aplikace.                           |
    | **Velikost**         | *Free*                   | [Cenová úroveň][app-service-pricing-tier] určuje funkce hostování. |

    :::image type="content" source="media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png" border="true" alt-text="Vytvořit nový plán hostování":::

1. Do pole **název** zadejte jedinečný název aplikace, který bude obsahovat pouze platné znaky `a-z` :,, `A-Z` `0-9` a `-` . Můžete přijmout automaticky generovaný jedinečný název. Adresa URL webové aplikace je `http://<app-name>.azurewebsites.net`, kde `<app-name>` je název vaší aplikace.
1. Vyberte **vytvořit** a vytvořte prostředky Azure.

    :::image type="content" source="media/quickstart-dotnetcore/web-app-name-vs2019.png" border="true" alt-text="Visual Studio – dialogové okno vytvořit prostředky aplikace":::

   Po dokončení průvodce se prostředky Azure vytvoří za vás a budete připraveni ho publikovat.

1. Kliknutím na tlačítko **Dokončit** zavřete průvodce.
1. Na stránce **publikovat** vyberte **publikovat**. Visual Studio sestaví, zabalí a publikuje aplikaci do Azure a pak aplikaci spustí ve výchozím prohlížeči.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Na stránce se zobrazí webová aplikace ASP.NET Core 3,1.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio – ASP.NET Core 3,1 webové aplikace v Azure":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Na stránce se zobrazí webová aplikace ASP.NET Core 5,0.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio – ASP.NET Core 5,0 webové aplikace v Azure":::

    ### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

    Na stránce se zobrazí webová aplikace ASP.NET Framework 4,8.

    :::image type="content" source="media/quickstart-dotnet/vs-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio – ASP.NET Framework 4,8 Web App v Azure":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Nasazení webové aplikace pomocí rozšíření Visual Studio Azure Tools:

1. V Visual Studio Code otevřete [**paletu příkazů**](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette) <kbd>CTRL</kbd> + <kbd>SHIFT</kbd> + <kbd>+</kbd>.
1. Vyhledejte a vyberte "Azure App Service: nasadit do webové aplikace".
1. Odpovězte na následující výzvy:

    - Jako složku, kterou chcete nasadit, vyberte *MyFirstAzureWebApp* .
    - Po zobrazení výzvy vyberte možnost **Přidat konfiguraci** .
    - Pokud se zobrazí výzva, přihlaste se ke svému stávajícímu účtu Azure.

    :::image type="content" source="media/quickstart-dotnet/vscode-sign-in-to-Azure.png" alt-text="Visual Studio Code – Přihlaste se k Azure." border="true":::

    - Vyberte své **předplatné**.
    - Vyberte **vytvořit novou webovou aplikaci... Upřesnit**.
    - Pro **Zadejte globálně jedinečný název** použijte název, který je jedinečný v rámci všech Azure (*platné znaky jsou `a-z` , `0-9` a `-`*). Dobrým vzorem je použití kombinace názvu vaší společnosti a identifikátoru aplikace.
    - Vyberte **vytvořit novou skupinu prostředků** a zadejte název jako `myResourceGroup` .
    - Po zobrazení výzvy k **výběru zásobníku modulu runtime**:
      - V případě *.NET core 3,1* vyberte **.NET Core 3,1 (LTS)** .
      - V případě *.net 5,0* vyberte **.NET 5** .
      - V *.NET Framework 4,8* vyberte **ASP.NET v 4.8** .
    - Vyberte operační systém (Windows nebo Linux).
        - Pro *.NET Framework 4,8* se Windows vybere implicitně.
    - Vyberte **vytvořit nový plán App Service**, zadejte název a vyberte [cenovou úroveň][app-service-pricing-tier] **F1 zdarma** .
    - Pro prostředek Application Insights vyberte **Přeskočit** .
    - Vyberte umístění blízko vás.

1. Po dokončení publikování vyberte **Procházet web** v oznámení a po zobrazení výzvy vyberte **otevřít** .

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Na stránce se zobrazí webová aplikace ASP.NET Core 3,1.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Webová aplikace Visual Studio Code-ASP.NET Core 3,1 v Azure":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Na stránce se zobrazí webová aplikace ASP.NET Core 5,0.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Webová aplikace Visual Studio Code-ASP.NET Core 5,0 v Azure":::

    ### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

    Na stránce se zobrazí webová aplikace ASP.NET Framework 4,8.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Webová aplikace Visual Studio Code ASP.NET Framework 4,8 v Azure":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Nasaďte kód do místního adresáře *MyFirstAzureWebApp* pomocí [`az webapp up`](/cli/azure/webapp#az_webapp_up) příkazu:

```azurecli
az webapp up --sku F1 --name <app-name> --os-type <os>
```

- Pokud se `az` příkaz nerozpoznal, ujistěte se, že máte nainstalované rozhraní příkazového řádku Azure, jak je popsané v části [požadavky](#prerequisites).
- Nahraďte `<app-name>` názvem, který je jedinečný v rámci všech Azure (*platné znaky jsou `a-z` , `0-9` a `-`*). Dobrým vzorem je použití kombinace názvu vaší společnosti a identifikátoru aplikace.
- `--sku F1`Argument vytvoří webovou aplikaci na [cenové úrovni][app-service-pricing-tier] **Free** . Vynechejte tento argument pro použití rychlejší úrovně Premium, což stojí za hodinu.
- Nahraďte `<os>` buď `linux` nebo `windows` . Je nutné použít, `windows` když cílíte na *ASP.NET Framework 4,8*.
- Volitelně můžete zahrnout argument `--location <location-name>` , kde `<location-name>` je dostupná oblast Azure. Seznam povolených oblastí pro váš účet Azure můžete načíst spuštěním [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) příkazu.

Dokončení příkazu může trvat několik minut. Když je spuštěný, poskytuje zprávy o vytvoření skupiny prostředků, plánování App Service a hostování aplikace, konfiguraci protokolování a následném nasazení souboru ZIP. Potom vytvoří výstup zprávy s adresou URL aplikace:

```azurecli
You can launch the app at http://<app-name>.azurewebsites.net
```

Otevřete webový prohlížeč a přejděte na adresu URL:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Na stránce se zobrazí webová aplikace ASP.NET Core 3,1.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Webová aplikace CLI-ASP.NET Core 3,1 v Azure":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Na stránce se zobrazí webová aplikace ASP.NET Core 5,0.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Webová aplikace CLI-ASP.NET Core 5,0 v Azure":::

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

Na stránce se zobrazí webová aplikace ASP.NET Framework 4,8.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="Webová aplikace CLI – ASP.NET Framework 4,8 v Azure":::

---

:::zone-end

## <a name="update-the-app-and-redeploy"></a>Aktualizace a opětovné nasazení aplikace

Pomocí těchto kroků aktualizujete a znovu nasadíte webovou aplikaci:

:::zone target="docs" pivot="development-environment-vs"

1. V **Průzkumník řešení** v rámci projektu otevřete *index. cshtml*.
1. Nahraďte první `<div>` prvek následujícím kódem:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Uložte provedené změny.

1. Pokud ho chcete znovu nasadit do Azure, klikněte pravým tlačítkem na projekt **MyFirstAzureWebApp** v **Průzkumník řešení** a vyberte **publikovat**.
1. Na stránce **publikovat** Souhrn vyberte **publikovat**.

    Po dokončení publikování spustí Visual Studio prohlížeč na adrese URL webové aplikace.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Na stránce se zobrazí aktualizovaná webová aplikace ASP.NET Core 3,1.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio – aktualizovaná webová aplikace ASP.NET Core 3,1 v Azure":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Na stránce se zobrazí aktualizovaná webová aplikace ASP.NET Core 5,0.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio – aktualizovaná webová aplikace ASP.NET Core 5,0 v Azure":::

    ### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

    Na stránce se zobrazí aktualizovaná webová aplikace ASP.NET Framework 4,8.

    :::image type="content" source="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio – aktualizovaná webová aplikace ASP.NET Framework 4,8 v Azure":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

1. Otevřete stránku *Index.cshtml*.
1. Nahraďte první `<div>` prvek následujícím kódem:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Uložte provedené změny.

1. Otevřete **postranní panel** Visual Studio Code, vyberte ikonu **Azure** a rozbalte její možnosti.
1. V uzlu **App Service** rozbalte své předplatné a klikněte pravým tlačítkem na **MyFirstAzureWebApp**.
1. Vyberte **nasazení do webové aplikace...**
1. Po zobrazení výzvy vyberte **nasadit** .
1. Po dokončení publikování vyberte **Procházet web** v oznámení a po zobrazení výzvy vyberte **otevřít** .

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Na stránce se zobrazí aktualizovaná webová aplikace ASP.NET Core 3,1.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code – aktualizace webové aplikace ASP.NET Core 3,1 v Azure":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Na stránce se zobrazí aktualizovaná webová aplikace ASP.NET Core 5,0.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code – aktualizace webové aplikace ASP.NET Core 5,0 v Azure":::

    ### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

    Na stránce se zobrazí aktualizovaná webová aplikace ASP.NET Framework 4,8.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code – aktualizace webové aplikace ASP.NET Framework 4,8 v Azure":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

V místním adresáři otevřete soubor *index. cshtml* . Nahraďte první `<div>` prvek:

```razor
<div class="jumbotron">
    <h1>.NET 💜 Azure</h1>
    <p class="lead">Example .NET app to Azure App Service.</p>
</div>
```

Uložte změny a pak znovu nasaďte aplikaci pomocí `az webapp up` příkazu:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

ASP.NET Core 3,1 je pro různé platformy v závislosti na předchozím nasazení nahradit `<os>` buď `linux` nebo `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

ASP.NET Core 5,0 je pro různé platformy v závislosti na předchozím nasazení nahradit `<os>` buď `linux` nebo `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

ASP.NET Framework 4,8 obsahuje závislosti rozhraní a musí být hostované ve Windows.

```azurecli
az webapp up --os-type windows
```

> [!TIP]
> Pokud vás zajímá hostování aplikací .NET v systému Linux, zvažte migraci z [ASP.NET Framework na ASP.NET Core](/aspnet/core/migration/proper-to-2x).

---

Tento příkaz používá hodnoty, které jsou uložené místně v souboru *. Azure/config* , včetně názvu aplikace, skupiny prostředků a plánu App Service.

Po dokončení nasazení přepněte zpátky do okna prohlížeče, které se otevřelo v kroku **přechod do aplikace** , a stiskněte tlačítko Aktualizovat.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Na stránce se zobrazí aktualizovaná webová aplikace ASP.NET Core 3,1.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI – aktualizace webové aplikace ASP.NET Core 3,1 v Azure":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Na stránce se zobrazí aktualizovaná webová aplikace ASP.NET Core 5,0.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI – aktualizace webové aplikace ASP.NET Core 5,0 v Azure":::

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

Na stránce se zobrazí aktualizovaná webová aplikace ASP.NET Framework 4,8.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="CLI – aktualizovaná webová aplikace ASP.NET Framework 4,8 v Azure":::

---

:::zone-end

## <a name="manage-the-azure-app"></a>Správa aplikace Azure

Pokud chcete spravovat webovou aplikaci, použijte [Azure Portal](https://portal.azure.com)a vyhledejte a vyberte **App Services**.

:::image type="content" source="media/quickstart-dotnetcore/app-services.png" alt-text="Azure Portal – vyberte možnost App Services.":::

Na stránce **App Services** vyberte název vaší webové aplikace.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Azure Portal – stránka App Services s vybranou ukázkovou webovou aplikací.":::

Stránka **Přehled** pro vaši webovou aplikaci obsahuje možnosti pro základní správu, jako je procházení, zastavení, spuštění, restartování a odstranění. V nabídce vlevo najdete další stránky pro konfiguraci vaší aplikace.

:::image type="content" source="media/quickstart-dotnetcore/web-app-overview-page.png" alt-text="Azure Portal – Stránka s přehledem App Service.":::

<!--
## Clean up resources - H2 added from the next three includes
-->
:::zone target="docs" pivot="development-environment-vs"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

:::zone target="docs" pivot="development-environment-vscode"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->
[!INCLUDE [Clean-up CLI resources](../../includes/cli-samples-clean-up.md)]
:::zone-end

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili a nasadili webovou aplikaci v ASP.NET pro Azure App Service.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

V dalším článku se dozvíte, jak vytvořit aplikaci .NET Core a jak ji připojit k SQL Database:

> [!div class="nextstepaction"]
> [Kurz: ASP.NET Core aplikace s využitím SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace ASP.NET Core 3,1](configure-language-dotnetcore.md)

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

V dalším článku se dozvíte, jak vytvořit aplikaci .NET Core a jak ji připojit k SQL Database:

> [!div class="nextstepaction"]
> [Kurz: ASP.NET Core aplikace s využitím SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace ASP.NET Core 5,0](configure-language-dotnetcore.md)

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

V dalším článku se dozvíte, jak vytvořit aplikaci .NET Framework a připojit ji k SQL Database:

> [!div class="nextstepaction"]
> [Kurz: aplikace ASP.NET s SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace ASP.NET Framework](configure-language-dotnet-framework.md)

---

[app-service-pricing-tier]: https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
