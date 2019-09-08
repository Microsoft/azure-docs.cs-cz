---
title: Vývoj Azure Functions pomocí sady Visual Studio | Microsoft Docs
description: Naučte se vyvíjet a testovat Azure Functions pomocí Azure Functionsch nástrojů pro Visual Studio 2019.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: glenga
ms.openlocfilehash: ebc900735dfbb25206c4b22e3d20da62d85c61df
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773160"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Vývoj Azure Functions pomocí sady Visual Studio  

Visual Studio umožňuje vyvíjet, testovat a nasazovat C# funkce knihovny tříd do Azure. Pokud je to vaše první prostředí s Azure Functions, můžete získat další informace v [úvodu k Azure Functions](functions-overview.md).

Visual Studio poskytuje při vývoji funkcí následující výhody: 

* Upravovat, sestavovat a spouštět funkce na místním počítači pro vývoj. 
* Publikujte svůj Azure Functions projekt přímo do Azure a podle potřeby vytvořte prostředky Azure. 
* Použijte C# atributy k deklaraci vazeb funkcí přímo v C# kódu.
* Vývoj a nasazení předem kompilovaných C# funkcí Předem splněné funkce poskytují lepší výkon pro studený start než C# funkce založené na skriptech. 
* Nahlaste své C# funkce v nástroji a přitom Využijte výhod vývoje sady Visual Studio. 

Tento článek poskytuje podrobné informace o tom, jak používat Visual Studio C# k vývoji funkcí knihoven tříd a jejich publikování do Azure. Před čtením tohoto článku byste měli dokončit rychlé zprovoznění [funkcí pro Visual Studio](functions-create-your-first-function-visual-studio.md). 

Pokud není uvedeno jinak, postupy a příklady jsou uvedeny pro Visual Studio 2019. 

## <a name="prerequisites"></a>Požadavky

Azure Functions nástroje jsou součástí úlohy vývoje Azure sady Visual Studio počínaje sadou Visual Studio 2017. Ujistěte se, že jste do instalace sady Visual Studio zahrnuli úlohu **vývoj pro Azure** .

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Další prostředky, které potřebujete, například účet Azure Storage, se ve vašem předplatném vytvoří během procesu publikování.

