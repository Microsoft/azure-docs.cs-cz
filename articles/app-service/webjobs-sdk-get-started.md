---
title: Začínáme se sadou WebJobs SDK
description: Seznámení se sadou WebJobs SDK pro zpracování na pozadí založené na událostech. Přečtěte si, jak získat přístup k datům ve službách Azure a službách třetích stran.
author: ggailey777
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 4a22602dd9638b981cfe3d8bae9b5cdaacbf90dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91652036"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Začínáme s událostmi řízeným zpracováním na pozadí pomocí sady Azure WebJobs SDK

V tomto článku se dozvíte, jak pomocí sady Visual Studio 2019 vytvořit projekt Azure WebJobs SDK, spustit ho místně a pak ho nasadit do [Azure App Service](overview.md). Verze 3. x sady WebJobs SDK podporuje konzolové aplikace .NET Core i .NET Framework. Další informace o práci se sadou WebJobs SDK najdete v článku [Jak používat sadu Azure WebJobs SDK pro zpracování na pozadí založené na událostech](webjobs-sdk-how-to.md).

V tomto článku se dozvíte, jak nasadit WebJobs jako konzolovou aplikaci .NET Core. Pokud chcete nasadit WebJobs jako konzolovou aplikaci .NET Framework, přečtěte si téma [WebJobs jako .NET Framework konzolové aplikace](webjobs-dotnet-deploy-vs.md#webjobs-as-net-framework-console-apps). Pokud vás zajímá Sada WebJobs SDK verze 2. x, která podporuje jenom .NET Framework, přečtěte si téma [vývoj a nasazení WebJobs pomocí sady Visual Studio – Azure App Service](webjobs-dotnet-deploy-vs.md).

## <a name="prerequisites"></a>Požadavky

* [Nainstalujte Visual Studio 2019](/visualstudio/install/) s úlohou **vývoj pro Azure** . Pokud již máte aplikaci Visual Studio, ale nemáte tuto úlohu, přidejte úlohu výběrem **nástrojů > získat nástroje a funkce**.

* Abyste mohli publikovat projekt sady WebJobs SDK do Azure, musíte mít [účet Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

## <a name="create-a-project"></a>Vytvoření projektu

1. V aplikaci Visual Studio vyberte možnost **vytvořit nový projekt**.

2. Vyberte **aplikace konzoly (.NET Core)**.

3. Pojmenujte projekt *WebJobsSDKSample*a pak vyberte **vytvořit**.

   ![Dialogové okno Nový projekt](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>Balíčky NuGet pro WebJobs

1. Nainstalujte nejnovější stabilní verzi 3. x [ `Microsoft.Azure.WebJobs.Extensions` balíčku NuGet](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/), která zahrnuje `Microsoft.Azure.WebJobs` .

     Tady je příkaz **konzoly Správce balíčků** :

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version <3_X_VERSION>
     ```

    V tomto příkazu nahraďte `<3_X_VERSION>` podporovanou verzí balíčku. 

## <a name="create-the-host"></a>Vytvoření hostitele

Hostitel je kontejner modulu runtime pro funkce, které naslouchá triggerům a volání funkcí. Následující kroky vytvoří hostitele, který implementuje [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost) , což je obecný hostitel v ASP.NET Core.

1. Do *program.cs*přidejte tyto `using` příkazy:

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

V ASP.NET Core jsou konfigurace hostitelů nastaveny voláním metod v [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) instanci. Další informace najdete v tématu [obecný hostitel .NET](/aspnet/core/fundamentals/host/generic-host). `ConfigureWebJobs`Metoda rozšíření inicializuje hostitele WebJobs. V nástroji `ConfigureWebJobs` můžete inicializovat specifická rozšíření WebJobs a nastavovat vlastnosti těchto rozšíření.  

## <a name="enable-console-logging"></a>Povolit protokolování konzoly

V této části nastavíte protokolování konzoly, které používá rozhraní [ASP.NET Core Logging Framework](/aspnet/core/fundamentals/logging).

1. Nainstalujte nejnovější stabilní verzi [ `Microsoft.Extensions.Logging.Console` balíčku NuGet](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/), která zahrnuje `Microsoft.Extensions.Logging` .

   Tady je příkaz **konzoly Správce balíčků** :

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version <3_X_VERSION>
   ```
   V tomto příkazu nahraďte `<3_X_VERSION>` podporovanou verzí balíčku 3. x.

1. Do *program.cs*přidejte `using` příkaz:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. Zavolejte [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) metodu na [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) . [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole)Metoda přidá protokolování konzoly do konfigurace.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    `Main`Metoda teď vypadá takto:

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

    Tato aktualizace provede následující kroky:

    * Zakáže [protokolování řídicího panelu](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Řídicí panel je starší verze nástroje pro monitorování a pro produkční scénáře s vysokou propustností se nedoporučuje protokolování řídicího panelu.
    * Přidá poskytovatele konzoly s výchozím [filtrováním](webjobs-sdk-how-to.md#log-filtering).

Nyní můžete přidat funkci, která je aktivována zprávami přicházejících do fronty Azure Storage.

## <a name="install-the-storage-binding-extension"></a>Instalace rozšíření vazby služby Storage

Počínaje verzí 3. x musíte explicitně nainstalovat rozšíření vazby úložiště, které vyžaduje Sada WebJobs SDK. V předchozích verzích byly vazby úložiště součástí sady SDK.

1. Nainstalujte nejnovější stabilní verzi balíčku NuGet  [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) , verze 3. x. 

    Tady je příkaz **konzoly Správce balíčků** :

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version <3_X_VERSION>
    ```
    
    V tomto příkazu nahraďte `<3_X_VERSION>` podporovanou verzí balíčku. 

2. V `ConfigureWebJobs` rozšiřující metodě zavolejte `AddAzureStorage` metodu na [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) instanci pro inicializaci rozšíření úložiště. V tomto okamžiku `ConfigureWebJobs` metoda vypadá jako v následujícím příkladu:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Vytvoření funkce

1. Klikněte pravým tlačítkem myši na projekt, vyberte možnost **Přidat**  >  **novou položku...**, zvolte možnost **Třída**, pojmenujte nový soubor třídy jazyka C# *Functions.cs*a vyberte možnost **Přidat**.

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

   `QueueTrigger`Atribut říká modulu runtime, aby tuto funkci volal při zápisu nové zprávy ve frontě Azure Storage s názvem `queue` . Obsah zprávy fronty je k dispozici pro kód metody v `message` parametru. Tělo metody je místo, kde se zpracovávají data triggeru. V tomto příkladu kód pouze protokoluje zprávu.

   `message`Parametr nemusí být řetězec. Můžete také vytvořit propojení s objektem JSON, bajtovým polem nebo objektem [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) . [Viz Použití triggeru fronty](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#usage). Každý typ vazby (například fronty, objekty blob nebo tabulky) má jinou sadu typů parametrů, které lze svázat.

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Emulátor Azure Storage, který se spouští místně, nemá všechny funkce, které vyžaduje Sada WebJobs SDK. Takže v této části vytvoříte účet úložiště v Azure a nakonfigurujete ho tak, aby ho používal. Pokud už máte účet úložiště, přeskočte dolů na krok 6.

1. Otevřete **Průzkumník serveru** v aplikaci Visual Studio a přihlaste se k Azure. Klikněte pravým tlačítkem na uzel **Azure** a pak vyberte **připojit k Microsoft Azure předplatnému**.

   ![Přihlášení k Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. V uzlu **Azure** v **Průzkumník serveru**klikněte pravým tlačítkem na **úložiště**a pak vyberte **vytvořit účet úložiště**.

   ![Nabídka vytvořit účet úložiště](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. V dialogovém okně **vytvořit účet úložiště** zadejte jedinečný název pro účet úložiště.

1. Vyberte si stejnou **oblast** , ve které jste vytvořili aplikaci App Service, nebo oblast, která je blízko vás.

1. Vyberte **Vytvořit**.

   ![Vytvořit účet úložiště](./media/webjobs-sdk-get-started/create-storage-account.png)

1. V uzlu **úložiště** v **Průzkumník serveru**vyberte nový účet úložiště. V okně **vlastnosti** vyberte tři tečky (**...**) napravo od pole hodnota **připojovacího řetězce** .

   ![Tři tečky připojovacího řetězce](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Zkopírujte připojovací řetězec a uložte tuto hodnotu někam tam, kde ji můžete snadno kopírovat.

   ![Kopírovat připojovací řetězec](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>Konfigurace úložiště pro místní spuštění

Sada WebJobs SDK hledá připojovací řetězec úložiště v nastavení aplikace v Azure. Když spouštíte místně, vyhledá tuto hodnotu v místním konfiguračním souboru nebo v proměnných prostředí.

1. Klikněte pravým tlačítkem myši na projekt, vyberte možnost **Přidat**  >  **novou položku...**, zvolte **konfigurační soubor JSON pro JavaScript**, pojmenujte nový soubor *appsettings.jsv* souboru a vyberte **Přidat**. 

1. V novém souboru přidejte `AzureWebJobsStorage` pole, jako v následujícím příkladu:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Nahraďte *{Storage Connection String}* připojovacím řetězcem, který jste zkopírovali dříve.

1. Vyberte *appsettings.js* v souboru v Průzkumník řešení a v okně **vlastnosti** nastavte **Kopírovat do výstupního adresáře** na kopírovat, **Pokud je novější**.

Později přidáte stejné nastavení aplikace připojovacího řetězce do aplikace v Azure App Service.

## <a name="test-locally"></a>Test lokálně

V této části sestavíte a spustíte projekt místně a aktivujete funkci vytvořením zprávy ve frontě.

1. Stisknutím **kombinace kláves CTRL + F5** spusťte projekt.

   V konzole se zobrazí, že modul runtime nalezl vaši funkci a čeká na aktivaci zpráv ve frontě. Následující výstup je generován hostitelem v3. x:

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

1. V **Průzkumník serveru** v aplikaci Visual Studio rozbalte uzel pro nový účet úložiště a pak klikněte pravým tlačítkem na **fronty**.

1. Vyberte **vytvořit frontu**.

1. Jako název fronty zadejte *Queue* a pak vyberte **OK**.

   ![Snímek obrazovky s informacemi o tom, kde vytvoříte frontu a pojmenujte ji "Queue". ](./media/webjobs-sdk-get-started/create-queue.png)

1. Klikněte pravým tlačítkem na uzel nové fronty a pak vyberte **Zobrazit frontu**.

1. Vyberte ikonu **přidat zprávu** .

   ![Snímek obrazovky, který zvýrazní ikonu Přidat zprávu](./media/webjobs-sdk-get-started/create-queue-message.png)

1. V dialogovém okně **přidat zprávu** zadejte *Hello World!* jako **text zprávy**a vyberte **OK**. Ve frontě je nyní zpráva.

   ![Vytvořit frontu](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Spusťte projekt znovu.

   Vzhledem k tomu, že jste `QueueTrigger` ve `ProcessQueueMessage` funkci použili atribut, modul runtime sady WeJobs SDK při spuštění naslouchá zprávám ve frontě. Najde novou zprávu fronty ve frontě s názvem *Queue* a zavolá funkci.

   Z důvodu [cyklického dotazování exponenciálního omezení rychlosti](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm)může trvat až 2 minuty, než modul runtime tuto zprávu najde a vyvolá funkci. Tato čekací doba se dá snížit spuštěním v [režimu vývoje](webjobs-sdk-how-to.md#host-development-settings).

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

1. Vraťte se do okna fronty a aktualizujte ho. Zpráva zmizela, protože byla zpracována místně spuštěnou funkcí. 

## <a name="add-application-insights-logging"></a>Přidat protokolování Application Insights

Když se projekt spustí v Azure, nemůžete monitorovat spouštění funkcí zobrazením výstupu konzoly. Řešení monitorování, které doporučujeme, je [Application Insights](../azure-monitor/app/app-insights-overview.md). Další informace najdete v tématu [monitorování Azure Functions](../azure-functions/functions-monitoring.md).

V této části provedete následující úlohy, abyste nastavili protokolování Application Insights před nasazením do Azure:

* Ujistěte se, že máte aplikaci App Service a instanci Application Insights, se kterou chcete pracovat.
* Nakonfigurujte aplikaci App Service tak, aby používala instanci Application Insights a účet úložiště, který jste vytvořili dříve.
* Nastavte projekt pro protokolování do Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Vytvoření instance App Service aplikace a Application Insights

1. Pokud ještě nemáte aplikaci App Service, kterou můžete použít, [vytvořte ji](quickstart-dotnet-framework.md). Když vytváříte aplikaci, můžete také vytvořit prostředek připojené Application Insights. Když to uděláte, `APPINSIGHTS_INSTRUMENTATIONKEY` nastaví se pro vás ve vaší aplikaci.

1. Pokud ještě nemáte prostředek Application Insights, který můžete použít, [vytvořte ho](../azure-monitor/app/create-new-resource.md ). Nastavte **Typ aplikace** na **Obecné**a přeskočte oddíly, které následují **po zkopírování klíče instrumentace**.

1. Pokud již máte Application Insights prostředek, který chcete použít, [Zkopírujte klíč instrumentace](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Konfigurace nastavení aplikace 

1. V **Průzkumník serveru** v aplikaci Visual Studio rozbalte uzel **App Service** v **Azure**.

1. Rozbalte skupinu prostředků, ve které je vaše aplikace App Service, a pak klikněte pravým tlačítkem na aplikaci App Service.

1. Vyberte **Nastavení zobrazení**.

1. Do pole **připojovací řetězce** přidejte následující položku.

   |Name  |připojovací řetězec  |Typ databáze|
   |---------|---------|------|
   |AzureWebJobsStorage | {připojovací řetězec úložiště, který jste zkopírovali dříve}|Vlastní|

1. Pokud pole **nastavení aplikace** nemá klíč instrumentace Application Insights, přidejte jej, který jste zkopírovali dříve. (Klíč instrumentace už může být v závislosti na tom, jak jste aplikaci App Service vytvořili.)

   |Name  |Hodnota  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {instrumentace klíče} |

1. Nahraďte *{Instrumentation Key}* klíčem instrumentace z Application Insights prostředku, který používáte.

1. Vyberte **Uložit**.

1. Přidejte do projektu připojení Application Insights, abyste ho mohli spustit místně. Do *appsettings.jsv* souboru přidejte `APPINSIGHTS_INSTRUMENTATIONKEY` pole, jako v následujícím příkladu:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    Nahraďte *{Instrumentation Key}* klíčem instrumentace z Application Insights prostředku, který používáte.

1. Uložte provedené změny.

### <a name="add-application-insights-logging-provider"></a>Přidat zprostředkovatele protokolování Application Insights

Pokud chcete využít výhod protokolování [Application Insights](../azure-monitor/app/app-insights-overview.md) , aktualizujte kód protokolování a proveďte následující:

* Přidejte poskytovatele protokolování Application Insights s výchozím [filtrováním](webjobs-sdk-how-to.md#log-filtering). Při místním spuštění se všechny informace a protokoly vyšší úrovně zapisují do konzoly i Application Insights.
* Vložte objekt [LoggerFactory](./webjobs-sdk-how-to.md#logging-and-monitoring) do `using` bloku, aby bylo zajištěno, že výstup protokolu bude vyprázdněn při ukončení hostitele.

1. Nainstalujte nejnovější stabilní verzi [ `Microsoft.Azure.WebJobs.Logging.ApplicationInsights` balíčku NuGet](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/)3. x.

   Tady je příkaz **konzoly Správce balíčků** :

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <3_X_VERSION>
   ```
    V tomto příkazu nahraďte `<3_X_VERSION>` podporovanou verzí balíčku.

1. Otevřete *program.cs* a nahraďte kód v `Main` metodě následujícím kódem:

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

    Tím přidáte poskytovatele Application Insights do protokolování pomocí klíče, který jste přidali do nastavení aplikace.

## <a name="test-application-insights-logging"></a>Protokolování Application Insights testů

V této části se znovu spustíte místně a ověříte, že data protokolování teď Application Insights a také na konzolu.

1. Pomocí **Průzkumník serveru** v aplikaci Visual Studio vytvořte zprávu fronty, jako jste použili [dříve](#test-locally), s výjimkou zadejte *Hello App Insights!* jako text zprávy.

1. Spusťte projekt.

   Sada WebJobs SDK zpracuje zprávu fronty a protokoly se zobrazí v okně konzoly.

1. Zavřete okno konzoly.

1. Pokud chcete zobrazit prostředek Application Insights, otevřete [Azure Portal](https://portal.azure.com/) . Vyhledejte a vyberte **Application Insights**.

1. Vyberte instanci Application Insights.

1. Vyberte **Hledat**.

   ![Vybrat hledání](./media/webjobs-sdk-get-started/select-search.png)

1. Pokud nevidíte službu *Hello pro App Insights!* Vyberte možnost **aktualizovat** pravidelně po dobu několika minut. (Protokoly se nezobrazují okamžitě, protože u klienta Application Insights vyprázdní protokoly, které zpracovávají.)

   ![Přihlášení Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Zavřete okno konzoly.

## <a name="deploy-to-azure"></a><a name="deploy-as-a-webjob"></a>Nasazení do Azure

Během nasazování vytvoříte instanci služby App Service, ve které se budou spouštět vaše funkce. Když publikujete konzolovou aplikaci .NET Core pro App Service v Azure, automaticky se spustí jako webová úloha. Další informace o publikování najdete v tématu [vývoj a nasazení WebJobs pomocí sady Visual Studio](webjobs-dotnet-deploy-vs.md).

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>Aktivace funkce v Azure

1. Ujistěte se, že nepracujete místně (zavřete okno konzoly, pokud je stále otevřeno). V opačném případě může být místní instance prvním pro zpracování všech vytvořených zpráv fronty.

1. Na stránce **fronta** v aplikaci Visual Studio přidejte zprávu do fronty jako dříve.

1. Aktualizujte stránku **fronty** a nová zpráva zmizí, protože byla zpracována funkcí spuštěnou v Azure.

   > [!TIP]
   > Při testování v Azure použijte [vývojový režim](webjobs-sdk-how-to.md#host-development-settings) k zajištění toho, aby se funkce triggeru fronty vyvolala hned, a aby se předešlo prodlevám v důsledku [cyklického dotazování exponenciálního omezení rychlostiu do fronty](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Zobrazit protokoly v Application Insights

1. Otevřete [Azure Portal](https://portal.azure.com/)a pokračujte na prostředek Application Insights.

1. Vyberte **Hledat**.

1. Pokud nevidíte *Hello Azure!* Vyberte možnost **aktualizovat** pravidelně po dobu několika minut.

   Zobrazí se protokoly z funkce spuštěné v úloze WebJob, včetně *Hello Azure.* text, který jste zadali v předchozí části.

## <a name="add-an-input-binding"></a>Přidání vstupní vazby

Vstupní vazby zjednodušují kód, který čte data. V tomto příkladu bude zpráva fronty název objektu BLOB a vy použijete název objektu BLOB k vyhledání a načtení objektu BLOB v Azure Storage.

1. V *Functions.cs*nahraďte `ProcessQueueMessage` metodu následujícím kódem:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   V tomto kódu `queueTrigger` je [výraz vazby](../azure-functions/functions-bindings-expressions-patterns.md), což znamená, že se při běhu překládá na jinou hodnotu.  Za běhu má obsah zprávy Queue.

1. Přidat `using` :

   ```cs
   using System.IO;
   ```

1. Vytvořte ve svém účtu úložiště kontejner objektů BLOB.

   a. V **Průzkumník serveru** v aplikaci Visual Studio rozbalte uzel pro svůj účet úložiště, klikněte pravým tlačítkem na **objekty blob**a pak vyberte **vytvořit kontejner objektů BLOB**.

   b. V dialogovém okně **vytvořit kontejner objektů BLOB** zadejte jako název kontejneru *kontejner* a pak klikněte na **OK**.

1. Nahrajte soubor *program.cs* do kontejneru objektů BLOB. (Tento soubor se tady používá jako příklad. můžete nahrát libovolný textový soubor a vytvořit zprávu fronty s názvem souboru.)

   a. V **Průzkumník serveru**dvakrát klikněte na uzel pro kontejner, který jste vytvořili.

   b. V okně **kontejner** vyberte tlačítko **nahrát** .

   ![Tlačítko pro nahrání objektu BLOB](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Najděte a vyberte *program.cs*a pak vyberte **OK**.

1. Ve frontě, kterou jste vytvořili dříve, vytvořte zprávu queue s *program.cs* jako text zprávy.

   ![Program.cs zprávy ve frontě](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Spustit projekt místně.

   Zpráva fronty aktivuje funkci, která pak přečte objekt BLOB a zaprotokoluje jeho délku. Výstup konzoly vypadá takto:

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

Výstupní vazby zjednodušují kód, který zapisuje data. Tento příklad upraví předchozí vytvořením kopie objektu BLOB místo protokolování jeho velikosti. Vazby úložiště objektů BLOB jsou součástí balíčku rozšíření Azure Storage, který jsme předtím nainstalovali.

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

1. Vytvoří další zprávu fronty s *program.cs* jako text zprávy.

1. Spustit projekt místně.

   Zpráva ve frontě aktivuje funkci, která pak přečte objekt blob, zaprotokoluje jeho délku a vytvoří nový objekt BLOB. Výstup konzoly je stejný, ale když přejdete do okna kontejneru objektů BLOB a vyberete **aktualizovat**, zobrazí se nový objekt BLOB s názvem *copy-program.cs.*

## <a name="republish-the-updates-to-azure"></a>Znovu publikovat aktualizace do Azure

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

1. V dialogovém okně **publikovat** se ujistěte, že je vybraný aktuální profil, a pak zvolte **publikovat**. Výsledky publikování jsou podrobně popsané v okně **výstup** .
 
1. Ověřte funkci v Azure tak, že znovu nahrajete soubor do kontejneru objektů BLOB a přidáte do fronty zprávu, která je názvem nahraného souboru. Zobrazí se zpráva odebrána z fronty a kopie souboru vytvořeného v kontejneru objektů BLOB. 

## <a name="next-steps"></a>Další kroky

Tento článek ukazuje, jak vytvořit, spustit a nasadit projekt WebJobs SDK 3. x.

> [!div class="nextstepaction"]
> [Další informace o sadě WebJobs SDK](webjobs-sdk-how-to.md)