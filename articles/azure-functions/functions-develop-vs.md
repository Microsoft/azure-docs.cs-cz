---
title: Vývoj služby Azure Functions pomocí sady Visual Studio
description: Naučte se vyvíjet a testovat Azure Functions pomocí Azure Functionsch nástrojů pro Visual Studio 2019.
ms.custom: vs-azure, devx-track-csharp
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 877c82e375b0ea469071402b83fadbd634177f3f
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655811"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Vývoj služby Azure Functions pomocí sady Visual Studio  

Visual Studio umožňuje vyvíjet, testovat a nasazovat funkce knihovny tříd C# do Azure. Pokud je toto prostředí vaše první s Azure Functions, přečtěte si [Úvod do Azure Functions](functions-overview.md).

Visual Studio poskytuje při vývoji funkcí následující výhody: 

* Upravovat, sestavovat a spouštět funkce na místním počítači pro vývoj. 
* Publikujte svůj Azure Functions projekt přímo do Azure a podle potřeby vytvořte prostředky Azure. 
* Použijte atributy jazyka C# k deklaraci vazeb funkcí přímo v kódu jazyka C#.
* Vývoj a nasazení předem kompilovaných funkcí jazyka C#. Předsplněné funkce poskytují lepší výkon pro studený start než funkce založené na skriptech jazyka C#. 
* Nahlaste své funkce v jazyce C# a využijte výhod vývoje sady Visual Studio. 

Tento článek poskytuje podrobné informace o tom, jak používat Visual Studio k vývoji funkcí knihoven tříd C# a jejich publikování do Azure. Než si přečtete tento článek, zvažte dokončení [rychlého startu funkcí pro Visual Studio](functions-create-your-first-function-visual-studio.md). 

Pokud není uvedeno jinak, postupy a příklady jsou uvedeny pro Visual Studio 2019. 

## <a name="prerequisites"></a>Požadavky

- Azure Functions nástroje. Pokud chcete přidat nástroje Azure Function Tools, zahrňte do instalace sady Visual Studio úlohu **vývoj pro Azure** . Azure Functions nástroje jsou k dispozici v rámci úlohy vývoje Azure počínaje sadou Visual Studio 2017.

