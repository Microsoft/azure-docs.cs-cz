---
title: Nasazení úlohy Azure Stream Analytics pomocí balíčku CI/CD npm
description: Tento článek popisuje, jak pomocí Azure Stream Analytics balíčku CI/CD npm nastavit proces průběžné integrace a nasazování.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: deb6c2439cc84f196b7f42fd9f49d3ebfd057cbb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76962155"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>Nasazení úlohy Azure Stream Analytics pomocí balíčku CI/CD npm 

Pomocí balíčku Azure Stream Analytics CI/CD npm můžete nastavit průběžnou integraci a proces nasazení pro úlohy Stream Analytics. Tento článek popisuje, jak obecně používat balíček npm s jakýmkoli systémem CI/CD a také s konkrétními pokyny pro nasazení s Azure Pipelines.

Další informace o nasazení pomocí PowerShellu najdete v tématu [nasazení pomocí souboru šablony Správce prostředků a Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Můžete si taky přečíst další informace o tom [, jak objekt použít jako parametr v šabloně správce prostředků](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

## <a name="build-the-vs-code-project"></a>Sestavení projektu VS Code

Můžete povolit průběžnou integraci a nasazení pro Azure Stream Analytics úlohy pomocí balíčku npm **ASA-streamanalytics-cicd** . Balíček npm poskytuje nástroje pro generování Azure Resource Manager šablon [Stream Analytics projektů Visual Studio Code](quick-create-vs-code.md). Dá se použít na Windows, macOS a Linux bez instalace Visual Studio Code.

Balíček si můžete [Stáhnout](https://www.npmjs.com/package/azure-streamanalytics-cicd) přímo nebo ho nainstalovat [globálně](https://docs.npmjs.com/downloading-and-installing-packages-globally) pomocí `npm install -g azure-streamanalytics-cicd` příkazu. Toto je doporučený postup, který se dá použít taky v úloze skriptu PowerShellu nebo Azure CLI kanálu sestavení v **Azure Pipelines**.

Po instalaci balíčku použijte následující příkaz pro výstup šablon Azure Resource Manager. Argument **ScriptPath** je absolutní cesta k souboru **asaql** v projektu. Ujistěte se, že soubory asaproj. JSON a JobConfig. JSON jsou ve stejné složce se souborem skriptu. Pokud není zadán parametr **outputPath** , šablony budou umístěny do složky **Deploy** ve složce **bin** projektu.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Příklad (na macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Po úspěšném sestavení Stream Analytics Visual Studio Code vygeneruje následující dva Azure Resource Manager soubory šablon ve složce **bin/[Debug/Retail]/Deploy** : 

*  Soubor šablony Správce prostředků

       [ProjectName].JobTemplate.json 

*  Soubor parametrů Správce prostředků

       [ProjectName].JobTemplate.parameters.json   

Výchozí parametry v souboru Parameters. JSON jsou z nastavení v projektu Visual Studio Code. Pokud chcete nasazení nasadit do jiného prostředí, nahraďte příslušné parametry odpovídajícím způsobem.

> [!NOTE]
> U všech přihlašovacích údajů jsou výchozí hodnoty nastavené na hodnotu null. Před nasazením do cloudu je **nutné** nastavit hodnoty.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Nasazení s využitím Azure Pipelines

Tato část popisuje, jak vytvořit Azure Pipelines kanály [sestavení](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) a [vydání](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) pomocí npm.

Otevřete webový prohlížeč a přejděte do projektu Azure Stream Analytics Visual Studio Code.

1. V části **kanály** v levé navigační nabídce vyberte **sestavení**. Pak vyberte **Nový kanál** .

   ![Vytvořit nový kanál Azure](./media/setup-cicd-vs-code/new-pipeline.png)

2. Pokud chcete vytvořit kanál bez YAML, vyberte **použít klasický Editor** .

3. Vyberte zdrojový typ, týmový projekt a úložiště. Potom vyberte **Pokračovat**.

   ![Vybrat Azure Stream Analytics projekt](./media/setup-cicd-vs-code/select-repo.png)

4. Na stránce **Zvolit šablonu** vyberte **prázdná úloha**.

### <a name="add-npm-task"></a>Přidat úlohu npm

1. Na stránce **úlohy** vyberte znaménko plus vedle **úlohy agenta 1**. Do hledání úloh zadejte "npm" a vyberte **npm**.

   ![Vybrat úlohu npm](./media/setup-cicd-vs-code/search-npm.png)

2. Zadejte **Zobrazovaný název**úkolu. Změňte možnost **příkazu** na *Custom* a v **příkazu a argumentech**zadejte následující příkaz. Ponechte zbývající výchozí možnosti.

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![Zadání konfigurací pro úlohu npm](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>Přidat úlohu příkazového řádku

1. Na stránce **úlohy** vyberte znaménko plus vedle **úlohy agenta 1**. Vyhledejte **příkazový řádek**.

2. Poskytněte **zobrazovanému názvu úlohy zobrazovaný název** a zadejte následující skript. Upravte skript s názvem úložiště a názvem projektu.

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![Zadejte konfigurace pro úlohu příkazového řádku](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>Úloha přidání souborů ke kopírování

1. Na stránce **úlohy** vyberte znaménko plus vedle **úlohy agenta 1**. Vyhledejte **soubory pro kopírování**. Pak zadejte následující konfigurace.

   |Parametr|Vstup|
   |-|-|
   |Zobrazované jméno|Kopírovat soubory do: $ (Build. artifactstagingdirectory)|
   |Zdrojová složka|`$(system.defaultworkingdirectory)`| 
   |Obsah| `**\Deploy\**` |
   |Cílová složka| `$(build.artifactstagingdirectory)`|

   ![Zadejte konfigurace pro úlohu kopírování](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>Přidat úlohu artefaktů sestavení pro publikování

1. Na stránce **úlohy** vyberte znaménko plus vedle **úlohy agenta 1**. Vyhledejte **artefakty sestavení pro publikování** a vyberte možnost s ikonou černé šipky. 

2. Neměňte žádnou z výchozích konfigurací.

### <a name="save-and-run"></a>Uložit a spustit

Až dokončíte přidávání úloh NPM, příkazového řádku, kopírování souborů a publikování artefaktů sestavení, vyberte **uložit & Queue**. Po zobrazení výzvy zadejte komentář Uložit a vyberte **Uložit a spustit**.

## <a name="release-with-azure-pipelines"></a>Vydání pomocí Azure Pipelines

Otevřete webový prohlížeč a přejděte do projektu Azure Stream Analytics Visual Studio Code.

1. V části **kanály** v levé navigační nabídce vyberte **verze**. Pak vyberte **Nový kanál**.

2. Vyberte možnost **začít s prázdnou úlohou**.

3. V poli **artefakty** vyberte **+ Přidat artefakt**. V části **zdroj**vyberte kanál sestavení, který jste právě vytvořili, a vyberte **Přidat**.

   ![Zadat artefakt kanálu sestavení](./media/setup-cicd-vs-code/build-artifact.png)

4. Změňte název **fáze 1** pro **nasazení úlohy do testovacího prostředí**.

5. Přidejte novou fázi a pojmenujte **úlohu nasadit do provozního prostředí**.

### <a name="add-tasks"></a>Přidat úlohy

1. V rozevíracím seznamu úlohy vyberte **nasadit úlohu do testovacího prostředí**. 

2. Vyberte **+** **úlohu agenta** u služby a vyhledejte *nasazení skupiny prostředků Azure*. Zadejte následující parametry:

   |Nastavení|Hodnota|
   |-|-|
   |Zobrazované jméno| *Nasazení myASAJob*|
   |Předplatné Azure| Zvolte vaše předplatné.|
   |Akce| *Vytvoření nebo aktualizace skupiny prostředků*|
   |Skupina prostředků| Vyberte název testovací skupiny prostředků, která bude obsahovat vaši úlohu Stream Analytics.|
   |Umístění|Vyberte umístění testovací skupiny prostředků.|
   |Umístění šablony| *Propojený artefakt*|
   |Šablona| $ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.json |
   |Parametry šablony|($ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.parameters.json|
   |Přepsání parametrů šablony|-Input_IoTHub1_iotHubNamespace $ (test_eventhubname)|
   |Režim nasazení|Přírůstkový|

3. V rozevíracím seznamu úlohy vyberte **nasadit úlohu do provozního prostředí**.

4. Vyberte **+** **úlohu agenta** u služby a vyhledejte *nasazení skupiny prostředků Azure*. Zadejte následující parametry:

   |Nastavení|Hodnota|
   |-|-|
   |Zobrazované jméno| *Nasazení myASAJob*|
   |Předplatné Azure| Zvolte vaše předplatné.|
   |Akce| *Vytvoření nebo aktualizace skupiny prostředků*|
   |Skupina prostředků| Vyberte název skupiny prostředků výroby, která bude obsahovat vaši úlohu Stream Analytics.|
   |Umístění|Vyberte umístění skupiny prostředků výroby.|
   |Umístění šablony| *Propojený artefakt*|
   |Šablona| $ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.json |
   |Parametry šablony|($ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.parameters.json|
   |Přepsání parametrů šablony|-Input_IoTHub1_iotHubNamespace $ (eventhubname)|
   |Režim nasazení|Přírůstkový|

### <a name="create-release"></a>Vytvořit vydanou verzi

Pokud chcete vytvořit vydanou verzi, vyberte **vytvořit vydání** v pravém horním rohu.

![Vytvoření vydání pomocí Azure Pipelines](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>Další zdroje

Pokud chcete použít spravovanou identitu pro Azure Data Lake Store Gen1 jako výstupní jímku, musíte před nasazením do Azure poskytnout přístup k instančnímu objektu pomocí PowerShellu. Přečtěte si další informace o [nasazení adls Gen1 se správou identity pomocí šablony Správce prostředků](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="next-steps"></a>Další kroky

* [Rychlý Start: vytvoření cloudové úlohy Azure Stream Analytics v Visual Studio Code (Preview)](quick-create-vs-code.md)
* [Test Stream Analytics dotazy místně pomocí Visual Studio Code (Preview)](visual-studio-code-local-run.md)
* [Prozkoumat Azure Stream Analytics s využitím Visual Studio Code (Preview)](visual-studio-code-explore-jobs.md)
