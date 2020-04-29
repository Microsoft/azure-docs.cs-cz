---
title: Vytvoření funkce Azure pomocí Java a IntelliJ
description: Naučte se vytvářet a publikovat jednoduchou aplikaci bez serveru s protokolem HTTP spuštěnou v Azure pomocí Java a IntelliJ.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 05074696ca2cc9d425269561523beb11eb18c4f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756445"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Vytvoření první funkce Azure pomocí Java a IntelliJ

V tomto článku najdete:
- Postup vytvoření projektu funkce bez [serveru](https://azure.microsoft.com/overview/serverless-computing/) s IntelliJ nápadem a Apache Maven
- Postup testování a ladění funkce v integrovaném vývojovém prostředí (IDE) ve vašem počítači
- Pokyny pro nasazení projektu funkce pro Azure Functions

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

K vývoji funkcí Java a IntelliJ nainstalujte následující software:

- [Java Developer Kit](https://www.azul.com/downloads/zulu/) (JDK), verze 8
- [Apache Maven](https://maven.apache.org), verze 3,0 nebo novější
- [INTELLIJ nápad](https://www.jetbrains.com/idea/download), verze z komunity nebo Ultimate s Maven
- [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> Aby bylo možné dokončit kroky v tomto článku, musí být proměnná prostředí JAVA_HOME nastavena na umístění instalace JDK.

 Doporučujeme nainstalovat [Azure Functions Core Tools, verze 2](functions-run-local.md#v2). Poskytuje místní vývojové prostředí pro psaní, spouštění a ladění Azure Functions.

## <a name="create-a-functions-project"></a>Vytvoření projektu Functions

1. V IntelliJ NÁPADu vyberte **vytvořit nový projekt**.  
1. V okně **Nový projekt** vyberte v levém podokně možnost **Maven** .
1. Zaškrtněte políčko **vytvořit z Archetype** a pak vyberte **Přidat Archetype** pro [Azure-Functions-Archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
1. V okně **Přidat Archetype** vyplňte pole následujícím způsobem:
    - ID _skupiny_: com. Microsoft. Azure
    - _ArtifactId_: Azure-Functions – Archetype
    - _Verze_: Podívejte se a použijte nejnovější verzi z [centrálního úložiště](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![. vytvoření projektu Maven z Archetype v IntelliJ nápadu](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Vyberte **OK**a pak vyberte **Další**.
1. Zadejte podrobnosti pro aktuální projekt a vyberte **Dokončit**.

Maven vytvoří soubory projektu v nové složce se stejným názvem, jako má hodnota _ArtifactId_ . Generovaný kód projektu je jednoduchá funkce [aktivovaná protokolem HTTP](/azure/azure-functions/functions-bindings-http-webhook) , která vypisuje tělo triggeru požadavku HTTP.

## <a name="run-functions-locally-in-the-ide"></a>Místní spuštění funkcí v integrovaném vývojovém prostředí

> [!NOTE]
> Chcete-li spustit a ladit funkce místně, ujistěte se, že jste nainstalovali [Azure Functions Core Tools, verze 2](functions-run-local.md#v2).

1. Importovat změny ručně nebo povolit [Automatický import](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Otevřete panel nástrojů **Maven projekty** .
1. Rozbalte **životní cyklus**a pak otevřete **balíček**. Řešení je sestaveno a zabaleno v nově vytvořeném cílovém adresáři.
1. Rozbalení **modulů plug-in** > **Azure – funkce** a otevření **Azure-Functions: Run** spustí Azure Functions místní modul runtime.  
  ![Panel nástrojů Maven pro Azure Functions](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

1. Až skončíte s testováním funkce, zavřete dialogové okno spustit. Pouze jeden hostitel funkce může být aktivní a spuštěn místně v jednom okamžiku.

## <a name="debug-the-function-in-intellij"></a>Ladění funkce v IntelliJ

1. Chcete-li spustit hostitele funkce v režimu ladění, přidejte parametr **-DenableDebug** jako argument při spuštění funkce. Můžete buď změnit konfiguraci v [Maven cílech](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) nebo spustit následující příkaz v okně terminálu:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Tento příkaz způsobí, že hostitel funkce otevře port pro ladění v 5005.

1. V nabídce **Spustit** vyberte **Upravit konfigurace**.
1. Pokud chcete přidat **vzdálené úložiště**, vyberte **(+)** .
1. Dokončete pole _název_ a _Nastavení_ a pak kliknutím na **tlačítko OK** konfiguraci uložte.
1. Po nastavení vyberte **ladit < název vzdálené konfigurace >** nebo stiskněte SHIFT + F9 na klávesnici a spusťte ladění.

1. Až budete hotovi, ukončete ladicí program a běžící proces. Pouze jeden hostitel funkce může být aktivní a spuštěn místně v jednom okamžiku.

## <a name="deploy-the-function-to-azure"></a>Nasazení funkce do Azure

1. Než budete moct nasadit funkci do Azure, musíte se [přihlásit pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Nasaďte svůj kód do nové funkce pomocí cíle `azure-functions:deploy` Maven. Můžete také vybrat možnost **Azure-Functions: Deploy** v okně projekty Maven.

   ```
   mvn azure-functions:deploy
   ```

1. Po úspěšném nasazení funkce vyhledejte v výstupu Azure CLI adresu URL vaší funkce.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Další kroky

- V [příručce pro vývojáře funkcí v Javě](functions-reference-java.md) najdete další informace o vývoji funkcí v Javě.
- Přidejte do projektu další funkce s různými triggery pomocí cíle `azure-functions:add` Maven.