- Další prostředky, které potřebujete, například účet Azure Storage, se ve vašem předplatném vytvoří během procesu publikování.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> V aplikaci Visual Studio 2017 se úloha vývoj pro Azure instaluje Azure Functions nástroje jako samostatná přípona. Když aktualizujete instalaci sady Visual Studio 2017, ujistěte se, že používáte nejnovější [verzi](#check-your-tools-version) nástrojů Azure Functions. V následujících částech se dozvíte, jak kontrolovat a (v případě potřeby) aktualizovat rozšíření Azure Functions nástrojů v aplikaci Visual Studio 2017. 
>
> Pokud používáte Visual Studio 2019, přeskočte tyto oddíly.

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>Podívejte se na verzi nástrojů v aplikaci Visual Studio 2017

1. V nabídce **nástroje** vyberte **rozšíření a aktualizace**. Rozbalte položku **nainstalované**  >  **nástroje** a pak zvolte možnost **Nástroje pro Azure functions a webové úlohy**.

    ![Ověření verze nástrojů Functions](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Poznamenejte si nainstalovanou **verzi** a Porovnejte tuto verzi s nejnovější verzí uvedenou v [poznámkách k verzi](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

1. Pokud je vaše verze starší, aktualizujte nástroje v aplikaci Visual Studio, jak je znázorněno v následující části.

### <a name="update-your-tools-in-visual-studio-2017"></a>Aktualizace nástrojů v aplikaci Visual Studio 2017

1. V dialogovém okně **rozšíření a aktualizace** rozbalte položku **aktualizace**  >  **Visual Studio Marketplace**, zvolte **Azure functions a nástroje webové úlohy** a vyberte **aktualizovat**.

    ![Aktualizace verze nástrojů Functions](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. Po stažení aktualizace nástrojů vyberte **Zavřít** a potom zavřete Visual Studio, aby se aktivovala aktualizace nástrojů pomocí instalačního programu VSIX.

1. V instalačním programu VSIX vyberte možnost **změnit** a aktualizujte nástroje. 

1. Po dokončení aktualizace klikněte na tlačítko **Zavřít** a pak restartujte aplikaci Visual Studio.

> [!NOTE]  
> V aplikaci Visual Studio 2019 nebo novější je rozšíření Azure Functionsch nástrojů aktualizováno v rámci sady Visual Studio.  

## <a name="create-an-azure-functions-project"></a>Vytvoření projektu služby Azure Functions

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Po vytvoření projektu Azure Functions vytvoří šablona projektu projekt C#, nainstaluje `Microsoft.NET.Sdk.Functions` balíček NuGet a nastaví cílovou architekturu. Nový projekt má následující soubory:

* **host.js**: umožňuje konfigurovat hostitele funkcí. Tato nastavení platí při místním spuštění i v Azure. Další informace najdete v tématu [host.jsv referenci](functions-host-json.md).

* **local.settings.json**: udržuje nastavení používaná při místním spouštění funkcí. Tato nastavení se nepoužívají při spuštění v Azure. Další informace najdete v tématu [místní nastavení souboru](#local-settings-file).

    >[!IMPORTANT]
    >Vzhledem k tomu, že local.settings.jsv souboru může obsahovat tajné kódy, je nutné ji vyloučit ze správy zdrojového kódu projektu. Ujistěte se, že nastavení **Kopírovat do výstupního adresáře** pro tento soubor je nastaveno na **Kopírovat, pokud je novější**. 

Další informace naleznete v tématu [Functions Class Library Project](functions-dotnet-class-library.md#functions-class-library-project).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Visual Studio automaticky neodešle nastavení v local.settings.jspři publikování projektu. Pokud chcete mít jistotu, že tato nastavení existují i ve vaší aplikaci Function App v Azure, nahrajte je po publikování projektu. Další informace najdete v tématu [nastavení aplikace Function App](#function-app-settings). Hodnoty v `ConnectionStrings` kolekci se nikdy nepublikují.

Váš kód může také číst hodnoty nastavení aplikace Function App jako proměnné prostředí. Další informace naleznete v tématu [proměnné prostředí](functions-dotnet-class-library.md#environment-variables).

## <a name="configure-your-build-output-settings"></a>Konfigurovat nastavení výstupu sestavení

Při sestavování projektu Azure Functions nástroj sestavení optimalizuje výstup tak, aby byla zachována pouze jedna kopie všech sestavení, která jsou sdílena s modulem runtime Functions. Výsledkem je optimalizované sestavení, které šetří co nejvíce místa. Pokud však přejdete na novější verzi libovolného sestavení projektu, nástroje sestavení nemusí znát, že tato sestavení musí být zachována. Aby se zajistilo, že jsou tato sestavení během procesu optimalizace zachovaná, můžete je zadat pomocí `FunctionsPreservedDependencies` prvků v souboru projektu (. csproj):

```xml
  <ItemGroup>
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Extensions.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Features.dll" />
  </ItemGroup>
```

## <a name="configure-the-project-for-local-development"></a>Konfigurace projektu pro místní vývoj

Modul runtime Functions používá interní účet Azure Storage. Pro všechny typy triggerů jiné než HTTP a Webhooky nastavte `Values.AzureWebJobsStorage` klíč na platný připojovací řetězec účtu Azure Storage. Aplikace Function App může použít také [emulátor Azure Storage](../storage/common/storage-use-emulator.md) pro `AzureWebJobsStorage` nastavení připojení, které je vyžadováno projektem. Chcete-li použít emulátor, nastavte hodnotu `AzureWebJobsStorage` na `UseDevelopmentStorage=true` . Před nasazením toto nastavení změňte na skutečný připojovací řetězec účtu úložiště.

Nastavení připojovacího řetězce účtu úložiště:

1. V aplikaci Visual Studio vyberte možnost **Zobrazit**  >  **Průzkumníka cloudu**.

2. V **Průzkumníku cloudu** rozbalte **účty úložiště** a pak vyberte svůj účet úložiště. Na kartě **vlastnosti** Zkopírujte hodnotu **primárního připojovacího řetězce** .

2. V projektu otevřete local.settings.jsv souboru a nastavte hodnotu `AzureWebJobsStorage` klíče na připojovací řetězec, který jste zkopírovali.

3. Opakujte předchozí krok a přidejte do pole jedinečné klíče `Values` pro všechna ostatní připojení požadovaná funkcemi. 

## <a name="add-a-function-to-your-project"></a>Přidání funkce do projektu

Ve funkcích knihovny tříd jazyka C# jsou vazby používané funkcí definovány použitím atributů v kódu. Při vytváření aktivační události funkce ze zadaných šablon se pro vás aplikují atributy triggeru. 

1. V **Průzkumník řešení** klikněte pravým tlačítkem myši na uzel projektu a vyberte možnost **Přidat**  >  **novou položku**. 

2. Vyberte **funkce Azure**, zadejte **název** třídy a pak vyberte **Přidat**.

3. Zvolte aktivační událost, nastavte vlastnosti vazby a pak vyberte **OK**. Následující příklad ukazuje nastavení pro vytvoření funkce triggeru služby Queue Storage. 

    ![Vytvoření funkce triggeru služby Queue Storage](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Tento příklad triggeru používá připojovací řetězec s klíčem s názvem `QueueStorage` . Definujte toto nastavení připojovacího řetězce v [local.settings.jssouboru](functions-run-local.md#local-settings-file).

4. Projděte si nově přidanou třídu. Zobrazí se statická `Run()` metoda, která je označena `FunctionName` atributem. Tento atribut označuje, že metoda je vstupním bodem pro funkci.

    Například následující třída jazyka C# představuje funkci triggeru úložiště základní fronty:

    ```csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.Extensions.Logging;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", 
                Connection = "QueueStorage")]string myQueueItem, ILogger log)
            {
                log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ```

Atribut specifický pro vazbu se použije na každý parametr vazby dodaný metodě vstupního bodu. Atribut přebírá informace o vazbě jako parametry. V předchozím příkladu má první parametr `QueueTrigger` použit atribut, který označuje funkci triggeru služby Queue Storage. Název fronty a název nastavení připojovacího řetězce jsou předány jako parametry `QueueTrigger` atributu. Další informace najdete v tématu [vazby Azure Queue Storage pro Azure Functions](functions-bindings-storage-queue-trigger.md).

Pomocí výše uvedeného postupu přidejte do projektu Function App další funkce. Každá funkce v projektu může mít jinou aktivační událost, ale funkce musí mít právě jednu aktivační událost. Další informace najdete v tématu [Azure Functions triggery a koncepty vazeb](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Přidání vazeb

Stejně jako u triggerů se vstupní a výstupní vazby přidávají do funkce jako atributy vazby. Přidejte vazby k funkci následujícím způsobem:

1. Ujistěte se, že jste [nakonfigurovali projekt pro místní vývoj](#configure-the-project-for-local-development).

2. Přidejte příslušný balíček rozšíření NuGet pro konkrétní vazbu. 

   Další informace naleznete v tématu [Knihovna tříd C# se sadou Visual Studio](./functions-bindings-register.md#local-csharp). V referenčním článku pro vazbu Najděte požadavky na balíček NuGet specifické pro vazbu. Můžete například vyhledat požadavky balíčku pro aktivační událost Event Hubs v [článku odkaz Event Hubs vazby](functions-bindings-event-hubs.md).

3. Pokud existují nastavení aplikace, která vazba potřebuje, přidejte je do `Values` kolekce v [souboru místního nastavení](functions-run-local.md#local-settings-file). 

   Funkce tyto hodnoty používá při místním spuštění. Když je funkce spuštěná v aplikaci Function App v Azure, používá [nastavení aplikace Function App](#function-app-settings).

4. Přidejte odpovídající atribut vazby do podpisu metody. V následujícím příkladu zpráva ve frontě aktivuje funkci a výstupní vazba vytvoří novou zprávu fronty se stejným textem v jiné frontě.

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            ILogger log)
        {
            log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
   Z nastavení se získá připojení k úložišti front `AzureWebJobsStorage` . Další informace najdete v referenčním článku pro konkrétní vazbu. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testování funkcí

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. Další informace najdete v tématu věnovaném [práci s Azure Functions Core Tools](functions-run-local.md). Při prvním spuštění funkce ze sady Visual Studio budete vyzváni k instalaci těchto nástrojů. 

Testování funkce v aplikaci Visual Studio:

1. Stiskněte klávesu F5. Po výzvě přijměte požadavek ze sady Visual Studio na stažení a instalaci nástrojů Azure Functions Core (CLI). Je také možné, že budete muset povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP.

2. Když je spuštěný projekt, otestujte kód stejně, jako byste otestovali nasazenou funkci. 

   Další informace najdete v tématu [strategie pro testování kódu v Azure Functions](functions-test-a-function.md). Při spuštění sady Visual Studio v režimu ladění se zarážky narazí podle očekávání.

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->


## <a name="publish-to-azure"></a>Publikování do Azure

Při publikování ze sady Visual Studio používá jednu ze dvou metod nasazení:

* [Nasazení webu](functions-deployment-technologies.md#web-deploy-msdeploy): balíčky a nasadí aplikace pro Windows na libovolný server IIS.
* [Nasazení souboru zip s povoleným spuštěním z balíčku](functions-deployment-technologies.md#zip-deploy): doporučuje se pro nasazení Azure Functions.

Pomocí následujících kroků můžete projekt publikovat do aplikace Function App v Azure.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Nastavení aplikace funkcí

Vzhledem k tomu, že Visual Studio neodesílá tato nastavení automaticky při publikování projektu, všechna nastavení, která přidáte do local.settings.jsv, musíte také přidat do aplikace Function App v Azure.

Nejjednodušší způsob, jak nahrát požadovaná nastavení do aplikace Function App v Azure, je vybrat odkaz **Správa nastavení Azure App Service** , který se zobrazí po úspěšném publikování projektu.

:::image type="content" source="./media/functions-develop-vs/functions-vstools-app-settings.png" alt-text="Nastavení v okně Publikovat":::

Po výběru tohoto odkazu se zobrazí dialogové okno **nastavení aplikace** pro aplikaci Function App, kde můžete přidat nová nastavení aplikace nebo upravit stávající.

![Nastavení aplikace](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Místní** zobrazí hodnotu nastavení v souboru local.settings.json a **Vzdálená** zobrazí aktuální hodnotu nastavení v aplikaci Function App v Azure. Vyberte **Přidat nastavení** a vytvořte nové nastavení aplikace. K zkopírování hodnoty nastavení do **vzdáleného** pole použijte odkaz **Vložit hodnotu z místního** . Nedokončené změny se zapisují do souboru místního nastavení a aplikace Function App, když vyberete **OK**.

> [!NOTE]
> Ve výchozím nastavení není local.settings.jsv souboru zkontrolován do správy zdrojového kódu. To znamená, že pokud naklonete projekt místní funkce ze správy zdrojového kódu, projekt nemá local.settings.jsv souboru. V takovém případě je nutné ručně vytvořit local.settings.jsv souboru v kořenovém adresáři projektu tak, aby dialog **nastavení aplikace** fungoval podle očekávání. 

Nastavení aplikace můžete spravovat také jedním z těchto způsobů:

* [Použijte Azure Portal](functions-how-to-use-azure-function-app-settings.md#settings).
* [Použijte `--publish-local-settings` možnost publikovat v Azure Functions Core Tools](functions-run-local.md#publish).
* [Použijte rozhraní příkazového řádku Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

## <a name="monitoring-functions"></a>Funkce monitorování

Doporučeným způsobem, jak monitorovat provádění vašich funkcí, je integrace aplikace Function App s Azure Application Insights. Když vytvoříte aplikaci funkcí v Azure Portal, tato integrace se ve výchozím nastavení provádí. Při vytváření aplikace Function App během publikování sady Visual Studio ale nebude dokončena integrace v aplikaci Function App v Azure. Informace o tom, jak připojit Application Insights k aplikaci Function App, najdete v tématu [Povolení integrace Application Insights](configure-monitoring.md#enable-application-insights-integration).

Další informace o monitorování pomocí Application Insights najdete v tématu [monitorování Azure Functions](functions-monitoring.md).

## <a name="next-steps"></a>Další kroky

Další informace o Azure Functions Core Tools najdete v tématu [práce s Azure Functions Core Tools](functions-run-local.md).

Další informace o vývoji funkcí jako knihoven tříd .NET naleznete v tématu [Azure Functions C# Reference pro vývojáře](functions-dotnet-class-library.md). Tento článek také obsahuje odkazy na příklady použití atributů k deklaraci různých typů vazeb, které jsou podporovány nástrojem Azure Functions.    
