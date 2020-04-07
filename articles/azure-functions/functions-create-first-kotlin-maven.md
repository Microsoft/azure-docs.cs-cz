---
title: Vytvořte si první funkci v Azure s Kotlinem a Mavenem
description: Vytvořte a publikujte funkci aktivovanou protokolem HTTP do Azure pomocí Kotlina a Mavenu.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: e4ac4f669d38f07d9fe4edbd600cc06f135fac03
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674101"
---
# <a name="quickstart-create-your-first-function-with-kotlin-and-maven"></a>Rychlý start: Vytvořte si první funkci s Kotlin a Maven

Tento článek vás provede pomocí nástroje příkazového řádku Maven k sestavení a publikování projektu funkce Kotlin do Funkce Azure. Až budete hotovi, kód funkce poběží v [plánu Consumption](functions-scale.md#consumption-plan) v Azure a je možné ho aktivovat prostřednictvím požadavku HTTP.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li vyvíjet funkce pomocí kotlinu, musíte mít nainstalovány následující:

- [Java Developer Kit](https://aka.ms/azure-jdks) verze 8
- [Apache Maven](https://maven.apache.org) verze 3.0 nebo novější
- [Azure CLI](https://docs.microsoft.com/cli/azure)
- [Základní nástroje Azure Functions](./functions-run-local.md#v2) verze 2.6.666 nebo vyšší

> [!IMPORTANT]
> Pro dokončení tohoto rychlého startu musí být proměnná prostředí JAVA_HOME nastavená na umístění instalace sady JDK.

## <a name="generate-a-new-functions-project"></a>Vygenerování nového projektu Functions

Spuštěním následujícího příkazu v prázdné složce vygenerujte projekt Functions z [archetypu Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

> [!NOTE]
> Pokud máte problémy se spuštěním příkazu, podívejte `maven-archetype-plugin` se, jaká verze se používá. Vzhledem k tomu, že příkaz `.pom` používáte v prázdném adresáři bez souboru, `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` může se pokoušet použít plugin starší verze z, pokud jste inovovali maven ze starší verze. Pokud ano, zkuste `maven-archetype-plugin` adresář smažit a příkaz znovu spustit.

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-kotlin-archetype"
```

# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```
---

Maven vás požádá o hodnoty potřebné k dokončení generování projektu. Informace o hodnotách _groupId_, _artifactId_ a _version_ najdete v referenčních informacích k [zásadám vytváření názvů pro Maven](https://maven.apache.org/guides/mini/guide-naming-conventions.html). Hodnota _appName_ musí být v rámci Azure jedinečná, takže Maven ve výchozím nastavení vygeneruje název aplikace na základě dříve zadané hodnoty _artifactId_. Hodnota _packageName_ určuje balíček Kotlin pro kód generované funkce.

Níže uvedené identifikátory `com.fabrikam.functions` a `fabrikam-functions` slouží jako příklad a k zpřehlednění pozdějších kroků v tomto rychlém startu. Doporučujeme vám, abyste mavenovi v tomto kroku poskytli své vlastní hodnoty.

<pre>
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: artifactId, Value: fabrikam-function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: packageInPathFormat, Value: com/fabrikam/function
[INFO] Parameter: appName, Value: fabrikam-function-20190524171507291
[INFO] Parameter: resourceGroup, Value: java-functions-group
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: appRegion, Value: westus
[INFO] Parameter: artifactId, Value: fabrikam-function
</pre>

Maven vytvoří soubory projektu, v tomto příkladu `fabrikam-functions`, v nové složce _artifactId_. Vygenerovaný kód připravený k použití v projektu je jednoduchá funkce [aktivovaná protokolem HTTP](/azure/azure-functions/functions-bindings-http-webhook), která vypisuje text žádosti:

```kotlin
class Function {

    /**
     * This function listens at endpoint "/api/HttpTrigger-Java". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/HttpTrigger-Java&code={your function key}
     * 2. curl "{your host}/api/HttpTrigger-Java?name=HTTP%20Query&code={your function key}"
     * Function Key is not needed when running locally, it is used to invoke function deployed to Azure.
     * More details: https://aka.ms/functions_authorization_keys
     */
    @FunctionName("HttpTrigger-Java")
    fun run(
            @HttpTrigger(
                    name = "req",
                    methods = [HttpMethod.GET, HttpMethod.POST],
                    authLevel = AuthorizationLevel.FUNCTION) request: HttpRequestMessage<Optional<String>>,
            context: ExecutionContext): HttpResponseMessage {

        context.logger.info("HTTP trigger processed a ${request.httpMethod.name} request.")

        val query = request.queryParameters["name"]
        val name = request.body.orElse(query)

        name?.let {
            return request
                    .createResponseBuilder(HttpStatus.OK)
                    .body("Hello, $name!")
                    .build()
        }

        return request
                .createResponseBuilder(HttpStatus.BAD_REQUEST)
                .body("Please pass a name on the query string or in the request body")
                .build()
    }
}
```

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Změňte adresář na složku nově vytvořeného projektu a sestavte a spusťte funkci pomocí Mavenu:

```
cd fabrikam-function
mvn clean package
mvn azure-functions:run
```

> [!NOTE]
> Pokud se zobrazuje tato výjimka `javax.xml.bind.JAXBException` pro Javu 9, najdete alternativní řešení na [GitHubu](https://github.com/jOOQ/jOOQ/issues/6477).

Když je funkce spuštěná místně ve vašem systému a připravená reagovat na požadavky HTTP, zobrazí se tento výstup:

<pre>
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpTrigger-Java: [GET,POST] http://localhost:7071/api/HttpTrigger-Java
</pre>

V novém okně terminálu aktivujte funkci z příkazového řádku pomocí příkazu curl:

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/HttpTrigger-Java
```

<pre>
Hello LocalFunction!
</pre>

Pomocí klávesové zkratky `Ctrl-C` v terminálu zastavte kód aplikace.

## <a name="deploy-the-function-to-azure"></a>Nasazení funkce do Azure

V procesu nasazení do služby Azure Functions se používají přihlašovací údaje účtu z Azure CLI. [Před pokračováním se přihlaste pomocí příkazového příkazového příkazu k Řešení Azure.](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

```azurecli
az login
```

Nasaďte svůj kód do nové aplikace funkcí s použitím cíle Maven `azure-functions:deploy`.

> [!NOTE]
> Když k nasazení aplikace Function použijete kód Visual Studio, nezapomeňte zvolit bezplatné předplatné, jinak se zobrazí chyba. Můžete sledovat předplatné na levé straně ide.

```
mvn azure-functions:deploy
```

Po dokončení nasazení se zobrazí adresa URL, pomocí které můžete přistupovat k vaší aplikaci funkcí Azure:

<pre>
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
</pre>

Otestujte aplikaci funkcí spuštěnou v Azure pomocí `cURL`. V níže uvedené ukázce budete muset změnit adresu URL, aby odpovídala adrese URL nasazené vlastní aplikace funkcí z předchozího kroku.

> [!NOTE]
> Ujistěte se, že `Anonymous`jste nastavili **přístupová práva** na aplikaci . Pokud zvolíte výchozí `Function`úroveň aplikace , je nutné prezentovat [funkční klíč](functions-bindings-http-webhook-trigger.md#authorization-keys) v požadavcích na přístup ke koncovému bodu funkce.

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/HttpTrigger-Java -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Provedení změn a opětovné nasazení

Abyste upravili text vrácený aplikací funkcí, upravte ve vygenerovaném projektu zdrojový soubor `src/main.../Function.java`. Změňte tento řádek:

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hello, $name!")
        .build()
```

Na následující kód:

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hi, $name!")
        .build()
```

Změny uložte a stejně jako předtím proveďte opětovné nasazení spuštěním příkazu `azure-functions:deploy`. Aplikace funkcí se aktualizuje a tento požadavek:

```
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

Zobrazí aktualizovaný výstup:

<pre>
Hi, AzureFunctionsTest
</pre>


## <a name="reference-bindings"></a>Referenční vazby

Chcete-li pracovat s [funkcemi aktivačních událostí a vazeb](functions-triggers-bindings.md) jiných než aktivační událost HTTP a časovač aktivační událost, je třeba nainstalovat rozšíření vazby. I když to nevyžaduje tento článek, budete potřebovat vědět, jak provést povolení rozšíření při práci s jinými typy vazeb.

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="next-steps"></a>Další kroky

Vytvořili jste aplikaci funkce Kotlin s jednoduchou aktivační událostí HTTP a nasadili jste ji do Azure Functions.

- Další informace o vývoji funkcí Javy a Kotlinu naleznete v [průvodci vývojářskými](functions-reference-java.md) funkcemi Java Functions.
- Do svého projektu můžete přidat další funkce s jinými triggery s použitím cíle Maven `azure-functions:add`.
- Funkce můžete psát a ladit místně pomocí nástrojů [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) a [Eclipse](functions-create-maven-eclipse.md). 
- Funkce ladění můžete do Azure nasadit pomocí editoru Visual Studio Code. Pokyny najdete v dokumentaci editoru Visual Studio Code o [aplikacích bez serveru v Javě](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud).
