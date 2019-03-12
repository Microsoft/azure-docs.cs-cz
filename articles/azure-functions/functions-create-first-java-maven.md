---
title: Vytvoření první funkce v Azure pomocí Javy a Mavenu | Dokumentace Microsoftu
description: Vytvořte a publikujte do Azure jednoduchou funkci aktivovanou protokolem HTTP pomocí Javy a Mavenu.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, functions, event processing, compute, serverless architecture
ms.service: azure-functions
ms.devlang: java
ms.topic: quickstart
ms.date: 08/10/2018
ms.author: routlaw, glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 96ac8522f94a3555fe63575baca8bbfbabc272d9
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570449"
---
# <a name="create-your-first-function-with-java-and-maven"></a>Vytvoření první funkce pomocí Javy a Mavenu

Tento článek vás provede pomocí nástroje příkazového řádku Maven k sestavení a publikování funkce Java do služby Azure Functions. Až budete hotovi, kód vaší funkce poběží [plánu Consumption](functions-scale.md#consumption-plan) v Azure a můžete aktivovat pomocí požadavku HTTP.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K vývoji funkcí pomocí Javy, musíte mít nainstalované tyto položky:

- [Java Developer Kit](https://www.azul.com/downloads/zulu/) verze 8.
- [Apache Maven](https://maven.apache.org) verze 3.0 nebo novější.
- [Azure CLI](https://docs.microsoft.com/cli/azure)
- [Nástroje Azure Functions Core](functions-run-local.md#v2) (vyžaduje **.NET Core 2.x SDK**)

> [!IMPORTANT]
> Pro dokončení tohoto rychlého startu musí být proměnná prostředí JAVA_HOME nastavená na umístění instalace sady JDK.

## <a name="generate-a-new-functions-project"></a>Vygenerování nového projektu Functions

Spuštěním následujícího příkazu v prázdné složce vygenerujte projekt Functions z [archetypu Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

### <a name="linuxmacos"></a>Linux/macOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

### <a name="windows"></a>Windows

```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-archetype"
```

```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

Maven vás vyzve k zadání hodnot požadovaných k dokončení generování projektu. Informace o hodnotách _groupId_, _artifactId_ a _version_ najdete v referenčních informacích k [zásadám vytváření názvů pro Maven](https://maven.apache.org/guides/mini/guide-naming-conventions.html). Hodnota _appName_ musí být v rámci Azure jedinečná, takže Maven ve výchozím nastavení vygeneruje název aplikace na základě dříve zadané hodnoty _artifactId_. Hodnota _packageName_ určuje balíček Java pro vygenerovaný kód funkce.

Níže uvedené identifikátory `com.fabrikam.functions` a `fabrikam-functions` slouží jako příklad a k zpřehlednění pozdějších kroků v tomto rychlém startu. V tomto kroku můžete do Mavenu zadat vlastní hodnoty.

```Output
Define value for property 'groupId': com.fabrikam.functions
Define value for property 'artifactId' : fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Confirm properties configuration: Y
```

Maven vytvoří soubory projektu, v tomto příkladu `fabrikam-functions`, v nové složce _artifactId_. Vygenerovaný kód připravený k použití v projektu je jednoduchá funkce [aktivovaná protokolem HTTP](/azure/azure-functions/functions-bindings-http-webhook), která vypisuje text žádosti:

```java
public class Function {
    /**
     * This function listens at endpoint "/api/hello". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/hello
     * 2. curl {your host}/api/hello?name=HTTP%20Query
     */
    @FunctionName("hello")
    public HttpResponseMessage<String> hello(
            @HttpTrigger(name = "req", methods = {"get", "post"}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");

        // Parse query parameter
        String query = request.getQueryParameters().get("name");
        String name = request.getBody().orElse(query);

        if (name == null) {
            return request.createResponse(400, "Please pass a name on the query string or in the request body");
        } else {
            return request.createResponse(200, "Hello, " + name);
        }
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

```Output
Listening on http://localhost:7071
Hit CTRL-C to exit...

Http Functions:

   hello: http://localhost:7071/api/hello
```

V novém okně terminálu aktivujte funkci z příkazového řádku pomocí příkazu curl:

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/hello
```

```Output
Hello LocalFunction!
```

Pomocí klávesové zkratky `Ctrl-C` v terminálu zastavte kód aplikace.

## <a name="deploy-the-function-to-azure"></a>Nasazení funkce do Azure

V procesu nasazení do služby Azure Functions se používají přihlašovací údaje účtu z Azure CLI. Než budete pokračovat, [přihlaste se pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

```azurecli
az login
```

Nasaďte svůj kód do nové aplikace funkcí s použitím cíle Maven `azure-functions:deploy`.

```
mvn azure-functions:deploy
```

Po dokončení nasazení se zobrazí adresa URL, pomocí které můžete přistupovat k vaší aplikaci funkcí Azure:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

Otestujte aplikaci funkcí spuštěnou v Azure pomocí `cURL`. V níže uvedené ukázce budete muset změnit adresu URL, aby odpovídala adrese URL nasazené vlastní aplikace funkcí z předchozího kroku.

> [!NOTE]
> Ujistěte se, že nastavíte **přístupová práva** k `Anonymous`. Při výběru výchozí úrovně `Function`, je potřeba k dispozici [klíč funkce](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) v žádosti o přístup ke koncovému bodu funkce.

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/hello -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Provedení změn a opětovné nasazení

Abyste upravili text vrácený aplikací funkcí, upravte ve vygenerovaném projektu zdrojový soubor `src/main.../Function.java`. Změňte tento řádek:

```java
return request.createResponse(200, "Hello, " + name);
```

Na následující:

```java
return request.createResponse(200, "Hi, " + name);
```

Změny uložte a stejně jako předtím proveďte opětovné nasazení spuštěním příkazu `azure-functions:deploy`. Aplikace funkcí se aktualizuje a tento požadavek:

```bash
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

Bude mít aktualizovaný výstup:

```Output
Hi, AzureFunctionsTest
```

## <a name="next-steps"></a>Další postup

Vytvořili jste aplikaci funkcí v Javě s jednoduchým triggerem HTTP a nasadili jste ji do služby Azure Functions.

- V [příručce pro vývojáře funkcí v Javě](functions-reference-java.md) najdete další informace o vývoji funkcí v Javě.
- Do svého projektu můžete přidat další funkce s jinými triggery s použitím cíle Maven `azure-functions:add`.
- Funkce můžete psát a ladit místně pomocí nástrojů [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) a [Eclipse](functions-create-maven-eclipse.md). 
- Funkce ladění můžete do Azure nasadit pomocí editoru Visual Studio Code. Pokyny najdete v dokumentaci editoru Visual Studio Code o [aplikacích bez serveru v Javě](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud).
