---
title: Rozšíření CLI
titleSuffix: Azure Machine Learning
description: Další informace o rozšíření Azure Machine Learning rozhraní příkazového řádku Azure CLI. Azure CLI je nástroj příkazového řádku napříč platformami, která umožňuje pracovat s prostředky v cloudu Azure. Machine Learning rozšíření vám umožní pracovat s Azure Machine Learning. Rozhraní příkazového řádku s příponou ML vytvoří a spravuje prostředky, jako je váš pracovní prostor, úložiště dat, datové sady, kanály, modely a nasazení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 11/05/2019
ms.custom: seodec18
ms.openlocfilehash: fe9cee44970da94c346caffc0d5fe76ffebf4a0b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845154"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>Pro Azure Machine Learning použít rozšíření CLI
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Rozhraní příkazového řádku Azure Machine Learning je rozšíření [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), multiplatformního rozhraní příkazového řádku pro platformu Azure. Toto rozšíření poskytuje příkazy pro práci s Azure Machine Learning. Umožňuje automatizovat aktivity machine learningu. Následující seznam uvádí některé ukázkové akce, které můžete provést s rozšířením CLI:

+ Spustit vytváření modelů strojového učení

+ Zaregistrujte modelů strojového učení pro využití ze strany zákazníků

+ Balení, nasazování a sledování životního cyklu vašich modelů machine learning

Rozhraní příkazového řádku, není to náhrada pro sadu SDK Azure Machine Learning. Jedná se o doplňkový nástroj, který je optimalizovaný pro zpracování vysoce parametrizovaných úloh, které se dobře hodí pro automatizaci.

## <a name="prerequisites"></a>Požadavky

* Pokud chcete používat rozhraní příkazového řádku, musíte mít předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

