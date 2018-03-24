---
title: Profil ASP.NET core Azure Linux web apps s Application Insights profileru | Microsoft Docs
description: Přehled koncept a podrobný kurz o tom, jak ji povolit
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
ms.openlocfilehash: 63a7ceacffe1ee33227d3a8272dda7de7b3b1135
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profil ASP.NET Core Linux webové aplikace Azure s Application Insights profileru

Tato funkce je aktuálně ve verzi preview

Zjistěte, jak dlouho je věnovaný každá metoda za provozu webové aplikace při používání [Application Insights](app-insights-overview.md). Profileru je nyní k dispozici pro ASP.NET core webové aplikace hostované v systému Linux na aplikační služby. Tato příručka poskytuje podrobný návod na tom, jak se můžou shromažďovat trasování profileru pro ASP.NET core Linux webové aplikace.

Po dokončení tohoto průvodce, vaše aplikace bude shromažďovat trasování profileru podobná následující snímek obrazovky. V tomto příkladu trasování profileru značí, že konkrétní webové žádosti je pomalé, protože je většina čas strávený v čekání. Aktivní trase v kódu, který ke zpomalení aplikace je před ikonu plamene. Tento příklad ukazuje `About` metoda v `HomeController` zpomalení webové aplikace, protože byl volání `Thread.Sleep`.

![Trasování profileru](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="pre-requisites"></a>Požadavky
Níže uvedené pokyny použít na všechny vývojové prostředí systému Windows, Linux a Mac:

* Nainstalujte [.NET core SDK 2.1.2 nebo novější](https://www.microsoft.com/net/download/windows/build)
* Nainstalovat Git pokynů v [Začínáme - instalace Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

## <a name="setup-project-locally"></a>Projekt instalace místně

1. Otevřete příkazový řádek na váš počítač. Podle pokynů níže funguje pro všechny vývojové prostředí systému Windows, Linuxu a Macu.

2. Vytvoření ASP.NET core webové aplikace MVC
    ```
    dotnet new mvc -n LinuxProfilerTest
    ```
3. Změnit adresář, v příkazovém řádku ke kořenové složce projektu

4. Přidejte balíček Nuget pro shromažďování profileru trasování.
    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```
5. Přidá řádek kódu náhodně prodlevu pár sekund v HomeController.cs

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
6. Uložte a potvrdit změny v místním úložišti

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-azure-app-service-for-hosting-your-project"></a>Vytvoření služby Azure App Service pro hostování projektu
1. Vytvoření prostředí Linux služby aplikace

    ![Vytvořit aplikaci služby platformy Linux](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. Vytvořte přihlašovací údaje nasazení. Poznamenejte si heslo jako budete potřebovat později při nasazení aplikace.

    ![Vytvořit přihlašovací údaje nasazení.](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Vyberte možnost nasazení. Nastavte místní úložiště Git ve webové aplikaci, postupujte pokynů na portálu Azure. Úložiště Git se automaticky vytvoří.

    ![Nastavení úložiště Git](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

Další možnosti nasazení jsou k dispozici [sem](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type)

## <a name="deploy-your-project"></a>Nasazení projektu

1. V příkazovém řádku přejděte do kořenové složky projektu. Přidejte vzdálené úložiště Git tak, aby odkazoval na jeden na aplikační služby:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```
    * Použít 'uživatelského jména' z kroku "vytvořit přihlašovací údaje nasazení."
    * Použijte aplikaci název z kroku "vytvoření služby app service."

2. Nasazení projektu vynucením změny do Azure

    ```
    git push azure master
    ```
Zobrazí se výstup podobný následujícímu:

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
1. [Vytvořte prostředek Application Insights](./app-insights-create-new-resource.md)
2. Zkopírujte iKey prostředku Application Insights a nastavte následující nastavení v aplikační služby

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![Nastavení aplikace](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    Změna nastavení aplikace se automaticky restartuje webu. Jakmile jsou použita nová nastavení, profileru začne okamžitě 2 minutách. pak se spustí po dvou minut za hodinu.

3. Generovat některé provoz na váš web. Můžete aktualizovat lokalitu ```About``` stránky pro několikrát.

4. Počkejte 2 až 5 minut, takže události se dají agregovat do služby Application Insights.

5. Přejděte do podokna výkonu Application Insights na portálu Azure. Zobrazí se profileru trasování, které jsou k dispozici v pravém dolním rohu.

    ![Zobrazení trasování](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>Známé problémy

### <a name="enable-button-in-profiler-configuration-pane-does-not-work"></a>Povolit tlačítko v konfiguraci profileru podokně nefunguje.
**Pokud hostujete vaší aplikace pomocí aplikace služby Linux, není nutné znovu zapnout profileru v podokně výkonu na portálu Statistika aplikace. Včetně balíček NuGet v projektu a nastavení aplikace Statistika iKey v nastavení aplikace jsou dostatečná k povolení profileru**.

Pokud budete postupovat podle [App Insights profileru pro systém Windows](./app-insights-profiler.md) povolování pracovní postup klikněte na tlačítko **povolit** v podokně Konfigurace profileru se zobrazí chyba, jak na tlačítko se pokusí nainstalovat verzi systému Windows profileru agenta v prostředí Linux.

Pracujeme na řešení tohoto problému v možnosti povolování.

![Nemusíte povolit profileru znovu v podokně výkonu aby profileru pracovat na Linux aplikační služby](./media/app-insights-profiler-aspnetcore-linux/issue-enable-profiler.png)


## <a name="next-steps"></a>Další kroky
Pokud používáte vlastní kontejnery hostitelem aplikační služby, postupujte podle pokynů v [ povolit služby profileru kontejnerové aplikace ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) povolit App Insights profileru

Pokud máte jakékoli problémy nebo návrhy, nahlaste na našem úložišti github: [ApplicationInsights. profileru AspNetCore: problémy](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues)
