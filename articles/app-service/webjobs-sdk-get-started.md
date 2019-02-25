---
title: Začínáme se sadou WebJobs SDK – Azure
description: Úvod do sady SDK pro WebJobs pro zpracování na pozadí založený na událostech. Zjistěte, jak získat přístup k datům v Azure služby a služby třetích stran.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 6b3318ced4c6e40b80172b19d7f664c53e5569cc
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750629"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Začínáme se sadou Azure WebJobs SDK pro zpracování na pozadí založený na událostech

Tento článek popisuje, jak používat Visual Studio 2017 k vytvoření projektu Azure WebJobs SDK, ji spustit místně a pak nasadit ji do [služby Azure App Service](overview.md). Je projekt vytvoříte konzolovou aplikaci .NET Core, který používá verzi 3.x sada WebJobs SDK. Pokud vás zajímá verze 2.x, který používá rozhraní .NET Framework, viz [vývoj a nasazení WebJobs pomocí sady Visual Studio – služby Azure App Service](webjobs-dotnet-deploy-vs.md).

Další informace o práci se sadou WebJobs SDK najdete v tématu [použití sady Azure WebJobs SDK pro zpracování na pozadí založený na událostech](webjobs-sdk-how-to.md).

## <a name="prerequisites"></a>Požadavky

* [Instalace sady Visual Studio 2017](/visualstudio/install/) s **vývoj pro Azure** pracovního vytížení. Pokud už máte sadu Visual Studio, ale nemají úlohy, přidejte úlohu tak, že vyberete **nástroje > Získejte nástroje a funkce**.

* Musíte mít [účet Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) publikovat WebJobs SDK projektu do Azure.

## <a name="create-a-project"></a>Vytvoření projektu

1. V sadě Visual Studio, vyberte **soubor > Nový > projekt**.

2. Vyberte **.NET Core > aplikace (.NET Core) konzoly**.

