---
title: Vývoj pomocí sady Visual Studio Azure Functions | Dokumentace Microsoftu
description: Zjistěte, jak vyvíjet a testovat funkce Azure s využitím Azure Functions Tools for Visual Studio 2017.
services: functions
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: glenga
ms.openlocfilehash: 318a39e244f0fca3a1b2d8531dd9197a15400e02
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205353"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Vývoj Azure Functions pomocí sady Visual Studio  

Azure Functions Tools for Visual Studio 2017 je rozšířením pro Visual Studio, která umožňuje vývoj, testování a nasazení funkce jazyka C# do Azure. Pokud toto prostředí je první s využitím Azure Functions, další informace najdete v [Úvod do služby Azure Functions](functions-overview.md).

Nástroje Azure Functions nabízí následující výhody: 

* Upravit, vytvářet a spouštět službu functions na místním počítači pro vývoj. 
* Projekt Azure Functions publikujte přímo do Azure. 
* Chcete-li deklarovat funkci vazby přímo v kódu jazyka C# namísto zachování samostatné function.json pro vazby definice použijte atributy WebJobs.
* Vývoj a nasazení předem kompilovaných funkcí jazyka C#. Předem splněny functions poskytuje lepší studený start výkonu než založených na skriptech funkcí jazyka C#. 
* Kód vaší funkce v jazyce C# přitom má všechny výhody vývoj sady Visual Studio. 

V tomto článku se dozvíte, jak používat Azure Functions Tools for Visual Studio 2017 na vývoj funkcí v jazyce C#. Také se dozvíte, jak publikovat projekt do Azure jako sestavení .NET.

> [!IMPORTANT]
> Nekombinujte místní vývoj pomocí portálu ve stejné aplikaci function app. Při publikování z místní projekt aplikace function app, procesu nasazení přepíše všechny funkce, které jste vytvořili na portálu.

## <a name="prerequisites"></a>Požadavky

