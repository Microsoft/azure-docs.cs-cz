---
title: Začínáme se sadou Azure WebJobs SDK
description: Úvod do WebJobs SDK pro zpracování na pozadí založeného na událostech. Zjistěte, jak získat přístup k datům v Azure služby a služby třetích stran.
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: tdykstra
ms.openlocfilehash: 5a3070582a32adeda45dff233782c3a971885c14
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37018451"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Začínáme s Azure WebJobs SDK pro zpracování na pozadí založeného na událostech

Tento článek ukazuje postup vytvoření projektu Azure WebJobs SDK, ji spustit místně a nasadíte ho do služby Azure App Service.

Tyto pokyny jsou pro [Visual Studio 2017](https://www.visualstudio.com/vs/), ale stejné úlohy, můžete provést pomocí jiných nástrojů, jako například [Visual Studio Code](https://code.visualstudio.com/).

## <a name="what-is-the-azure-webjobs-sdk"></a>Co je Azure WebJobs SDK

Sada Azure WebJobs SDK je rozhraní, které usnadňuje psaní kódu zpracování pozadí, který přistupuje k datům v Azure services. Sada SDK funkce deklarativní syntaxe pro určení události, které by měly aktivovat funkci, jako je například novou zprávu přidán do fronty. Podobně jako deklarativní syntaxe řídí čtení a zápis dat po funkce byla spuštěna. Tento systém triggerů a vazeb postará pro většinu nízké úrovně kódování úkoly spojené s přístupem do služby Azure a služby třetích stran.

### <a name="functions-triggers-and-bindings"></a>Funkce, triggerů a vazeb

Sada WebJobs SDK projektu definuje jednu nebo více *funkce*. Funkce je metoda, která obsahuje atribut aktivační události v jeho podpis metody. Aktivační události zadejte podmínky pro volání funkce a vazby určují, co ke čtení a zápisu. Například atribut aktivační události v následující funkce informuje, zavolejte funkci vždy, když se zobrazí zpráva fronty v modulu runtime `items` fronty. `Blob` Atribut informuje zprávu fronty použít ke čtení objektů blob v modulu runtime *pracovních* kontejneru. Obsah zprávy ve frontě &mdash; součástí `queueTrigger` parametr &mdash; je název objektu blob.

```cs
public static void Run(
    [QueueTrigger("items")] string myQueueItem,
    [Blob("workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

### <a name="versions-2x-and-3x"></a>Verze 2.x a 3.x

Podle pokynů informování, jak vytvořit projekt WebJobs SDK verze 2.x s poznámky o tom, co je jiný pro 3.x (ve verzi preview). Hlavní změna zaváděné 3.x je použití .NET Core místo rozhraní .NET Framework.

### <a name="azure-functions"></a>Azure Functions

[Azure Functions](../azure-functions/functions-overview.md) je založena na WebJobs SDK a když nemusíte používat přímo WebJobs SDK. Azure funkce 1.x využívá sadu WebJobs SDK 2.x. Další informace najdete v tématu [srovnání Azure Functions a WebJobs SDK](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte [účet Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a zkušenosti s [aplikace v Azure App Service](app-service-web-overview.md). Pokud chcete provést kroky v tomto článku:

* [Nainstalovat Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/) s **Azure development** zatížení. Pokud už máte Visual Studio, ale nemáte této úlohy, přidejte zatížení výběrem **nástroje > funkcí a nástrojů pro získání**.
* [Vytvořit aplikaci služby App Service](app-service-web-get-started-dotnet-framework.md). Pokud již účet máte, můžete nasadit vytvářené webové úlohy, které můžete použít místo vytvoření nové.

## <a name="create-a-project"></a>Vytvoření projektu

1. V sadě Visual Studio, vyberte **soubor > Nový projekt**.

2. Vyberte **klasický desktopový Windows > konzoly aplikace (rozhraní .NET Framework)**.

   Vytvoření projektu 3.x, vyberte **.NET Core > konzolové aplikace (.NET Core)**.
   
   > [!NOTE]
   > 3.x je stále ve verzi preview a je ve vývoji. 3.x pokyny v tomto článku jsou neúplné; nejnovější informace najdete v tématu Githubu problémy v dolní části stránky.

3. Název projektu *WebJobsSDKSample*a potom vyberte **OK**.

   ![Dialogové okno Nový projekt](./media/webjobs-sdk-get-started/new-project.png)

## <a name="add-webjobs-nuget-package"></a>Přidejte balíček NuGet pro webové úlohy

1. Nainstalujte nejnovější verzi stabilní 2.x balíčku NuGet `Microsoft.Azure.WebJobs`. (Pro WebJobs SDK 3.x, by vyberte nejnovější verzi 3.x.)
 
   Tady je **Konzola správce balíčků** příkazu pro verzi 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs -version 2.2.0
   ``` 

## <a name="create-the-jobhost"></a>Vytvořte JobHost

`JobHost` Objektu je kontejner modulu runtime pro funkce: naslouchá aktivačních událostí a volání funkce. 

1. V *Program.cs*, přidejte `using` příkaz:

   ```cs
   using Microsoft.Azure.WebJobs;
   ```

1. Nahraďte metodu `Main` následujícím kódem:

   ```cs
   static void Main()
   {
       var config = new JobHostConfiguration();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

## <a name="enable-console-logging"></a>Povolit protokolování konzoly

Existuje několik možností pro protokolování v projektu WebJobs SDK. Ten, doporučujeme vám, že je [rozhraní protokolování, která byla vyvinuta pro ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging). Toto rozhraní nabízí lepší výkon a větší flexibilitu v paměťových médií a filtrování. 

V této části můžete nastavit protokolování konzoly, který používá nové architektury.

1. Nainstalujte nejnovější stabilní verze následujících balíčků NuGet:

   * `Microsoft.Extensions.Logging` -Rozhraní protokolování.
   * `Microsoft.Extensions.Logging.Console` -Konzole *zprostředkovatele*. Protokoly pro konkrétní cíl zprostředkovatele v tomto případě odešle do konzoly. 
 
   Tady jsou **Konzola správce balíčků** příkazy pro verze 2.0.1:

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.0.1
   ``` 

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
   ``` 

2. V *Program.cs*, přidejte `using` příkaz:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

3. V `Main` metoda, přidat kód pro aktualizace `JobHostConfiguration` před vytvořením `JobHost`:
 
   ```
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   ```

   Tento kód provede tyto změny:

   * Zakáže [řídicí panel protokolování](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Řídicí panel je starší verze nástroje Sledování a protokolování řídicí panel se nedoporučuje pro produkční vysokou propustností scénáře.
   * Přidá zprostředkovatele konzoly s výchozí [filtrování](webjobs-sdk-how-to.md#log-filtering). 

   `Main` Metoda nyní vypadat třeba takto:

   ```
   var config = new JobHostConfiguration();
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   var host = new JobHost(config);
   host.RunAndBlock();
   ```
   
## <a name="create-a-function"></a>Vytvoření funkce

1. Vytvoření *Functions.cs* ve složce projektu a nahraďte kód šablony s tímto kódem:

   ```cs
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;

   namespace WebJobsSDKSample
   {
       public class Functions
       {
           public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
           {
               logger.LogInformation(message);
           }
       }
   }
   ```

   `QueueTrigger` Atribut informuje modul runtime pro volání této funkce, když dojde k zapsání novou zprávu ve frontě Azure Storage s názvem `queue`. Obsah zprávy ve frontě jsou k dispozici metoda kód v `message` parametr. Text metody je, kde zpracovat data aktivační události. V tomto příkladu kód právě zaprotokoluje zprávu.

   `message` Parametr nemusí to být řetězec. Také můžete vázat na objekt JSON, bajtové pole, nebo [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) objektu. [Zobrazit využití fronty aktivační událost](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Každý typ vazby (například front, objektů BLOB nebo tabulky) má jinou sadu typy parametrů, které můžete vázat na.

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Emulátor úložiště Azure, který běží místně nemá všechny funkce, které potřebuje WebJobs SDK. V této části tak vytvořit účet úložiště v Azure a konfigurace projektu pro použití.

1. Otevřete **Průzkumníka serveru** a přihlaste se k Azure. Klikněte pravým tlačítkem myši **Azure** uzel a potom vyberte **připojit k předplatnému Microsoft Azure**.

   ![Přihlášení k Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. V části **Azure** uzlu v **Průzkumníka serveru**, klikněte pravým tlačítkem na **úložiště**a potom vyberte **účet úložiště vytvořit**.

   ![Vytvořit nabídku účtu úložiště](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

2. V **vytvořit účet úložiště** dialogovém okně zadejte jedinečný název pro účet úložiště.

3. Vyberte stejnou **oblast** jste vytvořili vaší aplikaci služby App Service v, nebo oblast na můžete zavřít.

1. Vyberte **Vytvořit**.

   ![Vytvořit účet úložiště](./media/webjobs-sdk-get-started/create-storage-account.png)

1. V části **úložiště** uzlu v **Průzkumníka serveru**, vyberte nový účet úložiště. V **vlastnosti** okně vyberte se třemi tečkami (**...** ) na pravé straně **připojovací řetězec** pole value.

   ![Připojovací řetězec s třemi tečkami](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

2. Zkopírujte připojovací řetězec a uložte tuto hodnotu někde, můžete zkopírovat snadno znovu.

   ![Zkopírujte připojovací řetězec](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-for-running-locally"></a>Konfigurace úložiště pro provozování místně

Sada WebJobs SDK hledá připojovacího řetězce úložiště v kolekci nastavení aplikace. Když spustíte místně, hledá tuto hodnotu v *App.config* proměnné prostředí nebo souboru.

1. Přidejte následující XML tak, aby *App.config* souboru okamžitě po otevření `<configuration>` značky.

   ```xml
   <connectionStrings>
     <add name="AzureWebJobsStorage" connectionString="{storage connection string}" />
   </connectionStrings>
   ```

2. Nahraďte *{připojovacího řetězce úložiště}* s připojovací řetězec, který jste zkopírovali dříve.

   Později budete chtít připojovací řetězec znovu použít, když konfigurujete aplikace App Service v Azure.

## <a name="test-locally"></a>Test místně

V této části sestavení a spusťte projekt lokálně a aktivovat funkci tak, že vytvoříte zprávu fronty.

1. Stisknutím kombinace kláves Ctrl + F5 a spusťte projekt.

   Konzole ukazuje, že modul runtime najít funkce a čeká, fronty zpráv se ho spustit.

   ```console
   Found the following functions:
   WebJobsSDKSample.Functions.ProcessQueueMessage
   info: Host.Startup[0]
         Found the following functions:
         WebJobsSDKSample.Functions.ProcessQueueMessage
   Job host started
   info: Host.Startup[0]
         Job host started
   ```

   Mohou se zobrazit zpráva s upozorněním `ServicePointManager` nastavení. Pro testování, které budete by se tento projekt, můžete upozornění ignorovat. Další informace o upozornění najdete v tématu [jak používat sadu WebJobs SDK](webjobs-sdk-how-to.md#jobhost-servicepointmanager-settings).

2. Zavřete okno konzoly.

1. V **Průzkumníka serveru**, rozbalte uzel pro váš nový účet úložiště a pak klikněte pravým tlačítkem na **fronty**. 

2. Vyberte **vytvořit frontu**. 

3. Zadejte *fronty* jako název fronty a potom vyberte **OK**.

   ![Vytvořit frontu](./media/webjobs-sdk-get-started/create-queue.png)

4. Klikněte pravým tlačítkem na uzel pro novou frontu a potom vyberte **zobrazení fronty**.

5. Vyberte **přidat zprávu** ikonu.

   ![Vytvořit frontu](./media/webjobs-sdk-get-started/create-queue-message.png)

6. V **přidat zprávu** dialogové okno, zadejte *Hello, World!* jako **text zprávy**a potom vyberte **OK**.

   ![Vytvořit frontu](./media/webjobs-sdk-get-started/hello-world-text.png)

7. Spusťte projekt znovu.

   Protože jste použili `QueueTrigger` atribut `ProcessQueueMessage` funkce, modul runtime WeJobs SDK naslouchá fronty zpráv při spuštění. Najde nové zprávy fronty ve frontě s názvem *fronty* a volání funkce.

   Z důvodu [dotazování exponenciálního omezení rychlosti fronty](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm), může trvat stejně dlouho jako 2 minuty modulu runtime najít zprávu a vyvolání funkce. Tato doba čekání může snížit spuštěním v [režimu pro vývoj](webjobs-sdk-how-to.md#jobhost-development-settings).

  Výstup konzoly vypadá takto:

   ```console
   Found the following functions:
   WebJobsSDKSample.Functions.ProcessQueueMessage
   info: Host.Startup[0]
         Found the following functions:
         WebJobsSDKSample.Functions.ProcessQueueMessage
   Job host started
   info: Host.Startup[0]
         Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   info: Function[0]
         Hello World!
   info: Host.Results[0]
         Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   ```

8. Zavřete okno konzoly.

## <a name="add-application-insights-logging"></a>Přidejte Application Insights protokolování

Při spuštění projektu v Azure, nelze monitorovat spuštění funkce zobrazením výstup konzoly. Řešení monitorování, doporučujeme, abyste je [Application Insights](../application-insights/app-insights-overview.md). Další informace najdete v tématu [monitorování Azure Functions](../azure-functions/functions-monitoring.md).

V této části proveďte následující úkoly nastavení Application Insights protokolování před nasazením do Azure:

* Ujistěte se, že máte aplikaci služby App Service a instance služby Application Insights pro práci s.
* Nakonfigurujte aplikaci služby App Service, aby používala instance Application Insights a účet úložiště, který jste vytvořili dříve.
* Nastavení projektu pro protokolování do Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Vytvořit aplikaci služby App Service a instance služby Application Insights

1. Pokud ještě nemáte aplikaci služby App Service, která můžete použít, [vytvořit](app-service-web-get-started-dotnet-framework.md).

2. Pokud ještě nemáte prostředek Application Insights, který můžete použít, [vytvořit](../application-insights/app-insights-create-new-resource.md). Nastavit **typ aplikace** k **Obecné**a přeskočit oddíly, které následují **zkopírujte klíč instrumentace**.

3. Pokud již máte prostředek Application Insights, který chcete použít, [zkopírujte klíč instrumentace](../application-insights/app-insights-create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Konfigurace nastavení aplikace 

1. V **Průzkumníka serveru**, rozbalte **služby App Service** pod uzlem **Azure**.

1. Rozbalte skupinu prostředků, která aplikaci aplikační služby je v a pak klikněte pravým tlačítkem na aplikaci aplikační služby.

3. Vyberte **zobrazit nastavení**.

4. V **připojovací řetězce** pole, přidejte následující položku.

   |Název  |připojovací řetězec  |Typ databáze|
   |---------|---------|------|
   |AzureWebJobsStorage | {{Úložiště připojovací řetězec, který jste zkopírovali dříve}|Vlastní|
   
6. Pokud **nastavení aplikace** pole nemá instrumentace Application Insights klíče, přidejte ten, který jste zkopírovali dříve. (Klíč instrumentace již pravděpodobně existuje, v závislosti na tom, jak vytvořit aplikaci služby App Service.)

   |Název  |Hodnota  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {klíč instrumentace} |

2. Nahraďte *{klíč instrumentace}* s klíč instrumentace z prostředku Application Insights, který používáte.

2. Vyberte **Uložit**.

1. Přidejte následující XML tak, aby *App.config* souboru okamžitě po kolekci řetězce připojení.

   ```xml
   <appSettings>
     <add key="APPINSIGHTS_INSTRUMENTATIONKEY" value="{instrumentation key}" />
   </appSettings>
   ```

2. Nahraďte *{klíč instrumentace}* s klíč instrumentace z prostředku Application Insights, který používáte.

   Přidání těchto dat *App.config* souboru umožňuje otestovat připojení Application Insights, když spusťte projekt lokálně. 

3. Uložte provedené změny.

### <a name="add-application-insights-logging-provider"></a>Přidejte Application Insights protokolování zprostředkovatele

1. Nainstalujte nejnovější verzi stabilní 2.x balíčku NuGet pro zprostředkovatele protokolování Application Insights: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`. (Pro WebJobs SDK 3.x, zvolte nejnovější verzi balíčku 3.x.)

   Tady je **Konzola správce balíčků** příkazu pro verzi 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
   ``` 

1. Nainstalujte nejnovější verzi stabilní 4.x balíčku NuGet pro rozhraní .NET configuration manager: `System.Configuration.ConfigurationManager`.

   Tady je **Konzola správce balíčků** příkazu pro verzi 4.4.1:

   ```powershell
   Install-Package System.Configuration.ConfigurationManager -version 4.4.1
   ``` 

2. Otevřete *Program.cs* a přidejte `using` příkaz pro nástroj configuration manager:

   ```csharp
   using System.Configuration;
   ```

2. Nahraďte kód v `Main` metoda následujícím kódem:

   ```csharp
   using (var loggerFactory = new LoggerFactory())
   {
       var config = new JobHostConfiguration();
       var instrumentationKey =
           ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];
       config.DashboardConnectionString = "";
       config.LoggerFactory = loggerFactory
           .AddApplicationInsights(instrumentationKey, null)
           .AddConsole();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

   Tento kód provede tyto změny:

   * Přidá poskytovatele Application Insights protokolování s výchozí [filtrování](webjobs-sdk-how-to.md#log-filtering); všechny informace a vyšší úrovně protokoly nyní přejde na konzole i Application Insights když spouštíte místně. 
   * Převádí `LoggerFactory` objekt v `using` bloku zajistit, že výstup protokolu vyprazdňuje při ukončení hostitele. 

## <a name="test-application-insights-logging"></a>Test Application Insights protokolování

V této části můžete spustit místně znovu ověřit, že data protokolování bude také Application Insights, konzole.

1. Použití **Průzkumníka serveru** Pokud chcete vytvořit zprávu fronty, stejně jako jste to udělali [starší](#trigger-the-function), s výjimkou zadejte *Hello App Insights!* jako text zprávy.

1. Spusťte projekt.

   Sada WebJobs SDK zpracovává zprávy ve frontě a najdete v souborech protokolů v okně konzoly.

1. Zavřete okno konzoly.

1. Otevřete [portál Azure](https://portal.azure.com/)a přejděte k prostředku Application Insights.

2. Vyberte **vyhledávání**.

   ![Vyberte vyhledávání](./media/webjobs-sdk-get-started/select-search.png)

1. Pokud nevidíte *Hello App Insights!* zpráva, vyberte **aktualizovat** pravidelně pro několik minut. (Protokoly nezobrazí okamžitě vzhledem k tomu, jak dlouho trvá chvíli klienta Application Insights k vyprázdnění protokoly zpracovává.)

   ![Protokoly ve službě Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

3. Zavřete okno konzoly.

## <a name="deploy-as-a-webjob"></a>Nasadit jako webovou úlohu

V této části nasadíte projekt jako webová. Nasazení do aplikace služby App Service, které [vytvořený](#create-app-service-app-and-application-insights-instance). K testování kódu při spuštění v Azure, spustíte volání funkce vytvořením zprávy fronty.

1. V **Průzkumníku řešení**, klikněte pravým tlačítkem na projekt a pak vyberte **publikovat jako webová úloha Azure**.

1. V **přidat webové úlohy Azure** dialogovém okně, vyberte **OK**.

   ![Přidat webová úloha Azure](./media/webjobs-sdk-get-started/add-azure-webjob.png)

   Visual Studio automaticky nainstaluje balíček NuGet pro publikování webové úlohy.

1. V **profil** krok **publikovat** průvodci vyberte **Microsoft Azure App Service**.

   ![Dialogové okno publikování](./media/webjobs-sdk-get-started/publish-dialog.png)

1. V **služby App Service** dialogovém okně, vyberte **vaší skupiny prostředků > aplikaci aplikační služby**a potom vyberte **OK**.

   ![Dialogovém okně App Service](./media/webjobs-sdk-get-started/app-service-dialog.png)

1. V **připojení** krok v průvodci vyberte **publikovat**.

## <a name="trigger-the-function-in-azure"></a>Aktivovat funkci v Azure

1. Kontrola, zda nejsou spuštěny místně (zavřete okno konzole, pokud je stále otevřen). V opačném případě místní instance může být první zpracovat všechny zprávy fronty, které vytvoříte.

1. Použití **Průzkumníka serveru** Pokud chcete vytvořit zprávu fronty, stejně jako jste to udělali [starší](#trigger-the-function), s výjimkou zadejte *zdravím Azure!*.

7. Obnovit **fronty** stránka v sadě Visual Studio a novou zprávu zmizela, protože funkci spouštění v Azure App Service je zpracován.

   > [!TIP]
   > Pokud testujete v Azure, použijte [režimu pro vývoj](webjobs-sdk-how-to.md#jobhost-development-settings) Ujistěte se, že funkce frontě aktivační události je vyvolán hned a zamezit tak zpoždění kvůli [dotazování exponenciálního omezení rychlosti fronty](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Zobrazit protokoly ve službě Application Insights

1. Otevřete [portál Azure](https://portal.azure.com/)a přejděte k prostředku Application Insights.

2. Vyberte **vyhledávání**.

1. Pokud nevidíte *zdravím Azure!* zpráva, vyberte **aktualizovat** pravidelně pro několik minut.

   Najdete v souborech protokolů z funkce spuštěné v webové úlohy, včetně *zdravím Azure!* text, který jste zadali v předchozím oddílu.

## <a name="add-an-input-binding"></a>Přidat vazbu vstupní

Vstupní vazby zjednodušit kód, který čte data. V tomto příkladu zprávy ve frontě budou název objektu blob a název objektu blob budete používat k vyhledání a čtení objektů blob v Azure Storage.

1. V *Functions.cs*, nahraďte `ProcessQueueMessage` metoda následujícím kódem:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   V tomto kódu `queueTrigger` je [vazby výraz](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns), což znamená, že přeloží na jinou hodnotu za běhu.  V době běhu má obsah zprávy ve frontě.

2. Přidat `using`:

   ```cs
   using System.IO;
   ```

3. Vytvořte kontejner objektů blob v účtu úložiště.

   a. V **Průzkumníka serveru**, rozbalte uzel pro váš účet úložiště, klikněte pravým tlačítkem na **objekty BLOB**a potom vyberte **vytvořit kontejner objektů Blob**.

   b. V **vytvořit kontejner objektů Blob** dialogové okno, zadejte *kontejneru* jako název kontejneru a pak klikněte na tlačítko **OK**.

4. Nahrát *Program.cs* souboru do kontejneru objektů blob. (Tento soubor se zde slouží jako příklad; můžete nahrát libovolný textový soubor a vytvořit zprávu fronty s názvem souboru.)

   a. V **Průzkumníka serveru**, dvakrát klikněte na uzel pro kontejner, který jste právě vytvořili.

   b. V **kontejneru** vyberte **nahrát** tlačítko.

   ![Tlačítko Nahrát objekt BLOB](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Najděte a vyberte *Program.cs*a potom vyberte **OK**.

5. Vytvoření fronty zpráv ve frontě jste vytvořili dříve, s *Program.cs* jako text zprávy.

   ![Fronty zpráv Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

6. Spusťte projekt.

   Zprávy ve frontě aktivuje funkci, která pak přečte objekt blob a jeho délka protokoly. Výstup konzoly vypadá takto:

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>Přidání výstupní vazby

Výstup vazby zjednodušit kód, který zapisuje data. Tento příklad upravuje předchozímu vytvořením kopie objektu blob místo protokolování jeho velikost.

1. Nahraďte metodu `ProcessQueueMessage` následujícím kódem:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

5. Vytvořte další zprávu fronty s *Program.cs* jako text zprávy.

6. Spusťte projekt.

   Zprávy ve frontě aktivuje funkci, která pak přečte objekt blob, jeho délka protokoly a vytvoří nový objekt blob. Výstup konzoly je stejné, ale když přejít do okna kontejner objektů blob a vyberete **aktualizovat**, najdete v části s názvem nového objektu blob *kopie Program.cs.*

## <a name="next-steps"></a>Další postup

Tato příručka ukazuje, jak vytvořit, spuštění a nasazení projektu WebJobs SDK.

Zobrazit všechny položky, která přejde do projektu WebJobs SDK, měl pokynů můžete vytvořit projekt od začátku. Ale při vytváření projektu další uvažujte o **webové úlohy Azure** šablony v **cloudu** kategorie. Tato šablona vytvoří projekt se balíčky NuGet a ukázkový kód už nastavili. Všimněte si, že ukázkový kód muset změnit tak, aby pomocí nového rozhraní protokolování.

Další informace najdete v tématu [jak používat sadu WebJobs SDK](webjobs-sdk-how-to.md).
