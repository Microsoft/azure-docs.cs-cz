---
title: Rozšíření pro Machine Learning CLI
titleSuffix: Azure Machine Learning
description: Další informace o rozšíření Azure Machine Learning rozhraní příkazového řádku Azure CLI. Azure CLI je nástroj příkazového řádku napříč platformami, která umožňuje pracovat s prostředky v cloudu Azure. Machine Learning rozšíření vám umožní pracovat s Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 85ebcc36d32b86ec2640ce7ce02190deaab19d6b
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997086"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>Pro Azure Machine Learning použít rozšíření CLI

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


## <a name="remove-the-extension"></a>Odebrat rozšíření

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

    Další informace najdete v tématu [AZ ml Workspace Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ Připojte ke složce konfiguraci pracovního prostoru, aby bylo možné povolit sledování kontextu rozhraní příkazového řádku.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Tento příkaz vytvoří `.azureml` podadresář, který obsahuje příklady souborů prostředí RunConfig a conda. Obsahuje `config.json` taky soubor, který se používá ke komunikaci s vaším pracovním prostorem Azure Machine Learning.

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

* Spusťte běh experimentu. Při použití tohoto příkazu zadejte název souboru RunConfig (text před \*. RunConfig, pokud hledáte v systému souborů) s parametrem-c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach` Příkaz`.azureml` vytvoří podadresář, který obsahuje dva příklady souborů RunConfig. 
    >
    > Pokud máte skript Pythonu, který vytvoří objekt konfigurace spuštění programově, můžete použít [RunConfig. Save ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) a uložit ho jako soubor RunConfig.
    >
    > Další příklady souborů RunConfig naleznete v tématu [https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml).

    Další informace najdete v tématu [AZ ml Run odeslání-Script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Zobrazit seznam experimentů:

    ```azurecli-interactive
    az ml experiment list
    ```

    Další informace najdete v tématu [AZ ml experiment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

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

[!INCLUDE [inferenceconfig](../../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Schéma konfigurace nasazení

### <a name="local-deployment-configuration-schema"></a>Schéma konfigurace místního nasazení

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Schéma konfigurace nasazení služby Azure Container instance 

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Schéma konfigurace nasazení služby Azure Kubernetes

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Další postup

* [Odkaz na příkazy pro rozšíření Machine Learning CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)

* [Školení a nasazení modelů strojového učení pomocí Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
