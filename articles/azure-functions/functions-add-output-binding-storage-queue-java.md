---
title: Připojení funkce Java k Azure Storage
description: Zjistěte, jak připojit funkci Java spouštěnou http k Úložišti Azure pomocí vazby výstupu úložiště fronty.
author: KarlErickson
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: 8ae69bfa7ed00e310205332e05c071158c5fc9a3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78272809"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Připojení funkce Java k Azure Storage

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Tento článek ukazuje, jak integrovat funkci, kterou jste vytvořili v [předchozím článku rychlého startu](functions-create-first-java-maven.md) s frontou služby Azure Storage. Výstupní vazba, kterou přidáte do této funkce, zapisuje data z požadavku HTTP do zprávy ve frontě.

Většina vazeb vyžaduje uložený připojovací řetězec, který funkce používá pro přístup k vázané službě. Chcete-li toto připojení usnadnit, použijte účet úložiště, který jste vytvořili pomocí aplikace funkce. Připojení k tomuto účtu je již `AzureWebJobsStorage`uloženo v nastavení aplikace s názvem .  

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto článku proveďte kroky v [části 1 rychlého startu jazyka Java](functions-create-first-java-maven.md).

## <a name="download-the-function-app-settings"></a>Stažení nastavení aplikace pro funkce

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Povolení rozšiřujících balíčků

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Nyní můžete přidat úložiště výstupní vazby do projektu.

## <a name="add-an-output-binding"></a>Přidání výstupní vazby

V projektu Java jsou vazby definovány jako vazby poznámky na metodu funkce. Soubor *function.json* je pak automaticky generován na základě těchto anotací.

Přejděte do umístění kódu funkce pod _src/main/java_, otevřete soubor projektu *Function.java* a přidejte do definice `run` metody následující parametr:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

Parametr `msg` je [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) typ, který představuje kolekci řetězců, které jsou zapsány jako zprávy do výstupní vazby po dokončení funkce. V tomto případě je výstupem `outqueue`fronta úložiště s názvem . Připojovací řetězec pro účet `connection` úložiště je nastaven metodou. Spíše než samotný připojovací řetězec, předáte nastavení aplikace, která obsahuje připojovací řetězec účtu úložiště.

Definice `run` metody by nyní měla vypadat jako následující příklad:  

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

Nyní můžete použít nový `msg` parametr k zápisu do výstupní vazby z kódu funkce. Přidejte následující řádek kódu před odpověď úspěch `name` přidat `msg` hodnotu výstupní vazby.

```java
msg.setValue(name);
```

Při použití výstupní vazby, není potřeba použít kód Azure Storage SDK pro ověřování, získání odkazu na frontu nebo zápis dat. Funkce runtime a fronty výstupní vazba provést tyto úkoly za vás.

Vaše `run` metoda by nyní měla vypadat jako následující příklad:

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

Vzhledem k tomu, že archetyp také vytvoří sadu testů, `msg` je `run` třeba aktualizovat tyto testy zpracovat nový parametr v podpisu metody.  

Přejděte do umístění testovacího kódu pod _src/test/java_, otevřete soubor projektu *Function.java* a nahraďte řádek kódu pod `//Invoke` následujícím kódem.

```java
@SuppressWarnings("unchecked")
final OutputBinding<String> msg = (OutputBinding<String>)mock(OutputBinding.class);

// Invoke
final HttpResponseMessage ret = new Function().run(req, msg, context);
``` 

Nyní jste připraveni vyzkoušet novou vazbu výstupu místně.

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Stejně jako dříve použijte následující příkaz k sestavení projektu a spuštění prostředí Functions místně:

::: zone pivot="java-build-tools-maven"  
```bash
mvn clean package 
mvn azure-functions:run
```
::: zone-end

::: zone pivot="java-build-tools-gradle"  
```bash
gradle jar --info
gradle azureFunctionsRun
```
::: zone-end

> [!NOTE]  
> Vzhledem k tomu, že jste povolili balíčky rozšíření v host.json, [rozšíření vazby úložiště](functions-bindings-storage-blob.md#add-to-your-functions-app) byla stažena a nainstalována pro vás při spuštění, spolu s dalšími rozšířeními vazby Společnosti Microsoft.

Stejně jako dříve aktivujete funkci z příkazového řádku pomocí cURL v novém okně terminálu:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Tentokrát výstupní vazba také vytvoří `outqueue` frontu pojmenovanou ve vašem účtu úložiště a přidá zprávu se stejným řetězcem.

Dále použijete rozhraní příkazového příkazu K zobrazení nové fronty a ověříte, že byla přidána zpráva. Frontu můžete zobrazit taky pomocí [Průzkumníka úložiště Microsoft Azure][Azure Storage Explorer] nebo na [webu Azure Portal](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Přesadit projekt 

Chcete-li publikovanou aplikaci aktualizovat, spusťte znovu následující příkaz:  

::: zone pivot="java-build-tools-maven"  
```bash
mvn azure-functions:deploy
```
::: zone-end

::: zone pivot="java-build-tools-gradle"  
```bash
gradle azureFunctionsDeploy
```
::: zone-end

Znovu můžete použít cURL k testování nasazené funkce. Stejně jako dříve `AzureFunctions` předavěte hodnotu v textu požadavku POST na adresu URL, jako v tomto příkladu:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Můžete [znovu zkontrolovat zprávu fronty úložiště](#query-the-storage-queue) a ověřit, zda výstupní vazba generuje novou zprávu ve frontě, podle očekávání.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

Aktualizovali jste funkci aktivovanou protokolem HTTP tak, aby zapisovali data do fronty úložiště. Další informace o vývoji funkcí Azure pomocí Jazyka Java najdete v [průvodci vývojářem Azure Functions Java](functions-reference-java.md) a [v Azure Functions triggery a vazby](functions-triggers-bindings.md). Příklady kompletních projektů funkcí v javě naleznete v [ukázkách funkcí jazyka Java](/samples/browse/?products=azure-functions&languages=Java). 

Dále byste měli povolit monitorování Application Insights pro vaši aplikaci funkcí:

> [!div class="nextstepaction"]
> [Povolení integrace Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/
