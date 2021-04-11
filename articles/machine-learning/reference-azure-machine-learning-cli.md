---
title: Instalace & použití rozhraní příkazového řádku Azure Machine Learning
description: Naučte se používat rozšíření Azure CLI pro ML k vytváření & správě prostředků, jako je váš pracovní prostor, úložiště dat, datové sady, kanály, modely a nasazení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jordane
author: jpe316
ms.date: 04/02/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 9a22700782c584015add2c83ecc96ccc610b8509
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219616"
---
# <a name="install--use-the-cli-extension-for-azure-machine-learning"></a>Nainstalovat & použít rozšíření CLI pro Azure Machine Learning


Azure Machine Learning CLI je rozšíření [Azure CLI](/cli/azure/), rozhraní příkazového řádku pro více platforem pro platformu Azure. Toto rozšíření poskytuje příkazy pro práci s Azure Machine Learning. Umožňuje automatizovat aktivity machine learningu. Následující seznam uvádí některé ukázkové akce, které můžete provést s rozšířením CLI:

+ Spouštění experimentů k vytváření modelů strojového učení

+ Registrace modelů strojového učení pro zákaznické využití

+ Balení, nasazení a sledování životního cyklu modelů strojového učení

Rozhraní příkazového řádku není náhradou za sadu Azure Machine Learning SDK. Jedná se o doplňkový nástroj, který je optimalizovaný pro zpracování vysoce parametrizovaných úloh, které se dobře hodí pro automatizaci.

## <a name="prerequisites"></a>Požadavky

* Pokud chcete použít rozhraní příkazového řádku, musíte mít předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

* Pokud chcete v tomto dokumentu použít příkazy rozhraní příkazového řádku z vašeho **místního prostředí**, potřebujete [Azure CLI](/cli/azure/install-azure-cli).

    Použijete-li [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), k rozhraní příkazového řádku se dostanete v prohlížeči a v cloudu.

## <a name="full-reference-docs"></a>Úplné referenční dokumentace

Najděte [kompletní referenční dokumentaci pro rozšíření Azure CLI Azure CLI](/cli/azure/ext/azure-cli-ml/).

## <a name="connect-the-cli-to-your-azure-subscription"></a>Připojení rozhraní příkazového řádku k předplatnému Azure

> [!IMPORTANT]
> Pokud používáte Azure Cloud Shell, můžete tuto část přeskočit. Cloud Shell se automaticky ověřuje pomocí účtu, který se přihlašujete k předplatnému Azure.

Existuje několik způsobů, jak můžete z CLI ověřit předplatné Azure. Nejzákladnější je interaktivní ověřování pomocí prohlížeče. Chcete-li provést interaktivní ověřování, otevřete příkazový řádek nebo terminál a použijte následující příkaz:

```azurecli-interactive
az login
```