> [!NOTE]
> V aplikaci Visual Studio 2017 nainstalují úlohy vývoje Azure nástroje Azure Functions jako samostatné rozšíření. Při aktualizaci sady Visual Studio 2017 se ujistěte, že používáte nejnovější [verzi](#check-your-tools-version) Azure Functions nástrojů. V následujících částech se dozvíte, jak kontrolovat a (v případě potřeby) aktualizovat rozšíření Azure Functions nástrojů v aplikaci Visual Studio 2017. 
>
> Pokud používáte Visual Studio 2019, přeskočte prosím tyto oddíly.

### <a name="check-your-tools-version"></a>Podívejte se na verzi nástrojů v aplikaci Visual Studio 2017

1. Z **nástroje** nabídce zvolte **rozšíření a aktualizace**. Rozbalte položku **nainstalované** > **nástroje** a vyberte možnost **Nástroje pro Azure functions a webové úlohy**.

    ![Ověření verze nástrojů Functions](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Poznamenejte si nainstalovanou **verzi**. Tuto verzi můžete porovnat s nejnovější verzí uvedenou [v poznámkách k verzi](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

1. Pokud je vaše verze starší, aktualizujte své nástroje v sadě Visual Studio, jak je znázorněno v následující části.

### <a name="update-your-tools-in-visual-studio-2017"></a>Aktualizace nástrojů v aplikaci Visual Studio 2017

1. V dialogovém okně **rozšíření a aktualizace** rozbalte položku **aktualizace** > **Visual Studio Marketplace**, zvolte **Azure functions a nástroje webové úlohy** a vyberte **aktualizovat**.

    ![Aktualizace verze nástrojů Functions](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. Po stažení aktualizace nástroje zavřete sadu Visual Studio na trigger nástroje aktualizace pomocí instalátor VSIX.

1. V instalačním programu klikněte na **tlačítko OK** a začněte tak, že kliknete na tlačítko **Upravit** a aktualizujete nástroje. 

1. Po dokončení aktualizace klikněte na **Zavřít** a restartujte Visual Studio.

> [!NOTE]  
V aplikaci Visual Studio 2019 nebo novější je rozšíření Azure Functionsch nástrojů aktualizováno v rámci sady Visual Studio.  

## <a name="create-an-azure-functions-project"></a>Vytvoření projektu Azure Functions

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Šablona projektu vytvoří C# projekt, nainstaluje `Microsoft.NET.Sdk.Functions` balíček NuGet a nastaví cílovou architekturu. Nový projekt má následující soubory:

* **host.json**: Umožňuje konfigurovat hostitele funkcí. Tato nastavení platí při místním spuštění i v Azure. Další informace naleznete v tématu [reference Host. JSON](functions-host-json.md).

* **local.settings.json**: Udržuje nastavení použitá při místním spouštění funkcí. Tato nastavení se nepoužívají při spuštění v Azure. Další informace najdete v tématu [místní nastavení souboru](#local-settings-file).

    >[!IMPORTANT]
    >Vzhledem k tomu, že soubor Local. Settings. JSON může obsahovat tajné kódy, je nutné jej vyloučit ze správy zdrojového kódu projektu. Nastavení **Kopírovat do výstupního adresáře** pro tento soubor by se mělo vždycky **Kopírovat, pokud je novější**. 

Další informace naleznete v tématu [Functions Class Library Project](functions-dotnet-class-library.md#functions-class-library-project).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Nastavení v Local. Settings. JSON se při publikování projektu automaticky neodesílají. Abyste se ujistili, že tato nastavení existují i ve vaší aplikaci Function App v Azure, musíte je po publikování projektu nahrát. Další informace najdete v tématu [nastavení aplikace Function App](#function-app-settings).

Hodnoty v **connectionStrings** se nikdy nepublikují.

Hodnoty nastavení aplikace Function App lze ve vašem kódu přečíst také jako proměnné prostředí. Další informace naleznete v tématu [proměnné prostředí](functions-dotnet-class-library.md#environment-variables).

## <a name="configure-the-project-for-local-development"></a>Konfigurace projektu pro místní vývoj

Modul runtime Functions používá interní účet Azure Storage. Pro všechny typy triggerů kromě HTTP a webhooků je potřeba nastavit klíč **Values. AzureWebJobsStorage** na platný připojovací řetězec účtu Azure Storage. Aplikace Function App může také použít [emulátor úložiště Azure](../storage/common/storage-use-emulator.md) pro nastavení připojení **AzureWebJobsStorage** , které je vyžadováno projektem. Chcete-li použít emulátor, nastavte hodnotu **AzureWebJobsStorage** na `UseDevelopmentStorage=true`. Před nasazením toto nastavení změňte na skutečný připojovací řetězec účtu úložiště.

Nastavení připojovacího řetězce účtu úložiště:

1. V aplikaci Visual Studio otevřete **Průzkumníka cloudu**, rozbalte položku **účet** > úložiště**účtu úložiště**a potom na kartě **vlastnosti** Zkopírujte hodnotu **primární připojovací řetězec** .

2. V projektu otevřete soubor Local. Settings. JSON a nastavte hodnotu klíče **AzureWebJobsStorage** na připojovací řetězec, který jste zkopírovali.

3. Opakujte předchozí krok a přidejte jedinečné klíče do pole **hodnoty** pro všechna ostatní připojení požadovaná funkcemi. 

## <a name="add-a-function-to-your-project"></a>Přidání funkce do projektu

Ve C# funkcích knihovny tříd jsou vazby používané funkcí definovány použitím atributů v kódu. Při vytváření aktivační události funkce ze zadaných šablon se pro vás aplikují atributy triggeru. 

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na uzel projektu a vyberte **Přidat** > **Nová položka**. Vyberte **funkce Azure**, zadejte **název** třídy a klikněte na **Přidat**.

2. Vyberte aktivační událost, nastavte vlastnosti vazby a klikněte na **vytvořit**. Následující příklad ukazuje nastavení při vytváření funkce aktivované úložištěm Queue. 

    ![Vytvoření funkce aktivované frontou](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Tento příklad triggeru používá připojovací řetězec s klíčem s názvem **QueueStorage**. Toto nastavení připojovacího řetězce musí být definováno v [souboru Local. Settings. JSON](functions-run-local.md#local-settings-file).

3. Projděte si nově přidanou třídu. Zobrazí se statická metoda **Run** , která je označena atributem **Function** . Tento atribut označuje, že metoda je vstupním bodem pro funkci.

    Například následující C# třída reprezentuje funkci aktivovanou pro úložiště front:

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

    Atribut specifický pro vazbu se použije na každý parametr vazby dodaný metodě vstupního bodu. Atribut přebírá informace o vazbě jako parametry. V předchozím příkladu má první parametr použit atribut **QueueTrigger** , který označuje funkci aktivovanou ve frontě. Název fronty a název nastavení připojovacího řetězce jsou předány jako parametry atributu **QueueTrigger** . Další informace najdete v tématu [vazby Azure Queue Storage pro Azure Functions](functions-bindings-storage-queue.md#trigger---c-example).

Výše uvedený postup můžete použít k přidání dalších funkcí do projektu Function App. Každá funkce v projektu může mít jinou aktivační událost, ale funkce musí mít právě jednu aktivační událost. Další informace najdete v tématu [Azure Functions triggery a koncepty vazeb](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Přidat vazby

Stejně jako u triggerů se vstupní a výstupní vazby přidávají do funkce jako atributy vazby. Přidejte vazby k funkci následujícím způsobem:

1. Ujistěte se, že jste [nakonfigurovali projekt pro místní vývoj](#configure-the-project-for-local-development).

2. Přidejte příslušný balíček rozšíření NuGet pro konkrétní vazbu. Další informace naleznete v tématu [místní C# vývoj pomocí sady Visual Studio](./functions-bindings-register.md#local-csharp) v článku triggery a vazby. Požadavky na balíček NuGet specifické pro vazbu najdete v referenčním článku pro vazbu. Můžete například vyhledat požadavky balíčku pro aktivační událost Event Hubs v [článku odkaz Event Hubs vazby](functions-bindings-event-hubs.md).

3. Pokud existují nastavení aplikace, která vazba potřebuje, přidejte je do kolekce **Values** v [souboru místního nastavení](functions-run-local.md#local-settings-file). Tyto hodnoty se používají, když se funkce spustí místně. Když se funkce spustí v aplikaci Function App v Azure, použijí se [nastavení aplikace Function App](#function-app-settings) .

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
   Z `AzureWebJobsStorage` nastavení se získá připojení k úložišti front. Další informace najdete v referenčním článku pro konkrétní vazbu. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testování funkcí

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. K instalaci těchto nástrojů budete vyzváni při prvním spuštění funkce ze sady Visual Studio.

Pokud chcete funkci otestovat, stiskněte F5. Po výzvě přijměte požadavek ze sady Visual Studio na stažení a instalaci nástrojů Azure Functions Core (CLI). Může být také potřeba povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP.

V případě, že je spuštěný projekt, můžete otestovat kód stejně, jako byste otestovali nasazenou funkci. Další informace najdete v tématu [strategie pro testování kódu v Azure Functions](functions-test-a-function.md). Při spuštění v režimu ladění se zarážky narazí v aplikaci Visual Studio podle očekávání. 

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->

Další informace o používání Azure Functions Core Tools najdete v tématu [Code and test Azure Functions v místním](functions-run-local.md)prostředí.

## <a name="publish-to-azure"></a>Publikování do Azure

Při publikování ze sady Visual Studio se používá jedna ze dvou metod nasazení:

* [Nasazení webu](functions-deployment-technologies.md#web-deploy-msdeploy): balíčky a nasadí aplikace pro Windows na libovolný server IIS.
* [Nasazení souboru zip s povoleným spuštěním z balíčku](functions-deployment-technologies.md#zip-deploy): doporučuje se pro nasazení Azure Functions.

Pomocí následujících kroků můžete projekt publikovat do aplikace Function App v Azure.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Nastavení Function App

Všechna nastavení, která jste přidali v Local. Settings. JSON, se musí taky přidat do aplikace Function App v Azure. Tato nastavení nejsou nahrána automaticky při publikování projektu.

Nejjednodušším způsobem, jak nahrát požadovaná nastavení do aplikace Function App v Azure, je použít odkaz **Spravovat nastavení aplikace...** , který se zobrazí po úspěšném publikování projektu.

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Tím se zobrazí dialogové okno **nastavení aplikace** pro aplikaci Function App, kde můžete přidat nová nastavení aplikace nebo upravit stávající.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Místní** představuje hodnotu nastavení v souboru Local. Settings. JSON a **Vzdálená** je aktuální nastavení v aplikaci Function App v Azure.  Vyberte **Přidat nastavení** a vytvořte nové nastavení aplikace. K zkopírování hodnoty nastavení do **vzdáleného** pole použijte odkaz **Vložit hodnotu z místního** . Nedokončené změny se zapisují do souboru místního nastavení a aplikace Function App, když vyberete **OK**.

> [!NOTE]
> Ve výchozím nastavení se soubor Local. Settings. JSON nekontroluje do správy zdrojového kódu. To znamená, že když naklonete projekt místní funkce ze správy zdrojového kódu, projekt nemá soubor Local. Settings. JSON. V takovém případě je nutné ručně vytvořit soubor Local. Settings. JSON v kořenu projektu, aby dialog **nastavení aplikace** fungoval podle očekávání. 

Nastavení aplikace můžete spravovat také jedním z těchto způsobů:

* [Použití Azure Portal](functions-how-to-use-azure-function-app-settings.md#settings).
* [Použití možnosti publikovatvAzureFunctionsCoreTools.`--publish-local-settings` ](functions-run-local.md#publish)
* [Pomocí Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

## <a name="monitoring-functions"></a>Funkce monitorování

Doporučeným způsobem, jak monitorovat provádění vašich funkcí, je integrace aplikace Function App s Azure Application Insights. Když vytvoříte aplikaci funkcí v Azure Portal, tato integrace se ve výchozím nastavení provádí. Při vytváření aplikace Function App během publikování sady Visual Studio ale nebude dokončena integrace v aplikaci Function App v Azure.

Povolení Application Insights pro aplikaci Function App:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md).

## <a name="next-steps"></a>Další kroky

Další informace o Azure Functions Core Tools najdete v tématu [Code and test Azure Functions v místním](functions-run-local.md)prostředí.

Další informace o vývoji funkcí jako knihoven tříd .NET naleznete v tématu [Azure Functions C# reference pro vývojáře](functions-dotnet-class-library.md). Tento článek také obsahuje odkazy na příklady použití atributů k deklaraci různých typů vazeb, které jsou podporovány nástrojem Azure Functions.    
