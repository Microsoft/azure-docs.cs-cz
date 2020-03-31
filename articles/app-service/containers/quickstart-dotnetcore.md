---
title: 'Úvodní příručka: Spuštění aplikace Linux ASP.NET Core'
description: Začínáme s linuxovými aplikacemi ve službě Azure App Service nasazením první aplikace ASP.NET Core do kontejneru Linuxu ve službě App Service.
keywords: azure app service, web app, dotnet, core, linux, oss
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.tgt_pltfrm: linux
ms.topic: quickstart
ms.date: 03/27/2019
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: bd8cb11f5d4881eed4cb4371a7d85fc26818a651
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80046204"
---
# <a name="create-an-aspnet-core-app-in-app-service-on-linux"></a>Vytvoření aplikace ASP.NET Core ve službě App Service na Linuxu

> [!NOTE]
> Tento článek nasadí aplikaci do služby App Service v Linuxu. Pokud chcete nasadit službu App Service ve _Windows_, [přečtěte si témat u tématu Vytvoření aplikace ASP.NET Core v Azure](../app-service-web-get-started-dotnet.md).
>

[App Service na Linuxu](app-service-linux-intro.md) poskytuje vysoce škálovatelnou webhostingovou službu s vlastní mizákou pomocí operačního systému Linux. V tomto rychlém startu se dozvíte, jak vytvořit aplikaci [.NET Core](https://docs.microsoft.com/aspnet/core/) ve službě App Service v Linuxu. Vytvoříte aplikaci pomocí [rozhraní příkazového příkazu Konto Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)a pomocí Gitu nasadíte do aplikace kód .NET Core.

![Ukázková aplikace spuštěná v Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Postup v tomto článku můžete použít v případě počítačů se systémem Mac, Windows nebo Linux.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* <a href="https://git-scm.com/" target="_blank">Nainstalovat Git</a>.
* <a href="https://www.microsoft.com/net/core/" target="_blank">Instalace .NET Core</a>

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

Přejděte do nově vytvořené aplikace. Nahraďte _ &lt;>názvu aplikace_ názvem aplikace.

```bash
http://<app-name>.azurewebsites.net
```

Zde je to, co vaše nová aplikace by měla vypadat:

![Prázdná stránka aplikace](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 22, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (18/18), done.
Writing objects: 100% (22/22), 51.21 KiB | 3.94 MiB/s, done.
Total 22 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '741f16d1db'.
remote: Generating deployment script.
remote: Project file path: ./hellodotnetcore.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: ...............................................................................................
remote:   Restoring packages for /home/site/repository/hellodotnetcore.csproj...
remote: ....................................
remote:   Installing System.Xml.XPath 4.0.1.
remote:   Installing System.Diagnostics.Tracing 4.1.0.
remote:   Installing System.Threading.Tasks.Extensions 4.0.0.
remote:   Installing System.Reflection.Emit.ILGeneration 4.0.1.
remote:   ...
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Přechod do aplikace

V prohlížeči zadejte adresu nasazené aplikace.

```bash
http://<app_name>.azurewebsites.net
```

Ukázkový kód .NET Core běží ve službě App Service na Linuxu s integrovanou bitovou kopii.

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

Po dokončení nasazení přepněte zpět do okna prohlížeče, které se otevřelo v kroku **Procházet do aplikace,** a stiskněte tlačítko Aktualizovat.

![Aktualizovaná ukázková aplikace spuštěná ve službě Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

## <a name="manage-your-new-azure-app"></a>Správa nové aplikace Azure

Přejděte na <a href="https://portal.azure.com" target="_blank">portál Azure</a> a spravujte aplikaci, kterou jste vytvořili.

V levé nabídce klikněte na **App Services**a potom klikněte na název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/quickstart-dotnetcore/portal-app-service-list.png)

Zobrazí se stránka Přehled aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. 

![Stránka služby App Service na webu Azure Portal](media/quickstart-dotnetcore/portal-app-overview.png)

Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: aplikace ASP.NET Core s databází SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace ASP.NET Core](configure-language-dotnetcore.md)
