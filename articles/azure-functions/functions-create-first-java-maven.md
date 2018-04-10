---
title: Vytvoření první funkce v Azure pomocí Javy a Mavenu | Dokumentace Microsoftu
description: Vytvořte a publikujte do Azure jednoduchou funkci aktivovanou protokolem HTTP pomocí Javy a Mavenu.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, functions, event processing, compute, serverless architecture
ms.service: functions
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/02/2018
ms.author: routlaw, glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 9cf1d485f32c861ac5b5720cd77a988eee624f4d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-your-first-function-with-java-and-maven-preview"></a>Vytvoření první funkce pomocí Javy a Mavenu (Preview)

> [!NOTE] 
> Java pro službu Azure Functions je aktuálně ve verzi Preview.

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

[Nástroje Azure Functions Core 2.0](https://www.npmjs.com/package/azure-functions-core-tools) poskytují místní vývojové prostředí pro psaní, spouštění a ladění funkcí Azure Functions. 

Pokud je chcete nainstalovat, přejděte k části [Installing](https://github.com/azure/azure-functions-core-tools#installing) (Instalace) projektu nástrojů Azure Functions Core, kde najdete konkrétní pokyny pro váš operační systém.

Můžete je nainstalovat také ručně pomocí správce balíčků [npm](https://www.npmjs.com/), který je součástí [Node.js](https://nodejs.org/). Před tím je však potřeba nainstalovat následující požadované součásti:

-  Nejnovější verzi [.NET Core](https://www.microsoft.com/net/core).
-  [Node.js](https://nodejs.org/download/) verze 8.6 nebo novější.

Pokud chcete pokračovat v instalaci založené na npm, spusťte:

```
npm install -g azure-functions-core-tools@core
```

> [!NOTE]
> Pokud máte potíže s instalací nástrojů Azure Functions Core verze 2.0, podívejte se na [Modul runtime verze 2.x](/azure/azure-functions/functions-run-local#version-2x-runtime).

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

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/hello -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="next-steps"></a>Další kroky

Vytvořili jste aplikaci funkcí v Javě s jednoduchým triggerem HTTP a nasadili jste ji do služby Azure Functions.

- V [příručce pro vývojáře funkcí v Javě](functions-reference-java.md) najdete další informace o vývoji funkcí v Javě.
- Do svého projektu můžete přidat další funkce s jinými triggery s použitím cíle Maven `azure-functions:add`.
- Funkce můžete ladit místně pomocí Visual Studio Code. S nainstalovaným [balíčkem rozšíření Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) a projektem Functions otevřeným ve Visual Studio Code [připojte ladicí program](https://code.visualstudio.com/Docs/editor/debugging#_launch-configurations) k portu 5005. Pak v editoru nastavte zarážku a aktivujte vaši funkci, zatímco je místně spuštěná: ![Ladění funkcí ve Visual Studio Code](media/functions-create-java-maven/vscode-debug.png).
- Funkce můžete ladit vzdáleně pomocí Visual Studio Code. Pokyny najdete v dokumentaci popisující [psaní aplikací bez serveru v Javě](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud).
