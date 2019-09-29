---
title: Připojení funkcí k Azure Storage pomocí Visual Studio Code
description: Naučte se, jak přidat výstupní vazbu pro připojení vašich funkcí k Azure Storage frontě pomocí Visual Studio Code.
author: ggailey777
ms.author: glenga
ms.date: 06/25/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
manager: jeconnoc
ms.openlocfilehash: 63065c918a6f78510b4908c5e2ae80df67665b40
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672605"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio-code"></a>Připojení funkcí k Azure Storage pomocí Visual Studio Code

Azure Functions umožňuje připojit funkce ke službám Azure a dalším prostředkům bez nutnosti psát vlastní kód pro integraci. Tyto *vazby*, které představují vstupní i výstupní, jsou deklarovány v rámci definice funkce. Data z vazeb jsou k dispozici funkci jako parametry. Trigger je speciální typ vstupní vazby. I když má funkce pouze jednu Trigger, může mít více vstupních a výstupních vazeb. Další informace najdete v tématu [Azure Functions triggery a koncepty vazeb](functions-triggers-bindings.md).

V tomto článku se dozvíte, jak pomocí Visual Studio Code připojit funkci, kterou jste vytvořili v [předchozím článku rychlý Start](functions-create-first-function-vs-code.md) pro Azure Storage. Výstupní vazba, kterou do této funkce přidáte, zapisuje data z požadavku HTTP do zprávy ve frontě úložiště Azure Queue. 

Většina vazeb vyžaduje uložený připojovací řetězec, který funkce používá pro přístup k vázané službě. Pro snazší použití účtu úložiště, který jste vytvořili v aplikaci Function App. Připojení k tomuto účtu je již Uloženo v nastavení aplikace s názvem `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Požadavky

Než začnete tento článek, musíte splnit následující požadavky:

* Nainstalujte [Azure Storage rozšíření pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).
* Nainstalujte [Průzkumník služby Azure Storage](https://storageexplorer.com/). Průzkumník služby Storage je nástroj, který budete používat k prohlédnutí zpráv ve frontě generovaných výstupní vazbou. Průzkumník služby Storage se podporují v operačních systémech macOS, Windows a Linux.
* Nainstalovat [.NET Core CLI nástroje](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x) (C# jenom projekty).
* Proveďte kroky v [části 1 rychlého startu Visual Studio Code](functions-create-first-function-vs-code.md). 

V tomto článku se předpokládá, že jste už přihlášení k předplatnému Azure z Visual Studio Code. Můžete se přihlásit spuštěním `Azure: Sign In` z palety příkazů. 

## <a name="download-the-function-app-settings"></a>Stažení nastavení Function App

V [předchozím článku rychlý Start](functions-create-first-function-vs-code.md)jste vytvořili aplikaci funkcí v Azure spolu s požadovaným účtem úložiště. Připojovací řetězec pro tento účet je bezpečně uložený v nastavení aplikace v Azure. V tomto článku napíšete zprávy do fronty úložiště ve stejném účtu. Pokud se chcete připojit k účtu úložiště, když se funkce spouští místně, musíte si stáhnout nastavení aplikace do souboru Local. Settings. JSON. 

1. Stisknutím klávesy F1 otevřete paletu příkazů a pak vyhledejte a spusťte příkaz `Azure Functions: Download Remote Settings....`. 

1. Vyberte aplikaci funkcí, kterou jste vytvořili v předchozím článku. Vyberte možnost **Ano pro vše** , pokud chcete přepsat stávající místní nastavení. 

    > [!IMPORTANT]  
    > Protože obsahuje tajné kódy, soubor Local. Settings. JSON se nikdy nepublikuje a je vyloučený ze správy zdrojového kódu.

1. Zkopírujte hodnotu `AzureWebJobsStorage`, což je klíč pro hodnotu připojovacího řetězce účtu úložiště. Pomocí tohoto připojení ověříte, zda výstupní vazba funguje podle očekávání.

## <a name="register-binding-extensions"></a>Registrace rozšíření vazeb

Vzhledem k tomu, že používáte výstupní vazbu úložiště front, musíte mít nainstalované rozšíření úložiště vazeb před spuštěním projektu. 

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

S výjimkou aktivačních událostí protokolu HTTP a časovače jsou vazby implementovány jako balíčky rozšíření. Spusťte následující příkaz [dotnet přidat balíček](/dotnet/core/tools/dotnet-add-package) v okně terminálu a přidejte do projektu balíček rozšíření úložiště.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```
---
Nyní můžete přidat výstupní vazbu úložiště do projektu.

