---
title: Vývoj Azure Functions pomocí sady Visual Studio
description: Zjistěte, jak vyvíjet a testovat funkce Azure pomocí nástrojů Azure Functions Tools pro Visual Studio 2019.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: b3d683153a1e70f4c65dcc5e401f4ee702af8b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277099"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Vývoj Azure Functions pomocí sady Visual Studio  

Visual Studio umožňuje vyvíjet, testovat a nasazovat funkce knihovny tříd jazyka C# do Azure. Pokud toto prostředí je vaše první s Funkcemi Azure, můžete se [dozvědět](functions-overview.md)více na úvod do Funkce Azure .

Visual Studio poskytuje následující výhody při vývoji funkcí: 

* Upravujte, vytvářejte a spouštějte funkce v místním vývojovém počítači. 
* Publikujte svůj projekt Azure Functions přímo do Azure a podle potřeby vytvořte prostředky Azure. 
* Pomocí atributů Jazyka C# deklarujte vazby funkcí přímo v kódu jazyka C#.
* Vyvíjejte a nasazujte předkompilované funkce jazyka C#. Předslazené funkce poskytují lepší výkon studeného startu než funkce založené na skriptu jazyka C#. 
* Kód vaše funkce v jazyce C# při všech výhodách vývoje sady Visual Studio. 

Tento článek obsahuje podrobnosti o tom, jak pomocí sady Visual Studio vyvíjet funkce knihovny tříd jazyka C# a publikovat je do Azure. Než si přečtete tento článek, měli byste dokončit [funkce rychlý start pro Visual Studio](functions-create-your-first-function-visual-studio.md). 

Pokud není uvedeno jinak, postupy a příklady jsou pro Visual Studio 2019. 

## <a name="prerequisites"></a>Požadavky

Nástroje azure funkcí je součástí úlohy vývoje Azure Visual Studia počínaje Visual Studio 2017. Ujistěte se, že do instalace sady Visual Studio zahrnete vývojové úlohy **Azure.**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Další prostředky, které potřebujete, jako je například účet Azure Storage, se vytvoří ve vašem předplatném během procesu publikování.

