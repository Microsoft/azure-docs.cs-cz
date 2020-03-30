---
title: DevOps pro kanál ingestování dat
titleSuffix: Azure Machine Learning
description: Zjistěte, jak použít devOps postupy pro implementaci kanálu ingestování dat slouží k přípravě dat pro trénování modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/30/2020
ms.openlocfilehash: d987171d41bd6d80bab4cce91ef9ecec1f0dc7a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247176"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps pro kanál ingestování dat

Ve většině scénářů řešení pro ingestování dat je složení skriptů, vyvolání služby a kanálu orchestratující všechny aktivity. V tomto článku se dozvíte, jak použít devOps postupy pro životní cyklus vývoje společného kanálu pro ingestování dat. Kanál připraví data pro školení modelu machine learningu.

## <a name="the-solution"></a>Řešení

Zvažte následující pracovní postup při požití dat:

![datový kanál pro požití](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

V tomto přístupu se trénovací data ukládají v úložišti objektů blob Azure. Kanál Azure Data Factory načte data ze vstupního kontejneru objektů blob, transformuje je a ukládá data do výstupního kontejneru objektů blob. Tento kontejner slouží jako [úložiště dat](concept-data.md) pro službu Azure Machine Learning. Po připravené matné datě kanál data factory vyvolá kanál školení machine learning utrénovat model. V tomto konkrétním příkladu transformace dat se provádí poznámkový blok Pythonu, spuštěné v clusteru Azure Databricks. 

## <a name="what-we-are-building"></a>Co budujeme

Stejně jako u jiných softwarových řešení na něm pracuje tým (například datainženýři). 

![cicd-data-ingestion](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Spolupracují a sdílejí stejné prostředky Azure, jako je Azure Data Factory, Azure Databricks, účet Azure Storage a podobně. Kolekce těchto prostředků je vývojové prostředí. Datoví inženýři přispívají ke stejnému základu zdrojového kódu. Proces průběžné integrace sestavuje kód, kontroluje jej pomocí testů kvality kódu, testování částí a vytváří artefakty, jako je testovaný kód a šablony Azure Resource Manager. Proces nepřetržitého doručování nasazuje artefakty do navazujících prostředí. Tento článek ukazuje, jak automatizovat procesy CI a CD pomocí [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/).

## <a name="source-control-management"></a>Správa správy zdrojového kódu

Členové týmu pracují mírně odlišným způsobem, aby spolupracovali na zdrojovém kódu poznámkového bloku Pythonu a zdrojovém kódu Azure Data Factory. V obou případech je však kód uložen v úložišti správy zdrojového kódu (například Azure DevOps, GitHub, GitLab) a spolupráce je obvykle založena na některém modelu větvení (například [GitFlow).](https://datasift.github.io/gitflow/IntroducingGitFlow.html)

### <a name="python-notebook-source-code"></a>Zdrojový kód poznámkového bloku Pythonu

Datoví inženýři pracují se zdrojovým kódem poznámkového bloku Pythonu buď místně v ide (například [Visual Studio Code)](https://code.visualstudio.com)nebo přímo v pracovním prostoru Databricks. Ten dává možnost ladit kód ve vývojovém prostředí. V každém případě kód bude sloučen do úložiště podle zásad větvení. Důrazně doporučujeme ukládat kód `.py` do souborů, `.ipynb` nikoli ve formátu poznámkového bloku Jupyter. Zlepšuje čitelnost kódu a umožňuje automatické kontroly kvality kódu v procesu CI.

### <a name="azure-data-factory-source-code"></a>Zdrojový kód Azure Data Factory

Zdrojový kód kanálů Azure Data Factory je kolekce json souborů generovaných pracovního prostoru. Normálně datoví inženýři pracují s vizuálním návrhářem v pracovním prostoru Azure Data Factory, nikoli přímo se soubory zdrojového kódu. Nakonfigurujte pracovní prostor pomocí úložiště správy zdrojového kódu, jak je popsáno v [dokumentaci k Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration). S touto konfigurací na místě, datové inženýry jsou schopni spolupracovat na zdrojovém kódu po upřednostňované větvení pracovního postupu.    

## <a name="continuous-integration-ci"></a>Průběžná integrace (CI)

Konečným cílem procesu průběžné integrace je shromáždit společnou týmovou práci ze zdrojového kódu a připravit ji pro nasazení do navazujících prostředí. Stejně jako u správy zdrojového kódu se tento proces liší pro poznámkové bloky Pythonu a kanály Azure Data Factory. 

### <a name="python-notebook-ci"></a>Jazyk Ci poznámkového bloku Pythonu

Proces CI pro poznámkové bloky Pythonu získá kód z větve spolupráce (například ***master*** nebo ***develop)*** a provede následující činnosti:
* Kód linting
* Testování jednotek
* Uložení kódu jako artefaktu

Následující fragment kódu ukazuje implementaci těchto kroků v kanálu ***yaml*** Azure DevOps:

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

Kanál používá ***flake8*** k provádění kódu Pythonu linting. Spustí testy částí definované ve zdrojovém kódu a publikuje linting a výsledky testů, takže jsou k dispozici na obrazovce spuštění kanálu Azure:

![zkoušky jednotek](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Pokud je testování linting a částí úspěšné, kanál zkopíruje zdrojový kód do úložiště artefaktů, které bude použito v následujících krocích nasazení.

### <a name="azure-data-factory-ci"></a>Azure Data Factory CI

Proces CI pro kanál Azure Data Factory je kritickým bodem v celém článku CI/CD pro kanál pro ingestování dat. Neexistuje žádná ***průběžná*** integrace. Nasaditelný artefakt pro Azure Data Factory je kolekce šablon Azure Resource Manager. Jediný způsob, jak tyto šablony vytvořit, je kliknout na tlačítko ***publikovat*** v pracovním prostoru Azure Data Factory. Tady není žádná automatizace.
Datoví inženýři sloučí zdrojový kód ze svých větví funkcí do větve spolupráce, například ***master*** nebo ***develop***. Potom někdo s udělenými oprávněními klikne na tlačítko ***publikovat*** a vygeneruje šablony Azure Resource Manageru ze zdrojového kódu ve větvi spolupráce. Po kliknutí na tlačítko pracovní prostor ověří kanály (představte si to jako linting a testování částí), generuje šablony Azure Resource Manager (představte si to jako sestavení) a uloží generované šablony do technické větve ***adf_publish*** ve stejném úložišti kódu (představte si to jako publikování artefaktů). Tato větev se automaticky vytvoří v pracovním prostoru Azure Data Factory. Tento proces je popsán v podrobnostech v [dokumentaci K Tomu, aby se v Azure Data Factory .](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)

Je důležité se ujistit, že generované šablony Azure Resource Manager jsou nenostik prostředí. To znamená, že všechny hodnoty, které se mohou lišit mezi prostředími jsou parametrizovány. Azure Data Factory je dostatečně chytrá, aby odhalila většinu takových hodnot, jako jsou parametry. Například v následující šabloně jsou vlastnosti připojení k pracovnímu prostoru Azure Machine Learning vystaveny jako parametry:

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

Můžete však chtít vystavit vlastní vlastnosti, které nejsou zpracovány pracovního prostoru Azure Data Factory ve výchozím nastavení. Ve scénáři tohoto článku kanál Azure Data Factory vyvolá poznámkový blok Pythonu, který zpracovává data. Poznámkový blok přijímá parametr s názvem vstupního datového souboru.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

Tento název se liší pro ***prostředí Dev***, ***QA***, ***UAT***a ***PROD.*** Ve složitém kanálu s více aktivitami může existovat několik vlastních vlastností. Je vhodné shromáždit všechny tyto hodnoty na jednom místě a definovat je jako ***proměnné***kanálu :

![adf-proměnné](media/how-to-cicd-data-ingestion/adf-variables.png)

Aktivity kanálu může odkazovat na proměnné kanálu při jejich skutečném použití:

![adf-notebook-parametry](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

Pracovní prostor Azure Data Factory ***nezveřejňuje*** proměnné kanálu jako parametry šablon Azure Resource Manager ve výchozím nastavení. Pracovní prostor používá [výchozí šablonu parametrizace,](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) která diktuje, jaké vlastnosti kanálu by měly být vystaveny jako parametry šablony Azure Resource Manager. Chcete-li do seznamu přidat proměnné kanálu, aktualizujte část "Microsoft.DataFactory/factories/pipelines" [výchozí šablony parametrizace](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) pomocí následujícího fragmentu a umístěte výsledný soubor json do kořenové složky:

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

To vynutí pracovní prostor Azure Data Factory přidat proměnné do seznamu parametrů po kliknutí na tlačítko ***publikovat:***

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

Hodnoty v souboru json jsou výchozí hodnoty nakonfigurované v definici kanálu. Očekává se, že budou přepsány hodnotami cílového prostředí při nasazení šablony Azure Resource Manager.

## <a name="continuous-delivery-cd"></a>Nepřetržité doručování (CD)

Proces nepřetržitého doručování přebírá artefakty a nasazuje je do prvního cílového prostředí. Zajišťuje, že řešení funguje spuštěním testů. Pokud je úspěšná, pokračuje do dalšího prostředí. Kanál CD Azure se skládá z více fází představujících prostředí. Každá fáze obsahuje nasazení a [úlohy,](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) které provádějí následující kroky: [jobs](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml)
* Nasazení poznámkového bloku Pythonu do pracovního prostoru Azure Databricks
* Nasazení kanálu Azure Data Factory 
* Spuštění kanálu
* Kontrola výsledku požití dat

Fáze kanálu lze nakonfigurovat pomocí [schválení](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass) a [brány,](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops) které poskytují další kontrolu nad vývojem procesu nasazení v řetězci prostředí.

### <a name="deploy-a-python-notebook"></a>Nasazení poznámkového bloku Pythonu

Následující fragment kódu definuje [nasazení](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) kanálu Azure, které kopíruje poznámkový blok Pythonu do clusteru Databricks:

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

Artefakty vytvořené ci jsou automaticky zkopírovány do agenta nasazení a jsou k dispozici ve složce ***$(Pipeline.Workspace).*** V tomto případě úloha nasazení odkazuje na artefakt ***di-notebooků*** obsahující poznámkový blok Pythonu. Toto [nasazení](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) používá [databricks Azure DevOps rozšíření](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) ke kopírování souborů poznámkového bloku do pracovního prostoru Databricks.
Fáze ***Deploy_to_QA*** obsahuje odkaz na skupinu ***proměnných devops-ds-qa-vg*** definovanou v projektu Azure DevOps. Kroky v této fázi odkazují na proměnné z této skupiny proměnných (například $(DATABRICKS_URL), $(DATABRICKS_TOKEN)). Myšlenka spočívá v tom, že další fáze (například ***Deploy_to_UAT)*** bude fungovat se stejnými názvy proměnných definovanými ve vlastní skupině proměnných s rozsahem UAT.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Nasazení kanálu Azure Data Factory

Nasaditelný artefakt pro Azure Data Factory je šablona Azure Resource Manager. Proto se bude nasazovat s úlohou ***nasazení skupiny prostředků Azure,*** jak je to ukázáno v následujícím fragmentu:

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
Hodnota parametru název datového souboru pochází z proměnné $(DATA_FILE_NAME) definované ve skupině proměnných fáze QA. Podobně všechny parametry definované v ***ARMTemplateForFactory.json*** lze přepsat. Pokud tomu tak není, pak se použijí výchozí hodnoty.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>Spuštění kanálu a kontrola výsledku požití dat

Dalším krokem je ujistěte se, že nasazené řešení funguje. Následující definice úlohspustí kanál Azure Data Factory se [skriptem PowerShellu](https://github.com/microsoft/DataOps/tree/master/adf/utils) a spustí poznámkový blok Pythonu v clusteru Azure Databricks. Poznámkový blok zkontroluje, zda byla data správně přijata, a ověří datový soubor výsledků s názvem $(bin_FILE_NAME).

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

Konečný úkol v úloze zkontroluje výsledek spuštění poznámkového bloku. Pokud vrátí chybu, nastaví stav spuštění kanálu se nezdařilo.

## <a name="putting-pieces-together"></a>Dávat kousky dohromady

Výsledek tohoto článku je kanál CI/CD Azure, který se skládá z následujících fází:
* CI
* Nasazení do qa
    * Nasazení do databricks + nasazení do adf
    * Test integrace

Obsahuje počet fáze ***nasazení*** rovnající se počtu cílových prostředí, které máte. Každá fáze ***nasazení*** obsahuje dvě [nasazení,](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) která běží paralelně, a [úlohu,](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) která se spustí po nasazení k testování řešení v prostředí.

Ukázková implementace potrubí je sestavena v následujícím fragmentu ***yaml:***

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
* [DevOps pro Datové cihly Azure](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
