---
title: Vytvoření kanálu CI/CD pro úlohu Stream Analytics pomocí Azure DevOps
description: Tento článek popisuje, jak nastavit kanál průběžné integrace a nasazování (CI/CD) pro úlohu Azure Stream Analytics v Azure DevOps
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: d9b6dfc977aab7d8907b5d3c3851a22f96227d78
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91757754"
---
# <a name="use-azure-devops-to-create-a-cicd-pipeline-for-a-stream-analytics-job"></a>Vytvoření kanálu CI/CD pro úlohu Stream Analytics pomocí Azure DevOps

V tomto článku se dozvíte, jak vytvořit kanály [sestavení](/azure/devops/pipelines/get-started/pipelines-get-started) a [vydání](/azure/devops/pipelines/release/define-multistage-release-process) Azure DevOps pomocí nástrojů CI/CD pro Azure Stream Analytics.

## <a name="commit-your-stream-analytics-project"></a>Potvrzení projektu Stream Analytics

Než začnete, potvrďte kompletní Stream Analytics projekty jako zdrojové soubory do úložiště [Azure DevOps](/azure/devops/user-guide/source-control) . Na toto [ukázkové úložiště](https://dev.azure.com/wenyzou/azure-streamanalytics-cicd-demo) a [Stream Analytics zdrojový kód projektu](https://dev.azure.com/wenyzou/_git/azure-streamanalytics-cicd-demo?path=%2FmyASAProject) můžete odkazovat v Azure Pipelines.

Kroky v tomto článku používají Visual Studio Code projekt Stream Analytics. Pokud používáte projekt sady Visual Studio, postupujte podle kroků v části [Automatizace sestavení, testování a nasazení Azure Stream Analytics úlohy pomocí nástrojů CI/CD](cicd-tools.md).

## <a name="create-a-build-pipeline"></a>Vytvoření kanálu buildu

V této části se dozvíte, jak vytvořit kanál sestavení. Na tuto ukázku můžete odkazovat v Azure DevOps na tento ukázkový [profil automatického sestavení a testování](https://dev.azure.com/wenyzou/_git/azure-streamanalytics-cicd-demo?path=%2FmyASAProject) .

1. Otevřete webový prohlížeč a přejděte do projektu v Azure DevOps.  

1. V části **kanály** v levé navigační nabídce vyberte **sestavení**. Pak vyberte **Nový kanál**.

   :::image type="content" source="media/set-up-cicd-pipeline/new-pipeline.png" alt-text="Vytvořit nový kanál Azure":::

1. Pokud chcete vytvořit kanál bez YAML, vyberte **použít klasický Editor** .

1. Vyberte zdrojový typ, týmový projekt a úložiště. Pak vyberte **pokračovat**.

   :::image type="content" source="media/set-up-cicd-pipeline/select-repo.png" alt-text="Vytvořit nový kanál Azure":::

1. Na stránce **Zvolit šablonu** vyberte **prázdná úloha**.

## <a name="install-npm-package"></a>Nainstalovat balíček npm

1. Na stránce **úlohy** vyberte znaménko plus vedle **úlohy agenta 1**. Do hledání úloh zadejte *npm* a vyberte **npm**.

   :::image type="content" source="media/set-up-cicd-pipeline/search-npm.png" alt-text="Vytvořit nový kanál Azure":::

2. Zadejte **Zobrazovaný název**úkolu. Změňte možnost **příkazu** na *Custom* a v **příkazu a argumentech**zadejte následující příkaz. Ponechte zbývající výchozí možnosti.

   ```bash
   install -g azure-streamanalytics-cicd
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/npm-config.png" alt-text="Vytvořit nový kanál Azure":::

## <a name="add-a-build-task"></a>Přidat úlohu sestavení

1. Na stránce **proměnné** vyberte **+ Přidat** v **proměnných kanálu**. Přidejte následující proměnné. Nastavte následující hodnoty podle vaší předvolby:

   |Název proměnné|Hodnota|
   |-|-|
   |projectRootPath|Názevvašehoprojektu|
   |outputPath|Výstup|
   |deployPath|Nasadit|

2. Na stránce **úlohy** vyberte znaménko plus vedle **úlohy agenta 1**. Vyhledejte **příkazový řádek**.

3. Poskytněte **zobrazovanému názvu úlohy zobrazovaný název** a zadejte následující skript. Upravte skript s názvem úložiště a názvem projektu.

   ```bash
   azure-streamanalytics-cicd build -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(deployPath)
   ```

   Následující obrázek používá jako příklad projekt Stream Analytics Visual Studio Code.

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-build.png" alt-text="Vytvořit nový kanál Azure":::

## <a name="add-a-test-task"></a>Přidat testovací úkol

1. Na stránce **proměnné** vyberte **+ Přidat** v **proměnných kanálu**. Přidejte následující proměnné. Upravte hodnoty pomocí výstupní cesty a názvu úložiště.

   |Název proměnné|Hodnota|
   |-|-|
   |testPath|Test|

   :::image type="content" source="media/set-up-cicd-pipeline/pipeline-variables-test.png" alt-text="Vytvořit nový kanál Azure":::

2. Na stránce **úlohy** vyberte znaménko plus vedle **úlohy agenta 1**. Vyhledejte **příkazový řádek**.

3. Poskytněte **zobrazovanému názvu úlohy zobrazovaný název** a zadejte následující skript. Upravte skript s názvem souboru projektu a cestou k souboru konfigurace testu. 

   Podrobnosti o tom, jak přidat a nakonfigurovat testovací případy, najdete v tématu [pokyny k automatizovaným testům](cicd-tools.md#automated-test) .

   ```bash
   azure-streamanalytics-cicd test -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(testPath) -testConfigPath $(projectRootPath)/test/testConfig.json 
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-test.png" alt-text="Vytvořit nový kanál Azure":::

## <a name="add-a-copy-files-task"></a>Přidat úlohu kopírování souborů

Chcete-li zkopírovat soubor souhrnu testů a Azure Resource Manager soubory šablon do složky artefaktů, je nutné přidat úlohu Kopírovat soubor. 

1. Na stránce **úlohy** vyberte **+** vedle **úlohy agenta 1**. Vyhledejte **soubory pro kopírování**. Pak zadejte následující konfigurace. Přiřazením `**` **obsahu**se zkopírují všechny soubory výsledků testu.

   |Parametr|Vstup|
   |-|-|
   |Zobrazované jméno|Kopírovat soubory do: $ (Build. artifactstagingdirectory)|
   |Zdrojová složka|`$(system.defaultworkingdirectory)/$(outputPath)/`|
   |Obsah| `**` |
   |Cílová složka| `$(build.artifactstagingdirectory)`|

2. Rozbalte **možnosti ovládacího prvku**. Vyberte **i v případě neúspěchu předchozího úkolu, pokud se sestavení nezrušilo** při **spuštění této úlohy**.

   :::image type="content" source="media/set-up-cicd-pipeline/copy-config.png" alt-text="Vytvořit nový kanál Azure":::

## <a name="add-a-publish-build-artifacts-task"></a>Přidat úlohu artefaktů sestavení pro publikování

1. Na stránce **úlohy** vyberte znaménko plus vedle **úlohy agenta 1**. Vyhledejte **artefakty sestavení pro publikování** a vyberte možnost s ikonou černé šipky.

2. Rozbalte **možnosti ovládacího prvku**. Vyberte **i v případě neúspěchu předchozího úkolu, pokud se sestavení nezrušilo** při **spuštění této úlohy**.

   :::image type="content" source="media/set-up-cicd-pipeline/publish-config.png" alt-text="Vytvořit nový kanál Azure":::

## <a name="save-and-run"></a>Uložit a spustit

Až dokončíte přidávání úloh balíčku NPM, příkazového řádku, kopírování souborů a publikování artefaktů sestavení, vyberte **uložit & Queue**. Po zobrazení výzvy zadejte komentář Uložit a vyberte **Uložit a spustit**. Výsledky testování si můžete stáhnout ze stránky **Shrnutí** kanálu.

## <a name="check-the-build-and-test-results"></a>Kontrola výsledků sestavení a testů

Soubor souhrnu testů a soubory šablon Azure Resource Manager lze nalézt v části **Publikováno** .

   :::image type="content" source="media/set-up-cicd-pipeline/check-build-test-result.png" alt-text="Vytvořit nový kanál Azure":::

   :::image type="content" source="media/set-up-cicd-pipeline/check-drop-folder.png" alt-text="Vytvořit nový kanál Azure":::

## <a name="release-with-azure-pipelines"></a>Vydání pomocí Azure Pipelines

V této části se dozvíte, jak vytvořit kanál pro vydávání verzí. Na tento ukázkový [kanál verze](https://dev.azure.com/wenyzou/azure-streamanalytics-cicd-demo/_release?_a=releases&view=mine&definitionId=2&preserve-view=true) můžete odkazovat v Azure DevOps.

Otevřete webový prohlížeč a přejděte do projektu Azure Stream Analytics Visual Studio Code.

1. V části **kanály** v levé navigační nabídce vyberte **verze**. Pak vyberte **Nový kanál**.

2. Vyberte možnost **začít s prázdnou úlohou**.

3. V poli **artefakty** vyberte **+ Přidat artefakt**. V části **zdroj**vyberte kanál sestavení, který jste vytvořili, a vyberte **Přidat**.

   :::image type="content" source="media/set-up-cicd-pipeline/build-artifact.png" alt-text="Vytvořit nový kanál Azure":::

4. Změňte název **fáze 1** pro **nasazení úlohy do testovacího prostředí**.

5. Přidejte novou fázi a pojmenujte **úlohu nasadit do provozního prostředí**.

### <a name="add-deploy-tasks"></a>Přidat úlohy nasazení

1. V rozevíracím seznamu úlohy vyberte **nasadit úlohu do testovacího prostředí**.

2. Vyberte **+** **úlohu agenta** a vyhledejte **nasazení šablony ARM**. Zadejte následující parametry:

   |Parametr|Hodnota|
   |-|-|
   |Zobrazované jméno| *Nasazení myASAProject*|
   |Předplatné Azure| Zvolte vaše předplatné.|
   |Akce| *Vytvoření nebo aktualizace skupiny prostředků*|
   |Skupina prostředků| Vyberte název testovací skupiny prostředků, která bude obsahovat vaši úlohu Stream Analytics.|
   |Umístění|Vyberte umístění testovací skupiny prostředků.|
   |Umístění šablony| Propojený artefakt|
   |Šablona| $ (System. DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-Deploy/drop/myASAProject.JobTemplate.json |
   |Parametry šablony|$ (System. DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-Deploy/drop/myASAProject.JobTemplate.parameters.json |
   |Přepsání parametrů šablony|-<arm_template_parameter> "hodnotu". Parametry můžete definovat pomocí **proměnných**.|
   |Režim nasazení|Přírůstkový|

3. V rozevíracím seznamu úlohy vyberte **nasadit úlohu do provozního prostředí**.

4. Vyberte **+** **úlohu agenta** a vyhledejte *nasazení šablony ARM*. Zadejte následující parametry:

   |Parametr|Hodnota|
   |-|-|
   |Zobrazované jméno| *Nasazení myASAProject*|
   |Předplatné Azure| Zvolte vaše předplatné.|
   |Akce| *Vytvoření nebo aktualizace skupiny prostředků*|
   |Skupina prostředků| Vyberte název skupiny prostředků výroby, která bude obsahovat vaši úlohu Stream Analytics.|
   |Umístění|Vyberte umístění skupiny prostředků výroby.|
   |Umístění šablony| *Propojený artefakt*|
   |Šablona| $ (System. DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-Deploy/drop/myASAProject.JobTemplate.json |
   |Parametry šablony|$ (System. DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-Deploy/drop/myASAProject.JobTemplate.parameters.json |
   |Přepsání parametrů šablony|-<arm_template_parameter> "hodnotu".|
   |Režim nasazení|Přírůstkový|

### <a name="create-a-release"></a>Vytvoření vydané verze

Pokud chcete vytvořit vydanou verzi, vyberte **vytvořit vydání** v pravém horním rohu.

:::image type="content" source="media/set-up-cicd-pipeline/create-release.png" alt-text="Vytvořit nový kanál Azure":::

## <a name="next-steps"></a>Další kroky

* [Průběžná integrace a průběžné nasazování pro Azure Stream Analytics](cicd-overview.md)
* [Automatizace sestavení, testování a nasazení Azure Stream Analytics úlohy pomocí nástrojů CI/CD](cicd-tools.md)