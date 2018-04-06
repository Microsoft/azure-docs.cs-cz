---
title: Profil webové aplikace ASP.NET Core Azure Linux s Application Insights profileru | Microsoft Docs
description: Koncepční přehled a podrobný kurz o tom, jak použít Application Insights profileru.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: mbullwin
ms.openlocfilehash: 5596c4efeba14e9d2bfdadd7ce92bb6b2c9fcbf0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Webové aplikace ASP.NET Core Azure Linux profil s Application Insights profileru

Tato funkce je aktuálně ve verzi Preview.

Zjistěte, jak dlouho je věnovaný každá metoda za provozu webové aplikace při používání [Application Insights](app-insights-overview.md). Application Insights profileru je nyní k dispozici pro webové aplikace ASP.NET Core, které jsou hostované v systému Linux v Azure App Service. Tato příručka obsahuje podrobné pokyny o tom, jak se můžou shromažďovat trasování profileru pro webové aplikace ASP.NET Core Linux.

Po dokončení tohoto postupu, vaše aplikace může shromažďovat trasování profileru jako trasování, které jsou zobrazeny v bitové kopii. V tomto příkladu trasování profileru označuje, že konkrétní webové žádosti je pomalé z důvodu čekací čas strávený. *Aktivní trase* v kódu, který je zpomalení aplikace je označené ikonou plamene. **o** metoda v **HomeController** část je zpomalení webové aplikace, protože je volání metody **Thread.Sleep** funkce.

![Trasování profileru](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Požadavky
Následující pokyny platí pro všechny vývojové prostředí systému Windows, Linux a Mac:

* Nainstalujte [.NET Core SDK 2.1.2 nebo novější](https://www.microsoft.com/net/download/windows/build).
* Nainstalujte Git podle pokynů v [Začínáme - instalace Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>Nastavení projektu místně

1. Otevřete okno příkazového řádku na počítači. Podle následujících pokynů fungovat pro všechny vývojové prostředí systému Windows, Linuxu a Macu.

2. Vytvořte webovou aplikaci ASP.NET MVC jádra:

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

3. Změňte pracovní adresář ke kořenové složce projektu.

4. Přidejte balíček NuGet, který chcete shromažďovat trasování profileru:

    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

5. Přidá řádek kódu **HomeController.cs** části náhodně prodlevu pár sekund:

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

6. Uložte a potvrdit změny v místním úložišti:

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Vytvoření webové aplikace Linux k hostování projektu

1. Vytvoření prostředí webové aplikace pomocí služby App Service v systému Linux:

    ![Vytvoření webové aplikace Linux](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. Vytvořte přihlašovací údaje nasazení:

    > [!NOTE]
    > Zaznamenejte heslo pro pozdější použití při nasazení webové aplikace.

    ![Vytvořit přihlašovací údaje nasazení.](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Vyberte možnosti nasazení. Podle pokynů na portálu Azure nastavte místní úložiště Git ve webové aplikaci. Automaticky se vytvoří úložiště Git.

    ![Nastavení úložiště Git](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

Další možnosti nasazení najdete v tématu [v tomto článku](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type).

## <a name="deploy-your-project"></a>Nasazení projektu

1. V okně příkazového řádku přejděte do kořenové složky pro váš projekt. Přidejte vzdálené úložiště Git tak, aby odkazovaly do úložiště v App Service:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Použití **uživatelské jméno** lze vytvořit přihlašovací údaje nasazení.
    * Použití **název aplikace** jste použili k vytvoření webové aplikace pomocí služby App Service v systému Linux.

2. Nasazení projektu vynucením změny do Azure:

    ```
    git push azure master
    ```

Měli byste vidět výstup podobný v následujícím příkladu:

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

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Přidejte Application Insights k monitorování vaší webové aplikace

1. [Vytvořte prostředek Application Insights](./app-insights-create-new-resource.md).

2. Kopírování **iKey** hodnotu prostředku Application Insights a nastavte následující nastavení ve webových aplikacích:

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![Konfigurovat nastavení aplikace](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    Při změně nastavení aplikace, lokality se automaticky restartuje. Po použití nových nastavení, profileru okamžitě spustí dvě minuty. Profileru pak spustí dvě minuty každou hodinu.

3. Generovat některé provoz na váš web. Provoz můžete vygenerovat pomocí aktualizace lokality **o** stránky několikrát.

4. Události do pěti minut počkat, než agregační Application insights.

5. Přejděte do služby Application Insights **výkonu** podokně na portálu Azure. Můžete zobrazit profileru trasování ve spodní části podokna.

    ![Zobrazit trasování profileru](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>Známé problémy

### <a name="the-enable-action-in-the-profiler-configuration-pane-doesnt-work"></a>Povolit akce v podokně Konfigurace profileru nebude fungovat.

> [!NOTE]
> Pokud hostujete vaší aplikace pomocí služby App Service v systému Linux, nemusíte znovu povolit profileru v **výkonu** podokně na portálu služby Application Insights. Můžete zahrnout balíček NuGet do projektu a nastavte Application Insights **iKey** hodnota v nastavení webové aplikace, abyste povolili profileru.

Pokud budete postupovat podle povolování pracovního postupu pro [Application Insights profileru pro systém Windows](./app-insights-profiler.md) a vyberte **povolit** v **konfigurace profileru** podokně zobrazí chybové hlášení. Povolit akce se pokusí nainstalovat verzi systému Windows agenta profileru do prostředí Linux.

Pracujeme na řešení tohoto problému.

![Nepokoušejte profileru v podokně výkonu znovu zapnout.](./media/app-insights-profiler-aspnetcore-linux/issue-enable-profiler.png)


## <a name="next-steps"></a>Další postup
Pokud používáte vlastní kontejnery, které jsou hostované ve službě Azure App Service, postupujte podle pokynů v [ povolit profileru služby pro aplikaci ASP.NET Core kontejnerizované](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) povolit profileru Application Insights.

Sestavy jakékoli problémy nebo návrhy do úložiště Application Insights GitHub: [ApplicationInsights. profileru AspNetCore: problémy](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).
