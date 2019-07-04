---
title: Funkce připojení k Azure Storage pomocí Visual Studio Code
description: Informace o přidání výstupní vazby pro připojení vašich funkcí do fronty služby Azure Storage pomocí nástroje Visual Studio Code.
author: ggailey777
ms.author: glenga
ms.date: 06/25/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
manager: jeconnoc
ms.openlocfilehash: b207064f691391af2c180c7a6ab03e42ed79fcb6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450993"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio-code"></a>Funkce připojení k Azure Storage pomocí Visual Studio Code

Služba Azure Functions vám umožní připojit se funkce služby Azure a dalším prostředkům bez nutnosti psát vlastní kód integrace. Tyto *vazby*, které představují vstupní a výstupní, jsou deklarované uvnitř definice funkce. Data z vazby je k dispozici funkce jako parametry. Aktivační události je zvláštní druh vstupní vazby. Funkce obsahuje pouze jednu aktivační událost, může mít více vstupní a výstupní vazby. Další informace najdete v tématu [aktivace Azure Functions a vazby koncepty](functions-triggers-bindings.md).

Tento článek popisuje, jak používat Visual Studio Code pro připojení funkce, kterou jste vytvořili v [předchozím článku rychlý Start](functions-create-first-function-vs-code.md) do služby Azure Storage. Výstupní vazby, který přidáte do této funkce zapíše data z požadavku HTTP pro zprávu do fronty Azure Queue storage. 

