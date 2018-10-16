---
title: Vytvořit funkci Azure pomocí Javy a IntelliJ | Dokumentace Microsoftu
description: Zjistěte, jak vytvářet a publikovat jednoduchou aplikaci aktivovanou protokolem HTTP, bez serveru v Azure pomocí Javy a IntelliJ.
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
ms.openlocfilehash: eb8499ef6c0f872a0761f7be606e058387947b2b
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319876"
---
# <a name="create-your-first-azure-function-with-java-and-intellij-preview"></a>Vytvoření první funkce Azure pomocí Javy a IntelliJ (preview)

> [!NOTE]
> Java pro službu Azure Functions je aktuálně ve verzi Preview.

V tomto článku se dozvíte:
- Jak vytvořit [bez serveru](https://azure.microsoft.com/overview/serverless-computing/) projekt Functions s IntelliJ IDEA a Apache Maven
- Kroky pro testování a ladění funkcí v integrovaném vývojovém prostředí (IDE) ve vašem počítači
- Pokyny pro nasazení projektu funkce Azure Functions

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

K vývoji funkce pomocí Javy a IntelliJ, nainstalujte následující software:

- [Java Developer Kit](https://www.azul.com/downloads/zulu/) (JDK), verze 8
- [Nástroje Apache Maven](https://maven.apache.org), verze 3.0 nebo vyšší
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), komunity nebo Ultimate verze pomocí Mavenu
- [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> Proměnná prostředí JAVA_HOME musí nastavit na umístění instalace sady JDK k dokončení kroků v tomto článku.

 Doporučujeme, abyste nainstalovali [nástrojů Azure Functions Core, verze 2](functions-run-local.md#v2). Poskytuje místní vývojové prostředí pro psaní, spouštění a ladění funkcí Azure Functions.

## <a name="create-a-functions-project"></a>Vytvoření projektu funkce

1. V IntelliJ IDEA, vyberte **vytvořit nový projekt**.  
1. V **nový projekt** okně **Maven** v levém podokně.
1. Vyberte **vytvořit z archetype** zaškrtněte políčko a potom vyberte **přidat Archetype** pro [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
1. V **přidat Archetype** okna, vyplňte následující pole:
    - _GroupId_: com.microsoft.azure
    - _ArtifactId_: azure-functions-archetype
    - _Verze_: použijte nejnovější verzi z [centrální úložiště](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![vytvořte projekt Maven z archetype v IntelliJ IDEA](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Vyberte **OK**a pak vyberte **Další**.
1. Zadejte podrobnosti o pro aktuální projekt a vyberte **Dokončit**.

Maven vytvoří soubory projektu do nové složky se stejným názvem jako _ArtifactId_ hodnotu. Generovaný kód projektu je jednoduchý [aktivovanou protokolem HTTP](/azure/azure-functions/functions-bindings-http-webhook) funkce, která vypisuje text spouštěcí požadavku HTTP.

## <a name="run-functions-locally-in-the-ide"></a>Spuštění funkce místně v rozhraní IDE

> [!NOTE]
> Ke spuštění a ladění funkcí místně, ujistěte se, že jste nainstalovali [nástrojů Azure Functions Core, verze 2](functions-run-local.md#v2).

1. Importovat změny ručně nebo povolit [Automatický import](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Otevřít **projektech Maven** nástrojů.
1. Rozbalte **životního cyklu**a pak otevřete **balíčku**. Toto řešení je sestavení a zabalení do nově vytvořeného cílový adresář.
1. Rozbalte **moduly plug-in** > **azure functions** a otevřete **azure-functions: spuštění** spustit místní modul runtime Azure Functions.  
  ![Maven nástrojů pro službu Azure Functions](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

1. Zavřete dialogové okno spustit po dokončení testování vaší funkce. Jenom jednu funkci hostitel může být aktivní a v chodu místně v čase.

## <a name="debug-the-function-in-intellij"></a>Ladění funkce v IntelliJ

1. Chcete-li spustit hostitele funkce v režimu ladění, přidejte **- DenableDebug** jako argument při spuštění funkce. Můžete buď změnit konfiguraci v [cíle maven](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) nebo v okně terminálu spusťte následující příkaz:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Tento příkaz způsobí, že funkce hostitele pro otevření portu pro ladění na 5005.

1. Na **spustit** nabídce vyberte možnost **upravit konfigurace**.
1. Vyberte **(+)** přidáte **vzdálené**.
1. Dokončení _název_ a _nastavení_ pole a pak vyberte **OK** uložte konfiguraci.
1. Po dokončení instalace, vybrat **ladění < název vzdáleného konfigurace >** nebo stiskněte klávesy Shift + F9 kláves pro spuštění ladění.

   ![Ladění funkcí ve IntelliJ](media/functions-create-first-java-intellij/debug-configuration-intellij.PNG)

1. Jakmile budete hotovi, ukončete ladicí program a spuštěnému procesu. Jenom jednu funkci hostitel může být aktivní a v chodu místně v čase.

## <a name="deploy-the-function-to-azure"></a>Nasazení funkce do Azure

1. Funkci můžete nasadit do Azure, je nutné nejprve [přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Nasaďte svůj kód do nové funkce s použitím `azure-functions:deploy` cíle Maven. Můžete také vybrat **azure functions: nasazení** možnost v okně projekty Maven.

   ```
   mvn azure-functions:deploy
   ```

1. Po úspěšném nasazení funkce vyhledejte adresu URL funkce ve výstupu rozhraní příkazového řádku Azure.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Další postup

- V [příručce pro vývojáře funkcí v Javě](functions-reference-java.md) najdete další informace o vývoji funkcí v Javě.
- Přidat další funkce s jinými triggery s do svého projektu pomocí `azure-functions:add` cíle Maven.
