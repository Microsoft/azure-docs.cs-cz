---
title: 'Rychlý Start: spuštění aplikace ASP.NET Core pro Linux'
description: Začněte s aplikacemi pro Linux v Azure App Service nasazením první ASP.NET Core aplikace do kontejneru Linux v App Service.
keywords: azure app service, web app, dotnet, core, linux, oss
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.tgt_pltfrm: linux
ms.topic: quickstart
ms.date: 04/22/2020
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 1eeb5bbd4b10ef660a50f40d6c1300b0ca214561
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82206662"
---
# <a name="create-an-aspnet-core-app-in-app-service-on-linux"></a>Vytvoření aplikace ASP.NET Core v App Service v systému Linux

> [!NOTE]
> Tento článek nasadí aplikaci do služby App Service v Linuxu. Pokud chcete nasadit nástroj na App Service ve _Windows_, přečtěte si téma [Vytvoření aplikace ASP.NET Core v Azure](../app-service-web-get-started-dotnet.md).
>

[App Service v systému Linux](app-service-linux-intro.md) poskytuje vysoce škálovatelnou službu s automatickými opravami pro hostování webů pomocí operačního systému Linux. V tomto rychlém startu se dozvíte, jak vytvořit aplikaci [.NET Core](https://docs.microsoft.com/aspnet/core/) ve službě App Service v Linuxu. Vytvoříte aplikaci pomocí rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)a pomocí Gitu nasadíte kód .NET Core do aplikace.

![Ukázková aplikace spuštěná v Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Postup v tomto článku můžete použít v případě počítačů se systémem Mac, Windows nebo Linux.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* <a href="https://git-scm.com/" target="_blank">Nainstalovat Git</a>.
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Instalace nejnovější sady .NET Core 3,1 SDK</a>

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

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

Přejděte k nově vytvořené aplikaci. Nahraďte _ &lt;název aplikace>_ názvem vaší aplikace.

```bash
https://<app-name>.azurewebsites.net
```

Vaše nová aplikace by měla vypadat takto:

![Prázdná stránka aplikace](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

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

## <a name="browse-to-the-app"></a>Přechod do aplikace

V prohlížeči zadejte adresu nasazené aplikace.

```bash
http://<app_name>.azurewebsites.net
```

Vzorový kód .NET Core je spuštěný v App Service v systému Linux s předdefinovaným obrázkem.

![Ukázková aplikace spuštěná v Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Blahopřejeme!** Nasadili jste svoji první aplikaci .NET Core do služby App Service v Linuxu.

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

## <a name="manage-your-new-azure-app"></a>Správa nové aplikace Azure

Chcete-li spravovat aplikaci, kterou jste vytvořili, otevřete <a href="https://portal.azure.com" target="_blank">Azure Portal</a> .

V nabídce vlevo klikněte na **App Services**a pak klikněte na název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/quickstart-dotnetcore/portal-app-service-list.png)

Zobrazí se stránka s přehledem vaší aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. 

![Stránka služby App Service na webu Azure Portal](media/quickstart-dotnetcore/portal-app-overview.png)

Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: ASP.NET Core aplikace s SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace ASP.NET Core](configure-language-dotnetcore.md)