Většina vazby vyžadují uložené připojovací řetězec, který funkce používá pro přístup k vazbou služby. Aby bylo snazší, použijete účet úložiště, který jste vytvořili pomocí aplikace function app. Připojení k tomuto účtu je již uložen v aplikaci nastavení s názvem `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Požadavky

Předtím, než se pustíte do tohoto článku, musí splňovat následující požadavky:

* Nainstalujte [služby Azure Storage rozšíření pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).
* Nainstalujte [Průzkumníka služby Azure Storage](https://storageexplorer.com/). Průzkumník služby Storage je nástroj, který použijete k prozkoumání zpráv fronty se vygeneroval výstupní vazbu. Průzkumník služby Storage se podporuje v systémech macOS, Windows a operační systémy založené na Linuxu.
* Nainstalujte [nástroje rozhraní příkazového řádku .NET Core](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x) (C# pouze pro projekty).
* Proveďte kroky v [části 1 tohoto rychlého startu Visual Studio Code](functions-create-first-function-vs-code.md). 

Tento článek předpokládá, že jste již přihlášeni ke svému předplatnému Azure z Visual Studio Code. Můžete se přihlásit pomocí `Azure: Sign In` z palety příkazů. 

## <a name="download-the-function-app-settings"></a>Stáhněte si nastavení aplikace function app

V [předchozím článku rychlý Start](functions-create-first-function-vs-code.md), spolu se vyžaduje účet úložiště vytvoříte aplikaci function app v Azure. Připojovací řetězec pro tento účet je bezpečně uložen v nastavení aplikace v Azure. V tomto článku se zápis zpráv do fronty úložiště ve stejném účtu. Pro připojení k účtu úložiště při místním spuštění funkce, je třeba do souboru local.settings.json stáhnout nastavení aplikace. 

1. Stisknutím klávesy F1 otevřete paletu příkazů, vyhledejte a spusťte příkaz `Azure Functions: Download Remote Settings....`. 

1. Vyberte aplikace function app, kterou jste vytvořili v předchozím článku. Vyberte **Ano všem** přepsat stávající místní nastavení. 

    > [!IMPORTANT]  
    > Protože tajných kódů, obsahuje souboru local.settings.json nikdy publikuje a je vyřazeno ze správy zdrojů.

1. Zkopírujte hodnotu `AzureWebJobsStorage`, který je klíčem k hodnotě připojovacího řetězce účtu úložiště. Toto připojení použijete k ověření, že výstupní vazbu funguje podle očekávání.

## <a name="register-binding-extensions"></a>Registrace rozšíření vazeb

Vzhledem k tomu, že používáte výstupní vazbu Queue storage, musíte mít nainstalované před spuštěním projektu rozšíření vazby úložiště. 

### <a name="javascript"></a>JavaScript

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

### <a name="c-class-library"></a>C\# knihovny tříd

S výjimkou aktivační události HTTP a časovač vazby jsou implementovány jako balíčky rozšíření. Spusťte následující příkaz [se příkaz dotnet add package](/dotnet/core/tools/dotnet-add-package) příkazu v okně terminálu přidejte do projektu balíček rozšíření úložiště.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

Teď můžete přidat výstupní vazby do svého projektu úložiště.

## <a name="add-an-output-binding"></a>Přidání výstupní vazby

Ve funkcích, každý typ vazby vyžaduje `direction`, `type`a jedinečný `name` byly definovány v souboru function.json. Způsob, jak definovat tyto atributy závisí na jazyku vaší aplikace function App.

### <a name="javascript"></a>JavaScript

Vazby atributů jsou definovány přímo v souboru function.json. V závislosti na typu vazby může vyžadovat další vlastnosti. [Konfigurace výstupní fronty](functions-bindings-storage-queue.md#output---configuration) popisuje pole, vyžaduje se pro vazbu fronty Azure Storage. Toto rozšíření usnadňuje přidejte vazby na soubor function.json. 

Chcete-li vytvořit vazbu, klikněte pravým tlačítkem na (Ctrl + kliknutí v systému macOS) `function.json` souboru ve složce HttpTrigger a zvolte **přidat vazbu...** . Postupujte podle pokynů k definování následující vlastnosti vazby pro nový typ vazby:

| Výzva | Hodnota | Popis |
| -------- | ----- | ----------- |
| **Vyberte vazba – směrování** | `out` | Vazba je výstupní vazbu. |
| **Vyberte vazbu se směrem...** | `Azure Queue Storage` | Vazba je vazbu fronty Azure Storage. |
| **Název používaný k identifikaci této vazby v kódu** | `msg` | Název identifikující vazby parametru odkazovaného v kódu. |
| **Fronty, na který se pošle zpráva** | `outqueue` | Název, který vazba zapisuje do fronty. Když *queueName* neexistuje, vazba se vytvoří při prvním použití. |
| **Vyberte nastavení z "local.setting.json"** | `AzureWebJobsStorage` | Název nastavení aplikace, které obsahuje připojovací řetězec pro účet úložiště. `AzureWebJobsStorage` Nastavení obsahuje připojovací řetězec pro účet úložiště, který jste vytvořili pomocí aplikace function app. |

Vazba se přidá do `bindings` pole v souboru function.json by teď měl vypadat jako v následujícím příkladu:

```json
{
   ...

  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

### <a name="c-class-library"></a>C\# knihovny tříd

V C# projekt knihovny tříd, vazby jsou definované jako atributů vazby na funkce metody. Soubor function.json se pak automaticky generuje na základě těchto atributů.

Otevřete soubor projektu HttpTrigger.cs a přidejte následující `using` – příkaz:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Přidejte následující parametr `Run` definici metody:

```cs
[Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
```

`msg` Parametr je `ICollector<T>` typ, který představuje kolekci zpráv, které jsou zapsány do výstupní vazby, když funkce skončí. V takovém případě je výstup fronty úložiště s názvem `outqueue`. Připojovací řetězec pro účet úložiště se nastavil `StorageAccountAttribute`. Tento atribut určuje nastavení, které obsahuje připojovací řetězec účtu úložiště a je možné použít na třídu, metodu nebo parametr úroveň. V takovém případě může vynechat `StorageAccountAttribute` vzhledem k tomu, že už používáte výchozí účet úložiště.

Spustit definice metody by teď měl vypadat nějak takto:  

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
```

## <a name="add-code-that-uses-the-output-binding"></a>Přidání kódu, který používá výstupní vazbu

Po definování vazby můžete použít `name` vazby pro přístup k ní jako atribut v signatuře funkce. Když použijete výstupní vazbu, není nutné použít sadu SDK služby Azure Storage kód pro ověření, získat odkaz na frontu nebo zápis dat. Funkce modulu runtime a fronty výstupní vazbu udělal za vás tyto úlohy.

### <a name="javascript"></a>JavaScript

Přidejte kód, který se používá `msg` výstupní vazbu objektu na `context.bindings` k vytvoření zprávy fronty. Vložte tento kód před příkaz `context.res`.

```javascript
// Add a message to the Storage queue.
context.bindings.msg = "Name passed to the function: " + 
(req.query.name || req.body.name);
```

V tomto okamžiku funkce by měl vypadat takto:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    if (req.query.name || (req.body && req.body.name)) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " + 
        (req.query.name || req.body.name);
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};
```

### <a name="c"></a>C\#

Přidejte kód, který se používá `msg` výstupní vazbu objekt k vytvoření zprávy fronty. Tento kód vložte před metoda vrátí.

```cs
if (!string.IsNullOrEmpty(name))
{
    // Add a message to the output collection.
    msg.Add(string.Format("Name passed to the function: {0}", name));
}
```

V tomto okamžiku funkce by měl vypadat takto:

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a message to the output collection.
        msg.Add(string.Format("Name passed to the function: {0}", name));
    }
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Při prvním použití výstupní vazby vytvoří modul runtime Functions v účtu úložiště novou frontu s názvem **outqueue**. Chcete-li ověřit, že vytvoření fronty spolu s novou zprávu použijete Průzkumníka služby Storage.

### <a name="connect-storage-explorer-to-your-account"></a>Propojení Průzkumníka služby Storage s vaším účtem

