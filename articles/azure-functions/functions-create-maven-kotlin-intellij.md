---
title: Vytvoření funkce Azure s Kotlin a IntelliJ
description: Naučte se vytvářet a publikovat jednoduchou aplikaci bez serveru v Azure s aplikací Kotlin a IntelliJ spouštěnou http.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: 2eb1a016e04a4150a76112c68683926810f5c66d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674094"
---
# <a name="quickstart-create-your-first-http-triggered-function-with-kotlin-and-intellij"></a>Úvodní příručka: Vytvořte první funkci spuštěnou http s Kotlin a IntelliJ

Tento článek ukazuje, jak vytvořit projekt funkce [bez serveru](https://azure.microsoft.com/overview/serverless-computing/) s IntelliJ IDEA a Apache Maven. Také ukazuje, jak místně ladit kód funkce v integrovaném vývojovém prostředí (IDE) a pak nasadit projekt funkce do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

Chcete-li vyvinout funkci s Kotlin a IntelliJ, nainstalujte následující software:

- [Java Developer Kit](https://aka.ms/azure-jdks) (JDK), verze 8
- [Apache Maven](https://maven.apache.org), verze 3.0 nebo vyšší
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), Komunitní nebo Ultimate verze s Maven
- [Azure CLI](https://docs.microsoft.com/cli/azure)
- [Verze 2.x](functions-run-local.md#v2) základních nástrojů Azure functions. Poskytuje prostředí místního vývoje pro psaní, spouštění a ladění funkcí Azure.

> [!IMPORTANT]
> Proměnná prostředí JAVA_HOME musí být nastavena na umístění instalace sady JDK, aby bylo možné provést kroky v tomto článku.

## <a name="create-a-functions-project"></a>Vytvoření projektu Functions

1. V aplikaci IntelliJ IDEA vyberte **Vytvořit nový projekt**.  
1. V okně **Nový projekt** vyberte **maven** v levém podokně.
1. Zaškrtněte políčko **Vytvořit z archetypu** a pak vyberte **Přidat archetyp** pro [azurový-functions-kotlin-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype).
1. V okně **Přidat archetyp** vyplňte pole takto:
    - _Skupinové Id_: com.microsoft.azure
    - _ArtifactId_: azurové funkce-kotlin-archetyp
    - _Verze_: Použijte nejnovější verzi z [centrálního úložiště](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)
    ![Vytvořit projekt Maven z archetypu v IntelliJ IDEA](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
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
  ![Panel nástrojů Maven pro funkce Azure](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

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

   ![Ladicí funkce v IntelliJ](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

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

Teď, když jste nasadili svou první funkci Kotlin do Azure, přečtěte si [příručku pro vývojáře Java Functions,](functions-reference-java.md) kde najdete další informace o vývoji funkcí Java a Kotlin.
- Přidejte další funkce s různými aktivačními `azure-functions:add` událostmi do projektu pomocí cíle Maven.
