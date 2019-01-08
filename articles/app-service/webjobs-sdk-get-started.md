---
title: Začínáme se sadou WebJobs SDK – Azure
description: Úvod do sady SDK pro WebJobs pro zpracování na pozadí založený na událostech. Zjistěte, jak získat přístup k datům v Azure služby a služby třetích stran.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: glenga
ms.openlocfilehash: d9f3685e0a3961631f1577827cc415cb91d38a15
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54074236"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Začínáme se sadou Azure WebJobs SDK pro zpracování na pozadí založený na událostech

Tento článek ukazuje, jak vytvořit projekt sady Azure WebJobs SDK, ji spustit místně a nasaďte ho do služby Azure App Service.

Podle pokynů [Visual Studio 2017](https://www.visualstudio.com/vs/), ale stejné úkoly lze provést pomocí jiných nástrojů, jako například [Visual Studio Code](https://code.visualstudio.com/).

## <a name="what-is-the-azure-webjobs-sdk"></a>Co je Azure WebJobs SDK

Azure WebJobs SDK je architektura, která zjednodušuje úlohu psaní kódu pro zpracování na pozadí, který přistupuje k datům ve službách Azure. Sada SDK nabízí deklarativní syntaxe pro zadání události, které by měly aktivovat funkci, jako je například nové zprávy do fronty přidá. Podobně jako deklarativní syntaxe řídí čtení a zápis dat po aktivaci funkce. Tento systém triggerů a vazeb se postará o většinu nízké úrovně kódovací úlohy spojené s přístupem k Azure a služby třetích stran.

### <a name="functions-triggers-and-bindings"></a>Funkce, aktivační události a vazby

Sada WebJobs SDK projektu definuje jeden nebo více *funkce*. Funkce je metoda, která má atribut aktivační události v podpisu metody. Aktivační události určete podmínky pro volání funkce a vazby určují, jak číst a zapisovat. Například aktivační událost atribut v následující funkci oznamuje modul runtime pro volání funkce pokaždé, když se zobrazí zpráva fronty v `items` fronty. `Blob` Atribut oznamuje modul runtime pro použití fronty zpráv ke čtení objektů blob v *pracovní položky* kontejneru. Obsah zprávy fronty &mdash; součástí `queueTrigger` parametr &mdash; je název objektu blob.

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

Podle pokynů zjistit, jak vytvořit projekt sady WebJobs SDK verze 2.x. Nejnovější verzi sady SDK pro WebJobs je 3.x, ale je aktuálně ve verzi preview a v tomto článku ještě nemá pokyny pro tuto verzi. Hlavní změny zavedené ve verzi 3.x je použití .NET Core namísto rozhraní .NET Framework.

### <a name="azure-functions"></a>Azure Functions

[Služba Azure Functions](../azure-functions/functions-overview.md) je založena na sadě WebJobs SDK, a když není potřeba přímo použití sady WebJobs SDK. Azure Functions používá 1.x sada WebJobs SDK 2.x. Další informace najdete v tématu [srovnání Azure Functions a WebJobs SDK](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte [účet Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a vyzkoušejte si [aplikace ve službě Azure App Service](overview.md). K dokončení kroků v tomto článku:

* [Instalace sady Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/) s **vývoj pro Azure** pracovního vytížení. Pokud už máte sadu Visual Studio, ale nemají úlohy, přidejte úlohu tak, že vyberete **nástroje > Získejte nástroje a funkce**.
* [Vytvoření aplikace služby App Service](app-service-web-get-started-dotnet-framework.md). Pokud ještě nemáte, můžete nasadit úlohy WebJob k, můžete použít, který místo vytvoření nového.

## <a name="create-a-project"></a>Vytvoření projektu

1. V sadě Visual Studio, vyberte **soubor > Nový projekt**.

2. Vyberte **klasická plocha Windows > aplikace konzoly (.NET Framework)**.

3. Pojmenujte projekt *WebJobsSDKSample*a pak vyberte **OK**.

   ![Dialogové okno Nový projekt](./media/webjobs-sdk-get-started/new-project.png)

## <a name="add-webjobs-nuget-package"></a>Přidání balíčku NuGet pro webové úlohy

1. Nainstalujte nejnovější verzi 2.x stabilní balíček NuGet `Microsoft.Azure.WebJobs`.
 
   Tady je **Konzola správce balíčků** příkaz pro verzi 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs -version 2.2.0
   ``` 

## <a name="create-the-jobhost"></a>Vytvořte JobHost

`JobHost` Objekt je kontejner modulu runtime pro funkce: naslouchá aktivačních událostí a volání funkce. 

1. V *Program.cs*, přidejte `using` – příkaz:

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

## <a name="enable-console-logging"></a>Povolení protokolování konzoly

Existuje několik možností pro protokolování v projektu sady WebJobs SDK. Doporučujeme, abyste je jeden [protokolovacího rozhraní, který byl vyvinut pro ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging). Tento systém nabízí lepší výkon a větší flexibilitu v úložných médií a filtrování. 

V této části nastavíte konzoly protokolování, který používá novou architekturu.

1. Nainstalujte nejnovější stabilní verze následující balíčky NuGet:

   * `Microsoft.Extensions.Logging` -Protokolovacího rozhraní.
   * `Microsoft.Extensions.Logging.Console` -Konzole *poskytovatele*. Protokoly pro konkrétní cíl, zprostředkovatele v tomto případě odešle do konzoly. 
 
   Tady jsou **Konzola správce balíčků** příkazy pro verze 2.0.1:

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.0.1
   ``` 

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
   ``` 

1. V *Program.cs*, přidejte `using` – příkaz:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. V `Main` metodu, přidejte kód pro aktualizaci `JobHostConfiguration` před vytvořením `JobHost`:
 
   ```
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   ```

   Tento kód provede následující změny:

   * Zakáže [řídicí panel protokolování](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Řídicí panel je starší verze nástroj pro sledování a protokolování řídicí panel se nedoporučuje pro vysoce propustné produkční scénáře.
   * Přidá zprostředkovatele konzoly s výchozí [filtrování](webjobs-sdk-how-to.md#log-filtering). 

   `Main` Metoda nyní vypadá takto:

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

   `QueueTrigger` Atribut oznamuje modul runtime tuto funkci volat při zápisu novou zprávu ve frontě služby Azure Storage volá `queue`. Obsah zprávy fronty jsou k dispozici metoda kódu v `message` parametru. Tělo metody je, kde můžete zpracovávat data aktivační události. V tomto příkladu pouze kód zaprotokoluje zprávu.

   `message` Nemá parametr bude řetězec. Můžete také navázat na objekt JSON s polem bajtů nebo [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) objektu. [Zobrazit využití aktivační událost fronty](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Každý typ vazby (například fronty, objekty BLOB nebo tabulky) má jinou sadu typů parametrů polí, které lze svázat.

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Emulátor úložiště Azure, na kterém běží místně nemá všechny funkce, které vyžaduje služba WebJobs SDK. V této části tak vytvořit účet úložiště v Azure a nakonfigurujte projekt tak, aby ho použít.

1. Otevřít **Průzkumníka serveru** ve Visual Studiu a přihlášení do Azure. Klikněte pravým tlačítkem myši **Azure** uzlu a pak vyberte **připojit k předplatnému Microsoft Azure**.

   ![Přihlásit se k Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. V části **Azure** uzel v **Průzkumníka serveru**, klikněte pravým tlačítkem na **úložiště**a pak vyberte **účet úložiště vytvořit**.

   ![Vytvořit nabídku účtu úložiště](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. V **vytvořit účet úložiště** dialogového okna zadejte jedinečný název pro účet úložiště.

1. Vyberte stejné **oblasti** , že jste vytvořili aplikaci služby App Service v nebo oblast blízko vás.

1. Vyberte **Vytvořit**.

   ![Vytvoření účtu úložiště](./media/webjobs-sdk-get-started/create-storage-account.png)

1. V části **úložiště** uzel v **Průzkumníka serveru**, vyberte nový účet úložiště. V **vlastnosti** okna, vyberte tři tečky (**...** ) na pravé straně **připojovací řetězec** pole hodnoty.

   ![Připojovací řetězec tlačítko se třemi tečkami](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Zkopírujte připojovací řetězec a uloží tuto hodnotu někde, můžete zkopírovat snadno znovu.

   ![Zkopírujte připojovací řetězec](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-for-running-locally"></a>Konfigurace úložiště pro místní spuštění

Sada WebJobs SDK hledá připojovací řetězec úložiště v kolekci nastavení aplikace. Když spouštíte místně, hledá tuto hodnotu v *App.config* proměnné souboru nebo prostředí.

1. Přidejte následující kód XML do *App.config* soubor ihned po otevření `<configuration>` značky.

   ```xml
   <connectionStrings>
     <add name="AzureWebJobsStorage" connectionString="{storage connection string}" />
   </connectionStrings>
   ```

1. Nahraďte *{připojovací řetězec úložiště}* připojovacím řetězcem, který jste si zkopírovali dříve.

   Později budete chtít připojovací řetězec znovu použít, když konfigurujete aplikace služby App Service v Azure.

## <a name="test-locally"></a>Místní test

V této části sestavení a spusťte projekt lokálně a aktivovat funkci tak, že vytvoříte zprávy fronty.

1. Stiskněte kombinaci kláves Ctrl + F5 ke spuštění projektu.

   Konzole ukazuje, že modul runtime najít vaši funkci a čeká na zprávy fronty k aktivaci ho.

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

   Může zobrazit zpráva s upozorněním `ServicePointManager` nastavení. Pro testování, které budete dělat s tímto projektem, můžete upozornění ignorovat. Další informace o upozornění najdete v tématu [způsob použití sady WebJobs SDK](webjobs-sdk-how-to.md#jobhost-servicepointmanager-settings).

1. Zavřete okno konzoly.

1. V **Průzkumníka serveru** v sadě Visual Studio, rozbalte uzel pro nový účet úložiště a pak klikněte pravým tlačítkem na **fronty**. 

1. Vyberte **vytvořit frontu**. 

1. Zadejte *fronty* jako název fronty a pak vyberte **OK**.

   ![Vytvořit frontu](./media/webjobs-sdk-get-started/create-queue.png)

1. Klikněte pravým tlačítkem na uzel pro novou frontu a pak vyberte **Zobrazit frontu**.

1. Vyberte **přidat zprávu** ikonu.

   ![Vytvořit frontu](./media/webjobs-sdk-get-started/create-queue-message.png)

1. V **přidat zprávu** dialogové okno, zadejte *Hello World!* jako **text zprávy**a pak vyberte **OK**.

   ![Vytvořit frontu](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Spusťte projekt znovu.

   Protože jste použili `QueueTrigger` atribut `ProcessQueueMessage` funkce, WeJobs sady SDK modulu runtime naslouchá frontě zpráv při spuštění. Najde nové zprávy fronty ve frontě s názvem *fronty* a volá funkci.

   Z důvodu [dotazování exponenciálního omezení rychlosti fronty](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm), může trvat až 2 minuty, než modul runtime vyhledat zprávu a vyvolat funkci. Tato čekací doba může snížit spuštěním v [vývojový režim](webjobs-sdk-how-to.md#jobhost-development-settings).

  Výstup konzoly by měl vypadat takto:

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

1. Zavřete okno konzoly.

## <a name="add-application-insights-logging"></a>Přidání protokolování Application Insights

Při spuštění projektu v Azure, nelze monitorovat spuštění funkce zobrazením výstup na konzole. Doporučujeme vám, že je řešení pro monitorování [Application Insights](../application-insights/app-insights-overview.md). Další informace najdete v tématu [monitorování Azure Functions](../azure-functions/functions-monitoring.md).

V této části provedete následující úlohy k nastavení Application Insights protokolování před nasazením do Azure:

* Ujistěte se, že máte aplikaci služby App Service a instanci Application Insights pro práci s.
* Konfigurace aplikace App Service použít instanci Application Insights a účet úložiště, který jste vytvořili dříve.
* Nastavení projektu pro protokolování Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Vytvoření aplikace služby App Service a instanci Application Insights

1. Pokud ještě nemáte aplikaci služby App Service, které můžete použít, [vytvořit](app-service-web-get-started-dotnet-framework.md).

1. Pokud ještě nemáte prostředku Application Insights, které můžete použít, [vytvořit](../azure-monitor/app/create-new-resource.md ). Nastavte **typ aplikace** k **Obecné**a přeskočte následující části **zkopírujte klíč instrumentace**.

1. Pokud už máte prostředek Application Insights, který chcete použít, [zkopírujte klíč instrumentace](../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Konfigurace nastavení aplikace 

1. V **Průzkumníka serveru** v sadě Visual Studio, rozbalte **služby App Service** pod uzlem **Azure**.

1. Rozbalte skupinu prostředků, které vaše aplikace služby App Service je v a pak klikněte pravým tlačítkem na aplikaci služby App Service.

1. Vyberte **zobrazit nastavení**.

1. V **připojovací řetězce** pole, přidejte následující položku.

   |Název  |připojovací řetězec  |Typ databáze|
   |---------|---------|------|
   |AzureWebJobsStorage | {připojovací řetězec úložiště, který jste si zkopírovali dříve}|Vlastní|
   
1. Pokud **nastavení aplikace** pole nemá Application Insights Instrumentační klíč, přidejte ten, který jste si zkopírovali dříve. (Instrumentační klíč již pravděpodobně existuje, v závislosti na tom, jak vytvořit aplikace služby App Service.)

   |Název  |Hodnota  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {Instrumentační klíč} |

1. Nahraďte *{Instrumentační klíč}* s instrumentačním klíčem z prostředku Application Insights, který používáte.

1. Vyberte **Uložit**.

1. Přidejte následující kód XML do *App.config* soubor ihned po dokončení shromažďování řetězce připojení.

   ```xml
   <appSettings>
     <add key="APPINSIGHTS_INSTRUMENTATIONKEY" value="{instrumentation key}" />
   </appSettings>
   ```

1. Nahraďte *{Instrumentační klíč}* s instrumentačním klíčem z prostředku Application Insights, který používáte.

   Přidání těchto dat *App.config* soubor umožňuje otestovat připojení Application Insights, když místně spusťte projekt. 

1. Uložte provedené změny.

### <a name="add-application-insights-logging-provider"></a>Přidat zprostředkovatele pro protokolování Application Insights

1. Nainstalujte nejnovější verzi 2.x stabilní balíčku NuGet pro Application Insights zprostředkovatele: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`.

   Tady je **Konzola správce balíčků** příkaz pro verzi 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
   ``` 

1. Nainstalujte nejnovější stabilní 4.x verzi balíčku NuGet pro .NET configuration Manageru: `System.Configuration.ConfigurationManager`.

   Tady je **Konzola správce balíčků** příkaz verze 4.4.1:

   ```powershell
   Install-Package System.Configuration.ConfigurationManager -version 4.4.1
   ``` 

1. Otevřít *Program.cs* a přidejte `using` příkaz pro configuration manager:

   ```csharp
   using System.Configuration;
   ```

1. Nahraďte kód v `Main` metodu s následujícím kódem:

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

   Tento kód provede následující změny:

   * Přidá poskytovatele protokolování Application Insights s výchozí [filtrování](webjobs-sdk-how-to.md#log-filtering); všechny informace a vyšší úrovně protokoly nyní přejde do konzoly i Application Insights když používáte místně. 
   * Vloží `LoggerFactory` objekt `using` blok k Ujistěte se, že výstup protokolu vyprázdní při ukončení hostitele. 

## <a name="test-application-insights-logging"></a>Protokolování testů Application Insights

V této části spustíte místně znovu ověřte, že data protokolování teď bude také Application Insights jde o konzole.

1. Použití **Průzkumníka serveru** v sadě Visual Studio k vytvoření zprávy fronty, stejně jako jste to udělali [starší](#trigger-the-function-in-azure)s výjimkou případů, zadejte *Hello App Insights!* jako text zprávy.

1. Spusťte projekt.

   Sada WebJobs SDK zpracovává zprávy fronty a naleznete v protokolech v okně konzoly.

1. Zavřete okno konzoly.

1. Otevřít [webu Azure portal](https://portal.azure.com/)a přejděte do prostředku Application Insights.

1. Vyberte **hledání**.

   ![Vyberte hledání](./media/webjobs-sdk-get-started/select-search.png)

1. Pokud se nezobrazí *Hello App Insights!* zpráva, vyberte **aktualizovat** pravidelně pro několik minut. (Protokoly nezobrazí okamžitě, protože to chvíli trvat, než klient Application Insights k vyprázdnění protokolů, které zpracovává.)

   ![Protokoly ve službě Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Zavřete okno konzoly.

## <a name="deploy-as-a-webjob"></a>Nasazení jako webové úlohy

V této části nasadíte projekt jako webová úloha. Nasazení do aplikace služby App Service, kterou jste [vytvořili](#create-app-service-app-and-application-insights-instance). K testování kódu při spuštění v Azure, spustíte vyvolání funkce tak, že vytvoříte zprávy fronty.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a pak vyberte **publikovat jako Azure WebJob**.

1. V **přidat webovou úlohu Azure** dialogového okna, vyberte **OK**.

   ![Přidat úlohu WebJob Azure](./media/webjobs-sdk-get-started/add-azure-webjob.png)

   Visual Studio automaticky nainstaluje balíček NuGet pro publikování webové úlohy.

1. V **profilu** kroku **publikovat** průvodce, vyberte **Microsoft Azure App Service**.

   ![Dialogové okno pro publikování](./media/webjobs-sdk-get-started/publish-dialog.png)

1. V **služby App Service** dialogového okna, vyberte **vaší skupiny prostředků > aplikace app Service**a pak vyberte **OK**.

   ![Dialogové okno služby App Service](./media/webjobs-sdk-get-started/app-service-dialog.png)

1. V **připojení** kroku v průvodci vyberte **publikovat**.

## <a name="trigger-the-function-in-azure"></a>Aktivovat funkci v Azure

1. Ujistěte se, že nejsou spuštěné místně (Pokud je stále otevřen. Zavřete okno konzoly). V opačném případě místní instance může být první zpracovávat všechny zprávy fronty, které vytvoříte.


1. Aktualizovat **fronty** stránky ve Visual Studiu a nová zpráva zmizela vzhledem k tomu, že ji zpracovat funkce spuštěné v Azure App Service.

   > [!TIP]
   > Při testování v Azure, použijte [vývojový režim](webjobs-sdk-how-to.md#jobhost-development-settings) Ujistěte se, že je funkce pro aktivaci fronty okamžitě vyvolána a vyhnout se zpožděním kvůli [dotazování exponenciálního omezení rychlosti fronty](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Zobrazit protokoly ve službě Application Insights

1. Otevřít [webu Azure portal](https://portal.azure.com/)a přejděte do prostředku Application Insights.

1. Vyberte **hledání**.

1. Pokud se nezobrazí *Hello Azure!* zpráva, vyberte **aktualizovat** pravidelně pro několik minut.

   Můžete si zobrazit protokoly z funkce spuštěný ve webové úloze, včetně *Hello Azure!* text, který jste zadali v předchozím oddílu.

## <a name="add-an-input-binding"></a>Přidat vstupní vazby

Vstupní vazby zjednodušit kód, který čte data. V tomto příkladu zpráva fronty bude název objektu blob a název objektu blob budete používat k hledání a čtení objektů blob ve službě Azure Storage.

1. V *Functions.cs*, nahraďte `ProcessQueueMessage` metodu s následujícím kódem:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   V tomto kódu `queueTrigger` je [vazby výraz](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns), což znamená, že se překládá na jinou hodnotu za běhu.  Za běhu má obsah zprávy fronty.

1. Přidat `using`:

   ```cs
   using System.IO;
   ```

1. Vytvořte kontejner objektů blob v účtu úložiště.

   a. V **Průzkumníka serveru** v sadě Visual Studio, rozbalte uzel pro váš účet úložiště, klikněte pravým tlačítkem na **objekty BLOB**a pak vyberte **vytvořit kontejner objektů Blob**.

   b. V **vytvořit kontejner objektů Blob** dialogové okno, zadejte *kontejneru* jako název kontejneru a pak klikněte na tlačítko **OK**.

1. Nahrát *Program.cs* souboru do kontejneru objektů blob. (Tento soubor se zde slouží jako příklad; můžete nahrát libovolný textový soubor a vytvoření zprávy fronty s názvem souboru.)

   a. V **Průzkumníka serveru**, dvakrát klikněte na uzel kontejneru, který jste právě vytvořili.

   b. V **kontejneru** okna, vyberte **nahrát** tlačítko.

   ![Tlačítko Nahrát objekt BLOB](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Vyhledejte a vyberte *Program.cs*a pak vyberte **OK**.

1. Vytvoření zprávy fronty ve frontě, které jste vytvořili dříve, pomocí *Program.cs* jako text zprávy.

   ![Fronta zpráv Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Spusťte projekt.

   Zpráva fronty aktivuje funkci, která pak přečte objekt blob a jeho délka protokoly. Výstup konzoly by měl vypadat takto:

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

Výstupní vazby zjednodušit kód, který zapisuje data. Tento příklad upravuje předchozí napsáním kopírování objektu blob místo protokolování jeho velikost.

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

1. Vytvořit jiný fronta zpráv s *Program.cs* jako text zprávy.

1. Spusťte projekt.

   Queue zpráva aktivuje funkci, která pak načte objekt blob, jeho délka protokoly a vytvoří nový objekt blob. Výstup konzoly je stejný, ale když přejdete do okna kontejneru objektů blob a vyberete **aktualizovat**, uvidíte nový objekt blob s názvem *kopírování souboru Program.cs.*

## <a name="next-steps"></a>Další postup

Tato příručka ukazuje postup vytvoření, spuštění a nasazení projektu sady WebJobs SDK.

Chcete-li zobrazit všechno, co se přejde do projektu sady WebJobs SDK, měl pokynů můžete vytvořit projekt od začátku. Nicméně, když vytvoříte svůj další projekt, zvažte **Azure WebJob** šablony v **cloudu** kategorie. Tato šablona vytvoří projekt s balíčky NuGet a ukázkový kód už nastavili. Všimněte si, že ukázkový kód muset změnit na novou architekturu protokolování.

Další informace najdete v tématu [způsob použití sady WebJobs SDK](webjobs-sdk-how-to.md).
