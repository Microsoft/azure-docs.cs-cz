---
title: Azure Application Insights pro ASP.NET Core | Microsoft Docs
description: Sledování webových aplikací pro dostupnosti, výkonu a využití.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: mbullwin
ms.openlocfilehash: 2245fcdaa8b7e85ea37e9af9c939cd188c4d7ed9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights pro ASP.NET Core

Azure Application Insights poskytuje hlubší monitorování webové aplikace můžete snížit úroveň kódu. Můžete snadno monitorování vaší webové aplikace pro dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vaší aplikaci a nečekat na to, až je nahlásí uživatelé.

Tento článek vás provede procesem vytvoření ukázkové ASP.NET Core [stránky Razor](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) aplikace v sadě Visual Studio a jak začít monitorovat pomocí služby Azure Application Insights.

## <a name="prerequisites"></a>Požadavky

- NET Core 2.0.0 SDK nebo novější.
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) verze 15.3 nebo novější s ASP.NET a webové úlohy vývoj.

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Vytvoření projektu ASP.NET Core v sadě Visual Studio

1. Klikněte pravým tlačítkem myši a spusťte **Visual Studio 2017** jako správce.
2. Vyberte **soubor** > **nové** > **projektu** (Ctrl-Shift-N).

   ![Snímek obrazovky nabídky Nový projekt sady Visual Studio souboru](./media/app-insights-asp-net-core/0001-file-new-project.png)

3. Rozbalte položku **Visual C#** > vyberte **.NET Core** > **webové aplikace ASP.NET Core**. Zadejte **název** > **název řešení** > zkontrolujte **vytvoření nového úložiště Git**.

   ![Snímek obrazovky Průvodce projektem nový soubor sady Visual Studio](./media/app-insights-asp-net-core/0002-new-project-web-application.png)

4. Vyberte **.Net Core** > **jádro ASP.NET 2.0** **webovou aplikaci** > **OK**.

    ![Snímek obrazovky nabídky Nový projekt výběr sady Visual Studio souboru](./media/app-insights-asp-net-core/0003-dot-net-core.png)

## <a name="add-application-insights-telemetry"></a>Přidání Telemetrie Application Insights

1. Vyberte **projektu** > **přidat Telemetrii Application Insights...** (Případně je můžete kliknout pravým tlačítkem **připojené služby** > Přidat připojení služby.)

    ![Snímek obrazovky nabídky Nový projekt výběr sady Visual Studio souboru](./media/app-insights-asp-net-core/0004-add-application-insights-telemetry.png)

2. Vyberte **Začít zdarma**.

    ![Snímek obrazovky nabídky Nový projekt výběr sady Visual Studio souboru](./media/app-insights-asp-net-core/0005-start-free.png)

3. Vyberte odpovídající **předplatné** > **prostředků** > a zda se má povolit kolekce více než 1 GB za měsíc > **zaregistrovat**.

    ![Snímek obrazovky nabídky Nový projekt výběr sady Visual Studio souboru](./media/app-insights-asp-net-core/0006-register.png)

## <a name="changes-made-to-your-project"></a>Změní Made do projektu

Application Insights je velmi nízké režijní náklady. Chcete-li zkontrolovat na všechny změny do projektu přidáním telemetrie Application Insights:

Vyberte **zobrazení** > **Team Explorer** (Ctrl +\, Ctrl + M) > **projektu** > **změny**

- Celkový počet čtyři změny:

  ![Snímek obrazovky soubory změnit tak, že přidáte Application Insights](./media/app-insights-asp-net-core/0007-changes.png)

- K vytvoření nového souboru jeden:

   **ConnectedService.json**

  ![Snímek obrazovky soubory změnit tak, že přidáte Application Insights](./media/app-insights-asp-net-core/0008-connectedservice-json.png)

