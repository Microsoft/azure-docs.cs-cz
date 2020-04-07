---
title: Vytvoření funkce Azure pomocí Javy a IntelliJ
description: Naučte se vytvářet a publikovat jednoduchou aplikaci bez serveru v Azure s azure pomocí Javy a IntelliJ.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 05074696ca2cc9d425269561523beb11eb18c4f3
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756445"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Vytvoření první funkce Azure pomocí Javy a IntelliJ

V tomto článku najdete:
- Jak vytvořit projekt funkce [bez serveru](https://azure.microsoft.com/overview/serverless-computing/) s IntelliJ IDEA a Apache Maven
- Kroky pro testování a ladění funkce v integrovaném vývojovém prostředí (IDE) ve vašem počítači
- Pokyny pro nasazení projektu funkce do funkcí Azure

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

Chcete-li vyvinout funkci s java a intelliJ, nainstalujte následující software:

- [Java Developer Kit](https://www.azul.com/downloads/zulu/) (JDK), verze 8
- [Apache Maven](https://maven.apache.org), verze 3.0 nebo vyšší
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), Komunitní nebo Ultimate verze s Maven
- [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> Proměnná prostředí JAVA_HOME musí být nastavena na umístění instalace sady JDK, aby bylo možné provést kroky v tomto článku.

 Doporučujeme nainstalovat [nástroje Azure Functions Core Tools verze 2](functions-run-local.md#v2). Poskytuje prostředí místního vývoje pro psaní, spouštění a ladění funkcí Azure.

## <a name="create-a-functions-project"></a>Vytvoření projektu Functions

1. V aplikaci IntelliJ IDEA vyberte **Vytvořit nový projekt**.  
1. V okně **Nový projekt** vyberte **maven** v levém podokně.
1. Zaškrtněte políčko **Vytvořit z archetypu** a pak vyberte **Přidat archetyp** pro [azurový-funkční archetyp](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
1. V okně **Přidat archetyp** vyplňte pole takto:
    - _Skupinové Id_: com.microsoft.azure
    - _ArtifactId_: azurové funkce-archetyp
    - _Verze_: Kontrola a použití nejnovější verze z [centrálního úložiště](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Vytvoření projektu Maven z archetypu v IntelliJ IDEA](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Vyberte **OK**a pak vyberte **Další**.
1. Zadejte podrobnosti o aktuálním projektu a vyberte **Dokončit**.

Maven vytvoří soubory projektu v nové složce se stejným názvem jako _hodnota ArtifactId._ Vygenerovaný kód projektu je jednoduchá funkce [spouštěná protokolem HTTP,](/azure/azure-functions/functions-bindings-http-webhook) která odráží tělo spouštěcího požadavku HTTP.

## <a name="run-functions-locally-in-the-ide"></a>Spustit funkce místně v prostředí IDE

> [!NOTE]
> Chcete-li spouštět a ladit funkce místně, ujistěte se, že jste nainstalovali [nástroje Azure Functions Core Tools verze 2](functions-run-local.md#v2).

1. Importujte změny ručně nebo povolte [automatický import](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Otevřete panel nástrojů **Maven Projects.**
1. Rozbalte **položku Životní cyklus**a otevřete **balíček**. Řešení je sestaveno a zabaleno v nově vytvořeném cílovém adresáři.
1. Rozbalte modul y**Azure-functions** **a** > otevřete **azure-functions:run** a spusťte místní modul runtime Azure Functions.  
  ![Panel nástrojů Maven pro funkce Azure](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

1. Po dokončení testování funkce zavřete dialogové okno spustit. Pouze jeden hostitel funkce může být aktivní a běží místně najednou.

## <a name="debug-the-function-in-intellij"></a>Ladění funkce v aplikaci IntelliJ

1. Chcete-li spustit hostitele funkce v režimu ladění, přidejte **-DenableDebug** jako argument při spuštění funkce. Můžete buď změnit konfiguraci v [maven cíle](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) nebo spustit následující příkaz v okně terminálu:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Tento příkaz způsobí, že hostitel funkce otevře ladicí port v 5005.

1. V nabídce **Spustit** vyberte **možnost Upravit konfigurace**.
1. Výběrem **možnosti (+)** přidáte **dálkový ovladač**.
1. Vyplňte pole _Název_ a _nastavení_ a pak vyberte **OK,** chcete-li konfiguraci uložit.
1. Po instalaci vyberte **ladění < název vzdálené konfigurace >** nebo stisknutím Shift+F9 na klávesnici spusťte ladění.

1. Až budete hotovi, zastavte ladicí program a spuštěný proces. Pouze jeden hostitel funkce může být aktivní a běží místně najednou.

## <a name="deploy-the-function-to-azure"></a>Nasazení funkce do Azure

1. Než budete moci nasadit svou funkci do Azure, musíte [se přihlásit pomocí azure cli](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Nasazení kódu do nové funkce `azure-functions:deploy` pomocí cíle Maven. Můžete také vybrat **azure-functions:deploy** možnost v okně Maven Projekty.

   ```
   mvn azure-functions:deploy
   ```

1. Vyhledejte adresu URL pro vaši funkci ve výstupu Azure CLI po úspěšném nasazení funkce.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Další kroky

- V [příručce pro vývojáře funkcí v Javě](functions-reference-java.md) najdete další informace o vývoji funkcí v Javě.
- Přidejte další funkce s různými aktivačními `azure-functions:add` událostmi do projektu pomocí cíle Maven.
