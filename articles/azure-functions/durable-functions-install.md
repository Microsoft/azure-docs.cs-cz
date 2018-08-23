---
title: Instalace rozšíření Durable Functions a ukázky – Azure
description: Zjistěte, jak nainstalovat rozšíření Durable Functions pro službu Azure Functions pro vývoj portálu nebo vývoj sady Visual Studio.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 12b5c013a0704eb2d3f7df58011571b902955c75
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2018
ms.locfileid: "42059069"
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>Instalace rozšíření Durable Functions a ukázky (Azure Functions)

[Durable Functions](durable-functions-overview.md) rozšíření pro službu Azure Functions je zahrnutý v balíčku NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Tento článek ukazuje, jak nainstalovat balíček a řadu ukázek pro následující vývojových prostředích:

* Visual Studio 2017 (doporučeno pro jazyk C#) 
* Visual Studio Code (doporučeno pro jazyk JavaScript)
* portál Azure

## <a name="visual-studio-2017"></a>Visual Studio 2017

Visual Studio nyní poskytuje nejlepší prostředí pro vývoj aplikací, které používají Durable Functions.  Funkce můžete spustit místně a můžete také publikovat do Azure. Chcete začít s prázdným projektem nebo sadu ukázkových funkcích.

### <a name="prerequisites"></a>Požadavky

* Nainstalujte [nejnovější verzi sady Visual Studio](https://www.visualstudio.com/downloads/) (verze 15.3 nebo novější). Zahrnout **vývoj pro Azure** úloh v možnostech instalace.

### <a name="start-with-sample-functions"></a>Začněte s ukázkové funkce 

1. Stáhněte si [ukázkovou aplikaci v souboru .zip pro sadu Visual Studio](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip). Není nutné přidat odkaz NuGet, protože ukázkový projekt již existuje.
2. Nainstalujte a spusťte [emulátoru úložiště Azure](https://docs.microsoft.com/azure/storage/storage-use-emulator) verze 5.2 nebo vyšší. Alternativně můžete aktualizovat *local.appsettings.json* soubor s skutečné připojovacích řetězců Azure Storage.
3. Otevřete projekt v sadě Visual Studio 2017. 
4. Pokyny ke spuštění ukázky začínat [Function řetězení - Hello pořadí ukázka](durable-functions-sequence.md). Ukázku můžete spustit místně nebo publikovat do Azure.

### <a name="start-with-an-empty-project"></a>Začněte s prázdným projektem
 
Použijte stejné pokyny jako pro spouštění s ukázkou, ale proveďte následující kroky, nemusíte ho stahovat *ZIP* souboru:

1. Vytvořte projekt aplikace Function App.
2. Hledání následující NuGet package odkaz s využitím *spravovat balíčky NuGet* a přidejte ho do projektu: Microsoft.Azure.WebJobs.Extensions.DurableTask v1.5.0
   
## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code poskytuje místní vývojové prostředí pro všechny hlavní platformy – Windows, macOS a Linux.  Funkce můžete spustit místně a také publikovat do Azure. Chcete začít s prázdným projektem nebo sadu ukázkových funkcích.

### <a name="prerequisites"></a>Požadavky

* Nainstalujte [nejnovější verzi sady Visual Studio Code](https://code.visualstudio.com/Download) 

* Postupujte podle pokynů v části "Instalace nástrojů Azure Functions Core" na [kódu a testování služby Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-run-local)

    >[!IMPORTANT]
    > Pokud už máte Azure Functions pro různé platformy Tools, můžete je aktualizujte na nejnovější dostupnou verzi.

    >[!IMPORTANT]
    >Odolná služba Functions v jazyce JavaScript vyžaduje verzi 2.x nástrojů Azure Functions Core.

*  Pokud jste na počítači s Windows, nainstalujte a spusťte [emulátoru úložiště Azure](https://docs.microsoft.com/azure/storage/storage-use-emulator) verze 5.2 nebo vyšší. Alternativně můžete aktualizovat *local.appsettings.json* soubor s skutečných připojení služby Azure Storage. 


### <a name="start-with-sample-functions"></a>Začněte s ukázkové funkce

#### <a name="c"></a>C#

1. Klonování [Durable Functions úložiště](https://github.com/Azure/azure-functions-durable-extension.git).
2. Přejděte na počítači se [složce samples skriptu C#](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx). 
3. Spuštěním následujícího příkazu nainstalujte trvalý rozšíření Azure Functions okno Příkazový řádek nebo terminálu:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.5.0
    ```
4. Spuštěním následujícího příkazu nainstalujte rozšíření Azure Functions Twilio okno Příkazový řádek nebo terminálu:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.Twilio -v 3.0.0-beta5
    ```
5. Spustit emulátor úložiště Azure nebo aktualizace *local.appsettings.json* soubor s skutečné připojovacího řetězce služby Azure Storage.
6. Otevřete projekt ve Visual Studio Code. 
7. Pokyny ke spuštění ukázky začínat [Function řetězení - Hello pořadí ukázka](durable-functions-sequence.md). Ukázku můžete spustit místně nebo publikovat do Azure.
8. Spusťte projekt spuštěním v příkazu výzvy / terminálu následující příkaz:
    ```bash
    func host start
    ```

#### <a name="javascript-functions-v2-only"></a>JavaScript (jenom funkce v2)

1. Klonování [Durable Functions úložiště](https://github.com/Azure/azure-functions-durable-extension.git).
2. Přejděte na počítači se [složce samples JavaScript](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/javascript). 
3. Spuštěním následujícího příkazu nainstalujte trvalý rozšíření Azure Functions okno Příkazový řádek nebo terminálu:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.5.0
    ```
4. Obnovení balíčků npm spuštěním následujícího příkazu okno Příkazový řádek nebo terminálu:
    
    ```bash
    npm install
    ``` 
5. Aktualizace *local.appsettings.json* souboru skutečné připojovacím řetězcem služby Azure Storage.
6. Otevřete projekt ve Visual Studio Code. 
7. Pokyny ke spuštění ukázky začínat [Function řetězení - Hello pořadí ukázka](durable-functions-sequence.md). Ukázku můžete spustit místně nebo publikovat do Azure.
8. Spusťte projekt spuštěním v příkazu výzvy / terminálu následující příkaz:
    ```bash
    func host start
    ```

### <a name="start-with-an-empty-project"></a>Začněte s prázdným projektem
 
1. V příkazu výzvy / terminálu přejděte do složky, který bude hostovat vaši aplikaci function app.
2. Spuštěním následujícího příkazu nainstalujte trvalý rozšíření Azure Functions okno Příkazový řádek nebo terminálu:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.5.0
    ```
3. Vytvoření projektu aplikace Function App spuštěním následujícího příkazu:

    ```bash
    func init
    ``` 
4. Spustit emulátor úložiště Azure nebo aktualizace *local.appsettings.json* soubor s skutečné připojovacího řetězce služby Azure Storage.
5. Potom spuštěním následujícího příkazu vytvořte novou funkci a postupujte podle pokynů průvodce:

    ```bash
    func new
    ```
    >[!IMPORTANT]
    > V současné době není k dispozici šablona odolné funkce ale můžete začít s některou z podporovaných možností a pak upravte kód. Ukázky pro použijte pro referenci [klient Orchestrace](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/HttpStart), [Orchestrace Trigger](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence), a [aktivační událost pro aktivitu](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence).

6. Otevřít složku projektu ve Visual Studio Code a pokračujte tak, že upravíte kód šablony. 
7. Spusťte projekt spuštěním v příkazu výzvy / terminálu následující příkaz:
    ```bash
    func host start
    ```

## <a name="azure-portal"></a>portál Azure

Pokud dáváte přednost, můžete použít [webu Azure portal](https://portal.azure.com) pro vývoj Durable Functions.

   > [!NOTE]
   > Odolná služba Functions v jazyce JavaScript ještě nejsou k dispozici na portálu.

### <a name="create-an-orchestrator-function"></a>Vytvoření funkce orchestrátoru

1. Vytvořit novou aplikaci function app na portálu, jak je znázorněno [funkce tohoto článku rychlý Start](functions-create-first-azure-function.md#create-a-function-app).

2. Konfigurace aplikace function app k [používat verzi 2.0 runtime](set-runtime-version.md).

   Rozšíření Durable Functions funguje na modul runtime verze 1.X a 2.0 modulu runtime, ale webu Azure Portal šablony jsou dostupné jenom při cílení na modul runtime 2.0.

3. Vytvořit novou funkci tak, že vyberete **"vytvořit svoji vlastní funkci."** .

4. Změnit **jazyk** k **jazyka C#**, **scénář** k **Durable Functions** a vyberte **trvalý Spouštěč funkce protokolu Http -C#** šablony.

5. V části **rozšíření nejsou nainstalovaná**, klikněte na tlačítko **nainstalovat** ke stažení rozšíření z webu NuGet.org. 

6. Po dokončení instalace pokračujte ve vytváření funkce klienta Orchestrace – **"HttpStart"** vytvořený tak, že vyberete **odolné funkce Spouštěč protokolu Http - C#** šablony.

7. Teď vytvořte funkci Orchestrace **"HelloSequence"** z **Orchestrátor Durable Functions – C#** šablony.

8. A zavolá se funkce poslední **"Hello"** z **aktivita Durable Functions – C#** šablony.

9. Přejděte na **"HttpStart"** fungovat a zkopírujte její adresu URL.

10. Použijte Postman nebo nástroj cURL k volání odolné funkce. Před testováním, nahraďte v adrese URL **{functionName}** s názvem funkce produktu orchestrator - **HelloSequence**.  Žádná data nejsou povinné, použijte operaci POST. 

    ```bash
    curl -X POST https://{your function app name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

11. Potom zavolejte **"statusQueryGetUri"** koncový bod a zobrazit aktuální stav odolné funkce

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

12. Pokračovat volání **"statusQueryGetUri"** koncový bod, dokud se stav změní na **"Completed (dokončeno)** 

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Blahopřejeme! První odolné funkce je vytvořená a spuštěná na webu Azure Portal.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Spusťte funkci řetězení vzorku](durable-functions-sequence.md)