> [!NOTE]
> Ve Visual Studiu 2017 nainstaluje vývojové úlohy Azure nástroje Azure functions jako samostatné rozšíření. Při aktualizaci Visual Studia 2017 se také ujistěte, že používáte [nejnovější verzi](#check-your-tools-version) nástrojů Azure Functions. V následujících částech se ukazuje, jak zkontrolovat a (v případě potřeby) aktualizovat rozšíření nástroje funkce Azure v Visual Studiu 2017. 
>
> Při použití Visual Studia 2019 tuto část přeskočte.

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>Kontrola verze nástrojů ve Visual Studiu 2017

1. Z nabídky **Nástroje** zvolte **Rozšíření a aktualizace**. Rozbalte **Nainstalované** > **nástroje** a zvolte Nástroje Azure funkce a **webové úlohy**.

    ![Ověření verze nástrojů funkce](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Poznamenejte si nainstalovanou **verzi**. Tuto verzi můžete porovnat s nejnovější verzí [uvedenou v poznámkách k verzi](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

1. Pokud je vaše verze starší, aktualizujte nástroje v sadě Visual Studio, jak je znázorněno v následující části.

### <a name="update-your-tools-in-visual-studio-2017"></a>Aktualizace nástrojů ve Visual Studiu 2017

1. V dialogovém **okně Rozšíření a aktualizace** **rozbalte možnost Aktualizace** > **webu Visual Studio Marketplace**, zvolte Nástroje pro funkce Azure a webové **úlohy** a vyberte **Aktualizovat**.

    ![Aktualizace verze nástrojů funkce](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. Po stažení aktualizace nástrojů zavřete Visual Studio a spusťte aktualizaci nástrojů pomocí instalačního programu VSIX.

1. V instalačním programu zvolte **OK,** chcete-li spustit, a pak **Upravit,** chcete-li aktualizovat nástroje. 

1. Po dokončení aktualizace zvolte **Zavřít** a restartujte Visual Studio.

> [!NOTE]  
> V Visual Studiu 2019 a novějším se rozšíření nástrojů Azure Functions aktualizuje jako součást Visual Studia.  

## <a name="create-an-azure-functions-project"></a>Vytvoření projektu Azure Functions

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Šablona projektu vytvoří projekt Jazyka C#, nainstaluje balíček `Microsoft.NET.Sdk.Functions` NuGet a nastaví cílový rámec. Nový projekt má následující soubory:

* **host.json**: Umožňuje konfigurovat hostitele Functions. Tato nastavení platí při spuštění místně i v Azure. Další informace naleznete v [tématu host.json reference](functions-host-json.md).

* **local.settings.json**: Udržuje nastavení používaná při místním spuštění funkcí. Tato nastavení se nepoužívají při spuštění v Azure. Další informace naleznete v [tématu Local settings file](#local-settings-file).

    >[!IMPORTANT]
    >Vzhledem k tomu, že soubor local.settings.json může obsahovat tajné klíče, je nutné jej vyloučit ze správy zdrojového kódu projektu. Nastavení **Kopírovat do výstupního adresáře** pro tento soubor by mělo být vždy **Kopírovat, pokud**je novější . 

Další informace naleznete v tématu [Functions třídy knihovny projektu](functions-dotnet-class-library.md#functions-class-library-project).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Nastavení v souboru local.settings.json se při publikování projektu nenahraje automaticky. Chcete-li se ujistit, že tato nastavení existují také ve vaší aplikaci funkce v Azure, musíte je nahrát po publikování projektu. Další informace najdete v [tématu Nastavení aplikace Function](#function-app-settings).

Hodnoty v **připojovacích řetězcích** nejsou nikdy publikovány.

Hodnoty nastavení aplikace funkce lze také číst ve vašem kódu jako proměnné prostředí. Další informace naleznete v tématu [Proměnné prostředí](functions-dotnet-class-library.md#environment-variables).

## <a name="configure-the-project-for-local-development"></a>Konfigurace projektu pro místní rozvoj

Za běhu Functions používá účet Úložiště Azure interně. Pro všechny typy aktivačních událostí než HTTP a webhooky je nutné nastavit klíč **Values.AzureWebJobsStorage** na platný připojovací řetězec účtu úložiště Azure. Vaše aplikace funkce můžete také použít [emulátor úložiště Azure](../storage/common/storage-use-emulator.md) pro nastavení připojení **AzureWebJobsStorage,** které je vyžadováno projektem. Chcete-li použít emulátor, nastavte hodnotu **AzureWebJobsStorage** na `UseDevelopmentStorage=true`. Změňte toto nastavení na řetězec připojení skutečného účtu úložiště před nasazením.

Nastavení připojovacího řetězce účtu úložiště:

1. V Sadě Visual Studio **otevřete Průzkumníka Cloud**, > **rozbalte účet úložiště, váš účet úložiště**, pak na kartě **Vlastnosti** zkopírujte hodnotu **Primární připojovací řetězec.** **Storage Account**

2. V projektu otevřete soubor local.settings.json a nastavte hodnotu klíče **AzureWebJobsStorage** na zkopírovaný připojovací řetězec.

3. Opakováním předchozího kroku přidejte do pole **Hodnoty** jedinečné klíče pro všechna ostatní připojení vyžadovaná vašimi funkcemi. 

## <a name="add-a-function-to-your-project"></a>Přidání funkce do projektu

Ve funkcích knihovny tříd jazyka C# jsou vazby používané funkcí definovány použitím atributů v kódu. Když vytvoříte aktivační události funkce z poskytnutých šablon, budou pro vás použity atributy aktivační události. 

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na uzel projektu a vyberte **Přidat** > **Nová položka**. Vyberte **Funkce Azure**, zadejte **název** třídy a klepněte na tlačítko **Přidat**.

2. Zvolte aktivační událost, nastavte vlastnosti vazby a klepněte na **vytvořit**. Následující příklad ukazuje nastavení při vytváření funkce aktivované úložiště fronty. 

    ![Vytvoření funkce spuštěné frontou](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Tento příklad aktivační události používá připojovací řetězec s klíčem s názvem **QueueStorage**. Toto nastavení připojovacího řetězce musí být definováno v [souboru local.settings.json](functions-run-local.md#local-settings-file).

3. Prozkoumejte nově přidanou třídu. Zobrazí se statická **metoda Run,** která je přiřazena atributu **FunctionName.** Tento atribut označuje, že metoda je vstupním bodem pro funkci.

    Například následující třída C# představuje základní funkci aktivovanou úložištěm fronty:

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

    Atribut specifický pro vazbu je použit pro každý parametr vazby dodávaný metodě vstupního bodu. Atribut přebírá informace o vazbě jako parametry. V předchozím příkladu má první parametr použitý atribut **QueueTrigger,** což označuje funkci spuštěnou frontou. Název fronty a název nastavení připojovacího řetězce jsou předány jako parametry atributu **QueueTrigger.** Další informace najdete v tématu [Azure Queue vazby úložiště pro funkce Azure](functions-bindings-storage-queue-trigger.md).

Pomocí výše uvedeného postupu můžete přidat další funkce do projektu aplikace funkce. Každá funkce v projektu může mít jinou aktivační událost, ale funkce musí mít přesně jednu aktivační událost. Další informace najdete v [tématu Azure Functions aktivační události a vazby koncepty](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Přidání vazeb

Stejně jako u aktivačních událostí jsou vstupní a výstupní vazby přidány do funkce jako atributy vazby. Přidejte vazby do funkce takto:

1. Ujistěte se, že jste [nakonfigurovali projekt pro místní rozvoj](#configure-the-project-for-local-development).

2. Přidejte odpovídající balíček rozšíření NuGet pro konkrétní vazbu. Další informace naleznete [v tématu místní vývoj jazyka C# pomocí sady Visual Studio](./functions-bindings-register.md#local-csharp) v aktivační události a vazby článku. Požadavky balíčku NuGet specifické pro vazbu jsou k dispozici v referenčním článku pro vazbu. Například najít požadavky na balíček pro event huby aktivační události v [článku odkaz na vazby centra událostí](functions-bindings-event-hubs.md).

3. Pokud existují nastavení aplikace, které vazba potřebuje, přidejte je do kolekce **Hodnoty** v [místním souboru nastavení](functions-run-local.md#local-settings-file). Tyto hodnoty se používají při spuštění funkce místně. Když se funkce spustí v aplikaci funkce v Azure, nastavení [aplikace funkce](#function-app-settings) se používají.

4. Přidejte odpovídající atribut vazby k podpisu metody. V následujícím příkladu zpráva fronty aktivuje funkci a výstupní vazba vytvoří novou zprávu fronty se stejným textem v jiné frontě.

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
   Připojení k úložišti fronty `AzureWebJobsStorage` je získáno z nastavení. Další informace naleznete v referenčním článku pro konkrétní vazbu. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testování funkcí

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. K instalaci těchto nástrojů budete vyzváni při prvním spuštění funkce ze sady Visual Studio.

Pokud chcete funkci otestovat, stiskněte F5. Po výzvě přijměte požadavek ze sady Visual Studio na stažení a instalaci nástrojů Azure Functions Core (CLI). Může být také potřeba povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP.

Se spuštěným projektem můžete otestovat kód tak, jak byste testovali nasazenou funkci. Další informace najdete [v tématu Strategies for testing your code in Azure Functions](functions-test-a-function.md). Při spuštění v režimu ladění zarážky jsou přístupů v sadě Visual Studio podle očekávání. 

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->

Další informace o používání základních nástrojů Azure Functions najdete v [tématu Kód a testování funkcí Azure místně](functions-run-local.md).

## <a name="publish-to-azure"></a>Publikování aplikací do Azure

Při publikování z visual studia se používá jedna ze dvou metod nasazení:

* [Nasazení webu](functions-deployment-technologies.md#web-deploy-msdeploy): balíčky a nasazuje aplikace pro Windows na libovolný server služby IIS.
* [Nasazení zipu s povoleným run-from-package:](functions-deployment-technologies.md#zip-deploy)doporučeno pro nasazení funkcí Azure.

Pomocí následujících kroků publikujte projekt do aplikace funkce v Azure.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Nastavení aplikace funkcí

Všechna nastavení, která jste přidali v local.settings.json, musí být také přidána do aplikace funkce v Azure. Tato nastavení se při publikování projektu nenahrají automaticky.

Nejjednodušší způsob, jak nahrát požadovaná nastavení do aplikace funkce v Azure, je použít odkaz **Spravovat nastavení aplikací...** který se zobrazí po úspěšném publikování projektu.

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Zobrazí se dialogové **okno Nastavení aplikace** pro aplikaci funkce, kde můžete přidat nové nastavení aplikace nebo upravit stávající nastavení.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Local** představuje hodnotu nastavení v souboru local.settings.json a **Remote** je aktuální nastavení v aplikaci funkce v Azure.  Zvolte **Přidat nastavení,** chcete-li vytvořit nové nastavení aplikace. Ke zkopírování hodnoty nastavení do pole **Vzdálené** použijte **odkaz Vložit z místního.** Čekající změny se zapisují do souboru místního nastavení a aplikace funkcí, když vyberete **OK**.

> [!NOTE]
> Ve výchozím nastavení není soubor local.settings.json zařazován do správy zdrojového kódu. To znamená, že při klonování projektu místní funkce ze správy zdrojového kódu, projekt nemá soubor local.settings.json. V takovém případě je třeba ručně vytvořit soubor local.settings.json v kořenovém adresáři projektu tak, aby dialogové okno **Nastavení aplikace** fungovalo podle očekávání. 

Nastavení aplikace můžete spravovat také jedním z těchto způsobů:

* [Pomocí portálu Azure](functions-how-to-use-azure-function-app-settings.md#settings).
* [Použití `--publish-local-settings` možnosti publikování v základních nástrojích Azure Functions](functions-run-local.md#publish).
* [Použití příkazového příkazu k webu Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

## <a name="monitoring-functions"></a>Monitorovací funkce

Doporučený způsob, jak sledovat provádění vašich funkcí, je integrace vaší funkční aplikace s Azure Application Insights. Když na webu Azure Portal vytvoříte aplikaci funkcí, tato integrace se ve výchozím nastavení provádí za vás. Když ale vytvoříte aplikaci funkcí během publikování v Sadě Visual Studio, integrace do aplikace funkcí v Azure se neprovádí.

Povolení application insights pro vaši funkční aplikaci:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Další informace najdete [v tématu Monitorování funkcí Azure](functions-monitoring.md).

## <a name="next-steps"></a>Další kroky

Další informace o základních nástrojích Azure functions najdete v [tématu Kód a testování funkcí Azure místně](functions-run-local.md).

Další informace o vývoji funkcí jako knihoven tříd .NET najdete v [tématu Azure Functions C# odkaz na vývojáře](functions-dotnet-class-library.md). Tento článek také odkazy na příklady, jak používat atributy deklarovat různé typy vazeb podporovaných Funkce Azure.    