3. Pojmenujte projekt *WebJobsSDKSample*a pak vyberte **OK**.

   ![Dialogové okno Nový projekt](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>Balíčky NuGet pro webové úlohy

1. Nainstalujte nejnovější stabilní 3.x verze následující balíčky NuGet:

    * `Microsoft.Azure.WebJobs`
    * `Microsoft.Azure.WebJobs.Extensions`

    Tady je **Konzola správce balíčků** příkazy pro verzi 3.0.4:

    ```powershell
    Install-Package Microsoft.Azure.WebJobs -version 3.0.4
    Install-Package Microsoft.Azure.WebJobs.Extensions -version 3.0.1
    ```

## <a name="create-the-host"></a>Vytvořte hostitele

Hostitel je kontejner modulu runtime pro funkce, která naslouchá aktivačních událostí a volání funkce. Následující kroky slouží k vytvoření hostitele, který implementuje [ `IHost` ](/dotnet/api/microsoft.extensions.hosting.ihost), což je obecný hostitele v ASP.NET Core.

1. V *Program.cs*, přidejte `using` – příkaz:

    ```cs
    using Microsoft.Extensions.Hosting;
    ```

1. Nahraďte metodu `Main` následujícím kódem:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

V ASP.NET Core, jsou nastaveny konfigurace hostitele pomocí volání metody na [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) instance. Další informace najdete v tématu [obecný hostitele .NET](/aspnet/core/fundamentals/host/generic-host). `ConfigureWebJobs` Rozšiřující metoda inicializuje hostitele WebJobs. V `ConfigureWebJobs`, inicializovat konkrétní rozšíření WebJobs a nastavit vlastnosti těchto rozšíření.  

## <a name="enable-console-logging"></a>Povolení protokolování konzoly

V této části nastavíte konzoly protokolování, který používá [protokolovacího rozhraní ASP.NET Core](/aspnet/core/fundamentals/logging).

1. Nainstalujte nejnovější stabilní verze následující balíčky NuGet:

   * `Microsoft.Extensions.Logging` -Protokolovacího rozhraní.
   * `Microsoft.Extensions.Logging.Console` -Konzola zprostředkovateli, který posílá protokoly do konzoly.

   Tady jsou **Konzola správce balíčků** příkazy pro verzi 2.2.0:

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.2.0
   ```

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.2.0
   ```

1. V *Program.cs*, přidejte `using` – příkaz:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. Volání [ `ConfigureLogging` ](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) metoda [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder). [ `AddConsole` ](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) Metoda přidáte ke konfiguraci protokolování konzoly.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    `Main` Metoda nyní vypadá takto:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

    Tato aktualizace provede následující akce:

    * Zakáže [řídicí panel protokolování](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Řídicí panel je starší verze nástroj pro sledování a protokolování řídicí panel se nedoporučuje pro vysoce propustné produkční scénáře.
    * Přidá zprostředkovatele konzoly s výchozí [filtrování](webjobs-sdk-how-to.md#log-filtering).

Teď můžete přidat funkci, která se aktivuje zpráv přicházejících do [fronty Azure Storage](../azure-functions/functions-bindings-storage-queue.md).

## <a name="install-the-storage-binding-extension"></a>Instalace rozšíření vazby služby Storage

Počínaje verzí 3.x, je nutné explicitně nainstalovat rozšíření vazby úložiště vyžadované sada WebJobs SDK. V předchozích verzích vazby úložiště byly součástí sady SDK.

1. Nainstalujte nejnovější stabilní verzi [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) balíčku NuGet, verze 3.x. 

    Tady je **Konzola správce balíčků** příkaz pro verzi 3.0.3:

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.3
    ```

2. V `ConfigureWebJobs` – metoda rozšíření, volání `AddAzureStorage` metodu [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) instance se inicializovat rozšíření úložiště. V tomto okamžiku `ConfigureWebJobs` metoda vypadá jako v následujícím příkladu:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Vytvoření funkce

1. Klikněte pravým tlačítkem na projekt, vyberte **přidat** > **novou položku...** , zvolte **třídy**, pojmenujte novou C# soubor třídy *Functions.cs*a vyberte **přidat**.

1. V Functions.cs Nahraďte vygenerovanou šablonu s následujícím kódem:

   ```cs
   using Microsoft.Azure.WebJobs;
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

Emulátor úložiště Azure, na kterém běží místně nemá všechny funkce, které vyžaduje služba WebJobs SDK. V této části tak vytvořit účet úložiště v Azure a nakonfigurujte projekt tak, aby ho použít. Pokud již máte účet úložiště, přejděte na krok 6.

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

## <a name="configure-storage-to-run-locally"></a>Konfigurace úložiště pro místní spuštění

Sada WebJobs SDK hledá připojovací řetězec úložiště v nastavení aplikace v Azure. Když spouštíte místně, hledá tuto hodnotu v místním konfiguračním souboru nebo v proměnné prostředí.

1. Klikněte pravým tlačítkem na projekt, vyberte **přidat** > **novou položku...** , zvolte **konfigurační soubor JSON pro JavaScript**, pojmenujte nový soubor *appsettings.json* a vyberte možnost **přidat**. 

1. V novém souboru, přidejte `AzureWebJobsStorage` pole, jako v následujícím příkladu:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Nahraďte *{připojovací řetězec úložiště}* připojovacím řetězcem, který jste si zkopírovali dříve.

1. Vyberte *appsettings.json* souboru v Průzkumníku řešení a **vlastnosti** okno, nastavte **kopírovat do výstupního adresáře** k **kopírovat, pokud je novější**.

Později přidáte stejný připojovací řetězec nastavení aplikace, které ve vaší aplikaci ve službě Azure App Service.

## <a name="test-locally"></a>Místní test

V této části sestavení a spusťte projekt lokálně a aktivovat funkci tak, že vytvoříte zprávy fronty.

1. Stisknutím klávesy **Ctrl + F5** spusťte projekt.

   Konzole ukazuje, že modul runtime najít vaši funkci a čeká na zprávy fronty k aktivaci ho. Hostitel v3.x vygeneruje následující výstup:

   ```console
    info: Microsoft.Azure.WebJobs.Hosting.JobHostService[0]
          Starting JobHost
    info: Host.Startup[0]
          Found the following functions:
          WebJobsSDKSample.Functions.ProcessQueueMessage

    info: Host.Startup[0]
          Job host started
    Application started. Press Ctrl+C to shut down.
    Hosting environment: Development
    Content root path: C:\WebJobsSDKSample\WebJobsSDKSample\bin\Debug\netcoreapp2.1\
   ```

1. Zavřete okno konzoly.

1. V **Průzkumníka serveru** v sadě Visual Studio, rozbalte uzel pro nový účet úložiště a pak klikněte pravým tlačítkem na **fronty**.

1. Vyberte **vytvořit frontu**.

1. Zadejte *fronty* jako název fronty a pak vyberte **OK**.

   ![Vytvořit frontu](./media/webjobs-sdk-get-started/create-queue.png)

1. Klikněte pravým tlačítkem na uzel pro novou frontu a pak vyberte **Zobrazit frontu**.

1. Vyberte **přidat zprávu** ikonu.

   ![Vytvořit frontu](./media/webjobs-sdk-get-started/create-queue-message.png)

1. V **přidat zprávu** dialogové okno, zadejte *Hello World!* jako **text zprávy**a pak vyberte **OK**. Je teď zprávu ve frontě.

   ![Vytvořit frontu](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Spusťte projekt znovu.

   Protože jste použili `QueueTrigger` atribut `ProcessQueueMessage` funkce, WeJobs sady SDK modulu runtime naslouchá frontě zpráv při spuštění. Najde nové zprávy fronty ve frontě s názvem *fronty* a volá funkci.

   Z důvodu [dotazování exponenciálního omezení rychlosti fronty](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm), může trvat až 2 minuty, než modul runtime vyhledat zprávu a vyvolat funkci. Tato čekací doba může snížit spuštěním v [vývojový režim](webjobs-sdk-how-to.md#host-development-settings).

  Výstup konzoly by měl vypadat takto:

   ```console
    info: Function.ProcessQueueMessage[0]
          Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=2c319369-d381-43f3-aedf-ff538a4209b8)
    info: Function.ProcessQueueMessage[0]
          Trigger Details: MessageId: b00a86dc-298d-4cd2-811f-98ec39545539, DequeueCount: 1, InsertionTime: 1/18/2019 3:28:51 AM +00:00
    info: Function.ProcessQueueMessage.User[0]
          Hello World!
    info: Function.ProcessQueueMessage[0]
          Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=2c319369-d381-43f3-aedf-ff538a4209b8)
   ```

1. Zavřete okno konzoly. 

1. Vraťte se do okna fronty a obnovovat je. Zpráva je pryč, protože byla zpracována vaše funkce spuštěná místně. 

## <a name="add-application-insights-logging"></a>Přidání protokolování Application Insights

Při spuštění projektu v Azure, nelze monitorovat spuštění funkce zobrazením výstup na konzole. Doporučujeme vám, že je řešení pro monitorování [Application Insights](../azure-monitor/app/app-insights-overview.md). Další informace najdete v tématu [monitorování Azure Functions](../azure-functions/functions-monitoring.md).

V této části provedete následující úlohy k nastavení Application Insights protokolování před nasazením do Azure:

* Ujistěte se, že máte aplikaci služby App Service a instanci Application Insights pro práci s.
* Konfigurace aplikace App Service použít instanci Application Insights a účet úložiště, který jste vytvořili dříve.
* Nastavení projektu pro protokolování Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Vytvoření aplikace služby App Service a instanci Application Insights

1. Pokud ještě nemáte aplikaci služby App Service, které můžete použít, [vytvořit](app-service-web-get-started-dotnet-framework.md). Při vytváření vaší aplikace můžete také vytvořit propojené prostředku Application Insights. Pokud to provedete, `APPINSIGHTS_INSTRUMENTATIONKEY` nastavený pro vás ve vaší aplikaci.

1. Pokud ještě nemáte prostředku Application Insights, které můžete použít, [vytvořit](../azure-monitor/app/create-new-resource.md ). Nastavte **typ aplikace** k **Obecné**a přeskočte následující části **zkopírujte klíč instrumentace**.

1. Pokud už máte prostředek Application Insights, který chcete použít, [zkopírujte klíč instrumentace](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

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

1. Přidáte připojení k Application Insights do projektu, takže můžete spustit místně. V *appsettings.json* přidejte `APPINSIGHTS_INSTRUMENTATIONKEY` pole, jako v následujícím příkladu:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    Nahraďte *{Instrumentační klíč}* s instrumentačním klíčem z prostředku Application Insights, který používáte.

1. Uložte provedené změny.

### <a name="add-application-insights-logging-provider"></a>Přidat zprostředkovatele pro protokolování Application Insights

Abyste mohli využívat [Application Insights](../azure-monitor/app/app-insights-overview.md) protokolování, aktualizujte svůj kód protokolování můžete provádět následující:

* Přidání poskytovatele protokolování Application Insights s výchozí [filtrování](webjobs-sdk-how-to.md#log-filtering); všechny informace a vyšší úrovně protokoly přejde do konzoly i Application Insights používáte místně.
* Vložit `LoggerFactory` objekt `using` blok k Ujistěte se, že výstup protokolu vyprázdní při ukončení hostitele.

1. Nainstalujte nejnovější verzi 3.x stabilní verze balíčku NuGet pro Application Insights zprostředkovatele: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`.

   Tady je **Konzola správce balíčků** příkaz pro verzi 3.0.2:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version 3.0.2
   ```

1. Otevřít *Program.cs* a nahraďte kód v `Main` metodu s následujícím kódem:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                    b.AddAzureStorage();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();

                    // If the key exists in settings, use it to enable Application Insights.
                    string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                    if (!string.IsNullOrEmpty(instrumentationKey))
                    {
                        b.AddApplicationInsights(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

    To přidá poskytovatele Application Insights k protokolování pomocí klíče, které jste přidali dříve nastavení vaší aplikace.

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

## <a name="deploy-as-a-webjob"></a>Nasazení do Azure

Během nasazování vytvoříte instanci služby aplikace, ve kterém chcete spouštět funkce. Při publikování aplikace konzoly .NET Core do služby App Service v Azure se automaticky získá spouštět jako Webjobu. Další informace o publikování najdete v tématu [vývoj a nasazení WebJobs pomocí sady Visual Studio](webjobs-dotnet-deploy-vs.md).

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>Aktivovat funkci v Azure

1. Ujistěte se, že nejsou spuštěné místně (Pokud je stále otevřen. Zavřete okno konzoly). V opačném případě místní instance může být první zpracovávat všechny zprávy fronty, které vytvoříte.

1. V **fronty** stránce v sadě Visual Studio, stejně jako předtím přidejte zprávu do fronty.

1. Aktualizovat **fronty** stránky a nová zpráva zmizí vzhledem k tomu, že nezpracuje podle funkce spuštěné v Azure.

   > [!TIP]
   > Při testování v Azure, použijte [vývojový režim](webjobs-sdk-how-to.md#host-development-settings) Ujistěte se, že je funkce pro aktivaci fronty okamžitě vyvolána a vyhnout se zpožděním kvůli [dotazování exponenciálního omezení rychlosti fronty](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

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

   V tomto kódu `queueTrigger` je [vazby výraz](../azure-functions/functions-bindings-expressions-patterns.md), což znamená, že se překládá na jinou hodnotu za běhu.  Za běhu má obsah zprávy fronty.

1. Přidat `using`:

   ```cs
   using System.IO;
   ```

1. Vytvořte kontejner objektů blob v účtu úložiště.

   a. V **Průzkumníka serveru** v sadě Visual Studio, rozbalte uzel pro váš účet úložiště, klikněte pravým tlačítkem na **objekty BLOB**a pak vyberte **vytvořit kontejner objektů Blob**.

   b. V **vytvořit kontejner objektů Blob** dialogové okno, zadejte *kontejneru* jako název kontejneru a pak klikněte na tlačítko **OK**.

1. Nahrát *Program.cs* souboru do kontejneru objektů blob. (Tento soubor se zde slouží jako příklad; můžete nahrát libovolný textový soubor a vytvoření zprávy fronty s názvem souboru.)

   a. V **Průzkumníka serveru**, dvakrát klikněte na uzel kontejneru, který jste vytvořili.

   b. V **kontejneru** okna, vyberte **nahrát** tlačítko.

   ![Tlačítko Nahrát objekt BLOB](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Vyhledejte a vyberte *Program.cs*a pak vyberte **OK**.

1. Vytvoření zprávy fronty ve frontě, které jste vytvořili dříve, pomocí *Program.cs* jako text zprávy.

   ![Fronta zpráv Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Spusťte projekt lokálně.

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

Výstupní vazby zjednodušit kód, který zapisuje data. Tento příklad upravuje předchozí napsáním kopírování objektu blob místo protokolování jeho velikost. Vazby služby BLOB storage jsou součástí balíček rozšíření Azure Storage, který jsme dříve nainstalovali.

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

1. Spusťte projekt lokálně.

   Queue zpráva aktivuje funkci, která pak načte objekt blob, jeho délka protokoly a vytvoří nový objekt blob. Výstup konzoly je stejný, ale když přejdete do okna kontejneru objektů blob a vyberete **aktualizovat**, uvidíte nový objekt blob s názvem *kopírování souboru Program.cs.*

## <a name="republish-the-updates-to-azure"></a>Znovu publikujte aktualizace do Azure

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

1. V **publikovat** dialogového okna, ujistěte se, zda je vybrána aktuální profil a klikněte na tlačítko **publikovat**. Výsledky publikování jsou podrobně popsané na **výstup** okna.
 
1. Ověřte funkce v Azure můžete znovu po nahrání souboru do kontejneru objektů blob a přidání zprávy do fronty, která je název uloženého souboru. Zobrazí se zpráva odebírají z fronty a kopii souborů vytvořených v kontejneru objektů blob. 

## <a name="next-steps"></a>Další postup

Tento článek vám ukázal vytvoření, spuštění a nasazení projektu 3.x sada WebJobs SDK.

> [!div class="nextstepaction"]
> [Další informace o sadě WebJobs SDK](webjobs-sdk-how-to.md)
