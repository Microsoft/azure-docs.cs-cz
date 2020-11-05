---
title: DevOps pro kanál příjmu dat
titleSuffix: Azure Machine Learning
description: Naučte se, jak použít postupy DevOps k vytvoření kanálu pro příjem dat pro přípravu dat pomocí Azure Data Factory a Azure Databricks.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.openlocfilehash: fe2f35708f6a148f8db9ef6fd0a598e19e746fbd
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93358622"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps pro kanál příjmu dat

Ve většině scénářů je řešení pro příjem dat složením skriptů, vyvolání služeb a kanálu orchestrace všech aktivit. V tomto článku se dozvíte, jak uplatnit postupy DevOps na životní cyklus vývoje běžného kanálu pro příjem dat, který připraví data pro školení modelů ve službě Machine Learning. Kanál je sestavený pomocí následujících služeb Azure:

* __Azure Data Factory__ : přečte nezpracovaná data a orchestruje přípravu dat.
* __Azure Databricks__ : spustí Poznámkový blok Pythonu, který transformuje data.
* __Azure Pipelines__ : automatizuje proces průběžné integrace a vývoje.

## <a name="data-ingestion-pipeline-workflow"></a>Pracovní postup kanálu pro přijímání dat

Kanál pro přijímání dat implementuje následující pracovní postup:

1. Nezpracovaná data se čtou do kanálu Azure Data Factory (ADF).
1. Kanál ADF odesílá data do clusteru Azure Databricks, který spustí Poznámkový blok Pythonu pro transformaci dat.
1. Data jsou uložena do kontejneru objektů blob, kde je lze použít Azure Machine Learning k vytvoření výukového modelu.

