---
title: Použití jazyka Java a Maven k publikování funkce – Azure Functions
description: Vytvořte a publikujte do Azure jednoduchou funkci aktivovanou protokolem HTTP pomocí Javy a Mavenu.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, functions, event processing, compute, serverless architecture
ms.service: azure-functions
ms.topic: quickstart
ms.devlang: java
ms.date: 08/10/2018
ms.author: routlaw
ms.reviewer: glenga
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 5447fdcfa86c35b7c5cf079ae8446c30785e893f
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299398"
---
# <a name="quickstart-use-java-to-create-and-publish-a-function-to-azure-functions"></a>Rychlý start: Použití jazyka Java k vytvoření a publikování funkce pro Azure Functions

Tento článek popisuje, jak vytvořit a publikovat funkci Java ve službě Azure Functions pomocí nástroje příkazového řádku Maven. Jakmile budete hotovi, váš kód funkce se spustí v [plánu spotřeby](functions-scale.md#consumption-plan) v Azure a může se aktivovat pomocí požadavku HTTP.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Aby bylo možné vyvíjet funkce pomocí jazyka Java, je nutné mít nainstalované následující:

- [Java Developer Kit](https://aka.ms/azure-jdks), verze 8
- [Apache Maven](https://maven.apache.org), verze 3,0 nebo novější
- [Azure CLI](https://docs.microsoft.com/cli/azure)
- [Azure Functions Core Tools](./functions-run-local.md#v2) verze 2.6.666 nebo vyšší

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

> [!NOTE]
> Pokud máte problémy se spuštěním příkazu, Prohlédněte si, jaká `maven-archetype-plugin` verze se používá. Vzhledem k tomu, že příkaz spouštíte v prázdném adresáři `.pom` bez souboru, může se pokusit použít modul plug-in starší verze z `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` , pokud jste Maven upgradovali ze starší verze. Pokud ano, zkuste odstranit `maven-archetype-plugin` adresář a znovu spustit příkaz.

### <a name="windows"></a>Windows

```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-archetype"
```

```cmd
mvn archetype:generate ^
    "-DarchetypeGroupId=com.microsoft.azure" ^
    "-DarchetypeArtifactId=azure-functions-archetype"
```

Maven vás vyzve k zadání hodnot požadovaných k dokončení generování projektu. Informace o hodnotách _groupId_, _artifactId_ a _version_ najdete v referenčních informacích k [zásadám vytváření názvů pro Maven](https://maven.apache.org/guides/mini/guide-naming-conventions.html). Hodnota _appName_ musí být v rámci Azure jedinečná, takže Maven ve výchozím nastavení vygeneruje název aplikace na základě dříve zadané hodnoty _artifactId_. Hodnota _packageName_ určuje balíček Java pro vygenerovaný kód funkce.

Níže uvedené identifikátory `com.fabrikam.functions` a `fabrikam-functions` slouží jako příklad a k zpřehlednění pozdějších kroků v tomto rychlém startu. V tomto kroku můžete do Mavenu zadat vlastní hodnoty.

```Output
Define value for property 'groupId' (should match expression '[A-Za-z0-9_\-\.]+'): com.fabrikam.functions
Define value for property 'artifactId' (should match expression '[A-Za-z0-9_\-\.]+'): fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Define value for property 'appRegion' westus: :
Define value for property 'resourceGroup' java-functions-group: :
Confirm properties configuration: Y
```

Maven vytvoří soubory projektu, v tomto příkladu `fabrikam-functions`, v nové složce _artifactId_. Připravený ke spuštění vygenerovaného kódu v projektu je funkce [aktivovaná protokolem HTTP](/azure/azure-functions/functions-bindings-http-webhook) , která vypisuje tělo žádosti. Nahraďte *Src/Main/Java/com/Fabrikam/Functions/Function. Java* následujícím kódem: 

```java
package com.fabrikam.functions;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class Function {
    /**
     * This function listens at endpoint "/api/HttpTrigger-Java". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/HttpTrigger-Java
     * 2. curl {your host}/api/HttpTrigger-Java?name=HTTP%20Query
     */
    @FunctionName("HttpTrigger-Java")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
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

## <a name="enable-extension-bundles"></a>Povolit sady rozšíření

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Změňte adresář na nově vytvořenou složku projektu (tu, která obsahuje soubory Host. JSON a pom. XML) a sestavte a spusťte funkci s Maven:

```CMD
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

   hello: http://localhost:7071/api/HttpTrigger-Java
```

V novém okně terminálu aktivujte funkci z příkazového řádku pomocí příkazu curl:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java -d LocalFunction
```

```Output
Hello LocalFunction!
```

Pomocí klávesové zkratky `Ctrl-C` v terminálu zastavte kód aplikace.

## <a name="deploy-the-function-to-azure"></a>Nasazení funkce do Azure

V procesu nasazení do služby Azure Functions se používají přihlašovací údaje účtu z Azure CLI. Než budete pokračovat, přihlaste [se pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) .

```azurecli
az login
```

Nasaďte svůj kód do nové aplikace funkcí s použitím cíle Maven `azure-functions:deploy`. Tím se provede nasazení souboru zip s povoleným režimem [Spustit z balíčku](functions-deployment-technologies.md#zip-deploy) .

> [!NOTE]
> Když použijete Visual Studio Code k nasazení aplikace Function App, nezapomeňte zvolit předplatné bez bezplatného předplatného nebo se zobrazí chyba. Své předplatné můžete sledovat na levé straně rozhraní IDE.

```azurecli
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
> Ujistěte se, že jste nastavili `Anonymous` **přístupová práva** na. Když zvolíte výchozí úroveň `Function`, budete muset [klíč funkce](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) prezentovat v žádosti o přístup ke koncovému bodu funkce.

```azurecli
curl -w "\n" https://fabrikam-function-20170920120101928.azurewebsites.net/api/HttpTrigger-Java -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Provedení změn a opětovné nasazení

Abyste upravili text vrácený aplikací funkcí, upravte ve vygenerovaném projektu zdrojový soubor `src/main.../Function.java`. Změňte tento řádek:

```java
return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
```

Na následující:

```java
return request.createResponseBuilder(HttpStatus.OK).body("Hi, " + name).build();
```

Uložte změny. Spusťte MVN vyčistit balíček a znovu ho nasaďte `azure-functions:deploy` spuštěním z terminálu jako dřív. Aplikace funkcí se aktualizuje a tento požadavek:

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

> [!NOTE] 
> Můžete také vytvořit Azure Functions projekt založený na Kotlin pomocí Azure-Functions-Kotlin-Archetype. Další informace najdete v [úložišti GitHub](https://github.com/microsoft/azure-maven-archetypes/tree/develop/azure-functions-kotlin-archetype) .