- Tři soubory jsou upraveny:

  **appsettings.json**

   ![Snímek obrazovky soubory změnit tak, že přidáte Application Insights](./media/app-insights-asp-net-core/0009-appsettings-json.png)

  **ContosoDotNetCore.csproj**

   ![Snímek obrazovky soubory změnit tak, že přidáte Application Insights](./media/app-insights-asp-net-core/0010-contoso-netcore-csproj.png)

   **Program.cs**

   ![Snímek obrazovky soubory změnit tak, že přidáte Application Insights](./media/app-insights-asp-net-core/0011-program-cs.png)

## <a name="synthetic-transactions-with-powershell"></a>Syntetických transakcí v prostředí PowerShell

Spuštění aplikace a pak levým na kolem propojení ručně lze použít k vygenerování testu provoz. Často je však vhodné vytvořit jednoduché syntetických transakcí v prostředí PowerShell.

1. Spuštění aplikace kliknutím IIS Express ![Snímek obrazovky nástroje Visual Studio ikonu IIS Express](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Zkopírujte adresu url z panelu Adresa prohlížeče. Je ve formátu http://localhost:{random číslo portu}

   ![Snímek obrazovky panelu Adresa v prohlížeči adresu url](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Spusťte následující zacyklení prostředí PowerShell k vytvoření 100 syntetické transakce proti testování aplikace. Změňte číslo portu, po **localhost:** tak, aby odpovídaly adresu url, kterou jste zkopírovali v předchozím kroku.

   ```PS
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Otevřete aplikaci portálu Statistika

Po spuštění PowerShell z předchozí části, spusťte Application Insights se mají zobrazit transakce a potvrďte, že data jsou shromažďována. 

V nabídce sady Visual Studio vyberte **projektu** > **Application Insights** > **otevřete portálu Statistika aplikace**

   ![Snímek obrazovky Statistika aplikací – přehled](./media/app-insights-asp-net-core/0014-portal-01.png)

> [!NOTE]
> Ve výše uvedený snímek obrazovky příklad **živý datový proud**, **zobrazení času načítání stránky**, a **neúspěšné požadavky** nejsou aktuálně shromažďovat. V další části provede přidání každý. Pokud již shromažďujete **živý datový proud**, a **zobrazení času načítání stránky**, pouze postupujte podle pokynů pro **neúspěšné požadavky**.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>Shromažďovat neúspěšných požadavků, živý datový proud & čas načítání stránky zobrazení

### <a name="failed-requests"></a>Neúspěšné požadavky

Technicky **neúspěšné požadavky** jsou shromažďovány, ale zatím žádná došlo. V zájmu urychlení proces podél vlastní výjimky lze přidat do existujícího projektu pro simulaci reálného výjimky. Pokud vaše aplikace stále běží v sadě Visual Studio před pokračováním **Zastavte ladění** (Shift + F5)

1. V **Průzkumníku řešení** > rozbalte **stránky** > **About.cshtml** > Otevřete **About.cshtml.cs**.

   ![Snímek obrazovky Průzkumníka řešení Visual Studio](./media/app-insights-asp-net-core/0015-solution-explorer-about.png)

2. Přidat výjimku pod ``Message=`` > uložte změny do souboru.

   ```C#
   throw new Exception("Test Exception");
   ```

   ![Snímek obrazovky kód výjimky](./media/app-insights-asp-net-core/000016-exception.png)

### <a name="live-stream"></a>Live Stream

Pro přístup k funkci živý datový proud Application Insights s ASP.NET Core aktualizace **Microsoft.ApplicationInsights.AspNetCore 2.2.0** balíčky NuGet.

Ze sady Visual Studio, vyberte **projektu** > **spravovat balíčky NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > verze **2.2.0** > **aktualizace**.

  ![Snímek obrazovky Správce balíčků NuGet](./media/app-insights-asp-net-core/0017-update-nuget.png)

Více potvrzení výzvy bude dojít, přečtěte si a přijměte Pokud souhlasíte se změnami.

### <a name="page-view-load-time"></a>Čas načítání stránky zobrazení

1. V sadě Visual Studio přejděte do **Průzkumníku řešení** > **stránky** > bude nutné upravit dva soubory: **_Layout.cshtml**, a **_ ViewImports.cshtml**

2. V **_ViewImports.cshtml**, přidejte:

   ```C#
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```
     ![Snímek obrazovky změnu kódu _ViewImports.cshtml](./media/app-insights-asp-net-core/00018-view-imports.png)

3. V **_Layout.cshtml** přidejte řádek níže před ``</head>`` značky, ale před jiné skripty.

    ```C#
    @Html.Raw(snippet.FullScript)
    ```
    ![Snímek obrazovky změnu kódu layout.cshtml](./media/app-insights-asp-net-core/0018-layout-cshtml.png)

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>Test se nezdařil, požadavků, čas načítání stránky zobrazení, živý datový proud

Teď, když jste dokončili předchozích kroků můžete otestovat a potvrďte, že vše funguje.

1. Spuštění aplikace kliknutím IIS Express ![Snímek obrazovky nástroje Visual Studio ikonu IIS Express](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Přejděte na **o** stránky k aktivaci testovací výjimka. (Pokud používáte v režimu ladění, budete muset klikněte na tlačítko **pokračovat** v sadě Visual Studio před výjimka bude být zachyceny pomocí Application Insights.)

3. Znovu spusťte simulované skriptu transakce PowerShell z dříve (možná muset upravit číslo portu ve skriptu.)

4. Pokud není stále otevřít z nabídky vyberte možnost Visual Studio přehled Statistika aplikací **projektu** > **Application Insights** > **otevřete aplikace Portálu Statistika**. 

   > [!TIP]
   > Pokud nevidíte nový provozu ještě, podívejte se **čas rozsah** a klikněte na tlačítko **aktualizovat**.

   ![Snímek obrazovky Přehled okna](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Vyberte živý datový proud

   ![Snímek obrazovky za provozu metriky datového proudu](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   Pokud vaše prostředí PowerShell skriptu je stále spuštěná jste měli vidět za provozu metriky, pokud zastavil spustit skriptu znovu pomocí živý datový proud otevřete (.)

## <a name="app-insights-sdk-comparison"></a>App Insights SDK porovnání

Pevného pracuje se product group Application Insights k dosažení co nejblíže parity funkcí jako mezi [úplné rozhraní .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet) a .net Core SDK. 2.2.0 vydání [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) pro Application Insights do značné míry zavřel funkce mezery.

Další informace o rozdílech a kompromisy mezi [.NET a .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

   | Porovnání SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Metriky za provozu**      | **+** |**-** | **+** |
   | **Kanálu Telemetrii serveru** | **+** |**-** | **+**|
   |**Adaptivního vzorkování**| **+** | **-** | **+**|
   | **Volání závislost SQL**     | **+** |**-** | **+**|
   | **Čítače výkonu*** | **+** | **-**| **-**|

_Čítače výkonu_ v tomto kontextu odkazuje na [čítače výkonu na straně serveru](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) jako procesor, paměť a využití disku.

## <a name="open-source-sdk"></a>Open-source SDK
[Přečtěte si a přispívat ke kódu](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Další postup
* [Prozkoumejte toků uživatelé](app-insights-usage-flows.md) pochopit, jak uživatelé přecházejí mezi vaší aplikace.
* [Konfigurace shromažďování snímků](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) zobrazíte stav zdrojového kódu a proměnných v tuto chvíli je vyvolána výjimka.
* [Použít rozhraní API](app-insights-api-custom-events-metrics.md) k odeslání vlastní události a metriky pro další podrobné zobrazení výkonu a využití vaší aplikace.
* [Testy dostupnosti](app-insights-monitor-web-app-availability.md) zkontrolujte aplikaci neustále z po celém světě.