Tuto část přeskočte, pokud jste už nainstalovali Průzkumníka služby Azure Storage a propojili jste ho ke svému účtu Azure.

1. Spustit [Azure Storage Explorer] nástroj, vyberte ikonu připojení na levé straně a vyberte **přidat účet**.

    ![Azure přidat účet Microsoft Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. V **připojit** dialogovém okně zvolte **přidat účet Azure**, zvolte vaši **prostředí Azure**a vyberte **přihlásit...** . 

    ![Přihlášení k účtu Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Po úspěšném přihlášení ke svému účtu se zobrazí všechna předplatná Azure, které jsou spojené s vaším účtem.

### <a name="examine-the-output-queue"></a>Prozkoumání výstupní fronty

1. Ve Visual Studio Code, stisknutím klávesy F1 otevřete paletu příkazů, vyhledejte a spusťte příkaz `Azure Storage: Open in Storage Explorer` a zvolte název svého účtu úložiště. Váš účet úložiště se otevře v Průzkumníku služby Azure Storage.  

1. Rozbalte uzel **Fronty** a potom vyberte frontu s názvem **outqueue**. 

   Tato fronta obsahuje zprávu, kterou vytvořila výstupní vazba fronty při spuštění funkce aktivované protokolem HTTP. Pokud jste tuto funkci volali s výchozí `name` hodnotu *Azure*, zpráva fronty je *jméno předané funkci: Azure*.

    ![Zpráva fronty zobrazená v Průzkumníku služby Storage](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Spusťte funkci znovu odeslat další požadavek a zobrazí se vám ve frontě zobrazí nová zpráva.  

Teď je čas se publikovat aplikace aktualizace funkcí do Azure.

## <a name="redeploy-and-test-the-updated-app"></a>Znovu nasadit a otestovat aktualizované aplikace

1. Ve Visual Studio Code stisknutím klávesy F1 otevřete paletu příkazů. V paletu příkazů, vyhledejte a vyberte `Azure Functions: Deploy to function app...`.

1. Vyberte aplikace function app, kterou jste vytvořili v prvním článku. Vzhledem k tomu, že nasazujete projekt tak, aby stejné aplikaci, vyberte **nasadit** zavřete upozornění o přepsání souborů.

1. Po dokončení nasazení můžete znovu použít cURL nebo v prohlížeči opakovaně nasazeném funkci otestovat. Stejně jako předtím, připojte řetězec dotazu `&name=<yourname>` na adresu URL, jako v následujícím příkladu:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Znovu [zobrazení zprávy ve frontě úložiště](#examine-the-output-queue) k ověření, že výstupní vazbu znovu vygeneruje nové zprávy ve frontě.

## <a name="clean-up-resources"></a>Vyčištění prostředků

*Prostředky* v Azure odkazují na aplikace funkcí, funkce, účty úložiště atd. Jsou seskupené do *skupin prostředků*. Odstraněním skupiny odstraníte všechno, co v této skupině je.

Vytvořili jste prostředky k dokončení těchto rychlých startů. Tyto prostředky se vám mohou fakturovat, a to v závislosti na [stavu účtu](https://azure.microsoft.com/account/) a [cenách služeb](https://azure.microsoft.com/pricing/). Pokud prostředky už nepotřebujete, můžete k jejich odstranění použít tento postup:

1. Ve Visual Studio Code stisknutím klávesy F1 otevřete paletu příkazů. V paletu příkazů, vyhledejte a vyberte `Azure Functions: Open in portal`.

1. Zvolte vaši aplikaci function app a stiskněte klávesu Enter. Stránky aplikace funkcí je otevřen v [webu Azure portal](https://portal.azure.com).

1. V **přehled** zaškrtněte políčko s názvem odkazu **skupiny prostředků**.

    ![Vyberte skupinu prostředků, kterou chcete odstranit ze stránky aplikace funkcí.](./media/functions-add-output-binding-storage-queue-vs-code/functions-app-delete-resource-group.png)

1. Na stránce **Skupina prostředků** zkontrolujte seznam zahrnutých prostředků a ověřte, že jsou to ty, které chcete odstranit.
 
1. Vyberte **Odstranit skupinu prostředků** a postupujte podle pokynů.

   Odstranění může trvat několik minut. Po jeho dokončení se na několik sekund zobrazí oznámení. K zobrazení tohoto oznámení můžete také vybrat ikonu zvonku v horní části stránky.

## <a name="next-steps"></a>Další postup

Aktualizovali jsme vaši funkci aktivovanou protokolem HTTP k zápisu dat do fronty úložiště. Další informace o vývoji funkcí najdete v tématu [vývoj Azure Functions pomocí nástroje Visual Studio Code](functions-develop-vs-code.md).

Dále byste měli povolit monitorování pomocí Application Insights pro aplikaci funkcí:

> [!div class="nextstepaction"]
> [Povolit integraci modulu Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/
