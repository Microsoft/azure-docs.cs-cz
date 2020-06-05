---
title: DevOps kanálu pro přijímání dat
titleSuffix: Azure Machine Learning
description: Naučte se uplatňovat postupy DevOps na implementaci kanálu pro příjem dat, která se používá k přípravě dat pro školení modelů.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/30/2020
ms.openlocfilehash: c5ffd45eadfd2d1b0e455ea3de91bf91c11c2517
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434602"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps kanálu pro přijímání dat

Ve většině scénářů je řešení pro příjem dat složením skriptů, vyvolání služeb a kanálu orchestrace všech aktivit. V tomto článku se dozvíte, jak uplatnit postupy DevOps na životní cyklus vývoje běžného kanálu pro příjem dat. Kanál připraví data pro školení Machine Learningho modelu.

## <a name="the-solution"></a>Řešení

Vezměte v úvahu následující pracovní postup příjmu dat:

![přijímání dat – kanál](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

V tomto přístupu se školicí data ukládají do úložiště objektů BLOB v Azure. Kanál Azure Data Factory načte data ze vstupního kontejneru objektů blob, transformuje ho a uloží data do výstupního kontejneru objektů BLOB. Tento kontejner slouží jako [úložiště dat](concept-data.md) pro službu Azure Machine Learning. Když se dopravují data, kanál Data Factory vyvolá školicí Machine Learning kanál pro výuku modelu. V tomto konkrétním příkladu se transformace dat provádí pomocí poznámkového bloku Pythonu, který běží v clusteru Azure Databricks. 

## <a name="what-we-are-building"></a>Co sestavíme

Stejně jako u jakéhokoli softwarového řešení existuje tým (například technici dat), na kterém pracují. 

![cicd – přijímání dat](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Spolupracují a sdílejí stejné prostředky Azure, jako jsou Azure Data Factory, Azure Databricks, Azure Storage účet a jako takové. Kolekce těchto prostředků je vývojové prostředí. Technici pro data přispívají ke stejnému základu zdrojového kódu. Proces průběžné integrace sestaví kód, kontroluje ho pomocí testů kvality kódu, testování částí a generuje artefakty, jako je například testovaný kód a Azure Resource Manager šablony. Proces průběžného doručování nasadí artefakty do prostředí pro příjem dat. Tento článek ukazuje, jak automatizovat procesy CI a CD pomocí [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/).

## <a name="source-control-management"></a>Správa správy zdrojového kódu

Členové týmu pracují trochu různými způsoby, jak spolupracovat na zdrojovém kódu poznámkového bloku Python a ve zdrojovém kódu Azure Data Factory. V obou případech je však kód uložen v úložišti správy zdrojových kódů (například Azure DevOps, GitHub, GitLab) a spolupráce je obvykle založena na některém modelu větvení (například [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html)).

### <a name="python-notebook-source-code"></a>Zdrojový kód poznámkového bloku Python

Data technici pracují se zdrojovým kódem poznámkového bloku Pythonu místně v integrovaném vývojovém prostředí (například [Visual Studio Code](https://code.visualstudio.com)) nebo přímo v pracovním prostoru datacihly. Druhá z nich umožňuje ladit kód ve vývojovém prostředí. V každém případě bude kód sloučen do úložiště za použití zásad větvení. `.py`Místo ve `.ipynb` formátu poznámkového bloku Jupyter se důrazně doporučuje ukládat kód do souborů. Vylepšuje čitelnost kódu a umožňuje automatické kontroly kvality kódu v procesu CI.

### <a name="azure-data-factory-source-code"></a>Zdrojový kód Azure Data Factory

Zdrojový kód Azure Data Factorych kanálů je kolekcí souborů JSON generovaných pracovním prostorem. Datové technici normálně pracují s vizuálním návrhářem v pracovním prostoru Azure Data Factory, nikoli přímo se soubory zdrojového kódu. Nakonfigurujte pracovní prostor pomocí úložiště správy zdrojového kódu, jak je popsáno v [dokumentaci Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration). V rámci této konfigurace můžou technici dat spolupracovat na zdrojovém kódu, který následuje po upřednostňovaném pracovním postupu větvení.    

## <a name="continuous-integration-ci"></a>Průběžná integrace (CI)

Hlavním cílem procesu kontinuální integrace je shromáždění společného týmu práce ze zdrojového kódu a jeho Příprava na nasazení do prostředí pro příjem dat. Stejně jako u správy zdrojového kódu se tento proces liší od poznámkových bloků Pythonu a kanálů Azure Data Factory. 

### <a name="python-notebook-ci"></a>Položky konfigurace poznámkového bloku Python

Proces CI poznámkových bloků Pythonu získá kód z větve pro spolupráci (například ***Hlavní*** nebo ***vývoj***) a provede následující činnosti:
* Linting kódu
* Testování jednotek
* Uložení kódu jako artefaktu

Následující fragment kódu ukazuje implementaci těchto kroků v kanálu Azure DevOps ***YAML*** :

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
    testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks

```

Kanál používá ***flake8*** k tomu, aby linting kód Pythonu. Spouští testy jednotek definované ve zdrojovém kódu a publikuje linting a výsledky testů tak, aby byly k dispozici na obrazovce spuštění kanálu Azure:

![linting-Unit-Tests](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Pokud je testování linting a jednotek úspěšné, kanál zkopíruje zdrojový kód do úložiště artefaktů, které budou použity v následujících krocích nasazení.

### <a name="azure-data-factory-ci"></a>Azure Data Factory CI

Proces CI pro kanál Azure Data Factory je kritické místo v celém scénáři CI/CD pro kanál pro přijímání dat. Neexistuje žádná ***průběžná*** integrace. Nasaditelné artefakty pro Azure Data Factory je kolekce šablon Azure Resource Manager. Jediným způsobem, jak tyto šablony vyvolat, je kliknout na tlačítko ***publikovat*** v pracovním prostoru Azure Data Factory. Tady není žádná automatizace.
Data technici sloučí zdrojový kód od svých větví funkcí do větve pro spolupráci, například do ***hlavního*** nebo ***vývojového***. Potom někdo s udělenými oprávněními klikne na tlačítko ***publikovat*** a vygeneruje Azure Resource Manager šablony ze zdrojového kódu ve větvi pro spolupráci. Po kliknutí na tlačítko pracovní prostor ověří kanály (považujte je za linting a testování částí), vygeneruje Azure Resource Manager šablon (považuje se za sestavení) a uloží vygenerované šablony do technické větve ***adf_publish*** ve stejném úložišti kódu (můžete si ho představit jako artefakty publikování). Tato větev je automaticky vytvořena Azure Data Factory pracovním prostorem. Tento postup je popsaný v podrobnostech v [dokumentaci k Azure Data Factory](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment).

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

Tento název se liší od prostředí pro ***vývoj***, řešení ***QA***, ***UAT***a ***výr*** . Ve složitém kanálu s více aktivitami může existovat několik vlastních vlastností. Je vhodné shromáždit všechny tyto hodnoty na jednom místě a definovat je jako ***proměnné***kanálu:

![ADF – proměnné](media/how-to-cicd-data-ingestion/adf-variables.png)

Aktivity kanálu mohou odkazovat na proměnné kanálu při jejich současném použití:

![ADF – Poznámkový blok – parametry](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

***Pracovní prostor*** Azure Data Factory ve výchozím nastavení nevystavuje proměnné kanálu jako parametry Azure Resource Manager šablon. Pracovní prostor používá [výchozí šablonu Parametrizace](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) , která určuje, jaké vlastnosti kanálu by se měly zveřejnit jako parametry šablony Azure Resource Manager. Chcete-li přidat proměnné kanálu do seznamu, aktualizujte část Microsoft. DataFactory/Factory/kanály/kanály pro [výchozí šablonu Parametrizace](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) následujícím fragmentem kódu a uložte výsledný soubor JSON do kořenového adresáře zdrojové složky:

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "*": {
                    "defaultValue": "="
                }
            }
        }
    }
```

Tím vynutíte, aby pracovní prostor Azure Data Factory při kliknutí na tlačítko ***publikovat*** přidal proměnné do seznamu parametrů:

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

Proces průběžného doručování přebírá artefakty a nasadí je do prvního cílového prostředí. Zajišťuje, že řešení funguje spuštěním testů. V případě úspěchu pokračuje k dalšímu prostředí. Kanál Azure pro CD se skládá z několika fází, které představují prostředí. Každá fáze obsahuje [nasazení](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) a [úlohy](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) , které provádějí následující kroky:
* Nasazení poznámkového bloku Pythonu pro Azure Databricks pracovní prostor
* Nasazení kanálu Azure Data Factory 
* Spuštění kanálu
* Zkontroluje výsledek příjmu dat.

Fáze zřetězení se dají nakonfigurovat pomocí [schválení](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass) a [vratek](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops) , které poskytují další kontrolu nad tím, jak se proces nasazení vyvíjí prostřednictvím řetězce prostředí.

### <a name="deploy-a-python-notebook"></a>Nasazení poznámkového bloku Pythonu

Následující fragment kódu definuje [nasazení](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) kanálu Azure, které kopíruje Poznámkový blok Python do clusteru datacihly:

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

Artefakty vytvořené službou CI jsou automaticky zkopírovány do agenta nasazení a jsou k dispozici ve složce ***$ (kanál. pracovní prostor)*** . V tomto případě úloha nasazení odkazuje na artefakt ***di-poznámkové*** bloky obsahující Poznámkový blok Python. Toto [nasazení](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) používá [rozšíření datacihly Azure DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) ke zkopírování souborů poznámkových bloků do pracovního prostoru datacihly.
Fáze ***Deploy_to_QA*** obsahuje odkaz na skupinu proměnných ***DevOps-DS-QA-VG*** , která je definovaná v projektu Azure DevOps. Kroky v této fázi odkazují na proměnné z této skupiny proměnných (například $ (DATABRICKS_URL), $ (DATABRICKS_TOKEN)). Nápad je, že další fáze (například ***Deploy_to_UAT***) bude pracovat se stejnými názvy proměnných, které jsou definovány ve vlastní skupině proměnných s rozsahem UAT.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Nasazení kanálu Azure Data Factory

Nasaditelné artefakty pro Azure Data Factory jsou šablonou Azure Resource Manager. Proto se nasadí s úlohou ***nasazení skupiny prostředků Azure*** , jak je znázorněno v následujícím fragmentu kódu:

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
Hodnota parametru data filename přichází z proměnné $ (DATA_FILE_NAME) definované ve skupině proměnných fáze kontroly kvality. Podobně lze přepsat všechny parametry definované v ***ARMTemplateForFactory. JSON*** . Pokud nejsou, použijí se výchozí hodnoty.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>Spusťte kanál a ověřte výsledek příjmu dat.

Dalším krokem je zajistit, aby nasazené řešení fungovalo. Následující definice úlohy spustí kanál Azure Data Factory se [skriptem PowerShellu](https://github.com/microsoft/DataOps/tree/master/adf/utils) a spustí Poznámkový blok Pythonu na clusteru Azure Databricks. Poznámkový blok zkontroluje správnou příjem dat a ověří soubor výsledných dat s názvem $ (bin_FILE_NAME).

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

Výsledkem tohoto článku je kanál Azure CI/CD, který se skládá z následujících fází:
* CI
* Nasazení na QA
    * Nasazení na datacihly + nasazení na ADF
    * Test integrace

Obsahuje několik fází ***nasazení*** , které se rovnají počtu cílových prostředí, která máte. Každá fáze ***nasazení*** obsahuje dvě [nasazení](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) , která běží paralelně, a [úlohu](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) , která se po nasazení spustí za účelem testování řešení v prostředí.

Ukázková implementace kanálu je sestavena v následujícím fragmentu ***YAML*** :

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
        testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
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

* [Správa zdrojového kódu ve službě Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control)
* [Průběžná integrace a doručování v Azure Data Factory](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)
* [DevOps pro Azure Databricks](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
