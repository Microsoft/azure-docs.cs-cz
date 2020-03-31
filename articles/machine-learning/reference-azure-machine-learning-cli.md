---
title: Rozšíření CLI
titleSuffix: Azure Machine Learning
description: Přečtěte si o rozšíření ClI Azure Machine Learning pro Azure CLI. Azure CLI je nástroj příkazového řádku pro různé platformy, který umožňuje pracovat s prostředky v cloudu Azure. Rozšíření Machine Learning umožňuje pracovat s Azure Machine Learning. Cli ML vytváří a spravuje prostředky, jako je váš pracovní prostor, úložiště dat, datové sady, kanály, modely a nasazení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 471b26ebc4bd4aecb814ec43c7eba56e3d764fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402493"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>Použití rozšíření CLI pro Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Rozhraní příkazového řádku Azure Machine Learning je rozšíření matné [licenaté uživatelské rozhraní Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), rozhraní příkazového řádku pro platformu Azure. Toto rozšíření poskytuje příkazy pro práci s Azure Machine Learning. Umožňuje automatizovat vaše aktivity strojového učení. Následující seznam obsahuje několik příkladů akcí, které můžete provést s rozšířením příkazového příkazového příkazu:

+ Spouštění experimentů k vytváření modelů strojového učení

+ Registrace modelů strojového učení pro použití zákazníkem

+ Sbalte, nasazujte a sledujte životní cyklus modelů strojového učení

Příkaz příkazpříkaz není náhradou za azure machine learning sdk. Jedná se o doplňkový nástroj, který je optimalizován pro zpracování vysoce parametrizovaných úloh, které se dobře hodí k automatizaci.

## <a name="prerequisites"></a>Požadavky

* Chcete-li použít příkazové příkazové příkazové příkazy, musíte mít předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="full-reference-docs"></a>Úplné referenční dokumenty

