---
title: Profil ASP.NET Core služby Azure Linux Web Apps pomocí Application Insights Profiler | Microsoft Docs
description: Koncepční přehled a podrobný návod, jak používat Application Insights Profiler.
ms.topic: conceptual
ms.custom: devx-track-csharp
author: cweining
ms.author: cweining
ms.date: 02/23/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 69ec25348c3056536a2e09fd889b48e1e63ea7bb
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91992779"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profil ASP.NET Core webové aplikace Azure Linux pomocí Application Insights Profiler

Tato funkce je aktuálně ve verzi Preview.

Zjistěte, kolik času se stráví v každé metodě živé webové aplikace při použití [Application Insights](./app-insights-overview.md). Application Insights Profiler je teď k dispozici pro ASP.NET Core webové aplikace hostované v systému Linux na Azure App Service. Tato příručka poskytuje podrobné pokyny, jak můžete shromažďovat trasování profileru pro ASP.NET Core webové aplikace pro Linux.

Po dokončení tohoto návodu může vaše aplikace shromažďovat trasování profileru, jako jsou trasování zobrazené v imagi. V tomto příkladu trasování profileru indikuje, že určitý webový požadavek je pomalý z důvodu času stráveného čekáním. *Horká cesta* v kódu, který zpomaluje aplikaci, je označená ikonou plamene. Metoda **About** v části **HomeController** zpomaluje webovou aplikaci, protože metoda volá funkci **Thread. Sleep** .

![Trasování profileru](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Požadavky
Následující pokyny platí pro všechna prostředí pro vývoj pro Windows, Linux a Mac:

* Nainstalujte [.NET Core SDK 2.1.2 nebo novější](https://dotnet.microsoft.com/download/archives).
* Nainstalujte Git podle pokynů v tématu [Začínáme – instalace Gitu](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>Místní nastavení projektu

1. Otevřete okno příkazového řádku na svém počítači. Následující pokyny fungují pro všechna prostředí pro vývoj v systémech Windows, Linux a Mac.

1. Vytvoření webové aplikace ASP.NET Core MVC:

   ```console
   dotnet new mvc -n LinuxProfilerTest
   ```

1. Změňte pracovní adresář na kořenovou složku projektu.

1. Přidejte balíček NuGet pro shromáždění trasování profileru:

   ```console
   dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
   ```

1. Povolit Application Insights v Program.cs:

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseApplicationInsights() // Add this line of code to Enable Application Insights
            .UseStartup<Startup>();
    ```

1. Povolit Profiler v Startup.cs:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. Do části **HomeController.cs** přidejte řádek kódu pro náhodné zpoždění několika sekund:

    ```csharp
    using System.Threading;
    ...

    public IActionResult About()
        {
            Random r = new Random();
            int delay = r.Next(5000, 10000);
            Thread.Sleep(delay);
            return View();
        }
    ```

1. Uložte změny v místním úložišti a potvrďte je:

    ```console
    git init
    git add .
    git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Vytvoření webové aplikace pro Linux pro hostování projektu

1. Vytvoření prostředí webové aplikace pomocí App Service v systému Linux:

    ![Vytvoření webové aplikace pro Linux](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. Vytvořte přihlašovací údaje pro nasazení:

    > [!NOTE]
    > Poznamenejte si heslo pro pozdější použití při nasazování vaší webové aplikace.

    ![Vytvoření přihlašovacích údajů pro nasazení](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Vyberte možnosti nasazení. Pomocí pokynů na Azure Portal nastavte místní úložiště Git ve webové aplikaci. Automaticky se vytvoří úložiště Git.

    ![Nastavení úložiště Git](./media/profiler-aspnetcore-linux/setup-git-repo.png)

Další možnosti nasazení najdete v [dokumentaci App Service](../../app-service/index.yml).

## <a name="deploy-your-project"></a>Nasazení projektu

1. V okně příkazového řádku přejděte do kořenové složky vašeho projektu. Přidejte vzdálené úložiště Git, které bude odkazovat na úložiště v App Service:

    ```console
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Použijte **uživatelské jméno** , které jste použili k vytvoření přihlašovacích údajů pro nasazení.
    * Použijte **název aplikace** , který jste použili k vytvoření webové aplikace, pomocí App Service v systému Linux.

2. Nasaďte projekt vložením změn do Azure:

    ```console
    git push azure master
    ```

    Měl by se zobrazit výstup podobný následujícímu příkladu:

    ```output
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    ...
    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Přidání Application Insights pro monitorování webových aplikací

1. [Vytvořte prostředek Application Insights](./create-new-resource.md).

2. Zkopírujte hodnotu **ikey** prostředku Application Insights a ve svých webových aplikacích nastavte následující nastavení:

    `APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]`

    Po změně nastavení aplikace se web automaticky restartuje. Po použití nového nastavení se Profiler spustí hned po dvou minutách. Profiler se pak spustí každou hodinu za dvě minuty.

3. Vygenerujte na svém webu nějaký provoz. Provoz můžete vygenerovat tak, že na **stránce několikrát** aktualizujete lokalitu.

4. Počkejte dva až pět minut, než se události naagreguje do Application Insights.

5. V Azure Portal přejděte do podokna **výkonu** Application Insights. Trasování profileru můžete zobrazit v pravém dolním rohu podokna.

    ![Zobrazit trasování profileru](./media/profiler-aspnetcore-linux/view-traces.png)



## <a name="next-steps"></a>Další kroky
Pokud používáte vlastní kontejnery, které jsou hostovány Azure App Service, postupujte podle pokynů v části [ povolení Service profiler pro kontejnerové ASP.NET Core aplikace](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) , aby bylo možné povolit Application Insights Profiler.

Nahlaste všechny problémy nebo návrhy do úložiště GitHub Application Insights: [ApplicationInsights-Profiler-AspNetCore: problémy](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).