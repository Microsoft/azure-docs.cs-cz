---
title: Vytvoření aplikace funkcí Azure pomocí Javy a Eclipse | Dokumentace Microsoftu
description: Příručka k vytvoření a publikování jednoduchý HTTP aktivuje aplikace bez serveru pomocí Javy a Eclipse do služby Azure Functions.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: Azure functions, funkce, zpracování událostí, výpočetních, architektury bez serveru, java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 8b0681165fe84dd54f3b81f610c6698998e881de
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46125489"
---
# <a name="create-your-first-function-with-java-and-eclipse-preview"></a>Vytvoření první funkce pomocí Javy a Eclipse (Preview)

> [!NOTE] 
> Java pro službu Azure Functions je aktuálně ve verzi Preview.

V tomto článku se dozvíte, jak vytvořit [bez serveru](https://azure.microsoft.com/overview/serverless-computing/) projekt Functions pomocí integrovaného vývojového prostředí Eclipse a Apache Maven, testování a ladění a pak ji nasadit do služby Azure Functions. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

K vývoji aplikace funkcí pomocí Javy a Eclipse, musíte mít nainstalované tyto položky:

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/) verze 8.
-  [Apache Maven](https://maven.apache.org) verze 3.0 nebo novější.
-  [Eclipse](https://www.eclipse.org/downloads/packages/), pomocí Javy a Mavenu podporovat.
-  [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> Pro dokončení tohoto rychlého startu musí být proměnná prostředí JAVA_HOME nastavená na umístění instalace sady JDK.

Důrazně doporučujeme také nainstalovat [nástrojů Azure Functions Core, verze 2](functions-run-local.md#v2), které poskytují místní prostředí pro spouštění a ladění funkcí Azure Functions. 

## <a name="create-a-functions-project"></a>Vytvoření projektu funkce

1. V Eclipse, vyberte **souboru** nabídce pak vyberte **projektu**. 
1. Otevřít **projektu v Javě** složky **nový projekt** okna a vyberte **projekt Maven s**a pak vyberte **Další**.
1. Přijměte výchozí hodnoty v **nový projekt Maven s** dialog a vyberte **Další**.
1. Vyberte **přidat Archetype** a přidání položky [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - ID skupiny archetype: com.microsoft.azure
    - Archetype ID artefaktu: azure-functions-archetype
    - Verze: Použijte nejnovější verzi z [centrální úložiště](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![vytvoření Eclipse Maven](media/functions-create-first-java-eclipse/functions-create-eclipse.png)  
1. Klikněte na tlačítko **OK** a zadejte podrobnosti pro aktuální projekt a nakonec **Dokončit**.

Maven přesune soubory projektu do nové složky s názvem _artifactId_. Generovaný kód v projektu je jednoduchý [aktivovanou protokolem HTTP](/azure/azure-functions/functions-bindings-http-webhook) funkce, která vypisuje text spouštěcí požadavku HTTP.

## <a name="run-functions-locally-in-the-ide"></a>Spuštění funkce místně v rozhraní IDE

> [!NOTE]
> [Nástroje Azure Functions Core, verze 2](functions-run-local.md#v2) musí být nainstalována ke spouštění a ladění funkcí místně.

1. Klikněte pravým tlačítkem na generovaný projekt a pak zvolte **spustit jako** a **sestavení Maven**.
1. V **upravit konfiguraci** dialogové okno, zadejte `package` v **cíle** a **název** pole, vyberte **spustit**. To se sestavit a zabalit kód funkce.
1. Po dokončení sestavení vytvořit jinou konfiguraci spustit jak je uvedeno výše, pomocí `azure-functions:run` jako cíl a název. Vyberte **spustit** ke spuštění funkce v rozhraní IDE.

Ukončení modulu runtime v okně konzoly po dokončení testování vaší funkce. Jenom jednu funkci hostitel může být aktivní a v chodu místně v čase.

### <a name="debug-the-function-in-eclipse"></a>Ladění funkce v prostředí Eclipse

Ve vaší **spustit jako** konfigurační nastavení v předchozím kroku, změna `azure-functions:run` k `mvn azure-functions:run -DenableDebug` a spusťte aktualizovanou konfiguraci ke spuštění aplikace function app v režimu ladění.

Vyberte **spustit** nabídce a otevřete **ladění konfigurace**. Zvolte **Remote Java Application** a vytvořte novou. Zadejte název vaší konfigurace a vyplňte nastavení. Port by měl být konzistentní s otevřen hostitelem funkce, která ve výchozím nastavení je port pro ladění `5005`. Po dokončení instalace, klikněte na `Debug` pro spuštění ladění.

![Ladění funkcí v prostředí Eclipse](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

Nastavit zarážky a zkontrolovat objekty ve své funkci pomocí integrovaného vývojového prostředí. Až budete hotovi, ukončete ladicí program a spuštěné funkce hostitele. Jenom jednu funkci hostitel může být aktivní a v chodu místně v čase.

## <a name="deploy-the-function-to-azure"></a>Nasazení funkce do Azure

V procesu nasazení do služby Azure Functions se používají přihlašovací údaje účtu z Azure CLI. [Přihlaste se pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) před pokračováním pomocí příkazového řádku vašeho počítače.

```azurecli
az login
```

Nasaďte svůj kód do nové aplikaci funkcí s použitím `azure-functions:deploy` cíle Maven v novém **spustit jako** konfigurace.

Po dokončení nasazení se zobrazí adresa URL, pomocí které můžete přistupovat k vaší aplikaci funkcí Azure:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>Další postup

- V [příručce pro vývojáře funkcí v Javě](functions-reference-java.md) najdete další informace o vývoji funkcí v Javě.
- Do svého projektu můžete přidat další funkce s jinými triggery s použitím cíle Maven `azure-functions:add`.
