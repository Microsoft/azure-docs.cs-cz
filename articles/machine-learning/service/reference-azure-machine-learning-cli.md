---
title: Strojové učení rozšíření rozhraní příkazového řádku
titleSuffix: Azure Machine Learning service
description: Další informace o rozšíření Azure Machine Learning rozhraní příkazového řádku Azure CLI. Azure CLI je nástroj příkazového řádku napříč platformami, která umožňuje pracovat s prostředky v cloudu Azure. Rozšíření Machine Learning umožňuje pracovat s služby Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 3f8590d6f8a1f038de7e3bd43d0dc6e896efa948
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329230"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Použití rozšíření rozhraní příkazového řádku pro službu Azure Machine Learning

Rozhraní příkazového řádku Azure Machine Learning je rozšíření [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), multiplatformního rozhraní příkazového řádku pro platformu Azure. Toto rozšíření poskytuje příkazy pro práci se službou Azure Machine Learning z příkazového řádku. Umožňuje vytvořit skripty pro automatizaci strojového učení pracovních postupů. Například můžete vytvořit skripty, které provést následující akce:

+ Spustit vytváření modelů strojového učení

+ Zaregistrujte modelů strojového učení pro využití ze strany zákazníků

+ Balení, nasazování a sledování životního cyklu vašich modelů machine learning

Rozhraní příkazového řádku, není to náhrada pro sadu SDK Azure Machine Learning. Doplňkové nástroj, který je optimalizovaný pro zpracování vysoce parametrizované úkoly, jako je:

* Vytváření výpočetních prostředků

* odeslání experimentu parametrizované

* Registrace modelu

* Vytvoření Image

* Nasazení služby

## <a name="prerequisites"></a>Požadavky


