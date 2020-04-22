---
title: Vytvoření rozhraní Azure Cosmos DB .NET Framework, základní aplikace pomocí rozhraní Gremlin API
description: Obsahuje ukázku kódu v .NET Framework a .NET Core, kterou můžete použít k připojení ke službě Azure Cosmos DB a jejímu dotazování.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/21/2020
ms.author: lbosq
ms.openlocfilehash: bf453587b354b5db3f3ef1a80f974bcb8f8f4e14
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730018"
---
# <a name="quickstart-build-a-net-framework-or-core-application-using-the-azure-cosmos-db-gremlin-api-account"></a>Úvodní příručka: Vytvoření rozhraní .NET Framework nebo základní aplikace pomocí účtu rozhraní API Azure Cosmos DB Gremlin

> [!div class="op_single_selector"]
> * [Konzola Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete snadno vytvořit a dotazovat databáze dotazů, klíčů/hodnot a grafů, které tak můžou využívat výhody použitelnosti v celosvětovém měřítku a možností horizontálního škálování v jádru Azure Cosmos DB. 

Tento rychlý start ukazuje, jak vytvořit účet rozhraní API Azure Cosmos DB [Gremlin,](graph-introduction.md) databázi a graf (kontejner) pomocí portálu Azure. Potom sestavíte a spustíte konzolovou aplikaci pomocí open source ovladače [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet).  

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte nainstalovaný Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Přidání grafu

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď naklonujeme aplikaci rozhraní Gremlin API z GitHubu, nastavíme připojovací řetězec a spustíme ji. Přesvědčíte se, jak snadno se pracuje s daty prostřednictvím kódu programu. 

1. Otevřete příkazový řádek, vytvořte novou složku git-samples a pak příkazový řádek zavřete.

    ```bash
    md "C:\git-samples"
    ```

2. Otevřete okno terminálu Git, například Git Bash, a pomocí příkazu `cd` přejděte do nové složky, do které chcete nainstalovat ukázkovou aplikaci.

    ```bash
    cd "C:\git-samples"
    ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-gremlindotnet-getting-started.git
    ```

4. Potom otevřete sadu Visual Studio a otevřete soubor řešení.

5. Obnovte balíčky NuGet v projektu. Ty by měly zahrnovat ovladač Gremlin.Net i balíček Newtonsoft.Json.


6. Ovladač Gremlin.Net můžete nainstalovat také ručně, pomocí správce balíčků NuGet nebo pomocí [nástroje příkazového řádku nuget](https://docs.microsoft.com/nuget/install-nuget-client-tools): 

    ```bash
    nuget install Gremlin.Net
    ```

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Jinak můžete přeskočit přímo k části [Aktualizace informací o připojení](#update-your-connection-string). 

Všechny následující fragmenty kódu pocházejí ze souboru Program.cs.

* Nastavte parametry připojení na základě výše vytvořeného účtu: 

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="configureConnectivity":::

* Příkazy Gremlin, které mají být provedeny, jsou uvedeny ve slovníku:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineQueries":::

* Vytvořte `GremlinServer` nové `GremlinClient` a připojovací objekty pomocí výše uvedených parametrů:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineClientandServerObjects":::

* Spusťte každý gremlin dotaz pomocí objektu `GremlinClient` s asynchronní úlohou. Můžete si přečíst Gremlin dotazy ze slovníku definovaného v předchozím kroku a provést je. Později získat výsledek a číst hodnoty, které jsou formátovány `JsonSerializer` jako slovník, pomocí třídy z newtonsoft.Json balíčku:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="executeQueries":::

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace.

1. Na webu [Azure Portal](https://portal.azure.com/) přejděte k vašemu účtu databáze grafů. Na kartě **Přehled** se zobrazují dva koncové body: 
 
   **Identifikátor URI sady .NET SDK** – tato hodnota se používá při připojení k účtu grafu pomocí knihovny Microsoft.Azure.Graphs. 

   **Koncový bod Gremlin** – Tato hodnota se použije, když se k účtu databáze grafů připojíte s využitím knihovny Gremlin.Net.

    ![Kopírování koncového bodu](./media/create-graph-dotnet/endpoint.png)

   Chcete-li spustit tuto ukázku, zkopírujte hodnotu **Gremlin Endpoint,** odstraňte číslo portu na konci, to znamená, že se identifikátor URI stane `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com`. Hodnota koncového bodu by měla vypadat`testgraphacct.gremlin.cosmosdb.azure.com`

1. Dále přejděte na kartu **Klíče** a zkopírujte hodnotu **PRIMÁRNÍ KLÍČ** z webu Azure Portal. 

1. Po zkopírování identifikátoru URI a primárního klíče účtu je uložte do nové proměnné prostředí v místním počítači se spuštěnou aplikací. Chcete-li nastavit proměnnou prostředí, otevřete okno příkazového řádku a spusťte následující příkaz. Nezapomeňte nahradit hodnoty>> <Your_Azure_Cosmos_account_URI a <Your_Azure_Cosmos_account_PRIMARY_KEY Your_Azure_Cosmos_account_PRIMARY_KEY.

   ```console
   setx EndpointUrl "<your Azure Cosmos account name>.gremlin.cosmosdb.azure.com"
   setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
   ```

1. Otevřete *soubor Program.cs* a aktualizujte proměnné "databáze a "kontejner" s výše vytvořenými názvy databáze a kontejneru (což je také název grafu).

    `private static string database = "your-database-name";` `private static string container = "your-container-or-graph-name";`

1. Uložte soubor Program.cs. 

Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB. 

## <a name="run-the-console-app"></a>Spuštění aplikace konzoly

Spusťte aplikaci stisknutím CTRL+F5. Aplikace vypíše v konzole příkazy i výsledky dotazů Gremlin.

   V okně konzoly se zobrazí vrcholy a hrany, které se přidávají do grafu. Po dokončení skriptu stisknutím klávesy ENTER zavřete okno konzoly.

## <a name="browse-using-the-data-explorer"></a>Procházení pomocí Průzkumníku dat

Teď se můžete vrátit do Průzkumníku dat na webu Azure Portal, procházet nová data grafu a zadávat na ně dotazy.

1. V Průzkumníku dat se nová databáze zobrazí v podokně Graphs (Grafy). Rozbalte uzly databáze a kontejneru a pak klikněte na **Graph**.

2. Kliknutím na tlačítko **Použít filtr** použijte výchozí dotaz k zobrazení všech vrcholů v grafu. V podokně Graphs (Grafy) se zobrazí data vygenerovaná ukázkovou aplikací.

    Graf můžete přiblížit nebo oddálit, můžete rozšířit prostor pro zobrazení grafu, přidat další vrcholy a přesouvat vrcholy na zobrazovací ploše.

    ![Zobrazení grafu v Průzkumníku dat na webu Azure Portal](./media/create-graph-dotnet/graph-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu databáze Azure Cosmos, vytvoření grafu pomocí Průzkumníku dat a spuštění aplikace. Teď můžete pomocí konzoly Gremlin vytvářet složitější dotazy a implementovat účinnou logiku procházení grafů. 

> [!div class="nextstepaction"]
> [Dotazování pomocí konzoly Gremlin](tutorial-query-graph.md)

