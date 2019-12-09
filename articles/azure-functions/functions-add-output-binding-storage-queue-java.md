---
title: Připojte funkci jazyka Java k Azure Storage
description: Naučte se připojit funkci Java aktivovanou protokolem HTTP, která se Azure Storage pomocí výstupní vazby úložiště fronty.
ms.date: 10/14/2019
ms.topic: quickstart
ms.openlocfilehash: 98f49338f0df935347a26798aceccb80f9f43f50
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926903"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Připojte funkci jazyka Java k Azure Storage

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

V tomto článku se dozvíte, jak integrovat funkci, kterou jste vytvořili v [předchozím článku rychlý Start](functions-create-first-java-maven.md) s frontou Azure Storage. Výstupní vazba, kterou přidáte do této funkce, zapisuje data z požadavku HTTP do zprávy ve frontě.

Většina vazeb vyžaduje uložený připojovací řetězec, který funkce používá pro přístup k vázané službě. Aby bylo toto připojení snazší, použijte účet úložiště, který jste vytvořili v aplikaci Function App. Připojení k tomuto účtu je již Uloženo v nastavení aplikace s názvem `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Předpoklady

Než začnete s tímto článkem, proveďte kroky v [části 1 rychlého startu Java](functions-create-first-java-maven.md).

## <a name="download-the-function-app-settings"></a>Stažení nastavení Function App

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Povolit sady rozšíření

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Nyní můžete přidat výstupní vazbu úložiště do projektu.

## <a name="add-an-output-binding"></a>Přidání výstupní vazby

V projektu Java jsou vazby definovány jako anotace vazby v metodě Function. Soubor *Function. JSON* pak automaticky generuje na základě těchto poznámek.

Přejděte do umístění kódu funkce v části _Src/Main/Java_, otevřete soubor projektu *Function. Java* a do definice `run` metody přidejte následující parametr:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

Parametr `msg` je [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) typ, který představuje kolekci řetězců, které jsou zapsány jako zprávy do výstupní vazby po dokončení funkce. V tomto případě je výstupem fronta úložiště s názvem `outqueue`. Připojovací řetězec pro účet úložiště je nastaven metodou `connection`. Místo samotného připojovacího řetězce předáte nastavení aplikace, které obsahuje připojovací řetězec účtu úložiště.

Definice metody `run` by teď měla vypadat jako v následujícím příkladu:  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```

## <a name="add-code-that-uses-the-output-binding"></a>Přidání kódu, který používá výstupní vazbu

Nyní můžete použít nový parametr `msg` k zápisu do výstupní vazby z kódu funkce. Přidejte následující řádek kódu před odpověď na úspěch pro přidání hodnoty `name` do výstupní vazby `msg`.

```java
msg.setValue(name);
```

Když použijete výstupní vazbu, nemusíte používat Azure Storage kód SDK pro ověřování, získání odkazu na frontu nebo zápis dat. Úlohy za běhu functions a Queue výstupní vazby jsou za vás.

Vaše metoda `run` by teď měla vypadat jako v následujícím příkladu:

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    context.getLogger().info("Java HTTP trigger processed a request.");

    // Parse query parameter
    String query = request.getQueryParameters().get("name");
    String name = request.getBody().orElse(query);

    if (name == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
    } else {
        // Write the name to the message queue. 
        msg.setValue(name);

        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
}
```

## <a name="update-the-tests"></a>Aktualizace testů

Vzhledem k tomu, že Archetype také vytvoří sadu testů, je nutné aktualizovat tyto testy pro zpracování nového parametru `msg` v signatuře `run` metody.  

Přejděte do umístění testovacího kódu v části _Src/test/Java_, otevřete soubor projektu *Function. Java* a v části `//Invoke` nahraďte řádek kódu následujícím kódem.

```java
@SuppressWarnings("unchecked")
final OutputBinding<String> msg = (OutputBinding<String>)mock(OutputBinding.class);

// Invoke
final HttpResponseMessage ret = new Function().run(req, msg, context);
``` 

Nyní jste připraveni vyzkoušet novou výstupní vazbu místně.

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Stejně jako dřív použijte následující příkaz pro sestavení projektu a místní spuštění Functions Runtime:

```bash
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]  
> Vzhledem k tomu, že jste povolili sady rozšíření v Host. JSON, [rozšíření pro vytváření vazeb úložiště](functions-bindings-storage-blob.md#packages---functions-2x-and-higher) se během spouštění stáhlo a nainstalovalo společně s dalšími rozšířeními vazby Microsoftu.

Stejně jako dřív aktivujte funkci z příkazového řádku pomocí funkce kudrlinkou v novém okně terminálu:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Tentokrát výstupní vazba také vytvoří ve svém účtu úložiště frontu s názvem `outqueue` a přidá zprávu se stejným řetězcem.

Dále pomocí Azure CLI zobrazíte novou frontu a ověříte, že se přidala zpráva. Frontu můžete také zobrazit pomocí [Průzkumník služby Microsoft Azure Storage][Azure Storage Explorer] nebo v [Azure Portal](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Znovu nasadit projekt 

Pokud chcete aktualizovat publikovanou aplikaci, spusťte následující příkaz znovu:  

```azurecli
mvn azure-functions:deploy
```

Znovu můžete použít oblé k otestování nasazené funkce. Stejně jako dřív předejte hodnotu `AzureFunctions` v těle požadavku POST na adresu URL, jako v tomto příkladu:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Opětovným [zkontrolováním zprávy fronty úložiště](#query-the-storage-queue) můžete ověřit, zda výstupní vazba vygeneruje novou zprávu ve frontě, podle očekávání.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

Aktualizovali jste funkci aktivovanou protokolem HTTP, která zapisuje data do fronty úložiště. Další informace o vývoji Azure Functions pomocí jazyka Java najdete v tématu [Příručka pro vývojáře v Azure Functions Java](functions-reference-java.md) a [Azure Functions triggery a vazby](functions-triggers-bindings.md). Příklady kompletních projektů funkcí v jazyce Java naleznete v tématu [ukázky funkcí jazyka Java](/samples/browse/?products=azure-functions&languages=Java). 

Dále byste měli povolit Application Insights monitorování aplikace Function App:

> [!div class="nextstepaction"]
> [Povolení integrace Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/