## <a name="add-an-output-binding"></a>Přidání výstupní vazby

V případě funkcí vyžaduje každý typ vazby `direction`, `type` a jedinečné `name`, které budou definovány v souboru Function. JSON. Způsob, jakým definujete tyto atributy, závisí na jazyku aplikace Function App.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

Atributy vazby se definují přímo v souboru Function. JSON. V závislosti na typu vazby mohou být vyžadovány další vlastnosti. [Konfigurace výstupu fronty](functions-bindings-storage-queue.md#output---configuration) popisuje pole požadovaná pro vazbu fronty Azure Storage. Rozšíření usnadňuje přidávání vazeb do souboru Function. JSON. 

Vazbu vytvoříte tak, @no__t že kliknete pravým tlačítkem (Ctrl + kliknutí na macOS) do složky HttpTrigger a kliknete na **Přidat vazbu...** . Podle pokynů definujte následující vlastnosti vazby pro novou vazbu:

| Výzva | Value | Popis |
| -------- | ----- | ----------- |
| **Vybrat směr vazby** | `out` | Vazba je výstupní vazba. |
| **Vyberte vazbu se směrováním...** | `Azure Queue Storage` | Vazba je vazba fronty Azure Storage. |
| **Název, který slouží k identifikaci této vazby v kódu** | `msg` | Název, který identifikuje parametr vazby, na který se odkazuje v kódu. |
| **Fronta, do které bude odeslána zpráva** | `outqueue` | Název fronty, do které vazba zapisuje. Pokud pole *Queue* neexistuje, vytvoří ho při prvním použití. |
| **Vyberte nastavení z místní. nastavení. JSON.** | `AzureWebJobsStorage` | Název nastavení aplikace, které obsahuje připojovací řetězec pro účet úložiště. Nastavení `AzureWebJobsStorage` obsahuje připojovací řetězec pro účet úložiště, který jste vytvořili pomocí aplikace Function App. |

Do pole `bindings` v souboru Function. JSON se přidá vazba, která by teď měla vypadat jako v následujícím příkladu:

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

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

## <a name="add-code-that-uses-the-output-binding"></a>Přidání kódu, který používá výstupní vazbu

Po definování vazby můžete použít `name` vazby k přístupu jako atributu v signatuře funkce. Pomocí výstupní vazby nemusíte pro ověřování používat kód Azure Storage SDK, získat odkaz na frontu nebo zapisovat data. Úlohy za běhu functions a Queue výstupní vazby jsou za vás.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

Přidejte kód, který používá výstupní objekt vazby `msg` v `context.bindings` k vytvoření zprávy fronty. Vložte tento kód před příkaz `context.res`.

```javascript
// Add a message to the Storage queue.
context.bindings.msg = "Name passed to the function: " + 
(req.query.name || req.body.name);
```

V tomto okamžiku by měla funkce vypadat takto:

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

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

V účtu úložiště se vytvoří nová fronta s názvem **front** . modul runtime Functions při prvním použití výstupní vazby. Pomocí Průzkumník služby Storage ověříte, že se vytvořila fronta spolu s novou zprávou.

### <a name="connect-storage-explorer-to-your-account"></a>Propojení Průzkumníka služby Storage s vaším účtem

Pokud jste už Průzkumník služby Azure Storage nainstalovali a připojili ho k účtu Azure, přeskočte tuto část.

1. Spusťte nástroj [Azure Storage Explorer] , vyberte ikonu připojit na levé straně a vyberte **Přidat účet**.

    ![Přidat účet Azure do Průzkumník služby Microsoft Azure Storage](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. V dialogovém okně **připojit** zvolte **Přidat účet Azure**, zvolte **prostředí Azure**a pak vyberte **Přihlásit se...** . 

    ![Přihlášení k účtu Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Po úspěšném přihlášení ke svému účtu uvidíte všechna předplatná Azure přidružená k vašemu účtu.

### <a name="examine-the-output-queue"></a>Prozkoumání výstupní fronty

1. V Visual Studio Code stisknutím klávesy F1 otevřete paletu příkazů, potom vyhledejte a spusťte příkaz `Azure Storage: Open in Storage Explorer` a zvolte název svého účtu úložiště. Váš účet úložiště se otevře v Průzkumník služby Azure Storage.  

1. Rozbalte uzel **Fronty** a potom vyberte frontu s názvem **outqueue**. 

   Tato fronta obsahuje zprávu, kterou vytvořila výstupní vazba fronty při spuštění funkce aktivované protokolem HTTP. Pokud jste funkci vyvolali s výchozí `name` hodnotou *Azure*, zpráva fronty je *název předané funkci: Azure*.

    ![Zpráva fronty zobrazená v Průzkumník služby Azure Storage](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Znovu spusťte funkci, odešlete další žádost a ve frontě se zobrazí nová zpráva.  

Teď je čas na opětovné publikování aktualizované aplikace Function App do Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Opětovné nasazení a ověření aktualizované aplikace

1. V Visual Studio Code stisknutím klávesy F1 otevřete paletu příkazů. V paletě příkazů vyhledejte a vyberte `Azure Functions: Deploy to function app...`.

1. Vyberte aplikaci funkcí, kterou jste vytvořili v prvním článku. Vzhledem k tomu, že projekt znovu nasazujete do stejné aplikace, vyberte **nasadit** a zastavte tak upozornění týkající se přepsání souborů.

1. Po dokončení nasazení můžete znovu použít kudrlinkou nebo prohlížeč k otestování znovu nasazené funkce. Stejně jako dřív připojíte řetězec dotazu `&name=<yourname>` k adrese URL, jak je uvedeno v následujícím příkladu:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Opětovným [zobrazením zprávy ve frontě úložiště](#examine-the-output-queue) ověřte, zda výstupní vazba znovu generuje novou zprávu ve frontě.

## <a name="clean-up-resources"></a>Vyčištění prostředků

*Prostředky* v Azure odkazují na aplikace funkcí, funkce, účty úložiště atd. Jsou seskupené do *skupin prostředků*. Odstraněním skupiny odstraníte všechno, co v této skupině je.

Vytvořili jste prostředky k dokončení těchto rychlých startů. Tyto prostředky se vám mohou fakturovat, a to v závislosti na [stavu účtu](https://azure.microsoft.com/account/) a [cenách služeb](https://azure.microsoft.com/pricing/). Pokud prostředky už nepotřebujete, můžete k jejich odstranění použít tento postup:

1. V Visual Studio Code stisknutím klávesy F1 otevřete paletu příkazů. V paletě příkazů vyhledejte a vyberte `Azure Functions: Open in portal`.

1. Zvolte aplikaci Function App a stiskněte klávesu ENTER. Stránka Function App se otevře v [Azure Portal](https://portal.azure.com).

1. Na kartě **Přehled** vyberte v části **Skupina prostředků**odkaz pojmenovaný.

    ![Vyberte skupinu prostředků, kterou chcete odstranit ze stránky aplikace funkcí.](./media/functions-add-output-binding-storage-queue-vs-code/functions-app-delete-resource-group.png)

1. Na stránce **Skupina prostředků** zkontrolujte seznam zahrnutých prostředků a ověřte, že jsou to ty, které chcete odstranit.
 
1. Vyberte **Odstranit skupinu prostředků** a postupujte podle pokynů.

   Odstranění může trvat několik minut. Po jeho dokončení se na několik sekund zobrazí oznámení. K zobrazení tohoto oznámení můžete také vybrat ikonu zvonku v horní části stránky.

## <a name="next-steps"></a>Další kroky

Aktualizovali jste funkci aktivovanou protokolem HTTP, která zapisuje data do fronty úložiště. Další informace o vývoji funkcí najdete v tématu [vývoj Azure Functions používání Visual Studio Code](functions-develop-vs-code.md).

Dále byste měli povolit Application Insights monitorování aplikace Function App:

> [!div class="nextstepaction"]
> [Povolení integrace Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/
