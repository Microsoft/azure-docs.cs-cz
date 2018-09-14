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
ms.openlocfilehash: 16d6dd6a589256ad98a37465e64e847778d0cc7e
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092589"
---
# <a name="create-your-first-function-with-java-and-maven-preview"></a>Vytvoření první funkce pomocí Javy a Mavenu (Preview)

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

Tento rychlý start vás provede vytvořením funkce [bez serveru](https://azure.microsoft.com/overview/serverless-computing/) pomocí Mavenu, jejím místním otestováním a nasazením do služby Azure Functions. Až budete hotovi, budete mít aplikaci funkcí aktivovanou protokolem HTTP spuštěnou v Azure.

![Přístup k funkci Hello World z příkazového řádku pomocí cUrl](media/functions-create-java-maven/hello-azure.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky
K vývoji aplikace funkcí pomocí Javy potřebujete následující:

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/) verze 8.
-  [Apache Maven](https://maven.apache.org) verze 3.0 nebo novější.
-  [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> Pro dokončení tohoto rychlého startu musí být proměnná prostředí JAVA_HOME nastavená na umístění instalace sady JDK.

## <a name="install-the-azure-functions-core-tools"></a>Instalace nástrojů Azure Functions Core

Nástroje Azure Functions Core poskytují místní vývojové prostředí pro psaní, spouštění a ladění funkcí Azure Functions z terminálu nebo příkazového řádku. 

Než budete pokračovat, nainstalujte si v místním počítači [verzi 2 nástrojů Core](functions-run-local.md#v2).

## <a name="generate-a-new-functions-project"></a>Vygenerování nového projektu Functions

Spuštěním následujícího příkazu v prázdné složce vygenerujte projekt Functions z [archetypu Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

### <a name="linuxmacos"></a>Linux/MacOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

### <a name="windows-cmd"></a>Windows (Příkazový řádek)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

Maven vás vyzve k zadání hodnot požadovaných k dokončení generování projektu. Informace o hodnotách _groupId_, _artifactId_ a _version_ najdete v referenčních informacích k [zásadám vytváření názvů pro Maven](https://maven.apache.org/guides/mini/guide-naming-conventions.html). Hodnota _appName_ musí být v rámci Azure jedinečná, takže Maven ve výchozím nastavení vygeneruje název aplikace na základě dříve zadané hodnoty _artifactId_. Hodnota _packageName_ určuje balíček Java pro vygenerovaný kód funkce.

Hodnota `appRegion` určuje [oblast Azure](https://azure.microsoft.com/global-infrastructure/regions/), ve které chcete nasazenou aplikaci funkcí spouštět. Seznam hodnot názvů oblastí můžete získat spuštěním příkazu `az account list-locations` v Azure CLI. Hodnota `resourceGroup` určuje, ve které skupině prostředků Azure se aplikace funkcí vytvoří.

Níže uvedené identifikátory `com.fabrikam.functions` a `fabrikam-functions` slouží jako příklad a k zpřehlednění pozdějších kroků v tomto rychlém startu. V tomto kroku můžete do Mavenu zadat vlastní hodnoty.

```Output
Define value for property 'groupId': com.fabrikam.functions
Define value for property 'artifactId' : fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Define value for property 'appRegion' westus : 
Define value for property 'resourceGroup' java-functions-group: 
Confirm properties configuration: Y
```

Maven vytvoří soubory projektu, v tomto příkladu `fabrikam-functions`, v nové složce _artifactId_. Vygenerovaný kód připravený k použití v projektu je jednoduchá funkce [aktivovaná protokolem HTTP](/azure/azure-functions/functions-bindings-http-webhook), která vypisuje řetězec „Hello, “ následovaný textem požadavku.

```java
public class Function {
    @FunctionName("HttpTrigger-Java")
    public HttpResponseMessage HttpTriggerJava(
    @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");

        // Parse query parameter
        String query = request.getQueryParameters().get("name");
        String name = request.getBody().orElse(query);

        if (name == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
        } else {
            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        }
    }
}
```

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Změňte adresář na složku nově vytvořeného projektu a sestavte a spusťte funkci pomocí Mavenu:

```
cd fabrikam-functions
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]
> Pokud se zobrazuje tato výjimka `javax.xml.bind.JAXBException` pro Javu 9, najdete alternativní řešení na [GitHubu](https://github.com/jOOQ/jOOQ/issues/6477).

Když je funkce spuštěná místně ve vašem systému a připravená reagovat na požadavky HTTP, zobrazí se tento výstup:

```Output
Listening on http://0.0.0.0:7071/
Hit CTRL-C to exit...

Http Functions:

        HttpTrigger-Java: http://localhost:7071/api/HttpTrigger-Java
```

V novém okně terminálu aktivujte funkci z příkazového řádku pomocí příkazu curl:

```
curl -w '\n' -d LocalFunctionTest http://localhost:7071/api/HttpTrigger-Java
```

```Output
Hello, LocalFunctionTest
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

```
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

```Output
Hello, AzureFunctionsTest
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

## <a name="next-steps"></a>Další kroky

Vytvořili jste aplikaci funkcí v Javě s jednoduchým triggerem HTTP a nasadili jste ji do služby Azure Functions.

- V [příručce pro vývojáře funkcí v Javě](functions-reference-java.md) najdete další informace o vývoji funkcí v Javě.
- Do svého projektu můžete přidat další funkce s jinými triggery s použitím cíle Maven `azure-functions:add`.
- Funkce můžete psát a ladit místně pomocí nástrojů [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) a [Eclipse](functions-create-maven-eclipse.md). 
- Funkce ladění můžete do Azure nasadit pomocí editoru Visual Studio Code. Pokyny najdete v dokumentaci editoru Visual Studio Code o [aplikacích bez serveru v Javě](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud).