Nástroje Azure Functions je součástí sady funkcí vývoj pro Azure [Visual Studio 2017 verze 15.5](https://www.visualstudio.com/vs/), nebo novější. Ujistěte se, že zahrnete **vývoj pro Azure** úloh v instalaci sady Visual Studio 2017:

![Instalace sady Visual Studio 2017 se sadou funkcí Vývoj pro Azure](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Ujistěte se, že Visual Studio je aktuální a že používáte [nejnovější verzi](#check-your-tools-version) nástrojů Azure Functions.

### <a name="other-requirements"></a>Další požadavky

Vytvoření a nasazení služby functions, budete také muset:

* Aktivní předplatné Azure. Pokud nemáte předplatné Azure, [bezplatné účty](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) jsou k dispozici.

* Účet služby Azure Storage. Vytvoření účtu úložiště najdete v tématu [vytvořit účet úložiště](../storage/common/storage-create-storage-account.md#create-a-storage-account).

### <a name="check-your-tools-version"></a>Zkontrolujte verzi nástroje

1. Z **nástroje** nabídce zvolte **rozšíření a aktualizace**. Rozbalte **nainstalováno** > **nástroje** a zvolte **Azure Functions and Web Jobs Tools**.

    ![Ověření verze nástrojů funkce](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

2. Mějte na paměti nainstalované **verze**. Můžete porovnat tato verze na nejnovější verzi, uvedené [v poznámkách k verzi](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

3. Pokud je vaše verze starší, aktualizujte své nástroje v sadě Visual Studio, jak je znázorněno v následující části.

### <a name="update-your-tools"></a>Aktualizovat vaše nástroje

1. V **rozšíření a aktualizace** dialogového okna, rozbalte **aktualizace** > **Visual Studio Marketplace**, zvolte **Azure Functions and Web Jobs Tools**  a vyberte **aktualizace**.

    ![Aktualizovat verzi Functions nástroje](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

2. Po stažení aktualizace nástroje zavřete sadu Visual Studio na trigger nástroje aktualizace pomocí instalátor VSIX.

3. V instalačním programu, zvolte **OK** spuštění a pak **změnit** aktualizace nástrojů. 

4. Po dokončení aktualizace, zvolte **Zavřít** a restartujte aplikaci Visual Studio.

## <a name="create-an-azure-functions-project"></a>Vytvoření projektu Azure Functions

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Šablona projektu vytvoří projektu v jazyce C#, nainstaluje `Microsoft.NET.Sdk.Functions` balíčku NuGet a nastaví cílovou architekturu. Funkce 1.x zaměřené na rozhraní .NET Framework a funkce 2.x cíle .NET Standard. Nový projekt má následující soubory:

* **Host.JSON**: vám umožní nakonfigurovat hostitele funkce. Tato nastavení platí i při spuštění místně i v Azure. Další informace najdete v tématu [referenční materiály k host.json](functions-host-json.md).

* **Local.Settings.JSON**: udržuje nastavení používaná při místním spuštění funkce. Tato nastavení nejsou používány nástrojem Azure, jsou používány [nástrojů Azure Functions Core](functions-run-local.md). Tento soubor můžete použijte k určení nastavení aplikace pro proměnné, které vyžadují vaše funkce. Přidat novou položku do **hodnoty** pole pro každé připojení vyžaduje vazby funkce ve vašem projektu. Další informace najdete v tématu [souboru místní nastavení](functions-run-local.md#local-settings-file) v článku o Azure Functions Core Tools.

Další informace najdete v tématu [projekt knihovny tříd funkce](functions-dotnet-class-library.md#functions-class-library-project).

## <a name="configure-the-project-for-local-development"></a>Konfigurace projektu pro místní vývoj

Modul runtime služby Functions interně používá účet Azure Storage. Pro aktivaci všech typů jiných než HTTP a webhooky, musíte nastavit **Values.AzureWebJobsStorage** klíč platný připojovací řetězec účtu úložiště Azure. Můžete také použít aplikaci function app [emulátoru úložiště Azure](../storage/common/storage-use-emulator.md) pro **AzureWebJobsStorage** nastavení připojení, který je vyžadováno v projektu. Pokud chcete použít emulátor, nastavte hodnotu **AzureWebJobsStorage** k `UseDevelopmentStorage=true`. Toto nastavení musíte změnit na připojení k skutečného úložiště před nasazením.

Chcete-li nastavit připojovací řetězec účtu úložiště:

1. V sadě Visual Studio, otevřete **Průzkumníka cloudu**, rozbalte **účtu úložiště** > **svůj účet úložiště**a pak vyberte **vlastnosti**a zkopírujte **primární připojovací řetězec** hodnotu.

2. Ve vašem projektu otevřete soubor local.settings.json a nastavte hodnotu **AzureWebJobsStorage** zkopírujete připojovací řetězec klíče.

3. Opakujte předchozí krok a přidejte jedinečné klíče **hodnoty** pole pro všechna připojení, které vyžadují vaše funkce.

## <a name="create-a-function"></a>Vytvoření funkce

V předem zkompilované funkce jsou definovány vazby používá funkci použití atributů v kódu. Při použití nástrojů Azure Functions k vytvoření funkce z dodané šablony tyto atributy jsou použity pro vás. 

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na uzel projektu a vyberte **Přidat** > **Nová položka**. Vyberte **funkce Azure Functions**, zadejte **název** pro třídu a klikněte na tlačítko **přidat**.

2. Zvolte aktivační událost, nastavte vlastnosti vazby a klikněte na tlačítko **vytvořit**. Následující příklad ukazuje nastavení při vytváření fronty úložiště aktivaci funkce. 

    ![Vytvoření funkce aktivované frontou](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    V tomto příkladu aktivační události používá připojovací řetězec s klíčem s názvem **QueueStorage**. Tato nastavení připojovacího řetězce musí být definován v [souboru local.settings.json](functions-run-local.md#local-settings-file).

3. Prozkoumejte nově přidané třídy. Zobrazí se statickou **spustit** metody s atributem **FunctionName** atribut. Tento atribut označuje, že metoda je vstupním bodem pro funkci.

    Například následující třídy C# představuje základní funkce úložiště aktivované frontou:

    ````csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", Connection = "QueueStorage")]string myQueueItem, TraceWriter log)
            {
                log.Info($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ````
    Atribut specifické pro vazbu se použije pro každý parametr vazby zadaný pro metodu vstupního bodu. Atribut přijímá informace o vazbě jako parametry. V předchozím příkladu má první parametr **QueueTrigger** atribut, která funkce aktivované frontou. Název fronty a nastavení název připojovacího řetězce jsou předány jako parametry **QueueTrigger** atribut. Další informace najdete v tématu [vazby úložiště front Azure pro službu Azure Functions](functions-bindings-storage-queue.md#trigger---c-example).
    
Výše uvedený postup slouží k přidání dalších funkcí pro váš projekt aplikace funkcí. Každá funkce v projektu může mít jinou aktivační událost, ale funkce musí mít přesně jeden trigger. Další informace najdete v tématu [aktivace Azure Functions a vazby koncepty](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Přidat vazby

Stejně jako u triggerů, vstupní a výstupní vazby jsou přidány do funkce jako atributů vazby. Přidejte vazby na funkce takto:

1. Ujistěte se, že máte [nakonfigurovali projekt pro místní vývoj](#configure-the-project-for-local-development).

2. Přidejte si odpovídající balíček NuGet rozšíření pro konkrétní vazbu. Další informace najdete v tématu [místní C# vývoj pomocí sady Visual Studio](functions-triggers-bindings.md#local-csharp) v článku triggerů a vazeb. Požadavky balíčku NuGet specifické pro vazbu se nacházejí v článku odkaz pro vazbu. Například vyhledejte balíček požadavky pro trigger služby Event Hubs v [článku vazby služby Event Hubs](functions-bindings-event-hubs.md).

3. Pokud jsou nastavení aplikace, které potřebuje vazby, přidejte je do **hodnoty** kolekce [místní nastavení souboru](functions-run-local.md#local-settings-file). Tyto hodnoty se používají, když je funkce spuštěná místně. Když je funkce spuštěná v aplikaci function app v Azure, [fungovat nastavení aplikace](#function-app-settings) se používají.

4. Přidejte atribut příslušnou datovou vazbu do podpisu metody. V následujícím příkladu zpráva fronty aktivuje funkci a výstupní vazby vytvoří nové zprávy fronty se stejný text do jiné fronty.

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            TraceWriter log)
        {
            log.Info($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
Připojení k frontě úložiště se získává z `AzureWebJobsStorage` nastavení. Další informace najdete v článku odkaz pro konkrétní vazbu. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testování funkcí

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. K instalaci těchto nástrojů budete vyzváni při prvním spuštění funkce ze sady Visual Studio.

Pokud chcete funkci otestovat, stiskněte F5. Po výzvě přijměte požadavek ze sady Visual Studio na stažení a instalaci nástrojů Azure Functions Core (CLI). Může být také potřeba povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP.

Spuštění k projektu můžete otestovat kód, jako byste testovali nasazenou funkci. Další informace najdete v tématu [strategie pro testování kódu ve službě Azure Functions](functions-test-a-function.md). Při spuštění v režimu ladění, zarážky v sadě Visual Studio podle očekávání. 

Příklad toho, jak testovat funkce aktivované frontou, najdete v článku [rychlý úvodní kurz funkce aktivované frontou](functions-create-storage-queue-triggered-function.md#test-the-function).  

Další informace o používání nástrojů Azure Functions Core najdete v tématu [kódu a testování Azure functions místně](functions-run-local.md).

## <a name="publish-to-azure"></a>Publikování aplikací do Azure

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Nastavení Function App

Všechna nastavení, které jste přidali v kroku local.settings.json musí být rovněž přidán do aplikace function app v Azure. Tato nastavení nejsou automaticky nahraje při publikování tohoto projektu.

Nejjednodušší způsob, jak nahrát požadovaná nastavení do vaší aplikace funkcí v Azure je použít **spravovat nastavení aplikace...**  odkaz, který se zobrazí po úspěšném publikování projektu. 

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Zobrazí se **nastavení aplikace** dialogové okno pro aplikaci function app, ve kterém můžete přidat nové nastavení aplikace nebo upravte stávající.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

Můžete také spravovat nastavení aplikace v jednom z těchto způsobů:

* [Pomocí webu Azure portal](functions-how-to-use-azure-function-app-settings.md#settings).
* [Použití `--publish-local-settings` možnost publikování v Azure Functions Core Tools](functions-run-local.md#publish).
* [Použití Azure CLI](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set). 

## <a name="next-steps"></a>Další postup

Další informace o Azure Functions Tools najdete v části Nejčastější dotazy [Visual Studio 2017 Tools pro službu Azure Functions](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/) blogový příspěvek.

Další informace o Azure Functions Core Tools, naleznete v tématu [kódu a testování Azure functions místně](functions-run-local.md).

Další informace o vývoji funkcí jako knihoven tříd .NET najdete v tématu [referenční informace pro vývojáře Azure Functions C#](functions-dotnet-class-library.md). Tento článek taky obsahuje odkazy na příklady toho, jak použít atributy k deklarování různé typy vazeb Azure Functions podporuje.    
