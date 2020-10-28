---
title: Vytvoření funkce Azure pomocí Java a IntelliJ
description: Naučte se vytvářet a publikovat jednoduchou aplikaci bez serveru s protokolem HTTP spuštěnou v Azure pomocí Java a IntelliJ.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: 149d0ae99975628239f8b08f3987947a99e01cbb
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92893229"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Vytvoření první funkce Azure pomocí Java a IntelliJ

V tomto článku najdete:
- Postup vytvoření projektu funkce bez [serveru](https://azure.microsoft.com/overview/serverless-computing/) s nápadem na IntelliJ
- Postup testování a ladění funkce v integrovaném vývojovém prostředí (IDE) ve vašem počítači
- Pokyny pro nasazení projektu funkce pro Azure Functions

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Nastavíte vývojové prostředí

K vývoji funkcí Java a IntelliJ nainstalujte následující software:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
+ [Podporovaná sada Java Development Kit (JDK) v Azure](/azure/developer/java/fundamentals/java-jdk-long-term-support) pro Java 8
+ Nainstalované prostředí [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition nebo Community Edition
+ [Maven 3.5.0 +](https://maven.apache.org/download.cgi)
+ [Základní nástroje pro základní funkce](https://github.com/Azure/azure-functions-core-tools)


## <a name="installation-and-sign-in"></a>Instalace a přihlášení

1. V dialogovém okně Settings / Preferences (Nastavení / Předvolby, Ctrl + Alt + S) v IntelliJ IDEA vyberte **Plugins** (Moduly plug-in). Pak na **Marketplace** vyhledejte **Azure Toolkit for IntelliJ** a klikněte na **Install** (Nainstalovat). Po dokončení instalace modul plug-in aktivujte kliknutím na **Restart** (Restartovat). 

    ![Modul plug-in Azure Toolkit for IntelliJ na Marketplace][marketplace]

2. Pokud se chcete přihlásit ke svému účtu Azure, otevřete boční panel **Azure Explorer** a na horním panelu klikněte na ikonu **Azure Sign In** (Přihlášení k Azure). Tuto možnost najdete také v nabídce IDEA **Tools / Azure / Azure Sign In** (Nástroje / Azure / Přihlášení k Azure).
    ![Příkaz k přihlášení k Azure v IntelliJ][intellij-azure-login]

3. V okně **Azure Sign In** (Přihlášení k Azure) vyberte **Device Login** (Přihlášení zařízení) a pak klikněte na **Sign in** (Přihlásit se) ( [další možnosti přihlášení](/azure/developer/java/toolkit-for-intellij/sign-in-instructions)).

   ![Okno Azure Sign In (Přihlášení k Azure) s vybranou možností Device Login (Přihlášení zařízení)][intellij-azure-popup]

4. V dialogovém okně **Azure Device Login** (Přihlášení zařízení Azure) klikněte na **Copy&Open** (Zkopírovat a otevřít).

   ![Přihlašovací dialogové okno Azure][intellij-azure-copycode]

5. V prohlížeči vložte svůj kód zařízení (který se zkopíroval kliknutím na možnost **Copy&Open** (Zkopírovat a otevřít) v posledním kroku) a pak klikněte na **Next** (Další).

   ![Prohlížeč pro přihlášení zařízení][intellij-azure-link-ms-account]

6. V dialogovém okně **Select Subscriptions** (Výběr předplatných) vyberte předplatná, která chcete použít, a klikněte na **OK** .

   ![Dialogové okno Select Subscriptions (Výběr předplatných)][intellij-azure-login-select-subs]
   
## <a name="create-your-local-project"></a>Vytvořit místní projekt

V této části použijete Azure Toolkit for IntelliJ k vytvoření místního projektu Azure Functions. Později v tomto článku publikujete kód funkce do Azure. 

1. Otevřete uvítací dialog IntelliJ, vyberte *vytvořit nový projekt* . otevře se Průvodce vytvořením nového projektu, vyberte *Azure Functions* .

    ![Vytvořit projekt funkcí](media/functions-create-first-java-intellij/create-functions-project.png)

1. Vyberte *aktivační událost http* a pak klikněte na *Další* a postupujte podle pokynů průvodce a Projděte všechny konfigurace na následujících stránkách. potvrďte umístění projektu a pak klikněte na *Dokončit* . Intellj nápad pak otevře váš nový projekt.

    ![Dokončení projektu vytvoření funkcí](media/functions-create-first-java-intellij/create-functions-project-finish.png)

## <a name="run-the-function-app-locally"></a>Spustit Function App místně

1. Přejděte na pro zobrazení kódu, který se `src/main/java/org/example/functions/HttpTriggerFunction.java` vygeneroval. Vedle řádku *17* si všimnete, že máte zelené tlačítko *Spustit* , kliknete na něj a vyberete *Spustit Azure-Function-zkoušku...* uvidíte, že aplikace Function App je spuštěná místně s několika protokoly.

    ![Projekt místních funkcí spuštění](media/functions-create-first-java-intellij/local-run-functions-project.png)

    ![Výstup funkcí místního běhu](media/functions-create-first-java-intellij/local-run-functions-output.png)

1. Funkci můžete vyzkoušet přístupem k vytištěnému koncovému bodu z prohlížeče, jako je `http://localhost:7071/api/HttpTrigger-Java?name=Azure` .

    ![Výsledek testu místních funkcí spuštění](media/functions-create-first-java-intellij/local-run-functions-test.png)

1. Protokol je také vytištěn ve vaší MYŠLENce. Nyní můžete funkci zastavit kliknutím na tlačítko *zastavit* .

    ![Protokol testu místních funkcí spuštění](media/functions-create-first-java-intellij/local-run-functions-log.png)

## <a name="debug-the-function-app-locally"></a>Místní ladění Function App

1. Teď se pokusíme ladit Function App místně, kliknout na tlačítko *ladění* na panelu nástrojů (pokud ho nevidíte, kliknutím na tlačítko *Zobrazit – > vzhled – > panel* nástrojů povolíte panel nástrojů).

    ![Tlačítko místní funkce ladění](media/functions-create-first-java-intellij/local-debug-functions-button.png)

1. Kliknutím na řádek *20* souboru `src/main/java/org/example/functions/HttpTriggerFunction.java` přidáte zarážku, znovu přistupujete ke koncovému bodu, `http://localhost:7071/api/HttpTrigger-Java?name=Azure` zjistíte, že zarážka bude dosaženo, můžete zkusit další funkce ladění, jako je *Krok* , *sledování* a *vyhodnocení* . Kliknutím na tlačítko Zastavit zastavte ladicí relaci.

    ![Přerušení místních funkcí ladění](media/functions-create-first-java-intellij/local-debug-functions-break.png)

## <a name="deploy-your-function-app-to-azure"></a>Nasazení Function App do Azure

1. Klikněte pravým tlačítkem na projekt v Průzkumníku projektů IntelliJ a vyberte *Azure-> nasadit do Azure Functions*

    ![Nasazení funkcí do Azure](media/functions-create-first-java-intellij/deploy-functions-to-azure.png)

1. Pokud ještě nemáte Function App, klikněte na *žádná funkce k dispozici a kliknutím vytvořte novou* .

    ![Nasazení funkcí do Azure Create App](media/functions-create-first-java-intellij/deploy-functions-create-app.png)

1. Zadejte název aplikace Function App a vyberte vhodné předplatné/platformu/skupinu prostředků/App Service plán. můžete také vytvořit skupinu prostředků/App Service plán. Pak nastavení aplikace zůstane beze změny, klikněte na *OK* a počkejte několik minut, než se nová funkce vytvoří. Po *Vytvoření nového Function App...* indikátor průběhu zmizí.

    ![Průvodce nasazením funkcí do Azure Create App](media/functions-create-first-java-intellij/deploy-functions-create-app-wizard.png)

1. Vyberte aplikaci funkcí, do které chcete nasadit, a automaticky se vybere nová aplikace Function App, kterou jste právě vytvořili. Kliknutím na *Spustit* nasadíte své funkce.

    ![Snímek obrazovky se zobrazí v dialogovém okně nasadit Azure Functions.](media/functions-create-first-java-intellij/deploy-functions-run.png)

    ![Nasazení funkcí do protokolu Azure](media/functions-create-first-java-intellij/deploy-functions-log.png)

## <a name="manage-azure-functions-from-idea"></a>Správa Azure Functions ze NÁPADu

1. Můžete spravovat své funkce pomocí *Průzkumníka Azure* v nápadu, kliknout na *Function App* , zobrazí se zde všechny funkce.

    ![Zobrazit funkce v Průzkumníkovi](media/functions-create-first-java-intellij/explorer-view-functions.png)

1. Kliknutím vyberte jednu z vašich funkcí a klikněte pravým tlačítkem myši na vybrat *Zobrazit vlastnosti* a otevřete stránku podrobností. 

    ![Zobrazit vlastnosti funkcí](media/functions-create-first-java-intellij/explorer-functions-show-properties.png)

1. Klikněte pravým tlačítkem na funkci *HttpTrigger-Java* a vyberte možnost *aktivační funkce* . zobrazí se informace o tom, že se prohlížeč otevře s adresou URL aktivační události.

    ![Snímek obrazovky s jazykem U R L zobrazuje prohlížeč.](media/functions-create-first-java-intellij/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>Přidání dalších funkcí do projektu

1. Klikněte pravým tlačítkem na balíček *org. example. Functions* a vyberte *New-> třídy Azure Functions* . 

    ![Přidání funkcí do položky projektu](media/functions-create-first-java-intellij/add-functions-entry.png)

1. Do pole název třídy zadejte *HttpTest* a vyberte *HttpTrigger* v Průvodci vytvořením třídy funkce, klikněte na tlačítko *OK* . tímto způsobem můžete vytvořit nové funkce podle svých představ.

    ![Snímek obrazovky se zobrazí v dialogovém okně vytvořit třídu funkcí.](media/functions-create-first-java-intellij/add-functions-trigger.png)
    
    ![Přidání funkcí do výstupu projektu](media/functions-create-first-java-intellij/add-functions-output.png)

## <a name="cleaning-up-functions"></a>Čištění funkcí

1. Odstraňování Azure Functions v Průzkumníkovi Azure
      
      ![Snímek obrazovky znázorňující možnost Odstranit vybrané z kontextové nabídky.](media/functions-create-first-java-intellij/delete-function.png)
      

## <a name="next-steps"></a>Další kroky

Vytvořili jste projekt funkcí jazyka Java pomocí funkce aktivované protokolem HTTP, spustíte ji na místním počítači a nasadíte ji do Azure. Nyní svou funkci rozšíříte do...

> [!div class="nextstepaction"]
> [Přidání výstupní vazby fronty Azure Storage](./functions-add-output-binding-storage-queue-java.md)


[marketplace]:./media/functions-create-first-java-intellij/marketplace.png
[intellij-azure-login]: media/functions-create-first-java-intellij/intellij-azure-login.png
[intellij-azure-popup]: media/functions-create-first-java-intellij/intellij-azure-login-popup.png
[intellij-azure-copycode]: media/functions-create-first-java-intellij/intellij-azure-login-copyopen.png
[intellij-azure-link-ms-account]: media/functions-create-first-java-intellij/intellij-azure-login-linkms-account.png
[intellij-azure-login-select-subs]: media/functions-create-first-java-intellij/intellij-azure-login-selectsubs.png