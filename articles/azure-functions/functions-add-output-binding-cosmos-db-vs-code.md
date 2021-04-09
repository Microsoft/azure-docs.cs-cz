---
title: Připojení Azure Functions k Azure Cosmos DB pomocí Visual Studio Code
description: Naučte se připojit Azure Functions k účtu Azure Cosmos DB přidáním výstupní vazby do projektu Visual Studio Code.
author: ThomasWeiss
ms.date: 03/23/2021
ms.topic: quickstart
ms.author: thweiss
zone_pivot_groups: programming-languages-set-functions-temp
ms.openlocfilehash: 0a0c63ee54699185bcd02104b1a3f4d0070ea808
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023244"
---
# <a name="connect-azure-functions-to-azure-cosmos-db-using-visual-studio-code"></a>Připojení Azure Functions k Azure Cosmos DB pomocí Visual Studio Code

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

V tomto článku se dozvíte, jak pomocí Visual Studio Code připojit [Azure Cosmos DB](../cosmos-db/introduction.md) k funkci, kterou jste vytvořili v předchozím článku rychlý Start. Výstupní vazba, kterou do této funkce přidáte, zapisuje data z požadavku HTTP do dokumentu JSON, který je uložený v kontejneru Azure Cosmos DB. 

::: zone pivot="programming-language-csharp"
Než začnete, musíte dokončit rychlé zprovoznění [: vytvoření funkce jazyka C# v Azure pomocí Visual Studio Code](create-first-function-vs-code-csharp.md). Pokud jste již vyčistili prostředky na konci tohoto článku, Projděte kroky znovu a znovu vytvořte aplikaci funkcí a související prostředky v Azure.
::: zone-end
::: zone pivot="programming-language-javascript"  
Než začnete, musíte dokončit rychlé zprovoznění [: vytvoření funkce JavaScriptu v Azure pomocí Visual Studio Code](create-first-function-vs-code-node.md). Pokud jste již vyčistili prostředky na konci tohoto článku, Projděte kroky znovu a znovu vytvořte aplikaci funkcí a související prostředky v Azure.  
::: zone-end

## <a name="configure-your-environment"></a>Konfigurace prostředí

Než začnete, ujistěte se, že jste nainstalovali [rozšíření Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb) databases pro Visual Studio Code.

## <a name="create-your-azure-cosmos-db-account"></a>Vytvoření účtu Azure Cosmos DB

> [!IMPORTANT]
> [Azure Cosmos DB](../cosmos-db/serverless.md) dostupné bez serveru je teď k dispozici ve verzi Preview. Tento režim založený na spotřebě zajišťuje Azure Cosmos DB silnou možnost pro úlohy bez serveru. Pokud chcete použít Azure Cosmos DB v režimu bez serveru, při vytváření účtu vyberte možnost bez **serveru** jako **režim kapacity** .

1. V novém okně prohlížeče se přihlaste k webu [Azure Portal](https://portal.azure.com/).

2. Klikněte na **Vytvořit prostředek** > **Databáze** > **Azure Cosmos DB**.
   
    :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png" alt-text="Podokno Databáze na webu Azure Portal" border="true":::

3. Na stránce **vytvořit Azure Cosmos DB účet** zadejte nastavení pro nový účet Azure Cosmos DB. 
 
    Nastavení|Hodnota|Popis
    ---|---|---
    Předplatné|*Vaše předplatné*|V [předchozím článku](./create-first-function-vs-code-csharp.md)vyberte předplatné Azure, kde jste Function App vytvořili.
    Skupina prostředků|*Vaše skupina prostředků*|Vyberte skupinu prostředků, ve které jste Function App vytvořili v [předchozím článku](./create-first-function-vs-code-csharp.md).
    Název účtu|*Zadejte jedinečný název*|Zadejte jedinečný název, který identifikuje váš účet služby Azure Cosmos DB.<br><br>Název účtu může obsahovat jenom malá písmena, číslice a spojovníky (-) a musí mít délku 3 až 31 znaků.
    Rozhraní API|Core (SQL)|Pokud chcete vytvořit databázi dokumentů, kterou můžete dotazovat pomocí syntaxe SQL, vyberte **Core (SQL)** . [Přečtěte si další informace o Azure Cosmos DB rozhraní SQL API](../cosmos-db/introduction.md).|
    Umístění|*Vyberte oblast, která je nejblíže vašemu umístění.*|Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Abyste získali nejrychlejší přístup k datům, použijte umístění, které je nejblíže vašemu uživateli, nebo vašim uživatelům.
    Režim kapacity|Propustnost bez serveru nebo zřízené|Pokud chcete vytvořit účet v režimu bez [serveru](../cosmos-db/serverless.md) , vyberte možnost bez **serveru** . Vyberte **zřízenou propustnost** a vytvořte účet v režimu [zřízené propustnosti](../cosmos-db/set-throughput.md) .<br><br>Pokud začínáte s Azure Cosmos DB, vyberte možnost bez **serveru** .

4. Klikněte na **Zkontrolovat a vytvořit**. Můžete přeskočit část **síť** a **značky** . 

5. Zkontrolujte souhrnné informace a klikněte na **vytvořit**. 

6. Počkejte na vytvoření nového Azure Cosmos DB a pak vyberte **Přejít k prostředku**.

    :::image type="content" source="../cosmos-db/media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png" alt-text="Vytváření účtu Azure Cosmos DB bylo dokončeno." border="true":::

## <a name="create-an-azure-cosmos-db-database-and-container"></a>Vytvoření databáze Azure Cosmos DB a kontejneru

Z účtu Azure Cosmos DB vyberte **Průzkumník dat** a pak **Nový kontejner**. Vytvořte novou databázi s názvem *My-Database*, nový kontejner pojmenovaný *My Container* a vyberte možnost `/id` [klíč oddílu](../cosmos-db/partitioning-overview.md).

:::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/create-container.png" alt-text="Vytvoření nového kontejneru Azure Cosmos DB z Azure Portal" border="true":::

## <a name="update-your-function-app-settings"></a>Aktualizovat nastavení aplikace Function App

V [předchozím článku rychlý Start](./create-first-function-vs-code-csharp.md)jste vytvořili aplikaci funkcí v Azure. V tomto článku aktualizujete Function App pro zápis dokumentů JSON do kontejneru Azure Cosmos DB, který jste vytvořili výše. Pokud se chcete připojit k vašemu Azure Cosmos DB účtu, musíte do nastavení aplikace přidat svůj připojovací řetězec. Pak si nové nastavení Stáhněte do local.settings.jssouboru, abyste se mohli připojit k účtu Azure Cosmos DB při místním spuštění.

1. V Visual Studio Code vyhledejte účet Azure Cosmos DB, který jste právě vytvořili. Klikněte pravým tlačítkem na jeho název a vyberte **Kopírovat připojovací řetězec**.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/copy-connection-string.png" alt-text="Kopírování připojovacího řetězce Azure Cosmos DB" border="true":::

1. Stisknutím <kbd>klávesy F1</kbd> otevřete paletu příkazů a pak vyhledejte a spusťte příkaz `Azure Functions: Add New Setting...` .

1. Vyberte aplikaci funkcí, kterou jste vytvořili v předchozím článku. Podle pokynů na obrazovce zadejte tyto informace:

    + **Zadejte nový název nastavení aplikace**: typ `CosmosDbConnectionString` .

    + **Zadejte hodnotu pro "CosmosDbConnectionString"**: vložte připojovací řetězec účtu Azure Cosmos DB, jak jste zkopírovali dříve.

1. Stisknutím klávesy <kbd>F1</kbd> znovu otevřete paletu příkazů a pak vyhledejte a spusťte příkaz `Azure Functions: Download Remote Settings...` . 

1. Vyberte aplikaci funkcí, kterou jste vytvořili v předchozím článku. Vyberte možnost **Ano pro vše** , pokud chcete přepsat stávající místní nastavení. 

## <a name="register-binding-extensions"></a>Registrace rozšíření vazeb

Vzhledem k tomu, že používáte výstupní vazbu Azure Cosmos DB, je nutné před spuštěním projektu nainstalovat odpovídající rozšíření vazby. 

::: zone pivot="programming-language-csharp"

S výjimkou aktivačních událostí protokolu HTTP a časovače jsou vazby implementovány jako balíčky rozšíření. Spusťte následující příkaz [dotnet přidat balíček](/dotnet/core/tools/dotnet-add-package) v okně terminálu a přidejte do projektu balíček rozšíření úložiště.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB
```

::: zone-end

::: zone pivot="programming-language-javascript"

Projekt byl nakonfigurován tak, aby používal [sady rozšíření](functions-bindings-register.md#extension-bundles), které automaticky instalují předdefinované sady přípon balíčků. 

Použití sad rozšíření je povoleno v host.jsv souboru v kořenovém adresáři projektu, který se zobrazí takto:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

Nyní můžete přidat výstupní vazbu Azure Cosmos DB do projektu.

## <a name="add-an-output-binding"></a>Přidání výstupní vazby

V Functions každý typ vazby vyžaduje `direction` , `type` , a jedinečný, `name` který má být definován v function.jsv souboru. Způsob, jakým definujete tyto atributy, závisí na jazyku aplikace Function App.

::: zone pivot="programming-language-csharp"

V projektu knihovny tříd jazyka C# jsou vazby definovány jako atributy vazby v metodě Function. *function.jsv* souboru vyžadovaném funkcemi se pak automaticky generuje na základě těchto atributů.

Otevřete soubor projektu *HttpExample. cs* a přidejte do `Run` definice metody následující parametr:

```csharp
[CosmosDB(
    databaseName: "my-database",
    collectionName: "my-container",
    ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
```

`documentsOut`Parametr je <T> typ IAsyncCollector, který představuje kolekci dokumentů JSON, které budou po dokončení funkce zapsány do kontejneru Azure Cosmos DB. Konkrétní atributy určují název kontejneru a název jeho nadřazené databáze. Připojovací řetězec pro váš účet Azure Cosmos DB je nastaven pomocí `ConnectionStringSettingAttribute` .

Definice metody spuštění by teď měla vypadat takto:  

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
```

::: zone-end

::: zone pivot="programming-language-javascript"

Atributy vazby jsou definovány přímo v function.jsv souboru. V závislosti na typu vazby mohou být vyžadovány další vlastnosti. [Konfigurace Azure Cosmos DB Output](./functions-bindings-cosmosdb-v2-output.md#configuration) popisuje pole potřebná pro Azure Cosmos DB výstupní vazby. Rozšíření usnadňuje přidávání vazeb do function.jssouborů. 

Vazbu vytvoříte tak, že kliknete pravým tlačítkem (CTRL + kliknete na macOS) do `function.json` souboru ve složce HttpTrigger a kliknete na **Přidat vazbu...**. Podle pokynů definujte následující vlastnosti vazby pro novou vazbu:

| Výzva | Hodnota | Popis |
| -------- | ----- | ----------- |
| **Vybrat směr vazby** | `out` | Vazba je výstupní vazba. |
| **Výběr vazby se směrem "out"** | `Azure Cosmos DB` | Vazba je Azure Cosmos DB vazba. |
| **Název, který slouží k identifikaci této vazby v kódu** | `outputDocument` | Název, který identifikuje parametr vazby, na který se odkazuje v kódu. |
| **Cosmos DB databáze, do které budou zapsána data** | `my-database` | Název databáze Azure Cosmos DB obsahující cílový kontejner. |
| **Kolekce databáze, do které se budou zapisovat data** | `my-container` | Název kontejneru Azure Cosmos DB, do kterého budou zapsány dokumenty JSON. |
| **Je-li nastavená hodnota true, vytvoří se databáze a kolekce Cosmos DB** | `false` | Cílová databáze a kontejner již existují. |
| **Vyberte nastavení z local.setting.jszapnuto.** | `CosmosDbConnectionString` | Název nastavení aplikace, které obsahuje připojovací řetězec pro účet Azure Cosmos DB. |
| **Klíč oddílu (volitelné)** | *nechejte prázdné* | Vyžaduje se jenom v případě, že výstupní vazba vytvoří kontejner. |
| **Propustnost kolekce (nepovinné)** | *nechejte prázdné* | Vyžaduje se jenom v případě, že výstupní vazba vytvoří kontejner. |

Vazba je přidána do `bindings` pole v function.jsv, což by mělo vypadat takto:

```json
{
    "type": "cosmosDB",
    "direction": "out",
    "name": "outputDocument",
    "databaseName": "my-database",
    "collectionName": "my-container",
    "createIfNotExists": "false",
    "connectionStringSetting": "CosmosDbConnectionString"
}
```

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Přidání kódu, který používá výstupní vazbu

::: zone pivot="programming-language-csharp"  

Přidejte kód, který používá `documentsOut` výstupní objekt vazby k vytvoření dokumentu JSON. Před vrácením metody přidejte tento kód.

```csharp
if (!string.IsNullOrEmpty(name))
{
    // Add a JSON document to the output container.
    await documentsOut.AddAsync(new
    {
        // create a random ID
        id = System.Guid.NewGuid().ToString(),
        name = name
    });
}
```

V tomto okamžiku by měla funkce vypadat takto:

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a JSON document to the output container.
        await documentsOut.AddAsync(new
        {
            // create a random ID
            id = System.Guid.NewGuid().ToString(),
            name = name
        });
    }

    string responseMessage = string.IsNullOrEmpty(name)
        ? "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."
        : $"Hello, {name}. This HTTP triggered function executed successfully.";

    return new OkObjectResult(responseMessage);
}
```

::: zone-end

::: zone pivot="programming-language-javascript"  

Přidejte kód, který používá `outputDocument` výstupní vazbu objektu pro `context.bindings` k vytvoření dokumentu JSON. Přidejte tento kód před `context.res` příkaz.

```javascript
if (name) {
    context.bindings.outputDocument = JSON.stringify({
        // create a random ID
        id: new Date().toISOString() + Math.random().toString().substr(2,8),
        name: name
    });
}
```

V tomto okamžiku by měla funkce vypadat takto:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    if (name) {
        context.bindings.outputDocument = JSON.stringify({
            // create a random ID
            id: new Date().toISOString() + Math.random().toString().substr(2,8),
            name: name
        });
    }

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}
```

::: zone-end  

## <a name="run-the-function-locally"></a>Místní spuštění funkce

1. Jako v předchozím článku spusťte stisknutím klávesy <kbd>F5</kbd> projekt Function App a základní nástroje. 

1. Se základními nástroji, které běží, přejdete do oblasti **Azure: Functions** . V části **funkce** rozbalte **místní**  >  **funkce** projektu. Klikněte pravým tlačítkem (CTRL + klikněte na Mac) `HttpExample` funkce a pak vyberte **Spustit funkci...**

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Spustit funkci hned z Visual Studio Code":::

1. V části **Zadejte text žádosti** se zobrazí hodnota tělo zprávy žádosti `{ "name": "Azure" }` . Stisknutím klávesy ENTER odešlete tuto zprávu požadavku vaší funkci.  
 
1. Po vrácení odpovědi stiskněte <kbd>kombinaci kláves CTRL + C</kbd> a zastavte základní nástroje.

### <a name="verify-that-a-json-document-has-been-created"></a>Ověření, že byl vytvořen dokument JSON

1. V Azure Portal se vraťte na účet Azure Cosmos DB a vyberte **Průzkumník dat**.

1. Rozbalením databáze a kontejneru a výběrem **položek** zobrazíte seznam dokumentů vytvořených ve vašem kontejneru.

1. Ověřte, že výstupní vazba vytvořila nový dokument JSON.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/verify-output.png" alt-text="Ověření, zda byl v kontejneru Azure Cosmos DB vytvořen nový dokument" border="true":::

## <a name="redeploy-and-verify-the-updated-app"></a>Opětovné nasazení a ověření aktualizované aplikace

1. V Visual Studio Code stisknutím klávesy F1 otevřete paletu příkazů. V paletě příkazů vyhledejte a vyberte `Azure Functions: Deploy to function app...` .

1. Vyberte aplikaci funkcí, kterou jste vytvořili v prvním článku. Vzhledem k tomu, že projekt znovu nasazujete do stejné aplikace, vyberte **nasadit** a zastavte tak upozornění týkající se přepsání souborů.

1. Po dokončení nasazení můžete znovu použít funkci **Execute Function Now...** a aktivovat funkci v Azure.

1. Znovu [Zkontrolujte dokumenty vytvořené v kontejneru Azure Cosmos DB](#verify-that-a-json-document-has-been-created) a ověřte tak, že výstupní vazba znovu vygeneruje nový dokument JSON.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V Azure se *prostředky* odkazují na aplikace Function App, funkce, účty úložiště a tak dále. Jsou seskupené do *skupin prostředků* a odstraněním skupiny můžete všechno odstranit ze skupiny.

Vytvořili jste prostředky k dokončení těchto rychlých startů. Tyto prostředky se vám mohou fakturovat, a to v závislosti na [stavu účtu](https://azure.microsoft.com/account/) a [cenách služeb](https://azure.microsoft.com/pricing/). Pokud prostředky už nepotřebujete, můžete k jejich odstranění použít tento postup:

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>Další kroky

Aktualizovali jste funkci aktivovanou protokolem HTTP pro zápis dokumentů JSON do kontejneru Azure Cosmos DB. Nyní se můžete dozvědět víc o vývoji funkcí pomocí Visual Studio Code:

+ [Vývoj Azure Functions pomocí Visual Studio Code](functions-develop-vs-code.md)

+ [Azure Functions triggery a vazby](functions-triggers-bindings.md).
::: zone pivot="programming-language-csharp"  
+ [Příklady kompletních projektů funkcí v jazyce C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Referenční informace pro vývojáře v jazyce C# Azure Functions](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Příklady kompletních projektů funkcí v JavaScriptu](/samples/browse/?products=azure-functions&languages=javascript).

+ [Azure Functions příručka pro vývojáře JavaScriptu](functions-reference-node.md)  
::: zone-end  