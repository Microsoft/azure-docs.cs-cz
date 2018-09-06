---
title: Vytvoření aplikace funkcí Azure pomocí Javy a IntelliJ | Dokumentace Microsoftu
description: Příručka k vytvoření a publikování jednoduchý HTTP aktivuje aplikace bez serveru pomocí Javy a IntelliJ do služby Azure Functions.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: Azure functions, funkce, zpracování událostí, výpočetních, architektury bez serveru, java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 317d8bf40ef152e5b7dae6406be29330feaaa8d3
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "43842466"
---
# <a name="create-your-first-function-with-java-and-intellij-preview"></a>Vytvoření první funkce pomocí Javy a IntelliJ (Preview)

> [!NOTE] 
> Java pro službu Azure Functions je aktuálně ve verzi Preview.

V tomto článku se dozvíte, jak vytvořit [bez serveru](https://azure.microsoft.com/overview/serverless-computing/) projekt Functions s IntelliJ IDEA a Apache Maven, testování a ladění na vašem počítači v prostředí IDE a nasazení do služby Azure Functions. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

K vývoji aplikace funkcí pomocí Javy a IntelliJ, musíte mít nainstalované tyto položky:

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/) verze 8.
-  [Apache Maven](https://maven.apache.org) verze 3.0 nebo novější.
-  [IntelliJ IDEA](https://www.jetbrains.com/idea/download), komunity nebo Ultimate pomocí nástroje Maven.
-  [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> Pro dokončení tohoto rychlého startu musí být proměnná prostředí JAVA_HOME nastavená na umístění instalace sady JDK.

Je vysoce doporučené také nainstalovat [nástrojů Azure Functions Core, verze 2](functions-run-local.md#v2), které poskytují místní vývojové prostředí pro psaní, spouštění a ladění funkcí Azure Functions. 


## <a name="create-a-functions-project"></a>Vytvoření projektu funkce

1. V IntelliJ IDEA, klikněte na **vytvořit nový projekt**.  
1. Vytvořit z **Maven**
1. Zaškrtnutím příslušného políčka **vytvořit z archetype** a **přidat Archetype** pro [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - GroupId: com.microsoft.azure
    - ArtifactId: azure-functions-archetype
    - Verze: Použijte nejnovější verzi z [centrální úložiště](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![vytvoření Maven v IntelliJ](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Klikněte na tlačítko **Další** a zadejte podrobnosti pro aktuální projekt a nakonec **Dokončit**.

Maven přesune soubory projektu do nové složky s názvem _artifactId_. Generovaný kód v projektu je jednoduchý [aktivovanou protokolem HTTP](/azure/azure-functions/functions-bindings-http-webhook) funkce, která vypisuje text spouštěcí požadavku HTTP.

## <a name="run-functions-locally-in-the-ide"></a>Spuštění funkce místně v rozhraní IDE

> [!NOTE]
> [Nástroje Azure Functions Core, verze 2](functions-run-local.md#v2) musí být nainstalována ke spouštění a ladění funkcí místně.

1. Vyberte možnost změny importovat nebo zajistěte, aby [Automatický import](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html) je povolená.
1. Otevřít **projektech Maven** nástrojů
1. V rámci životního cyklu, dvakrát klikněte na panel **balíčku** balení a sestavte řešení a vytvořit cílový adresář.
1. V části moduly plug-in -> azure-functions dvakrát klikněte na **azure-functions: spuštění** spustit místní modul runtime azure functions.  
  ![Maven nástrojů pro službu Azure Functions](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

Zavřete dialogové okno pro spuštění po dokončení testování vaší funkce. Jenom jednu funkci hostitel může být aktivní a v chodu místně v čase.

### <a name="debug-the-function-in-intellij"></a>Ladění funkce v IntelliJ
Chcete-li spustit hostitele funkce v režimu ladění, přidejte **- DenableDebug** jako argument při spuštění funkce. Může spusťte následující příkazový řádek, v terminálu nebo v konfiguraci [cíle maven](https://www.jetbrains.com/help/idea/maven-support.html#run_goal). Potom hostitele funkcí otevře port pro ladění na 5005. 

```
mvn azure-functions:run -DenableDebug
```

Chcete-li ladit v IntelliJ, v **spustit** nabídky vyberte možnost **upravit konfigurace**. Klikněte na tlačítko **+** přidáte **vzdálené**. Vyplňte **název** a **nastavení**a potom klikněte na tlačítko **OK** uložte konfiguraci. Po dokončení instalace, klikněte na tlačítko **ladění** "Your Vzdálená konfigurace Name" nebo stiskněte klávesu **Shift + F9** pro spuštění ladění.

![Ladění funkcí ve IntelliJ](media/functions-create-first-java-intellij/debug-configuration-intellij.PNG)

Po dokončení ukončete ladicí program a spuštěnému procesu. Jenom jednu funkci hostitel může být aktivní a v chodu místně v čase.

## <a name="deploy-the-function-to-azure"></a>Nasazení funkce do Azure

V procesu nasazení do služby Azure Functions se používají přihlašovací údaje účtu z Azure CLI. [Přihlaste se pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) před pokračováním pomocí příkazového řádku vašeho počítače.

```azurecli
az login
```

Nasaďte svůj kód do nové aplikaci funkcí s použitím `azure-functions:deploy` cíle Maven, nebo vyberte funkce azure: nasazení – možnost v okně projekty Maven.

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

## <a name="next-steps"></a>Další postup

- V [příručce pro vývojáře funkcí v Javě](functions-reference-java.md) najdete další informace o vývoji funkcí v Javě.
- Do svého projektu můžete přidat další funkce s jinými triggery s použitím cíle Maven `azure-functions:add`.
