---
title: Vytvoření funkce Kotlin v Azure Functions pomocí IntelliJ
description: Naučte se používat IntelliJ k vytvoření jednoduché funkce Kotlin aktivované protokolem HTTP, kterou pak publikujete pro běh v prostředí bez serveru v Azure.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: f02643ee28d76d4f90206a1aa2879b4672da2a38
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179438"
---
# <a name="create-your-first-kotlin-function-in-azure-using-intellij"></a>Vytvoření první funkce Kotlin v Azure pomocí IntelliJ

V tomto článku se dozvíte, jak vytvořit funkci Java aktivovanou protokolem HTTP v projektu NÁPADu IntelliJ, jak spustit a ladit projekt v integrovaném vývojovém prostředí (IDE) a nakonec nasadit projekt funkce do aplikace Function App v Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Nastavíte vývojové prostředí

Pokud chcete vytvořit a publikovat funkce Kotlin do Azure pomocí IntelliJ, nainstalujte následující software:

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) (JDK), verze 8
- [Apache Maven](https://maven.apache.org), verze 3,0 nebo novější
- [INTELLIJ nápad](https://www.jetbrains.com/idea/download), verze z komunity nebo Ultimate s Maven
- [Azure CLI](/cli/azure)
- [Verze 2. x](functions-run-local.md#v2) Azure Functions Core Tools. Poskytuje místní vývojové prostředí pro psaní, spouštění a ladění Azure Functions.

> [!IMPORTANT]
> Aby bylo možné dokončit kroky v tomto článku, musí být proměnná prostředí JAVA_HOME nastavena na umístění instalace JDK.

## <a name="create-a-function-project"></a>Vytvoření projektu funkce

1. V IntelliJ NÁPADu vyberte **vytvořit nový projekt**.  
1. V okně **Nový projekt** vyberte v levém podokně možnost **Maven** .
1. Zaškrtněte políčko **vytvořit z Archetype** a pak vyberte **Přidat Archetype** pro [Azure-Functions-Kotlin-Archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype).
1. V okně **Přidat Archetype** vyplňte pole následujícím způsobem:
    - ID _skupiny_: com. Microsoft. Azure
    - _ArtifactId_: Azure-Functions-Kotlin-Archetype
    - _Verze_: použití nejnovější verze z [centrálního úložiště](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype) 
     ![ Vytvoření projektu Maven z Archetype v IntelliJ nápadu](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. Vyberte **OK** a pak vyberte **Další**.
1. Zadejte podrobnosti pro aktuální projekt a vyberte **Dokončit**.

Maven vytvoří soubory projektu v nové složce se stejným názvem, jako má hodnota _ArtifactId_ . Generovaný kód projektu je jednoduchá funkce [aktivovaná protokolem HTTP](./functions-bindings-http-webhook.md) , která vypisuje tělo triggeru požadavku HTTP.

## <a name="run-project-locally-in-the-ide"></a>Spustit projekt místně v integrovaném vývojovém prostředí

> [!NOTE]
> Chcete-li spustit a ladit projekt místně, ujistěte se, že jste nainstalovali [Azure Functions Core Tools, verze 2](functions-run-local.md#v2).

1. Importovat změny ručně nebo povolit [Automatický import](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Otevřete panel nástrojů **Maven projekty** .
1. Rozbalte **životní cyklus** a pak otevřete **balíček**. Řešení je sestaveno a zabaleno v nově vytvořeném cílovém adresáři.
1. Rozbalení **modulů plug-in**  >  **Azure – funkce** a otevření **Azure-Functions: Run** spustí Azure Functions místní modul runtime.  
  ![Panel nástrojů Maven pro Azure Functions](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. Až skončíte s testováním funkce, zavřete dialogové okno spustit. Pouze jeden hostitel funkce může být aktivní a spuštěn místně v jednom okamžiku.

## <a name="debug-the-project-in-intellij"></a>Ladění projektu v IntelliJ

1. Chcete-li spustit hostitele funkce v režimu ladění, přidejte parametr **-DenableDebug** jako argument při spuštění funkce. Můžete buď změnit konfiguraci v [Maven cílech](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) nebo spustit následující příkaz v okně terminálu:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Tento příkaz způsobí, že hostitel funkce otevře port pro ladění v 5005.

1. V nabídce **Spustit** vyberte **Upravit konfigurace**.
1. Pokud chcete přidat **vzdálené úložiště**, vyberte **(+)** .
1. Dokončete pole _název_ a _Nastavení_ a pak kliknutím na **tlačítko OK** konfiguraci uložte.
1. Po nastavení vyberte **ladit < název vzdálené konfigurace >** nebo stiskněte SHIFT + F9 na klávesnici a spusťte ladění.

   ![Ladit projekt v IntelliJ](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. Až budete hotovi, ukončete ladicí program a běžící proces. Pouze jeden hostitel funkce může být aktivní a spuštěn místně v jednom okamžiku.

## <a name="deploy-the-project-to-azure"></a>Nasazení projektu do Azure

1. Než budete moct projekt nasadit do aplikace Function App v Azure, musíte se [přihlásit pomocí Azure CLI](/cli/azure/authenticate-azure-cli).

   ``` azurecli
   az login
   ```

1. Nasaďte svůj kód do nové aplikace Function App pomocí `azure-functions:deploy` cíle Maven. Můžete také vybrat možnost **Azure-Functions: Deploy** v okně projekty Maven.

   ```
   mvn azure-functions:deploy
   ```

1. Po úspěšném nasazení aplikace Function App můžete najít adresu URL funkce triggeru HTTP ve výstupu rozhraní příkazového řádku Azure.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Další kroky

Teď, když jste nasadili svou první aplikaci funkcí Kotlin do Azure, najdete další informace o vývoji funkcí Java a Kotlin v [Azure Functions příručce pro vývojáře Java](functions-reference-java.md) .
- Pomocí cíle Maven přidejte do projektu další aplikace funkcí s různými triggery `azure-functions:add` .
