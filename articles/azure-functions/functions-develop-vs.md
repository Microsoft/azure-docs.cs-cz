---
title: Vývoj pomocí sady Visual Studio Azure Functions | Microsoft Docs
description: Zjistěte, jak pro vývoj a testování pomocí nástroje Azure funkce pro Visual Studio 2017 Azure Functions.
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
ms.openlocfilehash: bc280e9b4ade8a4fb8107ec2dcc1c33f538472e1
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081556"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Vývoj Azure Functions pomocí sady Visual Studio  

Azure funkce Tools for Visual Studio 2017 představuje rozšíření pro Visual Studio, která umožňuje vývoj, testování a nasazení funkce jazyka C# do Azure. Pokud toto prostředí je první díky Azure Functions, další informace najdete v [Úvod do Azure Functions](functions-overview.md).

Funkce nástroje Azure poskytuje následující výhody: 

* Upravit, sestavte a spusťte funkce ve svém místním vývojovém počítači. 
* Publikování projektu Azure Functions přímo do Azure. 
* Pomocí atributů WebJobs deklarovat vazby funkcí přímo v kódu jazyka C# místo zachování samostatné function.json pro vazby definice.
* Vývoj a nasazení předem kompilované funkce jazyka C#. Předem dodržela funkce zajistit lepší studený start výkonu než založených na skriptech funkcí jazyka C#. 
* Přitom má všechny výhody vývoje v sadě Visual Studio Code funkcí v jazyce C#. 

Tento článek ukazuje, jak pomocí nástroje Azure funkce pro Visual Studio 2017 vyvíjet funkcí v jazyce C#. Také zjistíte, jak publikovat projekt do Azure jako sestavení rozhraní .NET.

> [!IMPORTANT]
> Nekombinujte místní vývoj pomocí portálu vývoj v aplikaci stejné funkce. Při publikování z místní projektu do aplikace pro funkce procesu nasazení přepíše všechny funkce, které jste vytvořili v portálu.

## <a name="prerequisites"></a>Požadavky

