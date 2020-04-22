---
title: Začínáme se sadou WebJobs SDK
description: Úvod do sady WebJobs SDK pro zpracování na pozadí řízené událostmi. Zjistěte, jak přistupovat k datům ve službách Azure a ve službách třetích stran.
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 4ee724ec66d5fb474f8c8a9a967cc7235fef5e85
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732624"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Začínáme s událostmi řízeným zpracováním na pozadí pomocí sady Azure WebJobs SDK

Tento článek ukazuje, jak pomocí Visual Studia 2019 vytvořit projekt Sady Azure WebJobs SDK, spustit ho místně a pak ho nasadit do [služby Azure App Service](overview.md). Verze 3.x sady WebJobs SDK podporuje konzolové aplikace .NET Core i .NET Framework. Další informace o práci s sadou WebJobs SDK najdete v [tématu Jak používat sadu Azure WebJobs SDK pro zpracování na pozadí řízené událostmi](webjobs-sdk-how-to.md).

Tento článek ukazuje, jak nasadit WebJobs jako konzolovou aplikaci .NET Core. Pokud chcete nasadit webové úlohy jako konzolovou aplikaci rozhraní .NET Framework, přečtěte si [webovou úlohu jako konzolové aplikace rozhraní .NET Framework](webjobs-dotnet-deploy-vs.md#webjobs-as-net-framework-console-apps). Pokud vás zajímá webjobs SDK verze 2.x, který podporuje jenom rozhraní .NET Framework, přečtěte si informace [o vývoji a nasazování webových úloh pomocí sady Visual Studio – Azure App Service](webjobs-dotnet-deploy-vs.md).

## <a name="prerequisites"></a>Požadavky

* [Nainstalujte Visual Studio 2019](/visualstudio/install/) s **úlohou vývoje Azure.** Pokud už máte Visual Studio, ale nemáte tuto úlohu, přidejte úlohu výběrem **nástroje > získat nástroje a funkce**.

* K publikování projektu WebJobs SDK do Azure musíte mít [účet Azure.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

## <a name="create-a-project"></a>Vytvoření projektu

1. V Sadě Visual Studio vyberte **Vytvořit nový projekt**.

2. Vyberte **konzolovou aplikaci (jádro.NET)**.

3. Pojmenujte projekt *WebJobsSDKSample*a pak vyberte **Vytvořit**.

   ![Dialogové okno Nový projekt](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>Balíčky WebJobs NuGet

1. Nainstalujte nejnovější stabilní verzi [ `Microsoft.Azure.WebJobs.Extensions` 3.x balíčku NuGet](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/), která obsahuje `Microsoft.Azure.WebJobs`.

     Tady je příkaz **Konzola správce balíčků:**

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version <3_X_VERSION>
     ```

    V tomto příkazu nahraďte `<3_X_VERSION>` podporovanou verzí balíčku. 

## <a name="create-the-host"></a>Vytvoření hostitele

Hostitel je runtime kontejner pro funkce, které naslouchá aktivační události a volání funkcí. Následující kroky vytvořit hostitele, [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost)který implementuje , což je obecný hostitel v ASP.NET jádra.

1. V *Program.cs*přidejte tyto `using` příkazy:

    ```cs
    using System.Threading.Tasks;
    using Microsoft.Extensions.Hosting;
    ```

1. Nahraďte metodu `Main` následujícím kódem:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

V ASP.NET Core jsou konfigurace hostitele nastaveny [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) voláním metod instance. Další informace naleznete [v tématu Obecný hostitel rozhraní .NET](/aspnet/core/fundamentals/host/generic-host). Metoda `ConfigureWebJobs` rozšíření inicializuje hostitele WebJobs. V `ConfigureWebJobs`aplikaci inicializujete konkrétní rozšíření WebJobs a nastavíte vlastnosti těchto rozšíření.  

## <a name="enable-console-logging"></a>Povolení protokolování konzoly

V této části nastavíte protokolování konzoly, která používá [ASP.NET core protokolování rozhraní](/aspnet/core/fundamentals/logging).

1. Nainstalujte nejnovější stabilní verzi [ `Microsoft.Extensions.Logging.Console` balíčku NuGet](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/), který obsahuje `Microsoft.Extensions.Logging`.

   Tady je příkaz **Konzola správce balíčků:**

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version <3_X_VERSION>
   ```

1. V *Program.cs*přidejte příkaz: `using`

   ```cs
   using Microsoft.Extensions.Logging;
   ```

    V tomto příkazu nahraďte `<3_X_VERSION>` podporovanou verzí balíčku 3.x.

1. Volání [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) metody [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)na . Metoda [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) přidá protokolování konzoly do konfigurace.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    Metoda `Main` nyní vypadá takto:

    ```cs
    static async Task Main()
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
            await host.RunAsync();
        }
    }
    ```

    Tato aktualizace provádí následující akce:

    * Zakáže [protokolování řídicího panelu](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Řídicí panel je starší nástroj pro monitorování a protokolování řídicího panelu se nedoporučuje pro scénáře výroby s vysokou propustností.
    * Přidá zprostředkovatele konzoly s výchozím [filtrováním](webjobs-sdk-how-to.md#log-filtering).

Teď můžete přidat funkci, která se aktivuje zprávy přicházející do fronty Azure Storage.

## <a name="install-the-storage-binding-extension"></a>Instalace rozšíření vazby služby Storage

Počínaje verzí 3.x je nutné explicitně nainstalovat rozšíření vazby úložiště vyžadované sadou WebJobs SDK. V předchozích verzích storage vazby byly zahrnuty do sady SDK.

1. Nainstalujte nejnovější stabilní verzi balíčku [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet verze 3.x. 

    Tady je příkaz **Konzola správce balíčků:**

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version <3_X_VERSION>
    ```
    
    V tomto příkazu nahraďte `<3_X_VERSION>` podporovanou verzí balíčku. 

2. V `ConfigureWebJobs` metodě rozšíření `AddAzureStorage` volání metody [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) na instanci inicializovat rozšíření úložiště. V tomto okamžiku `ConfigureWebJobs` metoda vypadá jako následující příklad:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Vytvoření funkce

1. Klepněte pravým tlačítkem myši na projekt, vyberte **Přidat** > **novou položku...**, zvolte **Třídu**, pojmenujte nový soubor třídy C# *Functions.cs*a vyberte **Přidat**.

1. V Functions.cs nahraďte vygenerovanou šablonu následujícím kódem:
    
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

   Atribut `QueueTrigger` říká, že runtime volá tuto funkci, když je nová `queue`zpráva zapsána ve frontě Azure Storage s názvem . Obsah zprávy fronty jsou k dispozici kódu metody `message` v parametru. Tělo metody je místo, kde zpracováváte data aktivační události. V tomto příkladu kód pouze protokoluje zprávu.

   Parametr `message` nemusí být řetězec. Můžete také vytvořit vazbu na objekt JSON, bajtové pole nebo objekt [CloudQueueMessage.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) [Viz Využití aktivační události fronty](/azure/azure-functions/functions-bindings-storage-queue-trigger?tabs=csharp#usage). Každý typ vazby (například fronty, objekty BLOB nebo tabulky) má jinou sadu typů parametrů, které můžete vázat.

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Emulátor Úložiště Azure, který běží místně nemá všechny funkce, které webjobs SDK potřebuje. Takže v této části vytvoříte účet úložiště v Azure a nakonfigurujte projekt tak, aby ho používal. Pokud už účet úložiště máte, přejděte ke kroku 6.

1. Otevřete **Průzkumníka serveru** ve Visual studiu a přihlaste se do Azure. Klikněte pravým tlačítkem na uzel **Azure** a pak vyberte **Připojit k předplatnému Microsoft Azure**.

   ![Přihlášení k Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. V uzlu **Azure** v **Průzkumníkovi serveru**klikněte pravým tlačítkem myši na **Storage**a pak vyberte Vytvořit **účet úložiště**.

   ![Vytvořit nabídku účtu úložiště](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. V dialogovém okně **Vytvořit účet úložiště** zadejte jedinečný název účtu úložiště.

1. Zvolte stejnou **oblast,** ve které jste vytvořili aplikaci App Service, nebo oblast blízko vás.

1. Vyberte **Vytvořit**.

   ![Vytvořit účet úložiště](./media/webjobs-sdk-get-started/create-storage-account.png)

1. V části Uzel **úložiště** v **Průzkumníkovi serveru**vyberte nový účet úložiště. V okně **Vlastnosti** vyberte tři tečky (**...**) vpravo od pole hodnoty **připojovacího řetězce.**

   ![Připojovací řetězec tři tečky](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Zkopírujte připojovací řetězec a uložte tuto hodnotu někde, kde ji můžete zkopírovat znovu.

   ![Kopírovat připojovací řetězec](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>Konfigurace místního spouštění úložiště

Sada WebJobs SDK hledá připojovací řetězec úložiště v nastavení aplikace v Azure. Při spuštění místně hledá tuto hodnotu v místním konfiguračním souboru nebo v proměnných prostředí.

1. Klepněte pravým tlačítkem myši na projekt, vyberte **Přidat** > **novou položku...**, zvolte **konfigurační soubor JavaScript JSON**, pojmenujte nový soubor *appsettings.json* a vyberte **Přidat**. 

1. Do nového souboru `AzureWebJobsStorage` přidejte pole, jako v následujícím příkladu:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Nahraďte *{storage connection string}* připojovacím řetězcem, který jste zkopírovali dříve.

1. V Průzkumníkovi řešení vyberte soubor *appsettings.json* a v okně **Vlastnosti** nastavte **možnost Kopírovat do výstupního adresáře,** **pokud chcete kopírovat novější**.

Později přidáte stejné nastavení aplikace pro připojení řetězce ve vaší aplikaci ve službě Azure App Service.

## <a name="test-locally"></a>Testování místně

V této části vytvoříte a spustíte projekt místně a spustíte funkci vytvořením zprávy fronty.

1. Stisknutím **kláves Ctrl+F5** spusťte projekt.

   Konzole ukazuje, že runtime našel vaši funkci a čeká na zprávy fronty, které ji aktivují. Hostitel v3.x generuje následující výstup:

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

1. V **Průzkumníkovi serveru** v sadě Visual Studio rozbalte uzel pro nový účet úložiště a klikněte pravým tlačítkem myši na **položku Fronty**.

1. Vyberte **možnost Vytvořit frontu**.

1. Zadejte *frontu* jako název fronty a pak vyberte **OK**.

   ![Vytvořit frontu](./media/webjobs-sdk-get-started/create-queue.png)

1. Klepněte pravým tlačítkem myši na uzel nové fronty a potom vyberte **příkaz Zobrazit frontu**.

1. Vyberte ikonu **Přidat zprávu.**

   ![Vytvořit frontu](./media/webjobs-sdk-get-started/create-queue-message.png)

1. V dialogovém okně **Přidat zprávu** zadejte *Hello World!* jako **text Zprávy**a pak vyberte **OK**. Ve frontě je nyní zpráva.

   ![Vytvořit frontu](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Spusťte projekt znovu.

   Vzhledem k `QueueTrigger` tomu, `ProcessQueueMessage` že jste použili atribut ve funkci, za běhu Sady WeJobs SDK naslouchá zprávy fronty při spuštění. Najde novou zprávu fronty ve frontě s názvem *fronta* a zavolá funkci.

   Z důvodu [dotazování fronty exponenciální backoff](/azure/azure-functions/functions-bindings-storage-queue-trigger?tabs=csharp#polling-algorithm), může trvat až 2 minuty za běhu najít zprávu a vyvolat funkci. Tuto čekací dobu lze zkrátit spuštěním v [režimu vývoje](webjobs-sdk-how-to.md#host-development-settings).

   Výstup konzoly vypadá takto:

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

1. Vraťte se do okna Fronty a aktualizujte ho. Zpráva je pryč, protože byla zpracována vaší funkce spuštěna místně. 

## <a name="add-application-insights-logging"></a>Přidání protokolování přehledů aplikací

Když se projekt spustí v Azure, nemůžete sledovat spuštění funkce zobrazením výstupu konzoly. Monitorování řešení doporučujeme, je [Application Insights](../azure-monitor/app/app-insights-overview.md). Další informace naleznete v [tématu Sledování funkcí Azure](../azure-functions/functions-monitoring.md).

V této části můžete provést následující úkoly nastavit protokolování Application Insights před nasazením do Azure:

* Ujistěte se, že máte aplikaci App Service a instanci Application Insights, se kterou můžete pracovat.
* Nakonfigurujte aplikaci App Service tak, aby používala instanci Application Insights a účet úložiště, který jste vytvořili dříve.
* Nastavte projekt pro protokolování do Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Vytvoření aplikace služby App Service a instance Přehledy aplikací

1. Pokud ještě nemáte aplikaci App Service, kterou můžete použít, [vytvořte ji](app-service-web-get-started-dotnet-framework.md). Když vytvoříte aplikaci, můžete taky vytvořit připojený prostředek Application Insights. Když toto uděláte, `APPINSIGHTS_INSTRUMENTATIONKEY` je nastavena pro vás v aplikaci.

1. Pokud ještě nemáte prostředek Application Insights, který můžete použít, [vytvořte ho](../azure-monitor/app/create-new-resource.md ). Nastavte **typ aplikace** na **obecné**a přeskočte oddíly, které následují Za kopírovat **klíč instrumentace**.

1. Pokud již máte prostředek Application Insights, který chcete použít, [zkopírujte klíč instrumentace](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Konfigurace nastavení aplikace 

1. V **Průzkumníkovi serveru** v Visual Studiu rozbalte uzel **služby App Service** v části **Azure**.

1. Rozbalte skupinu prostředků, ve které se aplikace App Service nachází, a klikněte pravým tlačítkem myši na aplikaci App Service.

1. Vyberte **Zobrazit nastavení**.

1. Do pole **Připojovací řetězce** přidejte následující položku.

   |Název  |připojovací řetězec  |Typ databáze|
   |---------|---------|------|
   |AzureWebJobsStorage | {Připojovací řetězec úložiště, který jste zkopírovali dříve}.|Vlastní|

1. Pokud pole **Nastavení aplikace** nemá klíč instrumentace Application Insights, přidejte ten, který jste zkopírovali dříve. (Instrumentace klíč již může být tam, v závislosti na tom, jak jste vytvořili aplikaci App Service.)

   |Název  |Hodnota  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {instrumentační klíč} |

1. Nahraďte *{instrumentation key}* pomocí klíče instrumentace z prostředku Application Insights, který používáte.

1. Vyberte **Uložit**.

1. Přidejte připojení Application Insights k projektu, abyste ho mohli spustit místně. Do souboru *appsettings.json* `APPINSIGHTS_INSTRUMENTATIONKEY` přidejte pole, jako v následujícím příkladu:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    Nahraďte *{instrumentation key}* pomocí klíče instrumentace z prostředku Application Insights, který používáte.

1. Uložte provedené změny.

### <a name="add-application-insights-logging-provider"></a>Přidání zprostředkovatele protokolování Přehledy aplikací

Chcete-li využít využití protokolování [Application Insights,](../azure-monitor/app/app-insights-overview.md) aktualizujte kód protokolování takto:

* Přidejte zprostředkovatele protokolování Application Insights s výchozím [filtrováním](webjobs-sdk-how-to.md#log-filtering). Při spuštění místně, všechny informace a protokoly vyšší úrovně jsou zapsány do konzole a Application Insights.
* Vložte objekt [LoggerFactory](./webjobs-sdk-how-to.md#logging-and-monitoring) do `using` bloku, abyste zajistili, že výstup protokolu je vyprázdněn při ukončení hostitele.

1. Nainstalujte nejnovější stabilní verzi [ `Microsoft.Azure.WebJobs.Logging.ApplicationInsights` 3.x balíčku NuGet](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/).

   Tady je příkaz **Konzola správce balíčků:**

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <3_X_VERSION>
   ```
    V tomto příkazu nahraďte `<3_X_VERSION>` podporovanou verzí balíčku.

1. Otevřete *Program.cs* a nahraďte kód v metodě `Main` následujícím kódem:

    ```cs
    static async Task Main()
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
                        b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    Tím se do protokolování přidá poskytovatel Přehledy aplikací pomocí klíče, který jste přidali dříve do nastavení aplikace.

## <a name="test-application-insights-logging"></a>Protokolování protokolování přehledů aplikací

V této části znovu spustit místně ověřit, že data protokolování je nyní bude Application Insights, stejně jako do konzoly.

1. Pomocí **Průzkumníka serveru** v sadě Visual Studio můžete vytvořit zprávu fronty jako [dříve](#test-locally), kromě zadání *přehledů aplikací Hello!* jako text zprávy.

1. Spusťte projekt.

   Sada WebJobs SDK zpracuje zprávu fronty a v okně konzoly se zobrazí protokoly.

1. Zavřete okno konzoly.

1. Přejděte na [portál Azure](https://portal.azure.com/) a zobrazte si prostředek Application Insights. Vyhledejte a vyberte **Application Insights**.

1. Zvolte instanci Application Insights.

1. Vyberte **hledat**.

   ![Vybrat hledat](./media/webjobs-sdk-get-started/select-search.png)

1. Pokud nevidíte *Přehledy aplikací Hello!* zprávu, po několik minut pravidelně vyberte **Aktualizovat.** (Protokoly se nezobrazí okamžitě, protože chvíli trvá, než klient Application Insights vyprázdní protokoly, které zpracovává.)

   ![Protokoly v přehledech aplikací](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Zavřete okno konzoly.

## <a name="deploy-to-azure"></a><a name="deploy-as-a-webjob"></a>Nasazení do Azure

Během nasazení vytvoříte instanci služby aplikace, ve které chcete spouštět funkce. Když publikujete konzolovou aplikaci .NET Core do služby App Service v Azure, automaticky se spustí jako webová úloha. Další informace o publikování najdete v [tématu Vývoj a nasazení webových úloh pomocí sady Visual Studio](webjobs-dotnet-deploy-vs.md).

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>Spuštění funkce v Azure

1. Ujistěte se, že neběžíte místně (zavřete okno konzoly, pokud je stále otevřené). V opačném případě může být místní instance první, která zpracuje všechny zprávy fronty, které vytvoříte.

1. Na stránce **Fronta** v sadě Visual Studio přidejte zprávu do fronty jako předtím.

1. Aktualizujte stránku **Fronty** a nová zpráva zmizí, protože byla zpracována funkcí spuštěnou v Azure.

   > [!TIP]
   > Při testování v Azure, použijte režim vývoje, aby [zajistily,](webjobs-sdk-how-to.md#host-development-settings) že funkce aktivační událost fronty je vyvolána hned a vyhnout se zpoždění z důvodu [fronty dotazování exponenciální backoff](/azure/azure-functions/functions-bindings-storage-queue-trigger?tabs=csharp#polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Zobrazení protokolů v přehledech aplikací

1. Otevřete [portál Azure](https://portal.azure.com/)a přejděte na prostředek Application Insights.

1. Vyberte **hledat**.

1. Pokud nevidíte Hello *Azure!* zprávu, po několik minut pravidelně vyberte **Aktualizovat.**

   Zobrazí se protokoly z funkce spuštěné ve webové úloze, včetně *Hello Azure!* text, který jste zadali v předchozím oddíle.

## <a name="add-an-input-binding"></a>Přidání vstupní vazby

Vstupní vazby zjednodušit kód, který čte data. V tomto příkladu bude zpráva fronty název objektu blob a budete používat název objektu blob k vyhledání a čtení objektu blob ve službě Azure Storage.

1. V *Functions.cs*nahraďte metodu `ProcessQueueMessage` následujícím kódem:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   V tomto `queueTrigger` kódu je [výraz vazby](../azure-functions/functions-bindings-expressions-patterns.md), což znamená, že se překládá na jinou hodnotu za běhu.  Za běhu obsahuje obsah zprávy fronty.

1. Přidat `using`:

   ```cs
   using System.IO;
   ```

1. Vytvořte kontejner objektů blob ve vašem účtu úložiště.

   a. V **Průzkumníkovi serveru** v Sadě Visual Studio rozbalte uzel pro svůj účet úložiště, klikněte pravým tlačítkem myši na **objekty Blob**a vyberte **příkaz Vytvořit kontejner objektů blob**.

   b. V dialogovém okně **Vytvořit kontejner objektů blob** zadejte *kontejner* jako název kontejneru a klepněte na tlačítko **OK**.

1. Nahrajte *soubor Program.cs* do kontejneru objektů blob. (Tento soubor se zde používá jako příklad; můžete nahrát libovolný textový soubor a vytvořit zprávu fronty s názvem souboru.)

   a. V **Průzkumníkovi serveru**poklepejte na uzel vytvořeného kontejneru.

   b. V okně **Kontejner** vyberte tlačítko **Nahrát.**

   ![Tlačítko pro nahrávání objektů blob](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Vyhledání a výběr *Program.cs*a potom vyberte **OK**.

1. Vytvořte zprávu fronty ve frontě, kterou jste vytvořili dříve, s *Program.cs* jako text zprávy.

   ![Program.cs zprávy fronty](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Spusťte projekt místně.

   Zpráva fronty aktivuje funkci, která pak přečte objekt blob a zaznamená jeho délku. Výstup konzoly vypadá takto:

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

Výstupní vazby zjednodušit kód, který zapisuje data. Tento příklad upraví předchozí zápisem kopie objektu blob namísto protokolování jeho velikosti. Vazby úložiště objektů blob jsou součástí balíčku rozšíření úložiště Azure, který jsme nainstalovali dříve.

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

1. Vytvořte další zprávu fronty s *Program.cs* jako text zprávy.

1. Spusťte projekt místně.

   Zpráva fronty aktivuje funkci, která pak přečte objekt blob, zaznamená jeho délku a vytvoří nový objekt blob. Výstup konzoly je stejný, ale když přejdete do okna kontejneru objektů blob a vyberete **Aktualizovat**, zobrazí se nový objekt blob s názvem *copy-Program.cs.*

## <a name="republish-the-updates-to-azure"></a>Opětovné publikování aktualizací do Azure

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

1. V dialogovém okně **Publikovat** zkontrolujte, zda je vybrán aktuální profil, a pak zvolte **Publikovat**. Výsledky publikování jsou podrobně popsány v okně **Výstup.**
 
1. Ověřte funkci v Azure znovu nahráním souboru do kontejneru objektů blob a přidáním zprávy do fronty, která je názvem nahraného souboru. Zobrazí se zpráva získat odebrány z fronty a kopii souboru vytvořeného v kontejneru objektů blob. 

## <a name="next-steps"></a>Další kroky

Tento článek vám ukázal, jak vytvořit, spustit a nasadit projekt WebJobs SDK 3.x.

> [!div class="nextstepaction"]
> [Další informace o sdk webjobs](webjobs-sdk-how-to.md)