![pracovní postup kanálu pro přijímání dat](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

## <a name="continuous-integration-and-delivery-overview"></a>Přehled průběžné integrace a doručování

Stejně jako u mnoha softwarových řešení existuje tým (například technici dat), na kterém pracují. Spolupracují a sdílejí stejné prostředky Azure, jako jsou účty Azure Data Factory, Azure Databricks a Azure Storage. Kolekce těchto prostředků je vývojové prostředí. Technici pro data přispívají ke stejnému základu zdrojového kódu.

Průběžná integrace a systém doručování automatizuje proces vytváření, testování a doručování (nasazení) řešení. Proces průběžné integrace (CI) provádí následující úlohy:

* Sestaví kód
* Zkontroluje je s testy kvality kódu.
* Spustí testy jednotek.
* Vytvoří artefakty, jako je například testovaný kód a šablony Azure Resource Manager.

Proces průběžného doručování (CD) nasadí artefakty do prostředí pro příjem dat.

![Diagram přijímání dat cicd](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Tento článek ukazuje, jak automatizovat procesy CI a CD pomocí [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/).

## <a name="source-control-management"></a>Správa správy zdrojového kódu

Správa zdrojového kódu je nutná ke sledování změn a umožnění spolupráce mezi členy týmu.
Kód by měl být například uložený v úložišti Azure DevOps, GitHub nebo GitLab. Pracovní postup spolupráce je založený na modelu větvení. Například [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html).

### <a name="python-notebook-source-code"></a>Zdrojový kód poznámkového bloku Python

Data technici pracují se zdrojovým kódem poznámkového bloku Pythonu místně v integrovaném vývojovém prostředí (například [Visual Studio Code](https://code.visualstudio.com)) nebo přímo v pracovním prostoru datacihly. Po dokončení změn kódu budou tyto zásady sloučeny do úložiště po vytvoření větve.

> [!TIP] 
> `.py`Místo v Jupyter notebook formátu doporučujeme kód Uložit do souborů `.ipynb` . Vylepšuje čitelnost kódu a umožňuje automatické kontroly kvality kódu v procesu CI.

### <a name="azure-data-factory-source-code"></a>Zdrojový kód Azure Data Factory

Zdrojový kód Azure Data Factorych kanálů je kolekce souborů JSON vygenerovaných pracovním prostorem Azure Data Factory. Datové technici normálně pracují s vizuálním návrhářem v pracovním prostoru Azure Data Factory, nikoli přímo se soubory zdrojového kódu. 

Pokud chcete nakonfigurovat pracovní prostor tak, aby používal úložiště správy zdrojového kódu, přečtěte si téma [Autor s Azure Repos Integration Git](../data-factory/source-control.md#author-with-azure-repos-git-integration).   

## <a name="continuous-integration-ci"></a>Kontinuální integrace (CI)

Hlavním cílem procesu kontinuální integrace je shromáždění společného týmu práce ze zdrojového kódu a jeho Příprava na nasazení do prostředí pro příjem dat. Stejně jako u správy zdrojového kódu se tento proces liší od poznámkových bloků Pythonu a kanálů Azure Data Factory. 

### <a name="python-notebook-ci"></a>Položky konfigurace poznámkového bloku Python

Proces CI poznámkových bloků Pythonu získá kód z větve pro spolupráci (například * **Master** _ nebo _*_vývoj_*_ ) a provede následující činnosti: _ Code linting
* Testování jednotek
* Uložení kódu jako artefaktu

Následující fragment kódu ukazuje implementaci těchto kroků v kanálu Azure DevOps * **YAML** _:

```yaml
steps:
- script: |
   flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
  workingDirectory: '$(Build.SourcesDirectory)'
  displayName: 'Run flake8 (code style analysis)'  
  
- script: |
   python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
  displayName: 'Run unit tests'

- task: PublishTestResults@2
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: '$(Build.BinariesDirectory)/_-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks
```

Kanál používá [flake8](https://pypi.org/project/flake8/) k tomu, aby linting kód Pythonu. Spouští testy jednotek definované ve zdrojovém kódu a publikuje linting a výsledky testů tak, aby byly k dispozici na obrazovce spuštění kanálu Azure:

![testy jednotek linting](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Pokud je testování linting a jednotek úspěšné, kanál zkopíruje zdrojový kód do úložiště artefaktů, které budou použity v následujících krocích nasazení.

### <a name="azure-data-factory-ci"></a>Azure Data Factory CI

Proces CI pro kanál Azure Data Factory je při příjmu dat kritickým bodem. Neexistuje žádná průběžná integrace. Nasaditelné artefakty pro Azure Data Factory je kolekce šablon Azure Resource Manager. Jediným způsobem, jak tyto šablony vyvolat, je kliknout na tlačítko * **publikovat** _ v pracovním prostoru Azure Data Factory.

1. Data technici sloučí zdrojový kód od svých větví funkcí do větve pro spolupráci, například do _*_hlavního_*_ nebo _*_vývojového_*_. 
1. Někdo s udělenými oprávněními klikne na tlačítko _*_publikovat_*_ a vygeneruje šablony Azure Resource Manager ze zdrojového kódu ve větvi pro spolupráci. 
1. Pracovní prostor ověří kanály (považujte je za linting a testování částí), vygeneruje Azure Resource Manager šablon (považuje se za sestavení) a uloží vygenerované šablony do technické větve _*_adf_publish_*_ ve stejném úložišti kódu (můžete si ho představit jako artefakty publikování). Tato větev je automaticky vytvořena Azure Data Factory pracovním prostorem. 

Další informace o tomto procesu najdete v tématu [průběžná integrace a doručování v Azure Data Factory](../data-factory/continuous-integration-deployment.md).

Je důležité zajistit, aby vygenerované šablony Azure Resource Manager nezávislá prostředí. To znamená, že všechny hodnoty, které se mohou v různých prostředích lišit, jsou parametry. Azure Data Factory je dostatečně inteligentní, aby bylo možné vystavit většinu takových hodnot jako parametry. Například v následující šabloně se vlastnosti připojení k pracovnímu prostoru Azure Machine Learning zveřejňují jako parametry:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        "AzureMLService_servicePrincipalKey": {
            "value": ""
        },
        "AzureMLService_properties_typeProperties_subscriptionId": {
            "value": "0fe1c235-5cfa-4152-17d7-5dff45a8d4ba"
        },
        "AzureMLService_properties_typeProperties_resourceGroupName": {
            "value": "devops-ds-rg"
        },
        "AzureMLService_properties_typeProperties_servicePrincipalId": {
            "value": "6e35e589-3b22-4edb-89d0-2ab7fc08d488"
        },
        "AzureMLService_properties_typeProperties_tenant": {
            "value": "72f988bf-86f1-41af-912b-2d7cd611db47"
        }
    }
}
```

Můžete ale chtít zveřejnit vlastní vlastnosti, které ve výchozím nastavení nezpracovává pracovní prostor Azure Data Factory. Ve scénáři tohoto článku Azure Data Factory kanál Vyvolá Poznámkový blok Pythonu, který zpracovává data. Poznámkový blok přijme parametr s názvem vstupního datového souboru.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

Tento název se liší od prostředí pro _*_vývoj_*_ , řešení _*_QA_*_ , _*_UAT_*_ a _*_výr_*_ . Ve složitém kanálu s více aktivitami může existovat několik vlastních vlastností. Je vhodné shromáždit všechny tyto hodnoty na jednom místě a definovat je jako _*_proměnné_*_ kanálu:

![Snímek obrazovky s názvem PrepareData a M L Execute Pipeline s názvem M L Execute Pipeline v horní části s možností přidat nové proměnné, každý s názvem, typem a výchozí hodnotou.](media/how-to-cicd-data-ingestion/adf-variables.png)

Aktivity kanálu mohou odkazovat na proměnné kanálu při jejich současném použití:

![Snímek obrazovky s názvem PrepareData a M L Execute Pipeline s názvem M L Execute Pipeline v horní části s níže vybranou kartou nastavení.](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

_*_Pracovní prostor_*_ Azure Data Factory ve výchozím nastavení nevystavuje proměnné kanálu jako parametry Azure Resource Manager šablon. Pracovní prostor používá [výchozí šablonu Parametrizace](../data-factory/continuous-integration-deployment.md#default-parameterization-template) , která určuje, jaké vlastnosti kanálu by se měly zveřejnit jako parametry šablony Azure Resource Manager. Chcete-li přidat proměnné kanálu do seznamu, aktualizujte `"Microsoft.DataFactory/factories/pipelines"` část [výchozí šablony Parametrizace](../data-factory/continuous-integration-deployment.md#default-parameterization-template) následujícím fragmentem kódu a uložte výsledný soubor JSON do kořenového adresáře zdrojové složky:

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "_": {
                    "defaultValue": "="
                }
            }
        }
    }
```

Vynutí se tak, aby pracovní prostor Azure Data Factory přidal proměnné do seznamu parametrů při kliknutí na tlačítko * **publikovat** _:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        ...
        "data-ingestion-pipeline_properties_variables_data_file_name_defaultValue": {
            "value": "driver_prediction_train.csv"
        }        
    }
}
```

Hodnoty v souboru JSON jsou výchozí hodnoty konfigurované v definici kanálu. Očekává se, že budou přepsány hodnotami cílového prostředí při nasazení šablony Azure Resource Manager.

## <a name="continuous-delivery-cd"></a>Průběžné doručování (CD)

Proces průběžného doručování přebírá artefakty a nasadí je do prvního cílového prostředí. Zajišťuje, že řešení funguje spuštěním testů. V případě úspěchu pokračuje k dalšímu prostředí. 

Kanál Azure pro CD se skládá z několika fází, které představují prostředí. Každá fáze obsahuje [nasazení](/azure/devops/pipelines/process/deployment-jobs?view=azure-devops&preserve-view=true) a [úlohy](/azure/devops/pipelines/process/phases?tabs=yaml&view=azure-devops&preserve-view=true) , které provádějí následující kroky:

_ Nasazení poznámkového bloku Pythonu do pracovního prostoru Azure Databricks
* Nasazení kanálu Azure Data Factory 
* Spuštění kanálu
* Zkontroluje výsledek příjmu dat.

Fáze zřetězení se dají nakonfigurovat pomocí [schválení](/azure/devops/pipelines/process/approvals?tabs=check-pass&view=azure-devops&preserve-view=true) a [vratek](/azure/devops/pipelines/release/approvals/gates?view=azure-devops&preserve-view=true) , které poskytují další kontrolu nad tím, jak se proces nasazení vyvíjí prostřednictvím řetězce prostředí.

### <a name="deploy-a-python-notebook"></a>Nasazení poznámkového bloku Pythonu

Následující fragment kódu definuje [nasazení](/azure/devops/pipelines/process/deployment-jobs?view=azure-devops&preserve-view=true) kanálu Azure, které kopíruje Poznámkový blok Python do clusteru datacihly:

```yaml
- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'       
```            

Artefakty vytvořené službou CI jsou automaticky zkopírovány do agenta nasazení a jsou k dispozici ve `$(Pipeline.Workspace)` složce. V takovém případě úloha nasazení odkazuje na artefakt, který `di-notebooks` obsahuje Poznámkový blok Python. Toto [nasazení](/azure/devops/pipelines/process/deployment-jobs?view=azure-devops&preserve-view=true) používá [rozšíření datacihly Azure DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) ke zkopírování souborů poznámkových bloků do pracovního prostoru datacihly.

`Deploy_to_QA`Fáze obsahuje odkaz na `devops-ds-qa-vg` skupinu proměnných definovanou v projektu Azure DevOps. Kroky v této fázi odkazují na proměnné z této skupiny proměnných (například `$(DATABRICKS_URL)` a `$(DATABRICKS_TOKEN)` ). Nápad je, že další fáze (například `Deploy_to_UAT` ) bude pracovat se stejnými názvy proměnných, které jsou definovány ve vlastní skupině proměnných UAT.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Nasazení kanálu Azure Data Factory

Nasaditelné artefakty pro Azure Data Factory jsou šablonou Azure Resource Manager. Nasadí se pomocí úlohy * **nasazení skupiny prostředků Azure** _, jak je znázorněno v následujícím fragmentu kódu:

```yaml
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
```
Hodnota parametru data filename přichází z `$(DATA_FILE_NAME)` proměnné definované ve skupině proměnných fáze kontroly kvality. Podobně je možné přepsat všechny parametry definované v _*_ARMTemplateForFactory.jsna_*_ . Pokud nejsou, použijí se výchozí hodnoty.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>Spusťte kanál a ověřte výsledek příjmu dat.

Dalším krokem je zajistit, aby nasazené řešení fungovalo. Následující definice úlohy spustí kanál Azure Data Factory se [skriptem PowerShellu](https://github.com/microsoft/DataOps/tree/master/adf/utils) a spustí Poznámkový blok Pythonu na clusteru Azure Databricks. Poznámkový blok zkontroluje správnou příjem dat a ověří soubor výsledných dat s `$(bin_FILE_NAME)` názvem.

```yaml
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'
```

Poslední úkol v úloze kontroluje výsledek spuštění poznámkového bloku. Pokud se vrátí chyba, nastaví se stav spuštění kanálu jako neúspěšné.

## <a name="putting-pieces-together"></a>Vložení částí dohromady

Úplný kanál Azure CI/CD se skládá z následujících fází: _ CI
* Nasazení na QA
    * Nasazení na datacihly + nasazení na ADF
    * Test integrace

Obsahuje několik fází **nasazení** _, které odpovídají počtu cílových prostředí, které máte. Každá fáze _*_nasazení_*_ obsahuje dvě [nasazení](/azure/devops/pipelines/process/deployment-jobs?view=azure-devops&preserve-view=true) , která běží paralelně, a [úlohu](/azure/devops/pipelines/process/phases?tabs=yaml&view=azure-devops&preserve-view=true) , která se po nasazení spustí za účelem testování řešení v prostředí.

Ukázková implementace kanálu je sestavena v následujícím fragmentu _*_YAML_*_ :

```yaml
variables:
- group: devops-ds-vg

stages:
- stage: 'CI'
  displayName: 'CI'
  jobs:
  - job: "CI_Job"
    displayName: "CI Job"
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'
    - script: pip install --upgrade flake8 flake8_formatter_junit_xml
      displayName: 'Install flake8'
    - checkout: self
    - script: |
       flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
    workingDirectory: '$(Build.SourcesDirectory)'
    displayName: 'Run flake8 (code style analysis)'  
    - script: |
       python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
    displayName: 'Run unit tests'
    - task: PublishTestResults@2
    condition: succeededOrFailed()
    inputs:
        testResultsFiles: '$(Build.BinariesDirectory)/_-testresults.xml'
        testRunTitle: 'Linting & Unit tests'
        failTaskOnFailedTests: true
    displayName: 'Publish linting and unit test results'    

    # The CI stage produces two artifacts (notebooks and ADF pipelines).
    # The pipelines Azure Resource Manager templates are stored in a technical branch "adf_publish"
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/code/dataingestion
      artifact: di-notebooks
    - checkout: git://${{variables['System.TeamProject']}}@adf_publish    
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/devops-ds-adf
      artifact: adf-pipelines

- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'             
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'                

```

## <a name="next-steps"></a>Další kroky

* [Správa zdrojového kódu ve službě Azure Data Factory](../data-factory/source-control.md)
* [Průběžná integrace a doručování v Azure Data Factory](../data-factory/continuous-integration-deployment.md)
* [DevOps pro Azure Databricks](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)