Nástroje Azure funkce je součástí Azure development zatížení [Visual Studio 2017 verze 15,5](https://www.visualstudio.com/vs/), nebo novější. Zajistěte, aby zahrnete **Azure development** zatížení v instalaci sady Visual Studio 2017:

![Instalace sady Visual Studio 2017 se sadou funkcí Vývoj pro Azure](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Ujistěte se, že je aktuální Visual Studio a že používáte [nejnovější verzi](#check-your-tools-version) nástroje Azure Functions.

### <a name="other-requirements"></a>Další požadavky

Pokud chcete vytvořit a nasadit funkce, budete potřebovat:

* Aktivní předplatné Azure. Pokud nemáte předplatné Azure, [volné účty](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) jsou k dispozici.

* Účet služby Azure Storage. Pokud chcete vytvořit účet úložiště, najdete v části [vytvořit účet úložiště](../storage/common/storage-create-storage-account.md#create-a-storage-account).

### <a name="check-your-tools-version"></a>Zkontrolujte vaši verzi nástroje

1. Z **nástroje** nabídce zvolte **rozšíření a aktualizace**. Rozbalte položku **nainstalovaná** > **nástroje** a zvolte **Azure Functions a webové úlohy nástroje**.

    ![Ověřit verzi nástroje pro funkce](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

2. Všimněte si, nainstalovaná **verze**. Můžete porovnat tato verze na nejnovější verzi uvedené [v poznámkách k verzi](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

3. Pokud je vaše verze starší, aktualizace vašeho nástrojů v sadě Visual Studio, jak je znázorněno v následující části.

### <a name="update-your-tools"></a>Aktualizace vašeho nástrojů

1. V **rozšíření a aktualizace** dialogové okno, rozbalte položku **aktualizace** > **Visual Studio Marketplace**, zvolte **Azure Functions a webové úlohy nástroje**  a vyberte **aktualizace**.

    ![Aktualizace funkcí verze nástroje](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

2. Po stažení nástroje pro aktualizaci, zavřete Visual Studio k aktivační události nástroje aktualizace pomocí instalačního programu VSIX.

3. V instalačním programu vyberte **OK** spusťte a potom **upravit** aktualizace nástroje. 

4. Po dokončení aktualizace, zvolte **Zavřít** a restartujte Visual Studio.

## <a name="create-an-azure-functions-project"></a>Vytvoření projektu Azure Functions

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Šablona projektu vytvoří projekt C#, nainstaluje `Microsoft.NET.Sdk.Functions` balíček NuGet a nastaví cílové rozhraní. Funkce 1.x cíle rozhraní .NET Framework a funkce 2.x cíle .NET Standard. Nový projekt má následující soubory:

* **Host.JSON**: vám umožní nakonfigurovat funkce hostitele. Toto nastavení se týká i při spuštění místně a v Azure. Další informace najdete v tématu [host.json odkaz](functions-host-json.md).

* **Local.Settings.JSON**: udržuje nastavení používané při místním spuštění funkce. Tato nastavení nejsou používány nástrojem Azure, jsou používány [nástroje základní funkce Azure](functions-run-local.md). Tento soubor lze použijte k určení nastavení aplikace pro proměnné, které vyžadují funkcí. Přidat novou položku do **hodnoty** pole pro každé připojení vyžadují vazby funkce v projektu. Další informace najdete v tématu [nastavení místního souboru](functions-run-local.md#local-settings-file) v článku nástroje základní funkce Azure.

Další informace najdete v tématu [projektu knihovny tříd funkce](functions-dotnet-class-library.md#functions-class-library-project).

## <a name="configure-the-project-for-local-development"></a>Konfigurace projektu pro místní vývoj

Modul runtime funkce interně používá účet úložiště Azure. Pro všechny aktivovat jiného typu než HTTP a pomocí webhooků, musíte nastavit **Values.AzureWebJobsStorage** klíče na platný připojovací řetězec účet úložiště Azure. Můžete také použít aplikaci funkce [emulátoru úložiště Azure](../storage/common/storage-use-emulator.md) pro **AzureWebJobsStorage** nastavení připojení vyžadují projektu. Pokud chcete používat emulátor, nastavte hodnotu **AzureWebJobsStorage** k `UseDevelopmentStorage=true`. Toto nastavení musíte změnit na připojení k skutečné úložiště před nasazením.

Nastavení připojovacího řetězce pro účet úložiště:

1. V sadě Visual Studio otevřete **Průzkumník cloudu**, rozbalte položku **účet úložiště** > **váš účet úložiště**, pak vyberte **vlastnosti**a zkopírujte **primární připojovací řetězec** hodnotu.

2. V projektu, otevřete soubor local.settings.json a nastavte hodnotu **AzureWebJobsStorage** zkopírovanou klíče připojovací řetězec.

3. Opakujte předchozí krok pro přidání jedinečné klíče **hodnoty** pole pro všechna připojení, které vyžadují funkcí.

## <a name="create-a-function"></a>Vytvoření funkce

V předem kompilované funkce jsou definovány vazby používané funkce použití atributů v kódu. Když pomocí funkce nástroje Azure vytvářet funkce ze zadaného šablon, použijí se tyto atributy pro vás. 

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na uzel projektu a vyberte **Přidat** > **Nová položka**. Vyberte **funkce Azure**, zadejte **název** pro třídu a klikněte na tlačítko **přidat**.

2. Zvolte aktivační událost, nastavte vlastnosti vazby a klikněte na **vytvořit**. Následující příklad ukazuje nastavení při vytváření Queue storage aktivaci funkce. 

    ![Vytvoření fronty aktivované funkce](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Tento příklad aktivační událost používá připojovací řetězec s klíč s názvem **QueueStorage**. Tato nastavení připojovacího řetězce musí být definován v [local.settings.json soubor](functions-run-local.md#local-settings-file).

3. Zkontrolujte nově přidané třídy. Zobrazí statického **spustit** metody s atributem **%{FunctionName/** atribut. Tento atribut označuje, že metoda je vstupní bod pro funkci.

    Například následující třídy jazyka C# představuje základní funkce úložiště aktivuje fronty:

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
    Atribut specifické pro vazbu se použije pro každý zadaný pro vstupní bod metody parametr vazby. Atribut načítá informace o vazbě jako parametry. V předchozím příkladu má první parametr **QueueTrigger** atribut použité, s označením fronty aktivuje funkce. Název fronty a název nastavení připojovacího řetězce jsou předány jako parametry, které **QueueTrigger** atribut.
    
Výše uvedený postup můžete přidat další funkce do projektu aplikace funkce. Jednotlivé funkce v projektu může mít různé aktivační událost, ale funkce musí mít přesně jeden aktivační události. Další informace najdete v tématu [Azure Functions triggerů a vazeb koncepty](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Přidat vazby

Stejně jako u aktivačních událostí, jsou vstupní a výstupní vazby přidány do funkce jako vazby atributy. Přidáte vazby funkci následujícím způsobem:

1. Zajistěte, aby byla [nakonfigurované projekt pro místní vývoj](#configure-the-project-for-local-development).

2. Přidejte příslušné rozšíření balíček NuGet pro konkrétní vazbu. Další informace najdete v tématu [místní C# vývoj pomocí sady Visual Studio](functions-triggers-bindings.md#local-csharp) v článku triggerů a vazeb. Vazba konkrétních požadavků pro balíček NuGet se nacházejí v článku odkaz pro vazbu. Například vyhledat požadavků balíčku pro aktivační událost Event Hubs v [článku vazby služby Event Hubs](functions-bindings-event-hubs.md).

3. Pokud jsou nastavení aplikace, které potřebuje vazby, přidejte je do **hodnoty** kolekce v [místní nastavení souboru](functions-run-local.md#local-settings-file). Tyto hodnoty se používají, když je funkce spuštěná místně. Když je funkce spuštěná v aplikaci funkce v Azure, [funkce nastavení aplikace](#function-app-settings) se používají.

4. Do podpis metody přidáte atribut odpovídající vazby. V následujícím příkladu fronty zpráv se aktivuje funkce a výstup vazby vytvoří novou zprávu fronty se stejný text do jiné fronty.

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
Připojení k Queue storage se získávají z `AzureWebJobsStorage` nastavení. Další informace najdete v článku odkaz pro konkrétní vazbu. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testování funkcí

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. K instalaci těchto nástrojů budete vyzváni při prvním spuštění funkce ze sady Visual Studio.

Pokud chcete funkci otestovat, stiskněte F5. Po výzvě přijměte požadavek ze sady Visual Studio na stažení a instalaci nástrojů Azure Functions Core (CLI). Může být také potřeba povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP.

S projektem systémem můžete otestovat váš kód by při testování nasazené funkce. Další informace najdete v tématu [strategie pro testování kódu v Azure Functions](functions-test-a-function.md). Při spuštění v režimu ladění, jsou zarážky v sadě Visual Studio podle očekávání. 

Příklad fronty aktivuje funkci otestovat, najdete v článku [fronty aktivuje funkce Rychlý úvodní kurz](functions-create-storage-queue-triggered-function.md#test-the-function).  

Další informace o použití nástrojů pro základní funkce Azure, najdete v části [kód a testovat místně na Azure functions](functions-run-local.md).

## <a name="publish-to-azure"></a>Publikování aplikací do Azure

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Nastavení Function App

Všechna nastavení, které jste přidali v local.settings.json je nutné také přidat do funkce aplikace v Azure. Tato nastavení nejsou uloženy automaticky při publikování tohoto projektu.

Nejjednodušší způsob, jak nahrát požadovaná nastavení pro funkce aplikace v Azure je použití **spravovat nastavení aplikace...**  odkaz, který se zobrazí po úspěšném publikování projektu. 

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Zobrazí se **nastavení aplikace** dialogové okno pro funkce aplikace, kde můžete přidat nové nastavení aplikace nebo upravit existující.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

Můžete také spravovat nastavení aplikace v jednom z těchto dalších způsobů:

* [Pomocí portálu Azure](functions-how-to-use-azure-function-app-settings.md#settings).
* [Pomocí `--publish-local-settings` možnost publikování v nástrojích pro základní funkce Azure](functions-run-local.md#publish).
* [Použití Azure CLI](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set). 

## <a name="next-steps"></a>Další postup

Další informace o nástrojích funkce Azure, najdete v části Nejčastější dotazy [2017 nástroje sady Visual Studio pro Azure Functions](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/) příspěvku na blogu.

Další informace o nástrojích základní funkce Azure najdete v tématu [kód a testovat místně na Azure functions](functions-run-local.md).

Další informace o vývoji funkce jako knihovny tříd rozhraní .NET najdete v tématu [Azure funkcí jazyka C# referenční informace pro vývojáře](functions-dotnet-class-library.md). Tento článek obsahuje také odkazy na příklady, jak použít atributy deklarovat různé typy vazeb Azure Functions podporuje.    