* [Rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="full-reference-docs"></a>Úplné referenční dokumentace

Najděte [kompletní referenční dokumentaci pro rozšíření Azure CLI Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Instalace rozšíření

Pokud chcete nainstalovat rozšíření Machine Learning CLI, použijte následující příkaz:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Příklady souborů, které můžete použít s níže uvedenými příkazy, najdete [tady](https://aka.ms/azml-deploy-cloud).

Po zobrazení výzvy vyberte `y` nainstalovat rozšíření.

Pokud chcete ověřit, jestli je nainstalovaná rozšíření, použijte následující příkaz k zobrazení seznamu podpříkazů specifické pro ML:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>Aktualizace rozšíření

Chcete-li aktualizovat rozšíření Machine Learning CLI, použijte následující příkaz:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>Odebrání rozšíření

Chcete-li odebrat rozšíření rozhraní příkazového řádku, použijte následující příkaz:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Správa prostředků

Následující příkazy ukazují, jak používat rozhraní příkazového řádku ke správě prostředků používat Azure Machine Learning.

+ Pokud ho ještě nemáte, vytvořte skupinu prostředků:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Vytvořit Azure Machine Learning pracovní prostor:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > Tento příkaz vytvoří pracovní prostor edice Basic. Chcete-li vytvořit pracovní prostor organizace, použijte přepínač `--sku enterprise` s příkazem `az ml workspace create`. Další informace o edicích Azure Machine Learning najdete v tématu [co je Azure Machine Learning](overview-what-is-azure-ml.md#sku).

    Další informace najdete v tématu [AZ ml Workspace Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ Připojte ke složce konfiguraci pracovního prostoru, aby bylo možné povolit sledování kontextu rozhraní příkazového řádku.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Tento příkaz vytvoří podadresář `.azureml`, který obsahuje příklady souborů prostředí RunConfig a conda. Obsahuje taky `config.json` soubor, který se používá ke komunikaci s pracovním prostorem Azure Machine Learning.

    Další informace najdete v tématu [AZ ml složka připojit](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Připojte kontejner objektů blob Azure jako úložiště dat.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Další informace najdete v tématu [AZ ml DataStore Attach-BLOB](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Nahrajte soubory do úložiště dat.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Další informace najdete v tématu [AZ ml DataStore upload](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload).

+ Připojte cluster AKS jako cíl služby Compute.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Další informace najdete v tématu [AZ ml computetarget Attach AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ Vytvořte nový cíl AMLcompute.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    Další informace najdete v tématu [AZ ml computetarget Create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

## <a id="experiments"></a>Spustit experimenty

* Spusťte běh experimentu. Při použití tohoto příkazu zadejte název souboru RunConfig (text před \*. RunConfig, pokud hledáte systém souborů) s parametrem-c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Příkaz `az ml folder attach` vytvoří podadresář `.azureml`, který obsahuje dva příklady souborů RunConfig. 
    >
    > Pokud máte skript Pythonu, který vytvoří objekt konfigurace spuštění programově, můžete použít [RunConfig. Save ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) a uložit ho jako soubor RunConfig.
    >
    > Úplné schéma RunConfig lze nalézt v tomto [souboru JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). Schéma slouží k samoobslužnému dokumentování prostřednictvím `description` klíč každého objektu. Kromě toho existují výčty pro možné hodnoty a fragment šablony na konci.

    Další informace najdete v tématu [AZ ml Run odeslání-Script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Zobrazit seznam experimentů:

    ```azurecli-interactive
    az ml experiment list
    ```

    Další informace najdete v tématu [AZ ml experiment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="dataset-management"></a>Správa datových sad

Následující příkazy ukazují, jak pracovat s datovými sadami v Azure Machine Learning:

+ Registrovat datovou sadu:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Informace o formátu souboru JSON, který slouží k definování datové sady, získáte pomocí `az ml dataset register --show-template`.

    Další informace najdete v tématu [AZ ml DataSet Register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Archivace aktivní nebo zastaralé datové sady:

    ```azurecli-interactive
    az ml dataset archive -n dataset-name
    ```

    Další informace najdete v tématu [AZ ml DataSet Archive](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Zastaralá datová sada:

    ```azurecli-interactive
    az ml dataset deprecate -d replacement-dataset-id -n dataset-to-deprecate
    ```

    Další informace najdete v tématu [AZ ml DataSet](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Vypíše všechny datové sady v pracovním prostoru:

    ```azurecli-interactive
    az ml dataset list
    ```

    Další informace najdete v tématu [AZ ml DataSet list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Získat podrobnosti o datové sadě:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Další informace najdete v tématu [AZ ml DataSet show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Znovu aktivovat archivovanou nebo zastaralou datovou sadu:

    ```azurecli-interactive
    az ml dataset reactivate -n dataset-name
    ```

    Další informace najdete v části [AZ ml DataSet reactivate](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Zrušit registraci datové sady:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Další informace najdete v tématu [AZ ml DataSet Unregister](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

## <a name="environment-management"></a>Správa prostředí

Následující příkazy ukazují, jak vytvořit, zaregistrovat a vypsat Azure Machine Learning [prostředí](how-to-configure-environment.md) pro váš pracovní prostor:

+ Vytvořit soubory pro generování uživatelského rozhraní pro prostředí:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Další informace najdete v tématu [AZ ml Environment – generování uživatelského rozhraní](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Registrace prostředí:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Další informace najdete v tématu [AZ ml Environment Registry](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register).

+ Seznam registrovaných prostředí:

    ```azurecli-interactive
    az ml environment list
    ```

    Další informace najdete v tématu [AZ ml Environment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list).

+ Stažení registrovaného prostředí:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Další informace najdete v tématu [AZ ml Environment Download](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download).

### <a name="environment-configuration-schema"></a>Schéma konfigurace prostředí

Pokud jste použili příkaz `az ml environment scaffold`, vygeneruje soubor `azureml_environment.json` šablony, který lze upravit a použít k vytvoření vlastních konfigurací prostředí pomocí rozhraní příkazového řádku. Objekt nejvyšší úrovně se v sadě Python SDK volně mapuje na třídu [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) . 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

Následující tabulka podrobně popisuje každé pole nejvyšší úrovně v souboru JSON, jeho typ a popis. Pokud je typ objektu propojený se třídou ze sady Python SDK, je mezi jednotlivými poli JSON a názvem veřejné proměnné ve třídě Pythonu volná 1:1. V některých případech může být pole namapováno na argument konstruktoru, nikoli na proměnnou třídy. Například pole `environmentVariables` se mapuje na `environment_variables` proměnnou ve třídě [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) .

| Pole JSON | Typ | Popis |
|---|---|---|
| `name` | `string` | Název prostředí. Nespouštějte jméno pomocí **Microsoft** nebo **AzureML**. |
| `version` | `string` | Verze prostředí. |
| `environmentVariables` | `{string: string}` | Mapa hodnoty hash názvů a hodnot proměnných prostředí. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | Objekt, který definuje prostředí a překladač v Pythonu, který se má použít u cílového výpočetního prostředku. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | Definuje nastavení pro přizpůsobení image Docker sestavené do specifikací prostředí. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | Oddíl nakonfiguruje nastavení Sparku. Používá se jenom v případě, že je architektura nastavená na PySpark. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | Konfiguruje závislosti knihoven datacihly. |
| `inferencingStackVersion` | `string` | Určuje verzi zásobníku Inferencing přidanou k imagi. Chcete-li se vyhnout přidání Inferencing zásobníku, nechte toto pole `null`. Platná hodnota: "poslední". |

## <a name="ml-pipeline-management"></a>Správa kanálu ML

Následující příkazy ukazují, jak pracovat s kanály strojového učení:

+ Vytvoření kanálu strojového učení:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Další informace najdete v tématu [AZ ml Pipeline Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create).

    Další informace o souboru YAML kanálu najdete [v tématu definice kanálů strojového učení v YAML](reference-pipeline-yaml.md).

+ Spustit kanál:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Další informace najdete v tématu [AZ ml Run Submit-Pipeline](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Další informace o souboru YAML kanálu najdete [v tématu definice kanálů strojového učení v YAML](reference-pipeline-yaml.md).

+ Naplánování kanálu:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Další informace najdete v tématu [AZ ml Pipeline Create-Schedule](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    Další informace o souboru YAML plánu kanálu najdete [v tématu Definování kanálů strojového učení v YAML](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Registrace modelů, profilace, nasazení

Následující příkazy ukazují, jak registrace trénovaného modelu a pak ho nasadíme jako služby v produkčním prostředí:

+ Zaregistrujte model Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Další informace najdete v tématu [AZ ml model Register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **Volitelné** Profilujte model, abyste získali optimální hodnoty CPU a paměti pro nasazení.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Další informace najdete v tématu [AZ ml model Profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ Nasazení modelu do AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Další informace o schématu konfiguračního souboru odvození naleznete v tématu [schéma konfigurace](#inferenceconfig)odvození.
    
    Další informace o schématu konfiguračního souboru nasazení najdete v tématu [schéma konfigurace nasazení](#deploymentconfig).

    Další informace najdete v tématu [AZ ml model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Schéma konfigurace odvození

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Schéma konfigurace nasazení

### <a name="local-deployment-configuration-schema"></a>Schéma konfigurace místního nasazení

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Schéma konfigurace nasazení služby Azure Container instance 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Schéma konfigurace nasazení služby Azure Kubernetes

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Další kroky

* [Odkaz na příkazy pro rozšíření Machine Learning CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)

* [Školení a nasazení modelů strojového učení pomocí Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
