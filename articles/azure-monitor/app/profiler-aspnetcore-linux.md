---
title: Profil ASP.NET základní webové aplikace Azure Pro Linux pomocí profileru Application Insights | Dokumenty společnosti Microsoft
description: Koncepční přehled a podrobný návod, jak používat Profiler Přehledy aplikací.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 02/23/2018
ms.reviewer: mbullwin
ms.openlocfilehash: d845e245a242a88d16a2597f0144a0ae4a727cb0
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640978"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profil ASP.NET základní webové aplikace Azure Pro Linux s profilerem Application Insights

Tato funkce je aktuálně ve verzi Preview.

Zjistěte, kolik času strávíte v jednotlivých metodách vaší živé webové aplikace při používání [Application Insights](../../azure-monitor/app/app-insights-overview.md). Profiler Application Insights je teď dostupný pro ASP.NET webových aplikací core, které jsou hostované v Linuxu ve službě Azure App Service. Tato příručka obsahuje podrobné pokyny o tom, jak profiler trasování lze shromažďovat pro ASP.NET webových aplikací Core Linux.

Po dokončení tohoto návodu může vaše aplikace shromažďovat trasování profileru, jako jsou trasování zobrazená na obrázku. V tomto příkladu trasování profileru označuje, že konkrétní webový požadavek je pomalý z důvodu času stráveného čekáním. *Horká cesta* v kódu, který zpomaluje aplikaci, je označena ikonou plamene. **Metoda O aplikaci** v části **HomeController** zpomaluje webovou aplikaci, protože metoda volá funkci **Thread.Sleep.**

![Stopy profileru](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Požadavky
Následující pokyny platí pro všechna vývojová prostředí Windows, Linux a Mac:

* Nainstalujte [sdk 2.1.2 jádra .NET nebo novější](https://dotnet.microsoft.com/download/archives).
* Nainstalujte Git podle pokynů na [začátku - Instalace Gitu](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>Nastavení projektu místně

1. Otevřete v počítači okno příkazového řádku. Následující pokyny fungují pro všechna vývojová prostředí Windows, Linux a Mac.

1. Vytvořte ASP.NET základní webové aplikace MVC:

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

1. Změňte pracovní adresář do kořenové složky projektu.

1. Přidejte balíček NuGet pro shromažďování trasování profileru:

    ```shell
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

1. Povolit přehledy aplikací v Program.cs:

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseApplicationInsights() // Add this line of code to Enable Application Insights
            .UseStartup<Startup>();
    ```
    
1. Povolit profiler v Startup.cs:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. Přidejte řádek kódu do **části HomeController.cs,** abyste náhodně zpozdili několik sekund:

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

1. Uložte a potrespoujte změny v místním úložišti:

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Vytvoření webové aplikace pro Linux pro hostování projektu

1. Vytvořte prostředí webové aplikace pomocí služby App Service na Linuxu:

    ![Vytvoření webové aplikace pro Linux](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. Vytvořte pověření pro nasazení:

    > [!NOTE]
    > Zaznamenejte heslo, které chcete použít později při nasazování webové aplikace.

    ![Vytvoření přihlašovacích údajů pro nasazení](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Zvolte možnosti nasazení. Nastavte místní úložiště Git ve webové aplikaci podle pokynů na webu Azure Portal. Automaticky se vytvoří úložiště Git.

    ![Nastavení úložiště Git](./media/profiler-aspnetcore-linux/setup-git-repo.png)

Další možnosti nasazení naleznete v [tomto článku](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type).

## <a name="deploy-your-project"></a>Nasazení projektu

1. V okně příkazového řádku přejděte do kořenové složky projektu. Přidejte vzdálené úložiště Git, které bude ukazovat na úložiště ve službě App Service:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Použijte **uživatelské jméno,** které jste použili k vytvoření přihlašovacích údajů pro nasazení.
    * Použijte **název aplikace,** který jste použili k vytvoření webové aplikace pomocí služby App Service na Linuxu.

2. Nasazení projektu odesláním změny do Azure:

    ```
    git push azure master
    ```

Výstup by měl být podobný následujícímu příkladu:

    ```
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
    …

    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Přidání přehledů aplikací pro sledování webových aplikací

1. [Vytvořte prostředek Application Insights](./../../azure-monitor/app/create-new-resource.md ).

2. Zkopírujte hodnotu **iKey** prostředku Application Insights a nastavte ve svých webových aplikacích následující nastavení:

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ```

    Po změně nastavení aplikace se web automaticky restartuje. Po použití nového nastavení profileru okamžitě spustí po dobu dvou minut. Profiler pak běží po dobu dvou minut každou hodinu.

3. Vygenerujte nějaký provoz na vašich webových stránkách. Provoz můžete generovat několikanásobně aktualizací stránky **O** stránce.

4. Počkejte dvě až pět minut, než se události agregují do application insights.

5. Přejděte do podokna **Výkon** přehledů aplikací na webu Azure Portal. Trasování profileru můžete zobrazit v pravém dolním rohu podokna.

    ![Zobrazení trasování profileru](./media/profiler-aspnetcore-linux/view-traces.png)



## <a name="next-steps"></a>Další kroky
Pokud používáte vlastní kontejnery, které jsou hostované službou Azure App Service, postupujte podle pokynů v [povolit profilování služby pro kontejnerizované aplikace ASP.NET Core,](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) abyste povolili profiler Application Insights.

Nahlaste všechny problémy nebo návrhy do úložiště GitHub Application Insights: [ApplicationInsights-Profiler-AspNetCore: Problémy](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).