Najděte [úplné referenční dokumenty pro rozšíření azure-cli-ml azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Instalace rozšíření

Chcete-li nainstalovat rozšíření Machine Learning CLI, použijte následující příkaz:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Příklady souborů, které můžete použít s níže uvedenými příkazy, naleznete [zde](https://aka.ms/azml-deploy-cloud).

Po zobrazení výzvy vyberte `y` instalaci rozšíření.

Chcete-li ověřit, zda bylo rozšíření nainstalováno, zobrazte seznam dílčích příkazů specifických pro ml následující příkaz:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>Aktualizace rozšíření

Chcete-li aktualizovat rozšíření machine learningcli, použijte následující příkaz:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>Odebrání rozšíření

Chcete-li odebrat rozšíření příkazového příkazu, použijte následující příkaz:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Správa prostředků

Následující příkazy ukazují, jak používat zapisování příkazového příkazu ke správě prostředků používaných Azure Machine Learning.

+ Pokud ji ještě nemáte, vytvořte skupinu prostředků:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Vytvořte pracovní prostor Azure Machine Learning:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > Tento příkaz vytvoří základní edici pracovního prostoru. Chcete-li vytvořit pracovní prostor `--sku enterprise` pro `az ml workspace create` podniky, použijte přepínač s příkazem. Další informace o edicích Azure Machine Learning najdete v tématu [Co je Azure Machine Learning](overview-what-is-azure-ml.md#sku).

    Další informace naleznete [v tématu az ml pracovní prostor vytvořit](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ Připojte konfiguraci pracovního prostoru ke složce, abyste povolili kontextové povědomí cli.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Tento příkaz `.azureml` vytvoří podadresář, který obsahuje příklad runconfig a conda soubory prostředí. Obsahuje také `config.json` soubor, který se používá ke komunikaci s pracovním prostorem Azure Machine Learning.

    Další informace naleznete [v tématu az ml folder attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Připojte kontejner objektů blob Azure jako úložiště dat.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Další informace naleznete [v tématu az ml datastore attach-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Nahrajte soubory do datastore.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Další informace naleznete [v tématu az ml datastore upload](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload).

+ Připojte cluster AKS jako výpočetní cíl.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Další informace naleznete [v tématu az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ Vytvořte nový cíl AMLcompute.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    Další informace naleznete [v tématu az ml computetarget create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

## <a name="run-experiments"></a><a id="experiments"></a>Spouštění experimentů

* Spusťte spuštění experimentu. Při použití tohoto příkazu zadejte název souboru runconfig (text před \*.runconfig, pokud se díváte na systém souborů) proti parametru -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Příkaz `az ml folder attach` vytvoří `.azureml` podadresář, který obsahuje dva ukázkové soubory runconfig. 
    >
    > Pokud máte skript Pythonu, který programově vytvoří objekt konfigurace, můžete jej uložit jako soubor runconfig pomocí [runconfig.save().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-)
    >
    > Úplné schéma runconfig lze nalézt v tomto [souboru JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). Schéma je samodokumentující prostřednictvím `description` klíče každého objektu. Navíc existují výčty pro možné hodnoty a fragment šablony na konci.

    Další informace naleznete [v tématu az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Zobrazit seznam experimentů:

    ```azurecli-interactive
    az ml experiment list
    ```

    Další informace naleznete [v seznamu experimentů az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="dataset-management"></a>Správa datové sady

Následující příkazy ukazují, jak pracovat s datovými sadami v Azure Machine Learning:

+ Registrace datové sady:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Informace o formátu souboru JSON použitého k definování `az ml dataset register --show-template`datové sady použijte .

    Další informace naleznete [v registru datových sad AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Archivujte aktivní nebo zastaralou datovou sadu:

    ```azurecli-interactive
    az ml dataset archive -n dataset-name
    ```

    Další informace naleznete [v archivu datových sad AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Zamezení datové sady:

    ```azurecli-interactive
    az ml dataset deprecate -d replacement-dataset-id -n dataset-to-deprecate
    ```

    Další informace naleznete [v tématu az ml dataset zavržení .](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)

+ Seznam všech datových sad v pracovním prostoru:

    ```azurecli-interactive
    az ml dataset list
    ```

    Další informace naleznete [v seznamu datových sad AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Získejte podrobnosti o datové sadě:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Další informace naleznete [v tématu az ml dataset show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Opětovná aktivace archivované nebo zastaralé datové sady:

    ```azurecli-interactive
    az ml dataset reactivate -n dataset-name
    ```

    Další informace naleznete [v tématu az ml dataset reactivate](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Zrušení registrace datové sady:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Další informace naleznete [v tématu az ml dataset unregister](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

## <a name="environment-management"></a>Řízení životního prostředí

Následující příkazy ukazují, jak vytvořit, zaregistrovat a zobrazit seznam [prostředí](how-to-configure-environment.md) Azure Machine Learning pro váš pracovní prostor:

+ Vytvořte soubory pomocí systému lešení pro prostředí:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Další informace naleznete [v tématu az ml prostředí lešení](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Registrace prostředí:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Další informace naleznete [v registru prostředí az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register).

+ Seznam registrovaných prostředí:

    ```azurecli-interactive
    az ml environment list
    ```

    Další informace naleznete [v seznamu prostředí az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list).

+ Stáhněte si registrované prostředí:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Další informace naleznete [v tématu az ml environment download](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download).

### <a name="environment-configuration-schema"></a>Schéma konfigurace prostředí

Pokud jste `az ml environment scaffold` použili příkaz, generuje `azureml_environment.json` soubor šablony, který lze upravit a použít k vytvoření vlastní konfigurace prostředí s CLI. Objekt nejvyšší úrovně volně mapuje na třídu [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) v Pythonu SDK. 

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

V následující tabulce jsou uvedeny každé pole nejvyšší úrovně v souboru JSON, jeho typ a popis. Pokud je typ objektu propojen s třídou z sady Python SDK, je volná shoda 1:1 mezi jednotlivými poli JSON a názvem veřejné proměnné ve třídě Pythonu. V některých případech může pole mapovat na argument konstruktoru spíše než na proměnnou třídy. Například pole `environmentVariables` se mapuje na proměnnou `environment_variables` ve [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) třídě.

| Pole JSON | Typ | Popis |
|---|---|---|
| `name` | `string` | Název prostředí. Nezačínejte s názvem **Microsoft** nebo **AzureML**. |
| `version` | `string` | Verze prostředí. |
| `environmentVariables` | `{string: string}` | Mapa hash proměnných prostředí a hodnot. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | Objekt, který definuje prostředí Pythonu a interpret pro použití na cílové výpočetní prostředek. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | Definuje nastavení pro přizpůsobení image Dockeru vytvořené podle specifikací prostředí. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | V části konfiguruje nastavení Spark. Používá se pouze v případě, že je rozhraní nastaveno na PySpark. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | Konfiguruje závislosti knihovny Databricks. |
| `inferencingStackVersion` | `string` | Určuje verzi inferencingového zásobníku přidanou do bitové kopie. Chcete-li se vyhnout přidání inferencing ového zásobníku, ponechte toto pole `null`. Platná hodnota: "nejnovější". |

## <a name="ml-pipeline-management"></a>Správa potrubí ML

Následující příkazy ukazují, jak pracovat s kanály strojového učení:

+ Vytvoření kanálu strojového učení:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Další informace naleznete [v tématu az ml pipeline create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create).

    Další informace o souboru YAML kanálu najdete [v tématu Definování kanálů strojového učení v yaml](reference-pipeline-yaml.md).

+ Spuštění kanálu:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Další informace naleznete [v tématu az ml run submit-pipeline](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Další informace o souboru YAML kanálu najdete [v tématu Definování kanálů strojového učení v yaml](reference-pipeline-yaml.md).

+ Naplánování kanálu:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Další informace naleznete [v tématu az ml pipeline create-schedule](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    Další informace o souboru YAML plánu kanálu najdete [v tématu Definování kanálů strojového učení v yaml](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Registrace modelu, profilování, nasazení

Následující příkazy ukazují, jak zaregistrovat trénovaný model a potom jej nasadit jako produkční službu:

+ Registrace modelu pomocí Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Další informace naleznete [v registru modelů az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **NEPOVINNÉ** Profilujte model a získejte optimální hodnoty procesoru a paměti pro nasazení.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Další informace naleznete [v tématu az ml model profil](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ Nasazení modelu do AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Další informace o schématu konfiguračního souboru odvození naleznete v tématu [Inference configuration schema](#inferenceconfig).
    
    Další informace o schématu konfiguračního souboru nasazení naleznete v [tématu Schéma konfigurace nasazení](#deploymentconfig).

    Další informace naleznete [v tématu az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Schéma konfigurace odvození

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Schéma konfigurace nasazení

### <a name="local-deployment-configuration-schema"></a>Schéma konfigurace místního nasazení

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Schéma konfigurace konfigurace instance azure kontejneru 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Schéma konfigurace konfigurace služby Azure Kubernetes

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Další kroky

* [Odkaz na příkaz pro rozšíření cli strojového učení](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [Trénování a nasazování modelů strojového učení pomocí Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
