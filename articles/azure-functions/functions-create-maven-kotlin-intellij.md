---
title: Vytvoření funkce Azure pomocí Kotlin a IntelliJ
description: Naučte se vytvářet a publikovat jednoduchou aplikaci bez serveru s protokolem HTTP spuštěnou v Azure pomocí Kotlin a IntelliJ.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: fa834e3ac4946c4f617e857342f850445eebfd30
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87055440"
---
# <a name="quickstart-create-your-first-http-triggered-function-with-kotlin-and-intellij"></a>Rychlý Start: Vytvoření první funkce aktivované protokolem HTTP pomocí Kotlin a IntelliJ

V tomto článku se dozvíte, jak vytvořit projekt funkce bez [serveru](https://azure.microsoft.com/overview/serverless-computing/) s IntelliJ nápadem a Apache Maven. Také ukazuje, jak lokálně ladit kód vaší funkce v integrovaném vývojovém prostředí (IDE) a potom nasadit projekt funkce do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

Pokud chcete vyvíjet funkci s Kotlin a IntelliJ, nainstalujte následující software:

- [Java Developer Kit](https://aka.ms/azure-jdks) (JDK), verze 8
- [Apache Maven](https://maven.apache.org), verze 3,0 nebo novější
- [INTELLIJ nápad](https://www.jetbrains.com/idea/download), verze z komunity nebo Ultimate s Maven
- [Azure CLI](/cli/azure)
- [Verze 2. x](functions-run-local.md#v2) Azure Functions Core Tools. Poskytuje místní vývojové prostředí pro psaní, spouštění a ladění Azure Functions.

> [!IMPORTANT]
> Aby bylo možné dokončit kroky v tomto článku, musí být proměnná prostředí JAVA_HOME nastavena na umístění instalace JDK.

## <a name="create-a-functions-project"></a>Vytvoření projektu Functions

1. V IntelliJ NÁPADu vyberte **vytvořit nový projekt**.  
1. V okně **Nový projekt** vyberte v levém podokně možnost **Maven** .
1. Zaškrtněte políčko **vytvořit z Archetype** a pak vyberte **Přidat Archetype** pro [Azure-Functions-Kotlin-Archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype).
1. V okně **Přidat Archetype** vyplňte pole následujícím způsobem:
    - ID _skupiny_: com. Microsoft. Azure
    - _ArtifactId_: Azure-Functions-Kotlin-Archetype
    - _Verze_: použití nejnovější verze z [centrálního úložiště](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype) 
     ![ Vytvoření projektu Maven z Archetype v IntelliJ nápadu](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. Vyberte **OK**a pak vyberte **Další**.
1. Zadejte podrobnosti pro aktuální projekt a vyberte **Dokončit**.

Maven vytvoří soubory projektu v nové složce se stejným názvem, jako má hodnota _ArtifactId_ . Generovaný kód projektu je jednoduchá funkce [aktivovaná protokolem HTTP](./functions-bindings-http-webhook.md) , která vypisuje tělo triggeru požadavku HTTP.

## <a name="run-functions-locally-in-the-ide"></a>Místní spuštění funkcí v integrovaném vývojovém prostředí

> [!NOTE]
> Chcete-li spustit a ladit funkce místně, ujistěte se, že jste nainstalovali [Azure Functions Core Tools, verze 2](functions-run-local.md#v2).

1. Importovat změny ručně nebo povolit [Automatický import](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Otevřete panel nástrojů **Maven projekty** .
1. Rozbalte **životní cyklus**a pak otevřete **balíček**. Řešení je sestaveno a zabaleno v nově vytvořeném cílovém adresáři.
1. Rozbalení **modulů plug-in**  >  **Azure – funkce** a otevření **Azure-Functions: Run** spustí Azure Functions místní modul runtime.  
  ![Panel nástrojů Maven pro Azure Functions](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

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

   ![Funkce ladění v IntelliJ](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. Až budete hotovi, ukončete ladicí program a běžící proces. Pouze jeden hostitel funkce může být aktivní a spuštěn místně v jednom okamžiku.

## <a name="deploy-the-function-to-azure"></a>Nasazení funkce do Azure

1. Než budete moct nasadit funkci do Azure, musíte se [přihlásit pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Nasaďte svůj kód do nové funkce pomocí `azure-functions:deploy` cíle Maven. Můžete také vybrat možnost **Azure-Functions: Deploy** v okně projekty Maven.

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

Teď, když jste nasadili svou první funkci Kotlin do Azure, Projděte si [příručku pro vývojáře Java Functions](functions-reference-java.md) , kde najdete další informace o vývoji funkcí Java a Kotlin.
- Přidejte do projektu další funkce s různými triggery pomocí `azure-functions:add` cíle Maven.