* Pokud chcete používat rozhraní příkazového řádku, musíte mít předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](http://aka.ms/AMLFree) ještě dnes.

* [Rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Instalace rozšíření

Pokud chcete nainstalovat rozšíření Machine Learning CLI, použijte následující příkaz:

```azurecli-interactive
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-1.0.10-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

Po zobrazení výzvy vyberte `y` nainstalovat rozšíření.

Pokud chcete ověřit, jestli je nainstalovaná rozšíření, použijte následující příkaz k zobrazení seznamu podpříkazů specifické pro ML:

```azurecli-interactive
az ml -h
```

> [!TIP]
> Rozšíření je nutné aktualizovat __odebrat__ a potom __nainstalovat__ ho. Tím se nainstaluje nejnovější verzi.

## <a name="remove-the-extension"></a>Odebrat rozšíření

Chcete-li odebrat rozšíření rozhraní příkazového řádku, použijte následující příkaz:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Správa prostředků

Následující příkazy ukazují, jak používat rozhraní příkazového řádku ke správě prostředků používat Azure Machine Learning.


+ Vytvořte pracovní prostor služby Azure Machine Learning:

    ```azurecli-interactive
    az ml workspace create -n myworkspace -g myresourcegroup
    ```

+ Nastavení výchozího pracovního prostoru:

    ```azurecli-interactive
    az configure --defaults aml_workspace=myworkspace group=myresourcegroup
    ```
    
* Připojit AKS cluster

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

## <a name="experiments"></a>Experimenty

Následující příkazy ukazují, jak pracovat s experimenty pomocí rozhraní příkazového řádku:

* Před odesláním experiment připojte projekt (Konfigurace spuštění):

    ```azurecli-interactive
    az ml project attach --experiment-name myhistory
    ```

* Spusťte běh experimentu. Při použití tohoto příkazu, zadejte název souboru runconfig, který obsahuje konfigurace spuštění. Cílové výpočetní prostředí používá k vytvoření prostředí pro školení pro model konfigurace spuštění. V tomto příkladu je konfigurace spuštění načtena z `./aml_config/myrunconfig.runconfig` souboru.

    ```azurecli-interactive
    az ml run submit -c myrunconfig train.py
    ```

    Další informace o souboru runconfig, najdete v článku [RunConfig](#runconfig) oddílu.

* Zobrazte seznam odeslaných experimentů:

    ```azurecli-interactive
    az ml history list
    ```

## <a name="model-registration-image-creation--deployment"></a>Model registrace, vytvoření image a nasazení

Následující příkazy ukazují, jak registrace trénovaného modelu a pak ho nasadíme jako služby v produkčním prostředí:

+ Zaregistrujte model Azure Machine Learning:

  ```azurecli-interactive
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ Vytvořte bitovou kopii, která obsahuje vaše modelu strojového učení a závislosti: 

  ```azurecli-interactive
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ Nasazení bitové kopie do cílového výpočetního prostředí:

  ```azurecli-interactive
  az ml service create aci -n myaciservice --image-id myimage:1
  ```

## <a id="runconfig"></a> Soubor Runconfig

Konfigurace spuštění se používá ke konfiguraci školení prostředí sloužícího k natrénování modelu. Tato konfigurace je možné vytvořit pomocí sady SDK v paměti nebo může být načteno ze souboru runconfig.

Soubor runconfig je textový dokument, který popisuje konfigurační prostředí školení. Například uvádí název cvičný skript a soubor, který obsahuje potřebné pro trénování modelu závislosti systému conda.

Rozhraní příkazového řádku Azure Machine Learning vytvoří dvě výchozí `.runconfig` soubory s názvem `docker.runconfig` a `local.runconfig` připojíte-li projekt používá `az ml project attach` příkazu. 

Pokud máte kód, který vytvoří konfigurace spuštění pomocí [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) třídy, můžete použít `save()` metoda a zachová tak `.runconfig` souboru.

Následuje příklad obsahu `.runconfig` souboru:

```text
# The script to run.
script: train.py
# The arguments to the script file.
arguments: []
# The name of the compute target to use for this run.
target: local
# Framework to execute inside. Allowed values are "Python" ,  "PySpark", "CNTK",  "TensorFlow", and "PyTorch".
framework: PySpark
# Communicator for the given framework. Allowed values are "None" ,  "ParameterServer", "OpenMpi", and "IntelMpi".
communicator: None
# Automatically prepare the run environment as part of the run itself.
autoPrepareEnvironment: true
# Maximum allowed duration for the run.
maxRunDurationSeconds:
# Number of nodes to use for running job.
nodeCount: 1
# Environment details.
environment:
# Environment variables set for the run.
  environmentVariables:
    EXAMPLE_ENV_VAR: EXAMPLE_VALUE
# Python details
  python:
# user_managed_dependencies=True indicates that the environmentwill be user managed. False indicates that AzureML willmanage the user environment.
    userManagedDependencies: false
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
  docker:
# Set True to perform this run inside a Docker container.
    enabled: true
# Base image used for Docker-based runs.
    baseImage: mcr.microsoft.com/azureml/base:0.2.1
# Set False if necessary to work around shared volume bugs.
    sharedVolumes: true
# Run with NVidia Docker extension to support GPUs.
    gpuSupport: false
# Extra arguments to the Docker run command.
    arguments: []
# Image registry that contains the base image.
    baseImageRegistry:
# DNS name or IP address of azure container registry(ACR)
      address:
# The username for ACR
      username:
# The password for ACR
      password:
# Spark details
  spark:
# List of spark repositories.
    repositories:
    - https://mmlspark.azureedge.net/maven
    packages:
    - group: com.microsoft.ml.spark
      artifact: mmlspark_2.11
      version: '0.12'
    precachePackages: true
# Databricks details
  databricks:
# List of maven libraries.
    mavenLibraries: []
# List of PyPi libraries
    pypiLibraries: []
# List of RCran libraries
    rcranLibraries: []
# List of JAR libraries
    jarLibraries: []
# List of Egg libraries
    eggLibraries: []
# History details.
history:
# Enable history tracking -- this allows status, logs, metrics, and outputs
# to be collected for a run.
  outputCollection: true
# whether to take snapshots for history.
  snapshotProject: true
# Spark configuration details.
spark:
  configuration:
    spark.app.name: Azure ML Experiment
    spark.yarn.maxAppAttempts: 1
# HDI details.
hdi:
# Yarn deploy mode. Options are cluster and client.
  yarnDeployMode: cluster
# Tensorflow details.
tensorflow:
# The number of worker tasks.
  workerCount: 1
# The number of parameter server tasks.
  parameterServerCount: 1
# Mpi details.
mpi:
# When using MPI, number of processes per node.
  processCountPerNode: 1
# data reference configuration details
dataReferences: {}
# Project share datastore reference.
sourceDirectoryDataStore:
# AmlCompute details.
amlcompute:
# VM size of the Cluster to be created.Allowed values are Azure vm sizes.The list of vm sizes is available in 'https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-sizes-specs
  vmSize:
# VM priority of the Cluster to be created.Allowed values are "dedicated" , "lowpriority".
  vmPriority:
# A bool that indicates if the cluster has to be retained after job completion.
  retainCluster: false
# Name of the cluster to be created. If not specified, runId will be used as cluster name.
  name:
# Maximum number of nodes in the AmlCompute cluster to be created. Minimum number of nodes will always be set to 0.
  clusterMaxNodeCount: 1
```