Pokud rozhraní příkazového řádku může spustit výchozí prohlížeč, udělá to a načte přihlašovací stránku. V opačném případě je nutné otevřít prohlížeč a postupovat podle pokynů v příkazovém řádku. Pokyny zahrnují procházení [https://aka.ms/devicelogin](https://aka.ms/devicelogin) a zadávání autorizačního kódu.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

Další metody ověřování najdete v tématu [přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="install-the-extension"></a>Instalace rozšíření

Rozšíření se automaticky nainstaluje při prvním pokusu o použití příkazu, který začíná na `az ml` .

## <a name="update-the-extension"></a>Aktualizace rozšíření

Chcete-li aktualizovat rozšíření Machine Learning CLI, použijte následující příkaz:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="remove-the-extension"></a>Odebrání rozšíření

Pokud chcete odebrat rozšíření rozhraní příkazového řádku, použijte následující příkaz:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Správa prostředků

Následující příkazy ukazují, jak používat rozhraní příkazového řádku ke správě prostředků používaných Azure Machine Learning.

+ Pokud ho ještě nemáte, vytvořte skupinu prostředků:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Vytvořit Azure Machine Learning pracovní prostor:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    Další informace najdete v tématu [AZ ml Workspace Create](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-create).

+ Připojte ke složce konfiguraci pracovního prostoru, aby bylo možné povolit sledování kontextu rozhraní příkazového řádku.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Tento příkaz vytvoří `.azureml` podadresář, který obsahuje příklady souborů prostředí RunConfig a conda. Obsahuje taky `config.json` soubor, který se používá ke komunikaci s vaším pracovním prostorem Azure Machine Learning.

    Další informace najdete v tématu [AZ ml složka připojit](/cli/azure/ext/azure-cli-ml/ml/folder#ext-azure-cli-ml-az-ml-folder-attach).

+ Připojte kontejner objektů blob Azure jako úložiště dat.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Další informace najdete v tématu [AZ ml DataStore Attach-BLOB](/cli/azure/ext/azure-cli-ml/ml/datastore#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Nahrajte soubory do úložiště dat.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Další informace najdete v tématu [AZ ml DataStore upload](/cli/azure/ext/azure-cli-ml/ml/datastore#ext-azure-cli-ml-az-ml-datastore-upload).

+ Připojte cluster AKS jako cíl služby Compute.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Další informace najdete v tématu [AZ ml computetarget Attach AKS](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

### <a name="compute-clusters"></a>Výpočetní clustery

+ Vytvořte nový spravovaný výpočetní cluster.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```



+ Vytvoření nového spravovaného výpočetního clusteru se spravovanou identitou

  + Spravovaná identita přiřazená uživatelem

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  + Spravovaná identita přiřazená systémem

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
+ Přidejte spravovanou identitu do existujícího clusteru:

    + Spravovaná identita přiřazená uživatelem
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    + Spravovaná identita přiřazená systémem

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

Další informace najdete v tématu [AZ ml computetarget Create amlcompute](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

<a id="computeinstance"></a>

### <a name="compute-instance"></a>Instance služby Compute
Spravujte výpočetní instance.  Ve všech níže uvedených příkladech je název výpočetní instance **CPU** .

+ Vytvořte nový computeinstance.

    ```azurecli-interactive
    az ml computetarget create computeinstance -n cpu -s "STANDARD_D3_V2" -v
    ```

    Další informace najdete v tématu [AZ ml computetarget Create computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-computeinstance).

+ Zastavte computeinstance.

    ```azurecli-interactive
    az ml computetarget computeinstance stop -n cpu -v
    ```

    Další informace najdete v tématu [AZ ml computetarget computeinstance stop](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop).

+ Spusťte computeinstance.

    ```azurecli-interactive
    az ml computetarget computeinstance start -n cpu -v
    ```

    Další informace najdete v tématu [AZ ml computetarget computeinstance Start](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start).

+ Restartujte computeinstance.

    ```azurecli-interactive
    az ml computetarget computeinstance restart -n cpu -v
    ```

    Další informace najdete v tématu [AZ ml computetarget computeinstance restart](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart).

+ Odstraní computeinstance.

    ```azurecli-interactive
    az ml computetarget delete -n cpu -v
    ```

    Další informace najdete v tématu [AZ ml computetarget Delete computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget#ext-azure-cli-ml-az-ml-computetarget-delete).


## <a name="run-experiments"></a><a id="experiments"></a>Spustit experimenty

* Zahajte spuštění experimentu. Při použití tohoto příkazu zadejte název souboru RunConfig (text před \* . RunConfig, pokud hledáte v systému souborů) s parametrem-c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach`Příkaz vytvoří `.azureml` podadresář, který obsahuje dva příklady souborů RunConfig. 
    >
    > Pokud máte skript Pythonu, který vytvoří objekt konfigurace spuštění programově, můžete použít [RunConfig. Save ()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) a uložit ho jako soubor RunConfig.
    >
    > Úplné schéma RunConfig lze nalézt v tomto [souboru JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). Schéma slouží k samoobslužnému dokumentování prostřednictvím `description` klíče každého objektu. Kromě toho existují výčty pro možné hodnoty a fragment šablony na konci.

    Další informace najdete v tématu [AZ ml Run odeslání-Script](/cli/azure/ext/azure-cli-ml/ml/run#ext-azure-cli-ml-az-ml-run-submit-script).

* Zobrazit seznam experimentů:

    ```azurecli-interactive
    az ml experiment list
    ```

    Další informace najdete v tématu [AZ ml experiment list](/cli/azure/ext/azure-cli-ml/ml/experiment#ext-azure-cli-ml-az-ml-experiment-list).

### <a name="hyperdrive-run"></a>HyperDrive spuštění

K provedení ladění parametrů můžete použít HyperDrive s Azure CLI. Nejprve vytvořte konfigurační soubor HyperDrive v následujícím formátu. Podrobnosti o parametrech ladění parametrů naleznete v tématu [vyladění parametrů pro modelový](how-to-tune-hyperparameters.md) článek.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Přidejte tento soubor spolu s konfiguračními soubory spuštění. Pak odešlete HyperDrive běh pomocí:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Všimněte si oddílu *argumenty* v RunConfig a *prostoru parametrů* v souboru Hyperdrive config. Obsahují argumenty příkazového řádku, které se mají předat skriptu pro školení. Hodnota v RunConfig zůstává pro každou iteraci stejná, zatímco rozsah v HyperDrive config se prochází. Nezadávejte v obou souborech stejný argument.

## <a name="dataset-management"></a>Správa datových sad

Následující příkazy ukazují, jak pracovat s datovými sadami v Azure Machine Learning:

+ Registrovat datovou sadu:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Informace o formátu souboru JSON, který slouží k definování datové sady, získáte pomocí `az ml dataset register --show-template` .

    Další informace najdete v tématu [AZ ml DataSet Register](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-register).

+ Vypíše všechny datové sady v pracovním prostoru:

    ```azurecli-interactive
    az ml dataset list
    ```

    Další informace najdete v tématu [AZ ml DataSet list](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-list).

+ Získat podrobnosti o datové sadě:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Další informace najdete v tématu [AZ ml DataSet show](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-show).

+ Zrušit registraci datové sady:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Další informace najdete v tématu [AZ ml DataSet Unregister](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-archive).

## <a name="environment-management"></a>Správa prostředí

Následující příkazy ukazují, jak vytvořit, zaregistrovat a vypsat Azure Machine Learning [prostředí](how-to-configure-environment.md) pro váš pracovní prostor:

+ Vytvořit soubory pro generování uživatelského rozhraní pro prostředí:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Další informace najdete v tématu [AZ ml Environment – generování uživatelského rozhraní](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Registrace prostředí:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Další informace najdete v tématu [AZ ml Environment Registry](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-register).

+ Seznam registrovaných prostředí:

    ```azurecli-interactive
    az ml environment list
    ```

    Další informace najdete v tématu [AZ ml Environment list](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-list).

+ Stažení registrovaného prostředí:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Další informace najdete v tématu [AZ ml Environment Download](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-download).

### <a name="environment-configuration-schema"></a>Schéma konfigurace prostředí

Pokud jste použili `az ml environment scaffold` příkaz, vygeneruje `azureml_environment.json` soubor šablony, který lze upravit a použít k vytvoření vlastních konfigurací prostředí pomocí rozhraní příkazového řádku. Objekt nejvyšší úrovně se [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29) v sadě Python SDK volně mapuje na třídu. 

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

Následující tabulka podrobně popisuje každé pole nejvyšší úrovně v souboru JSON, jeho typ a popis. Pokud je typ objektu propojený se třídou ze sady Python SDK, je mezi jednotlivými poli JSON a názvem veřejné proměnné ve třídě Pythonu volná 1:1. V některých případech může být pole namapováno na argument konstruktoru, nikoli na proměnnou třídy. Například `environmentVariables` pole je mapováno na `environment_variables` proměnnou ve [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29) třídě.

| Pole JSON | Typ | Description |
|---|---|---|
| `name` | `string` | Název prostředí. Nespouštějte jméno pomocí **Microsoft** nebo **AzureML**. |
| `version` | `string` | Verze prostředí. |
| `environmentVariables` | `{string: string}` | Mapa hodnoty hash názvů a hodnot proměnných prostředí. |
| `python` | [`PythonSection`](/python/api/azureml-core/azureml.core.environment.pythonsection)Hat definuje prostředí a překladač Pythonu, které se mají použít u cílového výpočetního prostředku. |
| `docker` | [`DockerSection`](/python/api/azureml-core/azureml.core.environment.dockersection) | Definuje nastavení pro přizpůsobení image Docker sestavené do specifikací prostředí. |
| `spark` | [`SparkSection`](/python/api/azureml-core/azureml.core.environment.sparksection) | Oddíl nakonfiguruje nastavení Sparku. Používá se jenom v případě, že je architektura nastavená na PySpark. |
| `databricks` | [`DatabricksSection`](/python/api/azureml-core/azureml.core.databricks.databrickssection) | Konfiguruje závislosti knihoven datacihly. |
| `inferencingStackVersion` | `string` | Určuje verzi zásobníku Inferencing přidanou k imagi. Chcete-li se vyhnout přidání zásobníku Inferencing, nechte toto pole `null` . Platná hodnota: "poslední". |

## <a name="ml-pipeline-management"></a>Správa kanálu ML

Následující příkazy ukazují, jak pracovat s kanály strojového učení:

+ Vytvoření kanálu strojového učení:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Další informace najdete v tématu [AZ ml Pipeline Create](/cli/azure/ext/azure-cli-ml/ml/pipeline#ext-azure-cli-ml-az-ml-pipeline-create).

    Další informace o souboru YAML kanálu najdete [v tématu definice kanálů strojového učení v YAML](reference-pipeline-yaml.md).

+ Spustit kanál:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Další informace najdete v tématu [AZ ml Run Submit-Pipeline](/cli/azure/ext/azure-cli-ml/ml/run#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Další informace o souboru YAML kanálu najdete [v tématu definice kanálů strojového učení v YAML](reference-pipeline-yaml.md).

+ Naplánování kanálu:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Další informace najdete v tématu [AZ ml Pipeline Create-Schedule](/cli/azure/ext/azure-cli-ml/ml/pipeline#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    Další informace o souboru YAML plánu kanálu najdete [v tématu Definování kanálů strojového učení v YAML](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Registrace modelů, profilace, nasazení

Následující příkazy ukazují, jak registrovat vyškolený model a pak ho nasadit jako produkční službu:

+ Zaregistrujte model pomocí Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Další informace najdete v tématu [AZ ml model Register](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-register).

+ **Volitelné** Profilujte model, abyste získali optimální hodnoty CPU a paměti pro nasazení.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Další informace najdete v tématu [AZ ml model Profile](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-profile).

+ Nasazení modelu do AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Další informace o schématu konfiguračního souboru odvození naleznete v tématu [schéma konfigurace](#inferenceconfig)odvození.
    
    Další informace o schématu konfiguračního souboru nasazení najdete v tématu [schéma konfigurace nasazení](#deploymentconfig).

    Další informace najdete v tématu [AZ ml model Deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy).

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

* [Odkaz na příkazy pro rozšíření Machine Learning CLI](/cli/azure/ext/azure-cli-ml/ml)

* [Školení a nasazení modelů strojového učení pomocí Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
