---
title: Nasazení úlohy Azure Stream Analytics pomocí balíčku CI/CD npm
description: Tento článek popisuje, jak pomocí balíčku Azure Stream Analytics CI/CD npm nastavit proces průběžné integrace a nasazování.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: deb6c2439cc84f196b7f42fd9f49d3ebfd057cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76962155"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>Nasazení úlohy Azure Stream Analytics pomocí balíčku CI/CD npm 

Balíček UK/CD nPM služby Azure Stream Analytics můžete použít k nastavení procesu průběžné integrace a nasazení úloh Služby Stream Analytics. Tento článek popisuje, jak používat balíček npm obecně s jakýmkoli systémem CI/CD, stejně jako konkrétní pokyny pro nasazení s Azure Pipelines.

Další informace o nasazení v Powershellu najdete [v tématu nasazení se souborem šablony Správce prostředků a Azure PowerShellem](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Další informace o použití [objektu jako parametru](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)naleznete v šabloně Správce prostředků .

## <a name="build-the-vs-code-project"></a>Sestavení projektu VS Code

Můžete povolit průběžnou integraci a nasazení pro úlohy Azure Stream Analytics pomocí balíčku **asa-streamanalytics-cicd** npm. Balíček npm obsahuje nástroje pro generování šablon Azure Resource Manager [projektů Stream Analytics Visual Studio Code](quick-create-vs-code.md). Lze jej použít ve Windows, macOS a Linuxu bez instalace kódu Visual Studia.

[Balíček si](https://www.npmjs.com/package/azure-streamanalytics-cicd) můžete stáhnout přímo nebo jej `npm install -g azure-streamanalytics-cicd` nainstalovat [globálně](https://docs.npmjs.com/downloading-and-installing-packages-globally) pomocí příkazu. Toto je doporučený přístup, který lze také použít v powershellu nebo azure cli skript úlohy kanálu sestavení v **Azure Pipelines**.

Po instalaci balíčku použijte následující příkaz k výstupu šablon Azure Resource Manager. Argument **scriptPath** je absolutní cesta k souboru **asaql** v projektu. Ujistěte se, že soubory asaproj.json a JobConfig.json jsou ve stejné složce se souborem skriptu. Pokud není zadán **outputPath,** šablony budou umístěny do složky **Deploy** ve složce **přihrádky** projektu.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Příklad (v macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Když se projekt kódu Vizuální studio Stream Analytics úspěšně vytvoří, vygeneruje následující dva soubory šablon Azure Resource Manager u složky bin/[Debug/Retail]:When a Stream Analytics Visual Studio Code project successfulls, it generates the following two Azure Resource Manager template files under the **bin/[Debug/Retail]/Deploy** folder: 

*  Soubor šablony Správce prostředků

       [ProjectName].JobTemplate.json 

*  Soubor parametrů Správce prostředků

       [ProjectName].JobTemplate.parameters.json   

Výchozí parametry v souboru parameters.json jsou z nastavení v projektu kódu sady Visual Studio. Pokud chcete nasadit do jiného prostředí, odpovídajícím způsobem nahraďte parametry.

> [!NOTE]
> Pro všechna pověření jsou výchozí hodnoty nastaveny na hodnotu null. Před nasazením do cloudu je **nutné** nastavit hodnoty.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Nasazení s využitím Azure Pipelines

Tato část podrobně popisuje, jak vytvořit Azure Pipelines [sestavení](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) a [uvolnění](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) kanály pomocí npm.

Otevřete webový prohlížeč a přejděte do projektu Kódu Visual Studia Azure Stream Analytics.

1. V části **Potrubí** v levé navigační nabídce vyberte **Builds**. Pak vyberte **Nový kanál**

   ![Vytvoření nového kanálu Azure](./media/setup-cicd-vs-code/new-pipeline.png)

2. Vyberte **Použít klasický editor** k vytvoření kanálu bez YAML.

3. Vyberte typ zdroje, týmový projekt a úložiště. Potom vyberte **Pokračovat**.

   ![Výběr projektu Azure Stream Analytics](./media/setup-cicd-vs-code/select-repo.png)

4. Na stránce **Vybrat předlohu** vyberte **Prázdná úloha**.

### <a name="add-npm-task"></a>Přidat úkol npm

1. Na stránce **Úkoly** vyberte znaménko plus vedle **úlohy agenta 1**. Zadejte "npm" do hledání úlohy a vyberte **npm**.

   ![Vybrat úkol npm](./media/setup-cicd-vs-code/search-npm.png)

2. Přidejte úkolu **zobrazovaný název**. Změňte možnost **Příkaz** na *vlastní* a zadejte následující příkaz v **příkazu a argumentech**. Ponechte zbývající výchozí možnosti.

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![Zadání konfigurací pro úlohu npm](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>Přidat úkol příkazového řádku

1. Na stránce **Úkoly** vyberte znaménko plus vedle **úlohy agenta 1**. Vyhledejte **příkazový řádek**.

2. Pojmenujte úkol **zobrazovaným názvem** a zadejte následující skript. Upravte skript s názvem úložiště a názvem projektu.

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![Zadání konfigurací úlohy příkazového řádku](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>Úloha přidat soubory kopírování

1. Na stránce **Úkoly** vyberte znaménko plus vedle **úlohy agenta 1**. Vyhledejte **soubory kopírovat**. Poté zadejte následující konfigurace.

   |Parametr|Vstup|
   |-|-|
   |Zobrazované jméno|Kopírovat soubory do: $(build.artifactstagingdirectory)|
   |Zdrojová složka|`$(system.defaultworkingdirectory)`| 
   |Obsah| `**\Deploy\**` |
   |Cílová složka| `$(build.artifactstagingdirectory)`|

   ![Zadání konfigurací pro úlohu kopírování](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>Přidat úlohu Publikovat artefakty sestavení

1. Na stránce **Úkoly** vyberte znaménko plus vedle **úlohy agenta 1**. Vyhledejte **artefakty sestavení publikování** a vyberte možnost s ikonou černé šipky. 

2. Neměňte žádnou výchozí konfiguraci.

### <a name="save-and-run"></a>Uložit a spustit

Po přidání příkazového řádku npm, příkazového řádku, kopírování souborů a publikování úloh artefaktů sestavení vyberte **možnost Uložit & fronty**. Po zobrazení výzvy zadejte uložit komentář a vyberte **Uložit a spustit**.

## <a name="release-with-azure-pipelines"></a>Vydání s Azure Pipelines

Otevřete webový prohlížeč a přejděte do projektu Kódu Visual Studia Azure Stream Analytics.

1. V části **Potrubí** v levé navigační nabídce vyberte **Zprávy**. Pak vyberte **Nový kanál**.

2. Vyberte **možnost Začít s prázdnou úlohou**.

3. V poli **Artefakty** vyberte **+ Přidat artefakt**. V části **Zdroj**vyberte právě vytvořený kanál sestavení a vyberte **Přidat**.

   ![Zadejte artefakt kanálu sestavení](./media/setup-cicd-vs-code/build-artifact.png)

4. Změňte název **fáze 1** na Nasazení **úlohy do testovacího prostředí**.

5. Přidejte novou fázi a pojmenujte ji **Nasadit úlohu do produkčního prostředí**.

### <a name="add-tasks"></a>Přidání úkolů

1. V rozevíracím seznamu úloh vyberte **Nasadit úlohu k testování prostředí**. 

2. Vyberte **+** vedle **úlohy agenta** a vyhledejte *nasazení skupiny prostředků Azure*. Zadejte následující parametry:

   |Nastavení|Hodnota|
   |-|-|
   |Zobrazované jméno| *Nasazení myASAJob*|
   |Předplatné Azure| Zvolte vaše předplatné.|
   |Akce| *Vytvoření nebo aktualizace skupiny prostředků*|
   |Skupina prostředků| Zvolte název pro testovací skupinu prostředků, která bude obsahovat úlohu Stream Analytics.|
   |Umístění|Zvolte umístění testovací skupiny prostředků.|
   |Umístění šablony| *Propojený artefakt*|
   |Šablona| $(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.json |
   |Parametry šablony|($(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.parameters.json|
   |Přepsání parametrů šablony|-Input_IoTHub1_iotHubNamespace $(test_eventhubname)|
   |Režim nasazení|Přírůstkový|

3. V rozevíracím seznamu úloh vyberte **Nasadit úlohu do produkčního prostředí**.

4. Vyberte **+** vedle **úlohy agenta** a vyhledejte *nasazení skupiny prostředků Azure*. Zadejte následující parametry:

   |Nastavení|Hodnota|
   |-|-|
   |Zobrazované jméno| *Nasazení myASAJob*|
   |Předplatné Azure| Zvolte vaše předplatné.|
   |Akce| *Vytvoření nebo aktualizace skupiny prostředků*|
   |Skupina prostředků| Zvolte název pro skupinu produkčních prostředků, která bude obsahovat úlohu Stream Analytics.|
   |Umístění|Zvolte umístění skupiny produkčních prostředků.|
   |Umístění šablony| *Propojený artefakt*|
   |Šablona| $(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.json |
   |Parametry šablony|($(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.parameters.json|
   |Přepsání parametrů šablony|-Input_IoTHub1_iotHubNamespace $(eventhubname)|
   |Režim nasazení|Přírůstkový|

### <a name="create-release"></a>Vytvořit verzi

Chcete-li vytvořit verzi, vyberte **Vytvořit vydání** v pravém horním rohu.

![Vytvoření verze pomocí Azure Pipelines](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>Další zdroje

Chcete-li použít spravovanou identitu pro Azure Data Lake Store Gen1 jako výstupní jímku, musíte před nasazením do Azure poskytnout přístup k instančnímu objektu pomocí PowerShellu pomocí PowerShellu. Přečtěte si další informace o tom, jak [nasadit ADLS Gen1 pomocí spravované identity se šablonou Správce prostředků](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="next-steps"></a>Další kroky

* [Úvodní příručka: Vytvoření cloudové úlohy Azure Stream Analytics v kódu Visual Studia (preview)](quick-create-vs-code.md)
* [Test Stream Analytics dotazy místně s Visual Studio Code (Preview)](visual-studio-code-local-run.md)
* [Prozkoumejte Azure Stream Analytics pomocí kódu Visual Studia (preview)](visual-studio-code-explore-jobs.md)
